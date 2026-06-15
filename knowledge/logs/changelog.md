# ナレッジベース変更履歴

## 2026-06-15（第7回調査）

CHANGELOG（v2.1.176まで）・公式ドキュメント4種・コミュニティリポジトリ3種を正常取得。前回（v2.1.168）から8バージョン分の更新を記録。最大の発見は **Claude Fable 5 の登場**（v2.1.170、"Mythos-class model"）と **サブエージェントの5段階ネスト対応**（v2.1.172）で、マルチエージェント設計の可能性が大幅に拡大した。その他の主要発見は：`enforceAvailableModels` マネージド設定でデフォルトモデルを制限可能に（v2.1.175）；セッションタイトルが会話言語に合わせて自動生成されるように（v2.1.176）；Bedrock 認証情報キャッシュが固定1時間から実際の有効期限まで有効になった（v2.1.176）。公式ドキュメントからは `/sandbox` コマンド（OS レベルのファイル/ネットワーク分離・パーミッションプロンプト ~84% 削減）、`/loop` と `/schedule` コマンド（ローカル/クラウド定期タスク）、`<important if="...">` CLAUDE.md タグ（ドメイン特化ルールの無視防止）、コンテキスト劣化の具体的しきい値（300-400k トークン・30%以下維持推奨）が初めて記録された。コミュニティではセキュリティリスクが顕在化し、FlorianBruniaux ガイドが28件の CVE と655件の悪意あるスキルを追跡している。awesome-claude-code は大規模リオーガニゼーション中で詳細取得不可。

### 更新ファイル
- `knowledge/logs/2026-06-15.md`: 今回トレンド調査詳細レポート
- `knowledge/logs/latest.md`: 最新情報サマリー更新
- `docs/02_design-patterns.md`: /sandbox・ネストサブエージェント・/loop・コンテキスト劣化しきい値追記
- `docs/03_checklist.md`: 2026-06-15時点の新機能チェック項目追加

---

## 2026-06-08（第6回調査・第3回更新）

今回は公式ドキュメント（`best-practices.md`、`memory.md`、`agent-teams.md`、`agent-sdk/subagents.md`）をプレーンテキスト形式で初めて正常取得できた。また shanraisshan/claude-code-best-practice のコミュニティREADMEから Hot 機能一覧を取得した。CHANGELOG は前回から変化なし（v2.1.168 Bug fixes のみ）。

主要発見は以下の通り:
- **Ultrareview/Ultraplan (beta)**: マルチエージェント深掘りレビュー・計画機能が正式 beta 化
- **Channels (beta)**: 外部イベント（CI結果、Slack通知等）をセッションにプッシュ
- **No Flicker Mode (beta)**: `/tui fullscreen` でフリッカーフリーレンダリング
- **Agent SDK AgentDefinition 完全フィールド**: `background`/`effort`/`permissionMode`/`initialPrompt`/`skills`（プリロード）/`disallowedTools` が公式ドキュメント化
- **エージェントチーム専用フック**: `TeammateIdle`、`TaskCreated`、`TaskCompleted`（品質ゲート実装可能）
- **Managed Policy CLAUDE.md**: `/Library/Application Support/ClaudeCode/CLAUDE.md` などで組織全ユーザーに強制適用
- **`claudeMd` マネージドセッティングキー**: `managed-settings.json` 内に CLAUDE.md 内容を直接記述
- **`CLAUDE_CODE_NEW_INIT=1`**: `/init` のインタラクティブマルチフェーズフロー
- **CLAUDE.md 設計詳細**: 200行制限・HTMLコメント自動除去・`@path` インポート（4ホップ上限）・`claudeMdExcludes`・`.claude/rules/` パス限定ルール
- **AGENTS.md 相互運用**: `@AGENTS.md` インポートまたはシンリンクで他エージェントと設定共有
- **コスト最適化**: `AgentDefinition.model` でタスク別モデル（opus/sonnet/haiku）割り当てが標準パターン化
- **品質ゲート3層**: Stop フック（決定論）+ `/goal`（自律継続）+ `additionalContext`（フィードバック）

### 更新ファイル
- `knowledge/logs/2026-06-08-3rd.md`: 第3回トレンド調査詳細レポート
- `knowledge/logs/latest.md`: 最新情報サマリー更新

---

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

## 2026-06-08（第5回調査）

今回はGitHub CHANGELOG（v2.1.168まで）とコミュニティREADME（shanraisshan/claude-code-best-practice）を初めて正常取得できた。公式ドキュメント（SPA）は引き続き取得不可。最大の発見は **Opus 4.8 リリース**（v2.1.154）で、デフォルト high エフォートで動作し Fast Mode は 2.5倍速/2倍コストを実現。**ダイナミックワークフロー**（v2.1.154）は `ultracode` キーワードで数十〜数百のバックグラウンドエージェントを並列オーケストレーション。**fallbackModel**（v2.1.166）はプライマリモデル障害時のフォールバック最大3つを設定可能（本番信頼性向上）。**requiredMinimumVersion/MaxVersion**（v2.1.163）はエンタープライズバージョン管理を実現。Stop フックの **additionalContext**（v2.1.163）はブロックだけでなくClaudeへのフィードバックとして機能し品質ゲートの柔軟性が向上。セキュリティ面では SendMessage クロスセッション権限のブロック（v2.1.166）とシェルスタートアップファイルへの書き込み確認（v2.1.160）が追加。コミュニティではクロスモデルワークフロー（Router/Plugin/MCP型）という新カテゴリが確認された。

### 更新ファイル
- `knowledge/trends/2026-06-08.md`: トレンド調査レポート（第2回更新）
- `knowledge/logs/latest.md`: 最新情報サマリー更新

### 差分サマリー（15項目）
- Opus 4.8 リリース（v2.1.154）
- ダイナミックワークフロー・ultracode キーワード（v2.1.154）
- fallbackModel 設定・--fallback-model フラグ（v2.1.166）
- requiredMinimumVersion / requiredMaximumVersion（v2.1.163）
- Stop/SubagentStop フックの additionalContext（v2.1.163）
- スキルの `\$` エスケープ構文（v2.1.163）
- `/plugin list` コマンド（v2.1.163）
- `.claude/skills` 自動ロード + `claude plugin init`（v2.1.157）
- `claude agents --json` の `waitingFor` フィールド（v2.1.162）
- Bedrock/Vertex/Foundry の Auto Mode 対応（v2.1.158）
- シェルスタートアップファイル書き込み確認（v2.1.160）
- SendMessage クロスセッションセキュリティ強化（v2.1.166）
- MAX_THINKING_TOKENS=0 で思考無効化（v2.1.166）
- glob パターンの deny ルール（v2.1.166）
- コミュニティ主要ワークフロー・クロスモデル統合の全体像（初回取得）

---

## 2026-06-08（第4回調査・初回）

今週の調査では GitHub CHANGELOG と公式ドキュメント（SPA）を調査した。公式ドキュメントは引き続きcurlで取得不可。CHANGELOG（v2.1.163まで）から主要な変更を抽出。最大の発見は `/fork` コマンドのデフォルト有効化（v2.1.161）、Stop フックの決定論的ゲートパターン（最大8回ブロック）、MessageDisplay フックによるストリーミング中のテキスト変換、Agent フック型（実験的）によるフックからのサブエージェントスポーン。

### 更新ファイル
- `knowledge/trends/2026-06-08.md`: 初回 
- `knowledge/logs/latest.md`: 最新情報サマリー更新
- `docs/02_design-patterns.md`: フォーク詳細・Stop/MessageDisplay/Agentフックパターン追記
- `docs/03_checklist.md`: 2026-06-08時点の新機能チェック項目追加

---

## 2026-06-01（第4回調査）

今週の調査では GitHub CHANGELOG と公式ドキュメント（SPA）を調査した。最大の発見はビルトインサブエージェント7種の正式確認（Explore/Plan/general-purpose/statusline-setup/output-style-setup/claude-code-guide/code-review）、`Setup` フックイベント（一回限り初期化）、`SessionStart` フックの高機能化（`sessionTitle`/`initialUserMessage`/`reloadSkills`）、`mcp_tool` タイプフックのMCPサーバーツール直接呼び出し、逆境的レビューパターン（Adversarial Review）の公式化。

### 更新ファイル
- `knowledge/trends/2026-06-01.md`: トレンド調査レポート
- `knowledge/logs/latest.md`: 最新情報サマリー更新
- `docs/patterns/subagent-design.md`: ビルトイン7種・CLIフラグ定義
- `docs/patterns/hooks.md`: Setup/SessionStart高機能化/mcp_tool
- その他 patterns/ 各ファイル更新
- `docs/02_design-patterns.md`: 逆境的レビューパターン等追記
- `docs/03_checklist.md`: 2026-06-01時点の新機能チェック項目追加

---

## 2026-05-18（第2回調査）

今週の調査では GitHub 外部リポジトリへのアクセスが失敗したため、公式ドキュメント（best-practices, sub-agents, hooks, memory）のみから差分を抽出した。最も重要な発見はフックシステムの大幅拡張で、PostToolBatch・SubagentStart/Stop・PermissionRequest/Denied・WorktreeCreate/Remove・Elicitation など約15種類のイベントが新たに文書化された。また、`asyncRewake: true`（バックグラウンドフックが exit code 2 で Claude を再起動）と `once: true`（セッション中1回のみ）という2つの新フックオプションも確認。CLAUDE.md 関連では AGENTS.md 相互運用ガイドの追加、HTMLコメントのコンテキスト自動除去（メンテナーメモをトークンを消費せずに残せる）、サブエージェントの独自 Auto Memory 対応が目立つ。新コマンドとして `/btw`（会話履歴に残らないサイドクエスチョン）と、`AskUserQuestion` ツールを使ったClaude によるインタビューパターンが公式ベストプラクティスに掲載された。

### 更新ファイル
- `knowledge/trends/2026-05-18.md`: 第2回トレンド調査レポート
- `knowledge/trends/latest.md`: 最新情報サマリー更新
- `docs/02_design-patterns.md`: asyncRewake・AGENTS.md互換・/btw パターン追記
- `docs/03_checklist.md`: 新機能チェック項目追加（2026-05-18時点）
