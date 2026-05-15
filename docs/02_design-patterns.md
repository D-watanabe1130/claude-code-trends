# Claude Code 設計パターン集

最終更新: 2026-05-15

## アーキテクチャパターン

### Command → Agent → Skill（オーケストレーション）

```
/コマンド → エージェント → スキル
```

- **Command**: ユーザーが呼び出すエントリーポイント（`.claude/commands/`）
- **Agent**: 特定タスクに特化した専門AIアシスタント（`.claude/agents/`）
- **Skill**: 再利用可能なワークフロー・ドメイン知識（`.claude/skills/`）

### Writer / Reviewer パターン

- Session A（Writer）: 実装
- Session B（Reviewer）: コードレビュー（異なるコンテキストで偏りなし）

### Fan-out パターン（大規模処理）

```bash
for file in $(cat files.txt); do
  claude -p "Migrate $file" --allowedTools "Edit,Bash(git commit *)"
done
```

---

## コンテキスト管理

### 基本原則

- コンテキストウィンドウが最重要リソース
- CLAUDE.md は 200行以内に保つ
- タスク間は `/clear` でリセット
- 調査・探索にはサブエージェントを使用

### 推奨ワークフロー

1. **Explore**（Plan Mode）: ファイル読み込みのみ、変更なし
2. **Plan**: 詳細な実装計画作成（`Ctrl+G` でエディタ編集）
3. **Implement**: コーディング＋テスト検証
4. **Commit**: コミット＆PR作成

---

## サブエージェント設計

```yaml
# .claude/agents/専門名.md
---
name: security-reviewer
description: Reviews code for security vulnerabilities
tools: Read, Grep, Glob, Bash
model: opus
---
```

- `description` が自動委任の判断基準（具体的に書くほど精度が上がる）
- `tools` で権限を最小化
- `model` で用途別コスト最適化（調査 → haiku、品質レビュー → opus）

---

## フックパターン

### PreToolUse（危険コマンドブロック）

```json
{
  "matcher": "Bash(rm *)",
  "hooks": [{ "type": "command", "command": "block-rm.sh" }]
}
```

### PostToolUse（自動lint）

```json
{
  "matcher": "Edit|Write",
  "hooks": [{ "type": "command", "command": "lint-check.sh" }]
}
```

### exec形式（v2.1.139+）

シェルを介さず安全にコマンドを起動。パス中の特殊文字のクォート不要。

```json
{
  "type": "command",
  "command": "my-script",
  "args": ["--flag", "${tool_input.file_path}"]
}
```

### continueOnBlock（v2.1.139+）

PostToolUse でブロック後、拒否理由を Claude にフィードバックしてターン継続。

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [{ "type": "command", "command": "validate.sh", "continueOnBlock": true }]
      }
    ]
  }
}
```

### terminalSequence（v2.1.141+）

フックからデスクトップ通知を送信。

```json
{
  "terminalSequence": "\033]777;notify;Claude Code;処理完了\007"
}
```

---

## Auto Mode

```bash
claude --permission-mode auto -p "fix all lint errors"
```

- 分類モデルがコマンドを事前評価
- スコープ拡大・未知インフラ・悪意ある指示をブロック
- `-p` との組み合わせで完全自律実行（CI/CDに最適）

---

## メモリ設計

### CLAUDE.md ヒエラルキー（優先順）

```
マネージドポリシー > ~/.claude/CLAUDE.md > ./CLAUDE.md > CLAUDE.local.md
```

### パススコープルール（.claude/rules/）

特定ファイルパターンに一致する場合のみロード。コンテキスト節約に有効。

```yaml
---
paths:
  - "src/api/**/*.ts"
---
# このルールはAPIファイル編集時のみロード
- すべてのAPIエンドポイントには入力バリデーションを含めること
```

### Auto Memory

- 保存先: `~/.claude/projects/<project>/memory/MEMORY.md`
- 先頭200行 または 25KB をセッション開始時にロード
- `/memory` コマンドで閲覧・編集・トグル

---

## スケールパターン

### バックグラウンドエージェント（v2.1.139+）

```bash
claude --bg -p "run test suite and report failures"
claude agents  # 全セッション一覧
```

### /goal による自律継続（v2.1.139+）

```
/goal all tests pass
```

条件達成まで Claude が自動でターンを継続。経過時間・ターン数・トークン数をリアルタイム表示。

### Fan-out + allowedTools でCI安全実行

```bash
claude -p "fix lint errors" \
  --allowedTools "Edit,Bash(npm run lint)" \
  --permission-mode auto
```
