---
description: GitHubトレンドと公式ドキュメントを調査してknowledgeベースを更新する。「トレンド調査」「ベストプラクティス更新」「/trend-update」と言われたら使用する。
---

# /trendupdate

GitHubトレンドと公式ドキュメントの最新情報を取得し、設計パターンを自己更新するスキル。
週1回の定期実行を推奨（毎週月曜 9:00 JST にクラウドルーティンが自動実行）。

## 手順

### 1. 前回調査の確認
`~/Library/Mobile Documents/iCloud~md~obsidian/Documents/Vault/research/trendupdate/latest.md` を読み込んで前回との差分検出基準を把握する。

### 2. 並列調査
以下のソースを**同時に**調査する（gh CLI + WebFetch）:

**GitHub（gh api で取得）:**
- `gh api repos/shanraisshan/claude-code-best-practice/contents/README.md --jq '.content' | base64 -d | head -300`
- `gh api repos/FlorianBruniaux/claude-code-ultimate-guide/contents/README.md --jq '.content' | base64 -d | head -200`
- `gh api repos/hesreallyhim/awesome-claude-code/contents/README.md --jq '.content' | base64 -d | head -200`
- `gh api repos/anthropics/claude-code/contents/CHANGELOG.md --jq '.content' | base64 -d | head -300`

**公式ドキュメント（WebFetch）:**
- https://code.claude.com/docs/en/best-practices
- https://code.claude.com/docs/en/sub-agents
- https://code.claude.com/docs/en/hooks
- https://code.claude.com/docs/en/memory

**Agent SDK / Managed Agents（WebFetch）:**
- https://code.claude.com/docs/en/agent-sdk/overview
- https://code.claude.com/docs/en/agent-sdk/subagents
- https://code.claude.com/docs/en/agent-teams
- https://code.claude.com/docs/en/agent-view
- https://platform.claude.com/docs/en/managed-agents/overview

Agent SDK 調査で特に抽出すべき概念:
- オーケストレーター / ワーカーの役割分担原則
- エージェント間ハンドオフの設計（何を渡し、何を渡さないか）
- ガードレール（入力バリデーション・出力検証・エスカレーション条件）
- ツール設計原則（最小権限・冪等性・エラーの明示）

Managed Agents 調査で特に抽出すべき概念:
- Agent SDK との使い分け判断基準（自社インフラ vs Anthropic ホスト）
- サンドボックス設計（何がセッションをまたいで保持されるか）
- REST API としての統合パターン（カスタムツールの実行フロー）
- プロトタイプ → 本番移行のパス

**MCP 設計パターン（WebFetch）:**
- https://code.claude.com/docs/en/mcp
- https://modelcontextprotocol.io/introduction
- `gh api repos/modelcontextprotocol/servers/contents/README.md --jq '.content' | base64 -d | head -200`（公式 MCP サーバー一覧）

MCP 設計で特に抽出すべき概念:
- カスタム MCP サーバーを作るか既存を使うかの判断基準
- ツールスキーマ設計原則（命名・引数・レスポンス形式）
- MCP サーバーのスコープ設定（プロジェクト / ユーザー / グローバル）
- 認証・セキュリティ設計（トークン管理・スコープ制限）

**モデル選択・コスト最適化（既存ソースから抽出）:**
- 既存調査ソース（CHANGELOG・公式ドキュメント）からモデル選択基準を抽出

モデル選択で特に抽出すべき概念:
- タスク種別ごとの推奨モデル（haiku / sonnet / opus の使い分け）
- コンテキスト長とコストのトレードオフ
- サブエージェントへのモデル割り当てパターン
- Fast Mode（Opus 高速化）の活用場面

**CLAUDE.md 設計ベストプラクティス（既存ソース + 公式ドキュメントから抽出）:**
- https://code.claude.com/docs/en/memory
- 既存調査ソース（best-practice・awesome-claude-code 等）から CLAUDE.md に関する記述を抽出

CLAUDE.md 設計として特に抽出すべき概念:
- CLAUDE.md に書くべき内容と書かない内容の判断基準
- グローバル（~/.claude/）とプロジェクト（.claude/）の使い分け
- 効果的なセクション構成・記述スタイル
- 自動読み込みの仕組みと優先順位
- チームで共有する場合の注意点

**AI オーケストレーション最適化（既存ソース + 公式ドキュメントから抽出）:**
- https://code.claude.com/docs/en/agent-teams
- https://code.claude.com/docs/en/agent-sdk/overview
- 既存調査ソースから AI オーケストレーションに関する記述を抽出

AI オーケストレーションとして特に抽出すべき概念:
- オーケストレーター / ワーカーの責務分離パターン
- エージェント間のコンテキスト受け渡し設計（何を渡し、何を渡さないか）
- 並列 vs 直列の使い分け判断基準
- ループ・再試行・エスカレーションの設計
- コスト最適化（どのタスクにどのモデルを割り当てるか）

**スキル設計思想（既存ソース + 公式ドキュメントから抽出）:**
- https://code.claude.com/docs/en/skills（存在する場合）
- 既存調査ソース（awesome-claude-code・best-practice 等）からスキル設計に関する記述を抽出

**エラーリカバリー・冪等性（既存ソース + 公式ドキュメントから抽出）:**
- https://code.claude.com/docs/en/agent-sdk/overview（エラーハンドリング関連）
- 既存調査ソースからエラー設計に関する記述を抽出

エラーリカバリー・冪等性として特に抽出すべき概念:
- 部分失敗時のリカバリー戦略（どこから再開するか）
- 冪等な操作設計（同じ処理を複数回実行しても安全か）
- サイレント失敗の防止（エラーをどう上位に伝播するか）
- チェックポイント設計（長時間タスクの途中保存）
- タイムアウト・リトライの設計原則

スキル設計思想として特に抽出すべき概念:
- スキル / コマンド / エージェントの使い分け判断基準
- `description` の書き方（自動呼び出し精度に直結）
- スキルのスコープ定義（1スキル1責務の原則）
- スキル間の依存関係設計（循環依存の回避）
- 入出力の契約（何を受け取り、何を返すか）
- エラー時の振る舞い（サイレント失敗しない設計）

### 3. 差分検出・記録
- 前回調査との差分を検出する
- `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/Vault/research/trendupdate/logs/YYYY-MM-DD.md` に詳細レポートを保存する（日本語）
- `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/Vault/research/trendupdate/logs/latest.md` を更新する
- `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/Vault/research/trendupdate/logs/changelog.md` に差分サマリーを追記する

### 4. 更新案提示・承認フロー
- `docs/02_design-patterns.md`（インデックス）と `docs/patterns/` 配下の各ファイルの更新案を提示する
- `docs/03_checklist.md` の更新案を提示する
- ユーザーの明示的な承認を待つ
- 承認後に反映し、git commit & push する

### 5. Vault への同期
承認・push 後に git リポジトリの `docs/patterns/` を Vault へ同期する：
```bash
cp ~/.claude/skills/trendupdate/docs/patterns/*.md \
  ~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/Vault/research/trendupdate/patterns/
cp ~/.claude/skills/trendupdate/docs/02_design-patterns.md \
  ~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/Vault/research/trendupdate/design-patterns.md
```

## 制約

MUST: ユーザーの承認を得てから docs/ を更新する
MUST NOT: 承認なしに自動上書きする

## ナレッジベース

- Obsidian Vault: `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/Vault/research/trendupdate/`
- リモート: https://github.com/D-watanabe1130/claude-code-trends
- 週次スケジュールタスク（月曜 9:00 自動実行）が Vault に直接書き込む
