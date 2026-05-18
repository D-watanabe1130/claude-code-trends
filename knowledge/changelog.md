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

## 2026-05-18（第2回調査）

今週の調査では GitHub 外部リポジトリへのアクセスが失敗したため、公式ドキュメント（best-practices, sub-agents, hooks, memory）のみから差分を抽出した。最も重要な発見はフックシステムの大幅拡張で、PostToolBatch・SubagentStart/Stop・PermissionRequest/Denied・WorktreeCreate/Remove・Elicitation など約15種類のイベントが新たに文書化された。また、`asyncRewake: true`（バックグラウンドフックが exit code 2 で Claude を再起動）と `once: true`（セッション中1回のみ）という2つの新フックオプションも確認。CLAUDE.md 関連では AGENTS.md 相互運用ガイドの追加、HTMLコメントのコンテキスト自動除去（メンテナーメモをトークンを消費せずに残せる）、サブエージェントの独自 Auto Memory 対応が目立つ。新コマンドとして `/btw`（会話履歴に残らないサイドクエスチョン）と、`AskUserQuestion` ツールを使ったClaude によるインタビューパターンが公式ベストプラクティスに掲載された。

### 更新ファイル
- `knowledge/trends/2026-05-18.md`: 第2回トレンド調査レポート
- `knowledge/trends/latest.md`: 最新情報サマリー更新
- `docs/02_design-patterns.md`: asyncRewake・AGENTS.md互換・/btw パターン追記
- `docs/03_checklist.md`: 新機能チェック項目追加（2026-05-18時点）
