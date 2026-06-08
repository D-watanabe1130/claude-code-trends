---
tags: [claude-code, subagent, design-pattern]
updated: 2026-06-08
---

# サブエージェント設計

## 基本定義

```yaml
# .claude/agents/専門名.md
---
name: security-reviewer
description: Reviews code for security vulnerabilities
tools: Read, Grep, Glob, Bash
model: opus
memory: project          # 永続メモリ（project/user/local）
isolation: worktree      # 独立gitワークツリーで実行
effort: high             # 努力レベルのオーバーライド
color: red               # UIでの表示色
---
```

- `description` が自動委任の判断基準（具体的に書くほど精度が上がる）
- `tools` で権限を最小化（`disallowedTools` でdenylistも指定可）
- `model` で用途別コスト最適化（調査 → haiku、品質レビュー → opus）
- `memory: project` でセッションをまたいだ知識蓄積が可能

## 永続メモリパターン（2026-05-25+）

| スコープ | 保存先 | 用途 |
|---------|-------|------|
| `user` | `~/.claude/agent-memory/<name>/` | 全プロジェクト横断で知識蓄積 |
| `project` | `.claude/agent-memory/<name>/` | プロジェクト固有（バージョン管理で共有） |
| `local` | `.claude/agent-memory-local/<name>/` | プロジェクト固有・非共有 |

```yaml
---
name: code-reviewer
description: Expert code reviewer. Use proactively after code changes.
memory: project
---
Update your agent memory as you discover patterns, conventions, and recurring issues.
```

## フォークパターン（2026-05-25+）

現在の会話履歴を継承したサブエージェントを生成。コンテキスト再説明が不要な場合に有効。プロンプトキャッシュを共有するため安価。

```bash
CLAUDE_CODE_FORK_SUBAGENT=1 claude
/fork draft unit tests for the parser changes so far
```

## CLIフラグでセッションスコープ定義（2026-06-01+）

```bash
claude --agents '{
  "code-reviewer": {
    "description": "Expert code reviewer.",
    "tools": ["Read", "Grep", "Glob", "Bash"],
    "model": "sonnet"
  }
}'
```

## Agent(agent_type) 構文（スポーン制限）（2026-05-25+）

```yaml
---
name: coordinator
tools: Agent(worker, researcher), Read, Bash
---
```

`Agent(...)` なし → サブエージェント生成不可。`Agent` のみ → 全サブエージェント生成可。

## @-メンション明示呼び出し（2026-05-25+）

```text
@"code-reviewer (agent)" look at the auth changes
```

## ビルトインサブエージェント

| 名前 | モデル | 用途 |
|------|--------|------|
| Explore | - | CLAUDE.md と git status をスキップして高速調査 |
| Plan | - | 実装計画作成 |
| general-purpose | - | 汎用タスク |
| statusline-setup | Sonnet | ステータスライン設定 |
| output-style-setup | Sonnet | 出力スタイル設定 |
| claude-code-guide | Haiku | Claude Code ガイド |
| code-review | - | コードレビュー |

## CLAUDE_CODE_SUBAGENT_MODEL （一括モデル変更）（v2.1.161+）

```bash
CLAUDE_CODE_SUBAGENT_MODEL=claude-haiku-4-5 claude  # 全サブエージェントをHaikuで実行
```

サブエージェントのデフォルトモデルを環境変数で一括オーバーライド。テスト時のコスト削減に有効。

## CLAUDE_AUTOCOMPACT_PCT_OVERRIDE（圧縮しきい値変更）（v2.1.161+）

```bash
CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=80 claude  # コンテキストの80%で自動圧縮（デフォルト95%）
```

## /plugin list コマンド（v2.1.163+）

```bash
/plugin list            # インストール済プラグイン一覧
/plugin list --enabled  # 有効なプラグインのみ
/plugin list --disabled # 無効なプラグインのみ
```
