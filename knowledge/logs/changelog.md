# ナレッジベース変更履歴

## 2026-05-15（初回調査）

### 新規作成
- `knowledge/trends/2026-05-15.md`: 初回トレンド調査レポート
- `knowledge/trends/latest.md`: 最新情報サマリー
- `knowledge/changelog.md`: このファイル

### 発見事項サマリー
- Claude Code v2.1.142 が最新（Fast Mode → Opus 4.7）
- Agent View (`claude agents`) がv2.1.139で追加（大型機能）
- /goal コマンド追加（v2.1.139）
- フックの新形式（args exec形式、continueOnBlock）
- Auto Memory の正式ドキュメント化（200行/25KB制限）
- .claude/rules/ によるパススコープルール
- エコシステム: Superpowers(188k), Everything Claude Code(180k) が主要ワークフロー

### docs/ への反映状況
- `docs/02_design-patterns.md`: 新規作成（承認済み）
- `docs/03_checklist.md`: 新規作成（承認済み）

## 2026-05-25（第3回調査）

今週の調査では GitHub 外部リポジトリへのアクセスが引き続き失敗したため、公式ドキュメント（best-practices, sub-agents, hooks, memory）のみから差分を抽出した。最大の発見はサブエージェント機能の大幅拡張で、フロントマターフィールドが11個追加された（disallowedTools, permissionMode, maxTurns, skills, mcpServers, memory, background, effort, isolation, color, initialPrompt）。新設の `/agents` TUI コマンドでサブエージェントの作成・管理が視覚的に可能となり、実験的フォークモード（`CLAUDE_CODE_FORK_SUBAGENT=1` / `/fork`）で親会話履歴を継承したサブエージェントが起動できる。また `--agent <name>` フラグでセッション全体を特定サブエージェントとして動作させる機能も追加。フックシステムは5タイプ（command/http/mcp_tool/prompt/agent）・30イベントに確定し、新たに `if` フィールドと `CLAUDE_ENV_FILE` による環境変数永続化機能が判明。エコシステム面ではプラグインマーケットプレイス（`/plugin`）と実験的エージェントチーム（`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`）がベストプラクティスドキュメントに掲載された。

### 更新ファイル
- `knowledge/trends/2026-05-25.md`: 第3回トレンド調査レポート
- `knowledge/trends/latest.md`: 最新情報サマリー更新
- `docs/02_design-patterns.md`: フォーク・Agentチーム・/agents・サブエージェントメモリ等を追記
- `docs/03_checklist.md`: 新機能チェック項目追加（2026-05-25時点）

## 2026-05-18（第2回調査）

今週の調査では GitHub 外部リポジトリへのアクセスが失敗したため、公式ドキュメント（best-practices, sub-agents, hooks, memory）のみから差分を抽出した。最も重要な発見はフックシステムの大幅拡張で、PostToolBatch・SubagentStart/Stop・PermissionRequest/Denied・WorktreeCreate/Remove・Elicitation など約15種類のイベントが新たに文書化された。また、`asyncRewake: true`（バックグラウンドフックが exit code 2 で Claude を再起動）と `once: true`（セッション中1回のみ）という2つの新フックオプションも確認。CLAUDE.md 関連では AGENTS.md 相互運用ガイドの追加、HTMLコメントのコンテキスト自動除去（メンテナーメモをトークンを消費せずに残せる）、サブエージェントの独自 Auto Memory 対応が目立つ。新コマンドとして `/btw`（会話履歴に残らないサイドクエスチョン）と、`AskUserQuestion` ツールを使ったClaude によるインタビューパターンが公式ベストプラクティスに掲載された。

### 更新ファイル
- `knowledge/trends/2026-05-18.md`: 第2回トレンド調査レポート
- `knowledge/trends/latest.md`: 最新情報サマリー更新
- `docs/02_design-patterns.md`: asyncRewake・AGENTS.md互換・/btw パターン追記
- `docs/03_checklist.md`: 新機能チェック項目追加（2026-05-18時点）
