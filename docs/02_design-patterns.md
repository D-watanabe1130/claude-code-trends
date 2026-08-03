# Claude Code 設計パターン集

最終更新: 2026-08-03

## 直近の主要変更（2026-08-03）

- **Claude 5 ファミリーがサブエージェントドキュメントに公式例示**: `model` フィールドで `claude-opus-5` / `claude-sonnet-5` / `claude-fable-5` をフルIDとして指定可能
- **`Ctrl+G` でプランをエディタ直接編集（公式明記）**: プランモードの Plan ステップで `Ctrl+G` を押すとエディタが開き計画を編集できる
- **`--no-session-persistence` フラグが公式明記**: `-p` 非インタラクティブ実行はデフォルトでセッション保存。CI/CDの使い捨て実行では明示的に追加が必要
- **フック入力 `effort` フィールドがオブジェクト型として明文化**: `{ level: "low"|"medium"|"high"|"xhigh"|"max" }` 形式
- **`/context` vs `/memory` の役割整理**: 読み込み確認→`/context`、ファイル編集→`/memory`

## 直近の主要変更（2026-07-27）

- **Auto Memory `modified` タイムスタンプ（v2.1.214+）**: memory ファイルへの書き込み時に ISO 8601 形式の `modified` フィールドが自動記録される
- **フック exit 2 の JSON 検証失敗時でも確実ブロック（v2.1.214+）**: JSON エラーが優先されていた旧挙動を修正
- **`/memory` のノンブロッキング化（v2.1.216+）**: GUI エディタで開いてもセッション継続（ターミナルエディタは従来通り）
- **`isolation: worktree` の Bash コマンド内容チェック（v2.1.216+）**: `git -C` / `--git-dir` / `GIT_DIR` / `GIT_WORK_TREE` / `cd` チェーンでメインチェックアウトにアクセスするコマンドをエラーに
- **パス限定ルールのブレース展開予算制限（v2.1.217+）**: 1,000 パターン / 4 MiB 超過分はリテラル扱い（CLI クラッシュを防止）
- **サブエージェントフロントマターフックのワークスペーストラスト（v2.1.218+）**: フロントマター定義フックにもワークスペーストラスト承認が必要に

## 直近の主要変更（2026-07-20）

- **`/doctor` CLAUDE.md トリム提案（v2.1.206+）**: コードベースから推測可能な情報を削除提案する機能追加
- **プラグインフック `${user_config.*}` 参照禁止（v2.1.207+）**: シェル形式プラグインフックでの制約追加
- **無効 glob パターンが「マッチなし」に変更（v2.1.207+）**: `.claude/rules/` の `[` を含む無効パターンが全体エラーからマッチなしに改善
- **MEMORY.md 書き込み後サイズチェック強化（v2.1.210+）**: 上限に近いとリマインダー、超過するとエラー
- **MEMORY.md 測定からフロントマター/コメントを除外（v2.1.211+）**: ロード後コンテンツのみ測定
- **`isolation: worktree` WD チェックをリポジトリ全体に拡張（v2.1.210+）**: モノレポ対応強化
- **`--setting-sources project` 除外の挙動修正（v2.1.211+）**: パス限定ルールも確実スキップ

## 直近の主要変更（2026-07-13）

- **`/doctor` コマンド（v2.1.205+）**: 重複サブエージェントファイルを自動検出・解決提案
- **`--append-subagent-system-prompt` フラグ（v2.1.205+）**: 全サブエージェント（ネスト含む）のシステムプロンプト末尾に一括追記
- **Explore モデル継承変更（v2.1.198+）**: Haiku 固定 → メイン会話のモデルを継承（API上限Opus）
- **`background` フィールド追加（v2.1.198+）**: サブエージェントのデフォルト実行がバックグラウンドに変更
- **`permissionMode: "manual"` エイリアス（v2.1.200+）**: `"default"` の人間可読エイリアス
- **`isolation: worktree` バグ修正（v2.1.203+）**: ワークツリー削除後のサイレント fallback を防止
- **`/agents` ウィザード廃止（v2.1.198+）**: インタラクティブウィザード廃止、ファイル直接編集方式に
- **ネスト同名エージェント優先ルール（v2.1.178+）**: CWD最近傍の定義が優先
- **`PermissionDenied` フックの `retry: true`**: Auto Mode 拒否後のリトライ制御パターン明文化
- **フック出力 10,000 文字上限**: 超過時はファイルにフォールバックが明文化

## 直近の主要変更（2026-07-06）

- **新規フックイベント4件確認**: `PostToolUseFailure`（ツール失敗後）/ `Notification`（通知送信時）/ `ConfigChange`（設定変更時）/ `ElicitationResult`（MCP Elicitation 回答後）
- **マッチャー仕様完全明文化**: `SessionStart`=ソース種別 / `SubagentStart`/`Stop`=エージェントタイプ / `StopFailure`=エラータイプ / `FileChanged`=ファイル名でマッチ
- **v2.1.195+**: ハイフン付きツール名完全一致・カンマ区切りマッチャーリスト対応
- **v2.1.196+**: `prompt_id` フィールド追加（OpenTelemetry連携）
- **v2.1.198+**: symlink経由でもpath-scoped rulesが正しくマッチ
- **v2.1.199+**: `$CLAUDE_CODE_BRIDGE_SESSION_ID` 環境変数（Remote Control相関）
- **逆境的レビュー警告**: 「ギャップを探せ」指示は常に過剰報告→「正確性・要件のみ報告」と明示すること（公式追加）
- **`shell` パラメータ**: command フックでシェルを明示指定可能に

## 直近の主要変更（2026-06-29）

- **Artifacts機能（ベータ）の公式確認**: セッション出力をプライベートWebページとして公開。Team/Enterpriseプランのみ。`Ctrl+]`で再オープン。
- **Code intelligence（LSP Tool）がExtension公式カテゴリに昇格**: コードインテリジェンスプラグインによるシンボルナビゲーション・ライブ型エラー。
- **`skillOverrides`設定の追加**: ファイル編集なしでスキル可視性をオーバーライド可能。
- **MCP tool search デフォルト有効**: アイドルMCPツールのコンテキスト消費を最小化。
- **features-overview大幅更新**: 機能ごとのコンテキストコスト表・ロードタイミング図が公式ドキュメントに追加。
- **並列化オプション3段階明確化**: Subagents / Background agents（agent-view）/ Agent teams が公式に区分。

## 直近の主要変更（2026-06-22）

- **スキル・プラグインが公式ベストプラクティスの中核機能に格上げ**（SKILL.md フォーマット・`disable-model-invocation: true` の公式推奨）
- **フックプレースホルダー変数の明文化**（`${CLAUDE_PROJECT_DIR}`・`${CLAUDE_PLUGIN_ROOT}`・`${CLAUDE_PLUGIN_DATA}`）
- **フック5タイプの完全ドキュメント確認**（command/http/mcp_tool/prompt/agent）
- **JSON 出力フォーマット詳細**（`additionalContext`・`suppressOutput`・`permissionDecision` 等）
- **Auto Mode の非インタラクティブ時 abort 仕様**（`-p` フラグ時、繰り返しブロックで自動中断）
- **`/goal` 条件の仕様詳細**（別の評価エージェントが毎ターン後に条件を再チェック）
- **`/rewind` の2モード区別**（"Summarize from here" vs "Summarize up to here"）

## 直近の主要変更（2026-06-15）

- **Claude Fable 5 登場**（v2.1.170）: "Mythos-class model"、汎用利用向け安全化
- **サブエージェント5段階ネスト**（v2.1.172）: サブエージェントが更にサブエージェントをスポーン可能（最大5レベル）
- **`enforceAvailableModels`**（v2.1.175）: マネージド設定でデフォルトモデル選択を組織全体で制限
- **多言語セッションタイトル**（v2.1.176）: 会話の言語で自動生成
- **`/sandbox` コマンド**: OS レベルのファイル/ネットワーク分離（~84% プロンプト削減）
- **`/loop` / `/schedule` コマンド**: ローカル/クラウドの定期タスク
- **`<important if="...">` CLAUDE.md タグ**: ドメイン特化ルールの無視防止
- **コンテキスト劣化の具体的数値**: 30-40% 以下維持推奨、300-400k トークンで劣化開始
- **`context: fork` スキルフィールド**: スキルを独立サブエージェントで実行
- **`CLAUDE_AGENT_SDK_DISABLE_BUILTIN_AGENTS=1`**: 非対話/SDK でビルトインエージェント無効化

## 直近の主要変更（2026-06-08 第2回）

- **Opus 4.8 リリース**（v2.1.154）: デフォルト high エフォート、Fast Mode は2倍コスト→2.5倍速、`/effort xhigh` で最大精度
- **ダイナミックワークフロー**（v2.1.154）: `ultracode` キーワードで数十〜数百のバックグラウンドエージェントを並列実行、`/workflows` コマンド、`.claude/workflows/` ディレクトリ
- **fallbackModel 設定**（v2.1.166）: プライマリモデル障害時のフォールバック最大3つ、`--fallback-model` CLIフラグ
- **requiredMinimumVersion/MaxVersion**（v2.1.163）: 組織管理者がバージョン範囲を強制（エンタープライズ管理）
- **Stop フックの additionalContext**（v2.1.163）: ブロックだけでなくClaudeへのフィードバックとして機能
- **glob deny ルール**（v2.1.166）: `"*"` で全ツール拒否などglobパターンをdenyルールに利用可能
- **SendMessage セキュリティ強化**（v2.1.166）: クロスセッションの権限リクエストを拒否、Auto モードでリレーブロック
- **MAX_THINKING_TOKENS=0**（v2.1.166）: デフォルト思考モデルでの思考無効化によるコスト削減
- **`.claude/skills` 自動ロード**（v2.1.157）: プラグインマーケットプレイス不要でローカルプラグインを自動ロード
- **Auto Mode の拡大**（v2.1.158）: Bedrock/Vertex/Foundry でも利用可能（`CLAUDE_CODE_ENABLE_AUTO_MODE=1` でオプトイン）
- **コミュニティエコシステム全体像判明**（初回GitHub取得）: 主要ワークフロー13個、クロスモデルワークフロー新カテゴリ（Router/Plugin/MCP型）

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

### 推奨ワークフロー（2026-08-03 公式確認）

1. **Explore**（Plan Mode）: ファイル読み込みのみ、変更なし
2. **Plan**（Plan Mode）: 詳細な実装計画作成 → **`Ctrl+G` でエディタを開いて計画を直接編集**可能
3. **Implement**（Default Mode）: コーディング＋テスト検証
4. **Commit**: コミット＆PR作成

**`Ctrl+G` の活用場面:**
- Claude の計画を受け入れる前にスコープを絞りたい
- 計画の一部を差し替えてから実装させたい
- チームレビュー用に計画書を整形したい

---

## サブエージェント設計

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
background: true         # 常にバックグラウンド実行（v2.1.198+: 未設定時もデフォルトでバックグラウンド）
permissionMode: manual   # "default" と同義のエイリアス（v2.1.200+）
initialPrompt: "前回の続きから開始します。"  # 初回ユーザーターンに自動送信
---
```

- `description` が自動委任の判断基準（具体的に書くほど精度が上がる）
- `tools` で権限を最小化（`disallowedTools` でdenylistも指定可）
- `model` で用途別コスト最適化（調査 → haiku、品質レビュー → opus）
- `memory: project` でセッションをまたいだ知識蓄積が可能
- **v2.1.198+**: `background` 未設定でもデフォルトでバックグラウンド実行になった
- **v2.1.200+**: `permissionMode: "manual"` は `"default"` の人間可読エイリアス
- **`/doctor` コマンド（v2.1.205+）**: 同名エージェントファイルの重複を自動検出・解決提案

### サブエージェント永続メモリパターン（2026-05-25+）

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

### Claude 5 モデルID の直接指定パターン（2026-08-03 公式確認）

サブエージェントの `model` フィールドでフルモデル ID を使い最新世代を明示指定できる:

```yaml
---
name: premium-reviewer
description: 高品質コードレビュー（Opus 5 使用）
model: claude-opus-5    # Claude 5 世代を直接指定
tools: Read, Grep, Glob
---
```

利用可能なエイリアスとフルID:

| エイリアス | フルID例 |
|-----------|---------|
| `opus` | `claude-opus-5` |
| `sonnet` | `claude-sonnet-5` |
| `haiku` | `claude-haiku-4-5-20251001` |
| `fable` | `claude-fable-5` |

- エイリアスは Anthropic が最新世代に自動マッピング（ベストプラクティス）
- フルIDは特定バージョンに固定したい場合（再現性重視）
- `--model` CLIフラグと同じ形式を受け付ける

### Explore モデル上書きパターン（v2.1.198+）

Explore のデフォルトモデルが Haiku 固定からメイン会話継承に変更された。コスト管理のため Haiku に戻す場合は組み込みを上書き:

```markdown
# ~/.claude/agents/Explore.md
---
name: Explore
description: Fast read-only search agent
model: haiku
---
```

プロバイダー別の動作:
- **Claude API**: 継承モデルを Opus 上限でキャップ（Sonnet 以下はそのまま）
- **Bedrock/Vertex/Foundry**: 上限なしで継承

### `--append-subagent-system-prompt` パターン（v2.1.205+）

非インタラクティブモードで全サブエージェント（ネスト含む）のシステムプロンプト末尾に追記:

```bash
# 全サブエージェントに共通の制約を一括適用
claude --append-subagent-system-prompt \
  "Always respond in Japanese. Never modify files outside /src." \
  -p "investigate and fix the auth bug"
```

CI/CD でのユースケース:
- 組織セキュリティポリシーの一括適用
- 言語・応答形式の統一強制
- デバッグ情報追加（ロギング指示の一括注入）

### /doctor による設定ヘルスチェック（v2.1.205+, 機能拡張 v2.1.206+）

```bash
/doctor  # セッション内で実行
```

**v2.1.205+** で検出・報告する内容:
- 同一 `.claude/agents/` ディレクトリ内で `name` フロントマターが重複するファイル
- リネームまたは削除の提案を提示

**v2.1.206+** で追加された機能:
- CLAUDE.md のトリム提案（コードベースから推測できる情報の削除候補を提示）
  - 削除対象候補: ディレクトリ構成、依存リスト、アーキテクチャ概要（コードを読めば分かるもの）
  - 保持対象: ツールデフォルトと異なる落とし穴、コードベース固有の規約、理由付き設計判断

**予防策**: エージェントファイル名を `name` フロントマターと一致させる（ファイル名は識別に使わないが混乱防止）。

### フォークパターン（2026-05-25+）

現在の会話履歴を継承したサブエージェントを生成。コンテキスト再説明が不要な場合に有効。プロンプトキャッシュを共有するため安価。

```bash
# 環境変数で有効化
CLAUDE_CODE_FORK_SUBAGENT=1 claude

# /fork コマンドで起動
/fork draft unit tests for the parser changes so far
```

### CLIフラグでセッションスコープ定義（2026-06-01+）

ファイルを作成せず、`--agents` フラグで JSON を直接渡してセッション限定のサブエージェントを定義できる。CI/CD スクリプトや一時テストに有効。

```bash
claude --agents '{
  "code-reviewer": {
    "description": "Expert code reviewer. Use proactively after code changes.",
    "prompt": "You are a senior code reviewer.",
    "tools": ["Read", "Grep", "Glob", "Bash"],
    "model": "sonnet"
  }
}'
```

### Agent(agent_type) 構文（スポーン制限）（2026-05-25+）

```yaml
---
name: coordinator
tools: Agent(worker, researcher), Read, Bash
---
```

`Agent(...)` なし → サブエージェント生成不可。`Agent` のみ → 全サブエージェント生成可。

### セッション全体をサブエージェントとして動作（2026-05-25+）

```bash
claude --agent code-reviewer          # CLIフラグ

# .claude/settings.json でプロジェクトデフォルト設定
{"agent": "code-reviewer"}
```

### @-メンション明示呼び出し（2026-05-25+）

```text
@"code-reviewer (agent)" look at the auth changes
```

プラグイン提供のサブエージェント: `@agent-my-plugin:review:security`

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

### プロンプトフック（2026-05-25+）

Claudeにyes/no判断を委ねるフック。AIによる動的ゲートキーピング。

```json
{
  "type": "prompt",
  "prompt": "このBashコマンドは安全ですか？ yes または no で答えてください。"
}
```

### if フィールド（条件付きフック実行）（2026-05-25+）

```json
{
  "type": "command",
  "command": "security-check.sh",
  "if": "Bash(rm *)"
}
```

### CLAUDE_ENV_FILE（環境変数永続化）（2026-05-25+）

SessionStart、CwdChanged フックで Bash 後続コマンドへ環境変数を渡す：

```bash
#!/bin/bash
if [ -n "$CLAUDE_ENV_FILE" ]; then
  echo 'export NODE_ENV=production' >> "$CLAUDE_ENV_FILE"
  echo "export PROJECT_ROOT=$(pwd)" >> "$CLAUDE_ENV_FILE"
fi
```

### watchPaths（FileChanged監視登録）（2026-05-25+）

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "watchPaths": ["/project/.envrc", "/project/.env"]
  }
}
```

FileChanged フック（`.envrc|.env` など）と組み合わせて使用。

### Setup フックイベント（一回限り初期化）（2026-06-01+）

`--init-only` フラグと組み合わせて、プロジェクトへの初回セットアップ処理を定義する。通常の SessionStart とは異なり、初回のみ実行。

```json
{
  "hooks": {
    "Setup": [{
      "hooks": [{ "type": "command", "command": "first-time-setup.sh" }]
    }]
  }
}
```

### SessionStart フックによるセッション自動設定（2026-06-01+）

SessionStart フックの `hookSpecificOutput` で、セッション名・初期メッセージ・スキル再ロードを自動制御できる。

```json
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "sessionTitle": "auth-refactor",
    "initialUserMessage": "前回のissue #42の続きを始めます",
    "reloadSkills": true
  }
}
```

### mcp_tool フック型（MCPツール直接呼び出し）（2026-06-01+）

MCP サーバーのツールをフックから直接呼び出す。外部サービスとの統合や高度な検証に有効。

```json
{
  "type": "mcp_tool",
  "server": "my_server",
  "tool": "security_scan",
  "input": { "file_path": "${tool_input.file_path}" }
}
```

ツールのテキスト出力は command フックの stdout と同様に扱われる。有効な JSON であれば decision として解析される。

### asyncRewake（長時間バックグラウンドモニタリング）

バックグラウンドで実行し、exit code 2 で Claude を再起動させるパターン。

```json
{
  "type": "command",
  "command": "background-monitor.sh",
  "async": true,
  "asyncRewake": true
}
```

- バックグラウンドで実行（Claude をブロックしない）
- exit code 2 で Claude を再起動（stderrがsystem reminderとして表示）
- CIビルド監視・テスト完了待機などの長時間処理に最適

### once: true（セッション中1回のみ実行）

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [{ "type": "command", "command": "setup.sh", "once": true }]
      }
    ]
  }
}
```

### PermissionRequest / PermissionDenied（権限の自動制御）

```json
{
  "hooks": {
    "PermissionDenied": [
      {
        "matcher": "Bash",
        "hooks": [{ "type": "command", "command": "retry-check.sh" }]
      }
    ]
  }
}
```

`retry-check.sh` が `{retry: true}` を返すと操作をリトライ。

Auto Mode 分類モデルが拒否した操作を条件付きで再試行させるパターン（2026-07-13 明文化）:

```bash
#!/bin/bash
# retry-check.sh: 特定条件を満たす場合のみリトライを許可
INPUT=$(cat)
TOOL=$(echo "$INPUT" | jq -r '.tool_name')

# git 操作は再試行を許可
if echo "$TOOL" | grep -q "^Bash$"; then
  CMD=$(echo "$INPUT" | jq -r '.tool_input.command')
  if echo "$CMD" | grep -qE "^git (status|log|diff|show)"; then
    echo '{"hookSpecificOutput": {"hookEventName": "PermissionDenied", "retry": true}}'
    exit 0
  fi
fi
exit 0  # リトライなし（デフォルト）
```

**注意**: フック出力文字列は 10,000 文字が上限。超過すると自動でファイルに保存され、プレビュー＋パスが表示される（2026-07-13 明文化）。

### PostToolUseFailure（ツール失敗後の処理）（2026-07-06確認）

ツールの実行が失敗した後に発火。失敗監視・アラート・リトライ判断に活用：

```json
{
  "hooks": {
    "PostToolUseFailure": [
      {
        "matcher": "Bash",
        "hooks": [{ "type": "command", "command": "notify-failure.sh" }]
      }
    ]
  }
}
```

### Notification（通知イベント処理）（2026-07-06確認）

Claude Code が通知を送信するタイミングで発火。`matcher` で通知タイプをフィルタ：

```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "permission_prompt",
        "hooks": [{ "type": "command", "command": "log-permission.sh" }]
      }
    ]
  }
}
```

マッチ可能な通知タイプ例: `permission_prompt`, `auth_success`

### ConfigChange（設定ファイル変更時）（2026-07-06確認）

Claude Code の設定ファイルが変更された際に発火。設定変更後の処理（スキル再ロード等）に：

```json
{
  "hooks": {
    "ConfigChange": [
      {
        "hooks": [{ "type": "command", "command": "on-config-change.sh" }]
      }
    ]
  }
}
```

### ElicitationResult（MCP Elicitation 回答後）（2026-07-06確認）

`Elicitation`（MCPサーバーがユーザー入力を要求）に対してユーザーが回答した後に発火：

```json
{
  "hooks": {
    "ElicitationResult": [
      {
        "hooks": [{ "type": "command", "command": "log-elicitation-result.sh" }]
      }
    ]
  }
}
```

### SessionStart のソース別マッチ（2026-07-06確認）

`SessionStart` マッチャーでセッション開始ソースを絞り込み可能：

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "resume",
        "hooks": [{ "type": "command", "command": "restore-context.sh" }]
      },
      {
        "matcher": "compact",
        "hooks": [{ "type": "command", "command": "reload-critical-context.sh" }]
      }
    ]
  }
}
```

| ソース値 | 意味 |
|---------|------|
| `startup` | 新規セッション開始 |
| `resume` | セッション再開 |
| `clear` | `/clear` によるリセット後 |
| `compact` | `/compact` 後の再開 |

### SubagentStart/Stop のエージェントタイプマッチ（2026-07-06確認）

```json
{
  "hooks": {
    "SubagentStart": [
      {
        "matcher": "Explore",
        "hooks": [{ "type": "command", "command": "track-explore-agent.sh" }]
      }
    ]
  }
}
```

サブエージェントタイプ（`general-purpose`, `Explore`, カスタム名）でフィルタリング可能。

### StopFailure のエラータイプマッチ（2026-07-06確認）

```json
{
  "hooks": {
    "StopFailure": [
      {
        "matcher": "rate_limit",
        "hooks": [{ "type": "command", "command": "handle-rate-limit.sh" }]
      }
    ]
  }
}
```

| エラータイプ | 意味 |
|------------|------|
| `rate_limit` | レートリミット到達 |
| `overloaded` | APIサーバー過負荷 |
| `authentication_failed` | 認証失敗 |

### PostToolBatch（並列ツール呼び出し後の検証）

複数ツールが並列実行された後、次のモデル呼び出し前にブロックできる。

```json
{
  "hooks": {
    "PostToolBatch": [
      {
        "hooks": [{ "type": "command", "command": "batch-validate.sh" }]
      }
    ]
  }
}
```

### prompt_id によるOpenTelemetry連携（v2.1.196+）（2026-07-06確認）

フック入力の共通フィールドに `prompt_id` が追加。分散トレーシングシステムと相関させることが可能：

```bash
#!/bin/bash
INPUT=$(cat)
PROMPT_ID=$(echo "$INPUT" | jq -r '.prompt_id')
SESSION_ID=$(echo "$INPUT" | jq -r '.session_id')

# OpenTelemetryやログシステムにprompt_idを渡す
curl -s "$OTEL_ENDPOINT/traces" \
  -d "{\"prompt_id\": \"$PROMPT_ID\", \"session_id\": \"$SESSION_ID\"}"
```

### $CLAUDE_CODE_BRIDGE_SESSION_ID（v2.1.199+）（2026-07-06確認）

Remote Control セッションとの相関追跡用の環境変数。Webダッシュボードやリモートコントロール統合で活用：

```bash
#!/bin/bash
if [ -n "$CLAUDE_CODE_BRIDGE_SESSION_ID" ]; then
  # Remote Control セッションと相関したログを出力
  echo "Bridge session: $CLAUDE_CODE_BRIDGE_SESSION_ID" >> /tmp/session-log.txt
fi
```

### HTTPフック with allowedEnvVars（認証付き外部エンドポイント）

```json
{
  "type": "http",
  "url": "https://api.company.com/hooks",
  "headers": { "Authorization": "Bearer $CI_TOKEN" },
  "allowedEnvVars": ["CI_TOKEN"]
}
```

### AGENTS.md 互換パターン（マルチエージェントフレームワーク共存）

Claude Code は `CLAUDE.md` を読む（`AGENTS.md` は読まない）。複数エージェントが共存するリポジトリでは：

```markdown
<!-- CLAUDE.md -->
@AGENTS.md

## Claude Code 固有設定
src/billing/ の変更には plan mode を使うこと。
```

または シンボリックリンク（Unix のみ）:
```bash
ln -s AGENTS.md CLAUDE.md
```

`/init` 実行時は `AGENTS.md`, `.cursorrules`, `.windsurfrules` を自動認識して取り込む。

---

## CLAUDE.md 設計

### HTMLコメントによるメンテナーノート（トークン節約）

```markdown
<!-- このセクションは2026Q3廃止予定 - 変更時はdev@team.comに連絡 -->
- 実際の指示: ...
```

ブロックレベルHTMLコメントはコンテキスト注入前に自動除去される。コードブロック内は保持。

### /btw コマンド（コンテキスト非汚染クエリ）

```text
/btw このAPIのレート制限は？
```

回答が会話履歴に**残らない**ディスミス可能オーバーレイとして表示。実装中に細部を確認したい場面で有効。

### AskUserQuestion ツールパターン（Claude インタビュー）

大型機能の開発前に Claude がユーザーにインタビューする公式推奨パターン：

```text
[機能のブリーフ説明]を実装したい。AskUserQuestion ツールで詳細にインタビューしてほしい。
技術実装、UI/UX、エッジケース、トレードオフについて聞いてほしい。
終わったら完全な仕様を SPEC.md に書いてほしい。
```

仕様完成後は**新しいセッション**で実装開始（クリーンなコンテキスト）。

---

## 非インタラクティブ実行パターン（2026-08-03 公式詳細明記）

```bash
# デフォルト: セッションが保存され、後で --resume で再開できる
claude -p "analyze this code"

# CI/CD の使い捨て実行: セッションを保存しない
claude -p "analyze this code" --no-session-persistence

# 出力フォーマット
# json: result フィールドを含む単一JSONオブジェクト（スクリプト解析向け）
claude -p "list API endpoints" --output-format json
# stream-json: 1行1JSON・init イベントから開始（リアルタイム処理向け）
claude -p "analyze logs" --output-format stream-json --verbose
```

**`/context` vs `/memory` の使い分け（2026-08-03 公式整理）:**

| コマンド | 用途 |
|---------|------|
| `/context` | ロード済みのメモリファイル一覧とその内容を確認（デバッグ用） |
| `/memory` | メモリファイルを開いて編集・Auto Memory のトグル（編集用） |

CLAUDE.md が正しくロードされているか確認するには `/context` を実行してメモリファイルリストをチェックする。

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

**glob パターンの注意点（v2.1.207+）**:
- `[` を含む無効なパターン（例: `photos [2024/**`）は v2.1.207 以降、そのパターンのみ「マッチなし」になる（他パターンは継続動作）
- v2.1.207 以前はこの無効パターンがそのルールを評価する全 Read ツール呼び出しを失敗させていた
- リテラルの `[` を使う場合はエスケープが必要: `photos \[2024/**`

```yaml
---
paths:
  - "photos \\[2024/**"  # リテラル [ のエスケープ
  - "src/**/*.ts"         # 有効パターン（これは正常動作）
---
```

### Auto Memory

- 保存先: `~/.claude/projects/<project>/memory/MEMORY.md`（デフォルト）
- 先頭200行 または 25KB をセッション開始時にロード（MEMORY.md のみ、トピックファイルはオンデマンド）
- `/memory` コマンドで閲覧・編集・トグル
- **サブエージェントも独自の Auto Memory を持てる**（専門知識の蓄積に有効）
- カスタム保存場所: `autoMemoryDirectory: "~/custom-dir"` を `~/.claude/settings.json` に設定

### MEMORY.md サイズ管理（v2.1.210+, v2.1.211+）

**v2.1.210+**: MEMORY.md への書き込み後、Claude Code が 200行/25KB 上限に対してサイズチェック:

| 状態 | 動作 |
|------|------|
| 上限に近い | リマインダーを返す（「1行1エントリ化、詳細をトピックファイルへ移動、古いエントリ削除」を促す） |
| 上限超過 | 書き込みは成功するが Claude Code がエラーを返す（次回ロード時に切り捨てられるため書き直し要求） |

**v2.1.211+**: サイズ測定の対象が「ロード後コンテンツ」に変更:
- YAML frontmatter とブロックレベル HTML コメントはロード前に除去されるため、測定対象から除外
- コメントが多くても誤って上限エラーが出なくなった

```markdown
# MEMORY.md 管理のベストプラクティス
- インデックスは1エントリ1行（概要のみ）
- 詳細は debugging.md, patterns.md 等のトピックファイルに分離
- 古くなったエントリは積極的に削除またはマージ
- YAML frontmatter はサイズ測定から除外される（v2.1.211+）
```

### Auto Memory `modified` フィールドによる鮮度管理（v2.1.214+）

Claude が YAML フロントマターを含む memory ファイルを書き込むと、Claude Code が `modified` フィールドに ISO 8601 タイムスタンプを自動記録する。

```yaml
---
modified: 2026-07-27T09:15:00Z
---
## ビルドコマンド
npm run build
```

- 既存フロントマターがあれば次回書き込み時に自動付与（なければ追加されない）
- v2.1.214 以降が必要
- 人間・Claude どちらも「この情報はいつ記録したか」をファイルを開かずに判断できる

**活用パターン**: 定期タスク（週次 trendupdate 等）で古い memory エントリを整理する際の判断基準として使う。

```bash
# MEMORY.md の中で modified が古いエントリを探す
grep -A1 "modified:" ~/.claude/projects/*/memory/MEMORY.md
```

### `isolation: worktree` の Bash コマンドチェックパターン（v2.1.216+）

`isolation: worktree` を使うサブエージェントでは、ワーキングディレクトリに加えて Bash コマンドの内容自体もチェックされる。

**ブロックされるコマンドパターン:**

```bash
# NG: git -C でメインチェックアウトを指定
git -C /path/to/main-checkout status

# NG: --git-dir でリダイレクト
git --git-dir=/path/to/.git log

# NG: 環境変数でリダイレクト
GIT_DIR=/path/to/.git git status

# NG: cd チェーンでメインチェックアウトに移動
cd /path/to/main-checkout && git push

# NG: 複雑すぎて解析不能（単純なコマンドに分割するよう求めるエラー）
cd /worktree && eval "$(cat complex_script.sh)"
```

**安全なパターン:**

```bash
# OK: ワークツリー内でそのまま git 操作（CWD がワークツリー内）
git add -A && git commit -m "changes"

# OK: 相対パスの使用
cp src/file.ts ./dst/

# OK: PowerShell（コマンド内容チェック対象外、WD チェックのみ）
Move-Item source.txt destination.txt
```

**設計指針**: `isolation: worktree` サブエージェントでは git 操作をシンプルな単一コマンドにとどめる。複数コマンドをチェーンする場合はシェルスクリプトに切り出し、そのスクリプト内で完結させる（スクリプト自体はワークツリー内の WD で実行されるため OK）。

### claudeMdExcludes（モノレポ向け）（2026-05-25+）

```json
{
  "claudeMdExcludes": [
    "**/monorepo/CLAUDE.md",
    "/home/user/monorepo/other-team/.claude/rules/**"
  ]
}
```

マネージドポリシーの CLAUDE.md は除外不可（組織全体で必ず適用）。

### ユーザーレベルルール（2026-05-25+）

```
~/.claude/rules/
├── preferences.md   # 全プロジェクトに適用する個人設定
└── workflows.md     # 個人ワークフロー
```

プロジェクトルールより先にロードされるため、プロジェクトルールが優先。

### .claude/rules/ シンボリックリンク（2026-05-25+）

```bash
ln -s ~/shared-claude-rules .claude/rules/shared
ln -s ~/company-standards/security.md .claude/rules/security.md
```

---

## プラグイン設計（2026-05-25+）

プラグインはスキル・フック・サブエージェント・MCPサーバーをバンドルしたインストール単位。

```
/plugin  # マーケットプレイスを開く
```

プラグインからのサブエージェントはセキュリティ上の理由から `hooks`・`mcpServers`・`permissionMode` フロントマターが無視される。必要な場合は `.claude/agents/` にコピーして使う。

### コードインテリジェンスプラグイン

typed言語（TypeScript、Python等）向け。シンボルナビゲーション・自動エラー検出を提供。

---

## Agent Teams パターン（実験的）（2026-05-25+）

```bash
CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1 claude
```

サブエージェント間を `SendMessage` ツールで通信。チームリードが複数ワーカーを協調制御。

```text
# サブエージェントを再開するパターン
Use the code-reviewer subagent to review the authentication module
[完了後]
Continue that code review and now analyze the authorization logic
```

サブエージェントIDは `~/.claude/projects/{project}/{sessionId}/subagents/agent-{id}.jsonl` で確認。

---

## CLAUDE.md 設計

> ★ 未取得 — 次回 trendupdate で `code.claude.com/docs/en/mcp` および `modelcontextprotocol.io` を調査して補完する

### カスタム MCP vs 既存 MCP の選択基準

| 状況 | 選択 |
|------|------|
| 公式・コミュニティ製サーバーが存在する | 既存を使う |
| 社内 API・独自サービスへのアクセスが必要 | カスタムを作る |
| 認証要件が複雑（OAuth 等） | 既存サーバーで対応できるか確認してから判断 |
| プロトタイプ段階 | 既存で試してから不足を特定する |

### ツールスキーマ設計原則

- ツール名は動詞+名詞で意図を明確に（例: `search_files`, `create_page`）
- 引数は必要最小限に絞る（Claude が推論で補える情報は省く）
- エラーレスポンスは Claude が次のアクションを判断できる情報を含める
- 冪等性を意識する（同じ引数で複数回呼ばれても安全な設計）

### スコープ設定

```json
// プロジェクト固有: .claude/settings.json
// ユーザー全体: ~/.claude/settings.json
// 特定セッションのみ: claude --mcp-config で渡す
```

---

## モデル選択・コスト最適化

> ★ 未取得 — 次回 trendupdate で公式ドキュメント・CHANGELOG から補完する

### タスク種別ごとの推奨モデル（暫定）

| タスク | 推奨モデル | 理由 |
|--------|-----------|------|
| ファイル探索・grep・調査 | Haiku | 速度重視・コスト低 |
| コード生成・実装 | Sonnet | バランス型 |
| 設計レビュー・品質チェック | Opus | 精度重視 |
| 大量並列処理（CI 等） | Haiku / Sonnet | コスト管理 |
| 高速レスポンスが必要な Opus タスク | Fast Mode (Opus) | 速度と品質を両立 |

### サブエージェントへの割り当てパターン

```yaml
# .claude/agents/researcher.md
---
model: haiku  # 調査系はコスト優先
tools: Read, Glob, Grep, WebSearch
---

# .claude/agents/reviewer.md
---
model: opus   # レビュー系は精度優先
tools: Read, Glob
---
```

### コスト管理の原則

- オーケストレーター（判断役）: Sonnet または Opus
- ワーカー（実行役）: タスクの複雑さに応じて Haiku か Sonnet
- `allowedTools` を絞るとコンテキスト消費を抑えられる

---

## エラーリカバリー・冪等性設計

> ★ 未取得 — 次回 trendupdate で公式ドキュメント・コミュニティソースから補完する

### 冪等性の原則

- 同じ操作を複数回実行しても結果が変わらない設計にする
- ファイル生成前に「既に存在するか」を確認する
- git commit 前に「変更差分があるか」を確認する（空コミット防止）
- 外部 API 呼び出しは成功済みかをチェックしてからリトライする

### 部分失敗時のリカバリー

```
タスク全体
  ├── ステップ1: 完了 ✓（スキップしてよい）
  ├── ステップ2: 失敗 ✗ ← ここから再開
  └── ステップ3: 未実行
```

- 長いタスクはステップ単位でチェックポイントを設ける
- 完了済みステップは冪等性を担保してスキップできるようにする
- 失敗したステップのエラー内容を上位（オーケストレーター/ユーザー）に明示する

### サイレント失敗の防止

- エラーを握りつぶして処理を続けない
- 失敗時は「何が失敗したか」「次に何をすべきか」をユーザーに伝える
- ワーカーエージェントは失敗をオーケストレーターに返し、自己判断で続行しない

### チェックポイント設計（長時間タスク）

- trendupdate の場合: 調査完了後に `knowledge/trends/YYYY-MM-DD.md` を保存してから承認フローへ
- 承認フロー失敗でも調査結果は保持され、次回は承認フローから再開できる

### タイムアウト・リトライ原則

- 外部 API（WebFetch・GitHub API）は一時障害を想定してリトライする
- リトライ回数は最大3回、間隔は指数バックオフ（1s → 2s → 4s）
- タイムアウト後は「取得できなかったソース」として記録し、残りを続行する

---

## スキル設計思想（Skill Design）

> ★ 未取得 — 次回 trendupdate で公式ドキュメント・コミュニティソースから抽出して補完する

### スキル / コマンド / エージェントの使い分け

| 種別 | 使う場面 |
|------|---------|
| **Skill** | 複数プロジェクトをまたいで再利用する手順・ドメイン知識 |
| **Command** | プロジェクト固有の繰り返し作業（`/deploy`, `/review` 等） |
| **Agent** | 単一責務の専門タスクに特化した常駐アシスタント |

### `description` の書き方原則

- 自動呼び出しのトリガーとなるキーワードを含める
- 「何をするスキルか」より「いつ使うか」を優先して書く
- 例: `「〇〇したいとき」「/xxx と言われたら」` 形式が有効

### スコープ定義（1スキル1責務）

- スキルが扱う入力・出力・副作用を明示する
- 複数の責務が混在したら分割を検討する
- 依存スキルは `## 関連ファイル` セクションに列挙し循環依存を避ける

### 入出力の契約

- スキルが受け取る前提情報（ファイルパス・ユーザー回答等）を冒頭に定義する
- 生成物（ファイル・コミット・レポート等）を完了条件として明示する
- エラー時はサイレントに続行せず、ユーザーへの確認または停止を選ぶ

---

## 実行基盤の選択：Agent SDK vs Managed Agents

> ★ 未取得 — 次回 trendupdate で公式ドキュメントを取得して補完する

### 選択基準

| 観点 | Agent SDK | Managed Agents |
|------|-----------|---------------|
| **実行場所** | 自分のプロセス・インフラ | Anthropic クラウド |
| **インターフェース** | Python / TypeScript ライブラリ | REST API |
| **作業対象** | 自分のファイルシステム・サービス | Anthropic 管理サンドボックス |
| **セッション状態** | ローカル JSONL | Anthropic ホストイベントログ |
| **向いている用途** | ローカル開発・自社インフラ上のエージェント | 外部ユーザー向けプロダクション製品 |

### 使い分けの原則

- **自社インフラ完結・ファイル操作中心** → Agent SDK
- **外部ユーザー向け・サンドボックスが必要** → Managed Agents
- **プロトタイプ段階** → Agent SDK で開発 → 本番移行時に Managed Agents へ

### Claude Code との関係

```
Claude Code CLI（対話型開発）
    ↕ 同じ能力・異なるインターフェース
Agent SDK（自動化・CI/CD・プログラム制御）
    ↕ 実行基盤のみ異なる
Managed Agents（Anthropic ホスト型・REST API）
```

---

## エージェントシステム設計（Agent SDK）

> ★ 未取得 — 次回 trendupdate で `code.claude.com/docs/en/agent-sdk` 等を調査して補完する

### オーケストレーター / ワーカー分離

- **オーケストレーター**: タスクの分解・委任・結果の統合を担う。直接ファイル操作しない
- **ワーカー**: 単一責務に特化。ツールセットを最小化し、スコープ外は拒否する
- 委任の判断基準は `description` の精度に依存（具体的なトリガー条件を書く）

### ハンドオフ設計

- 渡すべきもの: タスクの目的・制約・完了条件
- 渡さないもの: 不要な過去コンテキスト（コンテキストウィンドウ汚染を防ぐ）
- 完了シグナルを明示的に定義する（ファイル生成・コミット・レポート出力等）

### ガードレール

- **入力側**: オーケストレーターがワーカーに渡す前にスコープを検証
- **出力側**: ワーカーの出力をオーケストレーターが受け取る前に検証
- **エスカレーション**: ワーカーが判断できない場合はオーケストレーターに差し戻す（サイレント失敗しない）

### ツール設計原則

- **最小権限**: 各エージェントに必要なツールのみ付与（`tools:` で明示）
- **冪等性**: 同じ操作を複数回実行しても結果が変わらない設計
- **エラーの明示**: 失敗時はサイレントに続行せず、エラー内容をオーケストレーターに返す

---

## /sandbox による OS レベル分離パターン（2026-06-15+）

ファイルシステムとネットワークアクセスを OS レベルで制限し、パーミッションプロンプトを大幅削減する。

```bash
# セッション起動時にサンドボックスを有効化
claude --sandbox

# または /sandbox コマンドでトグル
/sandbox
```

- `--permission-mode auto` より更に強固なサンドボックス環境
- コミュニティ計測でパーミッションプロンプトを約 84% 削減
- 内部チェック・テスト・ビルドなど副作用が限定的なタスクに最適
- 外部 API 呼び出しが必要なタスクは auto mode または手動承認が必要

---

## ネストサブエージェントパターン（v2.1.172+）

サブエージェントが自分のサブエージェントをスポーンできるようになった（最大5段階）。

```
メインセッション（orchestrator）
  └── L1: research-coordinator
        ├── L2: file-searcher（Explore）
        └── L2: code-analyzer（general-purpose）
              └── L3: security-validator
                    └── L4: report-generator
```

**設計原則:**
- 各レベルのサブエージェントは単一責務に限定する（コンテキスト節約）
- L3 以深は軽量モデル（haiku）を推奨（コスト管理）
- 最大5段階は複雑なマルチエージェントワークフロー向け上限

```yaml
# .claude/agents/research-coordinator.md
---
name: research-coordinator
description: 大規模調査タスクを複数の専門サブエージェントに委任
model: sonnet
tools: Agent(file-searcher, code-analyzer), Read
---
```

---

## /loop・/schedule による定期タスクパターン（2026-06-15+）

| コマンド | 実行環境 | 最大間隔 | 用途 |
|---------|---------|---------|------|
| `/loop` | ローカル | 7日間 | 開発中の継続監視・定期チェック |
| `/schedule` | Anthropic クラウド | 未定 | 本番自動化・定期メンテナンス |

```text
# ローカル定期タスク例
/loop 30m check-logs-and-report

# クラウドスケジュール例
/schedule daily run-weekly-trend-update
```

---

## コンテキスト管理の数値目標（2026-06-15 確認）

コンテキスト使用量としきい値:

| 使用量 | 状況 | 対処 |
|--------|------|------|
| 〜30% | 最適（経験者はここを維持） | 継続 |
| 40% | "dumb zone" 入り口 | `/compact` 検討 |
| 70% | 精度低下開始 | `/compact` 実行 |
| 85%+ | 幻覚増加 | `/clear` または新セッション |
| 90%+ | 不規則レスポンス | 即 `/clear` |

- **300-400k トークン**: コンテキストウィンドウが詰まり始め劣化開始
- 自動圧縮はデフォルト 95% で発動（`CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` で変更可）

---

## `<important if="...">` CLAUDE.md タグパターン（2026-06-15 確認）

ドメイン特化ルールを CLAUDE.md の長さに関係なく遵守させるためのタグ。

```markdown
<!-- CLAUDE.md -->
<important if="working on authentication code">
- すべての認証変更にはセキュリティレビューが必要
- JWT 有効期限は必ず検証すること
- パスワードのログ出力禁止
</important>

<important if="modifying database migrations">
- 本番 DB に直接実行する前に staging で必ずテスト
- ロールバック手順を必ず記述
</important>
```

通常の CLAUDE.md 指示より強い遵守を引き出す。CLAUDE.md が 200行を超えていても重要ルールを目立たせられる。

---

## `context: fork` スキルパターン（2026-06-15 確認）

スキルを独立したフォークサブエージェントで実行する。メインコンテキストを汚染せずに重い処理を実行できる。

```markdown
---
name: heavy-refactor
description: 大規模リファクタリング作業
context: fork
---
以下の手順でリファクタリングを実施:
1. 対象ファイルの全体像を把握
2. テストカバレッジを確認
3. リファクタリング実施
4. テスト再実行で確認
```

---

## フォークパターン詳細（2026-06-08: /fork がデフォルト有効化）

v2.1.161 から `/fork` コマンドがデフォルト有効。フォーク観察パネルで実行状況を監視・制御できる。

```text
/fork draft unit tests for the parser changes so far
```

**フォークパネルのキー操作:**
- `↑`/`↓`: フォーク間を移動
- `Enter`: フォークのトランスクリプトを開き追加指示を送る
- `x`: 完了フォークを閉じる / 実行中を停止
- `Esc`: プロンプト入力にフォーカス戻す

| 比較点 | フォーク | 名前付きサブエージェント |
|-------|---------|---------------------|
| コンテキスト | 全会話履歴を継承 | フレッシュコンテキスト |
| システムプロンプト | メインセッションと同一 | 定義ファイルから |
| プロンプトキャッシュ | メインセッションと共有（安価） | 別キャッシュ |
| 権限プロンプト | ターミナルに表示 | バックグラウンド時は自動拒否 |

---

## 決定論的Stopゲートパターン（2026-06-08+）

テストやビルドがパスするまでターン終了をブロックするパターン。フォールバック: 8回連続ブロックでClaudeCodeが強制終了。

```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{ "type": "command", "command": "run-checks.sh" }]
    }]
  }
}
```

`run-checks.sh` が exit 2 を返すとブロック継続。exit 0 でターン終了を許可。

**チェック強度の選択：**
1. プロンプト内にテスト実行を含める（最軽量）
2. `/goal` 条件（ターン毎に評価サブエージェントが再チェック）
3. `Stop` フック（決定論的、最大8回ブロック）
4. 検証サブエージェント（独立コンテキストで批評的評価）

---

## Agent フックパターン（実験的）（2026-06-08+）

フックから検証用サブエージェントを直接スポーンする。PostToolUse や Stop と組み合わせて品質ゲートを実装。

```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{
        "type": "agent",
        "prompt": "Review the changes: $ARGUMENTS. Report only correctness issues.",
        "model": "claude-haiku-4-5-20251001",
        "timeout": 60
      }]
    }]
  }
}
```

---

## MessageDisplay フックパターン（2026-06-08+）

アシスタントメッセージのストリーミング中にテキストを変換・フィルタリングできる。会話履歴には影響しない。

```json
{
  "hooks": {
    "MessageDisplay": [{
      "hooks": [{
        "type": "command",
        "command": "transform-output.sh",
        "timeout": 10
      }]
    }]
  }
}
```

`transform-output.sh` が JSON で `displayContent` を返すと表示テキストが置き換わる。タイムアウトデフォルトは10秒（短め）。

---

## 品質保証パターン

### 逆境的レビューパターン（Adversarial Review）（2026-06-01+）

実装後に独立コンテキストのサブエージェントでレビューを行う公式推奨パターン。実装バイアスなしに要件との一致を確認する。

```text
Use a subagent to review the rate limiter diff against PLAN.md.
Check that every requirement is implemented, the listed edge cases have tests,
and nothing outside the task's scope changed.
Report only gaps that affect correctness or the stated requirements.
Style preferences are optional.
```

- ビルトインの `/code-review` スキルが同様の機能を提供
- **重要（2026-07-06 公式追加）**: 「ギャップを探す」よう指示されたレビュアーは実装が正しくても何かを報告する傾向がある。これは「余分な抽象化・防衛的コード・実際には起きないケースのテスト」という過剰エンジニアリングを招く。**「正確性と要件に関わるもののみ」と明示**することが必須。
- レビュアーが結果を受け取り、修正して再レビューする内部ループを形成できる

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

### ダイナミックワークフロー（v2.1.154+）

数十〜数百のバックグラウンドエージェントを並列オーケストレーション。

```bash
/effort ultracode  # ダイナミックワークフローをトリガー
/workflows         # ワークフロー一覧
```

- `.claude/workflows/` ディレクトリでワークフローを管理
- Deep Research などのバンドルワークフローが利用可能

---

## フォールバック・信頼性設計

### fallbackModel 設定（v2.1.166+）

```json
{
  "fallbackModel": ["claude-sonnet-4-5", "claude-haiku-4-5"]
}
```

- プライマリモデル障害時にフォールバック（最大3つ）
- `--fallback-model` CLIフラグでインタラクティブセッションにも適用
- 非リトライ可能なAPIエラー時に自動的に1回リトライ

### MAX_THINKING_TOKENS によるコスト制御（v2.1.166+）

```bash
MAX_THINKING_TOKENS=0 claude    # 思考を無効化（コスト削減）
--thinking disabled              # CLIフラグでも可
```

### エンタープライズバージョン管理（v2.1.163+）

```json
{
  "requiredMinimumVersion": "2.1.150",
  "requiredMaximumVersion": "2.1.200"
}
```

managed-settings に設定。バージョン範囲外の Claude Code は起動を拒否する。

---

## フックプレースホルダー変数パターン（2026-06-22 確認）

プラグイン開発・フック設定でパス解決に使用する公式変数:

| 変数 | 内容 |
|------|------|
| `${CLAUDE_PROJECT_DIR}` | プロジェクトルートの絶対パス |
| `${CLAUDE_PLUGIN_ROOT}` | プラグインのインストールディレクトリ |
| `${CLAUDE_PLUGIN_DATA}` | プラグインの永続データディレクトリ（セッション間で保持） |

```json
{
  "type": "command",
  "command": "node",
  "args": ["${CLAUDE_PLUGIN_ROOT}/script.js", "${CLAUDE_PROJECT_DIR}/target"]
}
```

プラグイン内のスクリプトを参照する際は `${CLAUDE_PLUGIN_ROOT}`、プラグインが状態を保持する際は `${CLAUDE_PLUGIN_DATA}` を使う。

---

## フック JSON 出力 `additionalContext` パターン（2026-06-22 確認）

`PostToolUse` や `Stop` フックから Claude にシステムリマインダーを注入する:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "additionalContext": "このファイルは生成済み。直接編集せず src/schema.ts を変更すること"
  }
}
```

- エラーとしてブロックせず、Claude へのコンテキスト情報としてターンを継続できる
- `Stop` フックの場合はフィードバック付きで Claude を再起動するパターンに活用

---

## スキル vs CLAUDE.md のオフロードパターン（2026-06-22 確認）

| 情報の種類 | 置き場所 | 読み込みタイミング |
|-----------|---------|-----------------|
| 毎回必要なコンベンション・ビルドコマンド | `CLAUDE.md` | 毎セッション常時 |
| 繰り返しのワークフロー手順 | `.claude/skills/` | オンデマンド（呼び出し時のみ） |
| コードベースの一部にしか関係しない知識 | `.claude/rules/*.md`（`paths` frontmatter） | 該当ファイル操作時のみ |
| タスク固有の参照資料 | スキル | オンデマンド |

**判断基準:** CLAUDE.md からある行を削除して「Claude がその行なしに間違いを犯すか」と問い、犯さないなら削除するかスキルへ移動する。

```markdown
# .claude/skills/fix-issue/SKILL.md
---
name: fix-issue
description: Fix a GitHub issue end-to-end
disable-model-invocation: true
---
1. `gh issue view $ARGUMENTS` で詳細確認
2. 関連ファイルを検索・修正
3. テスト実行
4. コミット＆PR 作成
```

`disable-model-invocation: true` → `/fix-issue 1234` で明示的に呼び出す専用ワークフロー。

---

## Auto Mode の非インタラクティブ abort パターン（2026-06-22 確認）

CI/CD パイプラインで auto mode を使う場合の注意事項:

```bash
claude --permission-mode auto -p "fix all lint errors"
```

**重要:** `-p`（非インタラクティブ）と `auto` を組み合わせた場合、分類モデルが繰り返しブロックすると**自動的に中断（abort）**する。ユーザーへのフォールバックができないため。

対処法:
1. `--allowedTools` で事前に許可ツールを明示する（分類モデルのブロックを減らす）
2. ブロックされたアクションを事前に確認し、プロンプトを調整する
3. 完全自動化が必要なタスクは、スコープを絞って auto mode が介入しにくくする

---

---

## Artifacts パターン（ベータ）（2026-06-29 確認）

セッション出力をプライベートWebページとして公開する（Team/Enterprise プランのみ）。

```text
# PR レビューアーティファクト
Make an artifact that walks through this PR. Render the diff with margin annotations
and color-code findings by severity.

# 進捗トラッキング
Turn this migration plan into a checklist artifact. Check items off as you complete them.

# 比較表
Make an artifact with four distinctly different layouts for the settings panel.
```

**操作:**
- `Ctrl+]`: 最新アーティファクトをターミナルから再オープン
- `CLAUDE_CODE_ARTIFACT_AUTO_OPEN=0`: 自動ブラウザ起動を無効化

**設定:**
```json
{ "disableArtifact": true }
```
または環境変数: `CLAUDE_CODE_DISABLE_ARTIFACT=1`

**制約:**
- CSPにより外部リクエスト・バックエンド不可（静的ページのみ）
- サイズ上限 16 MiB
- ソースは `.html`, `.htm`, `.md` のみ
- Anthropic API のみ（Bedrock/Vertex/Foundry 不可）
- `/login` でサインイン必須

**コスト削減:**
- ラスター画像の代わりにSVGやHTML/CSSを使う
- 大規模データセットは全インラインではなく要約を表示

---

## skillOverrides によるスキル可視性制御（2026-06-29 確認）

スキルファイルを編集せずに設定ファイルからスキルの可視性をオーバーライドできる:

```json
{
  "skillOverrides": {
    "my-heavy-skill": { "modelInvocable": false }
  }
}
```

- `modelInvocable: false` = `disable-model-invocation: true` と同等の効果
- ファイル編集権限のないプラグイン提供スキルに対して有効
- コンテキストコストをゼロにしたいが手動呼び出しは維持したい場合に使用

---

## MCP Tool Search による コンテキスト最小化（2026-06-29 確認）

MCP tool search がデフォルト有効になり、アイドル中のMCPツールはコンテキストを最小限しか消費しない:

```
/mcp    # 接続状態とトークンコストを確認
```

不要なMCPサーバーを切断する（接続したままでもtool searchでコスト削減可能だが、使わないなら切断が確実）。

---

## 機能ごとのコンテキストコスト設計（2026-06-29 確認）

| 機能 | ロードタイミング | コンテキストコスト |
|------|----------------|-----------------|
| CLAUDE.md | セッション開始時に全内容 | 毎リクエスト消費 |
| スキル（modelInvocable=true） | 開始時にdescription、使用時にフルコンテンツ | description分が常時 |
| スキル（modelInvocable=false） | 手動呼び出し時のみ | ゼロ（呼び出しまで） |
| MCPサーバー | 開始時にツール名、需要時にスキーマ | tool search有効ならほぼゼロ |
| Code intelligence | ファイル編集後・オンデマンド | 低（ファイル読み込みを削減） |
| サブエージェント | スポーン時（独立コンテキスト） | メインセッションから独立 |
| フック | トリガー時（外部実行） | ゼロ（出力返却時のみ加算） |

**設計原則:** 毎回必要でない情報はスキル（`disable-model-invocation: true`）または `.claude/rules/`（pathsフィルタ付き）に置く。

---

## 並列化オプション選択パターン（2026-06-29 確認）

| 種別 | ドキュメント | 特徴 | 使い所 |
|------|------------|------|-------|
| **Subagents** | `/en/sub-agents` | セッション内で実行、結果をメインに返す | 探索タスク・コンテキスト隔離 |
| **Background agents** | `/en/agent-view` | 複数の独立セッションを並列実行・監視 | 独立したタスクの大規模並列化 |
| **Agent teams** | `/en/agent-teams` | セッション間でP2Pメッセージ通信 | 複雑な協調・競合仮説・実験的 |

```
# 移行パス（コンテキスト限界に達したら）
Subagents → Agent teams（サブエージェントが互いに通信する必要が出たとき）
Background agents → Agent teams（セッション間の協調が必要になったとき）
```

---

## `/goal` 条件パターン（詳細仕様 2026-06-22 確認）

```text
/goal all tests pass and no lint errors
```

- **別の評価エージェント**がターン終了のたびに条件を再チェック
- 条件を満たすまで Claude が自動で作業を継続
- Stop フックとの違い: Stop フックはスクリプト実行（決定論的）、`/goal` は LLM による評価（柔軟）

**使い分け:**
| 条件 | `/goal` | `Stop` フック |
|------|---------|-------------|
| "全テストが通過" のような自然言語条件 | ✅ 向いている | ❌ スクリプトでの評価が難しい |
| "exit code 0" のような確定的条件 | △ 可能だが重い | ✅ 向いている |
| 未監視の長時間実行 | ✅ 向いている | ✅ 向いている（最大8回） |
