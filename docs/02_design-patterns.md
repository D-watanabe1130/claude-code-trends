# Claude Code 設計パターン集

最終更新: 2026-06-08（第2回更新）

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

- 保存先: `~/.claude/projects/<project>/memory/MEMORY.md`（デフォルト）
- 先頭200行 または 25KB をセッション開始時にロード（MEMORY.md のみ、トピックファイルはオンデマンド）
- `/memory` コマンドで閲覧・編集・トグル
- **サブエージェントも独自の Auto Memory を持てる**（専門知識の蓄積に有効）
- カスタム保存場所: `autoMemoryDirectory: "~/custom-dir"` を `~/.claude/settings.json` に設定

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
Report gaps, not style preferences.
```

- ビルトインの `/code-review` スキルが同様の機能を提供
- **注意**: 「ギャップを探す」指示は過剰エンジニアリングを招くため、「正確性・要件に関わるもののみ報告」と明示すること
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
