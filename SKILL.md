---
description: GitHubトレンドと公式ドキュメントを調査してknowledgeベースを更新する。「トレンド調査」「ベストプラクティス更新」「/trend-update」と言われたら使用する。
---

# /trendupdate

GitHubトレンドと公式ドキュメントの最新情報を取得し、設計パターンを自己更新するスキル。
週1回の定期実行を推奨（毎週月曜 9:00 JST にクラウドルーティンが自動実行）。

## 手順

### 1. 前回調査の確認
`~/.claude/skills/trendupdate/knowledge/trends/latest.md` を読み込んで前回との差分検出基準を把握する。

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

### 3. 差分検出・記録
- 前回調査との差分を検出する
- `knowledge/trends/YYYY-MM-DD.md` に詳細レポートを保存する（日本語）
- `knowledge/trends/latest.md` を更新する
- `knowledge/changelog.md` に差分サマリーを追記する

### 4. 更新案提示・承認フロー
- `docs/02_design-patterns.md` の更新案を提示する
- `docs/03_checklist.md` の更新案を提示する
- ユーザーの明示的な承認を待つ
- 承認後に反映し、git commit & push する

## 制約

MUST: ユーザーの承認を得てから docs/ を更新する
MUST NOT: 承認なしに自動上書きする

## ナレッジベース

- ローカル: `~/.claude/skills/trendupdate/knowledge/`
- リモート: https://github.com/D-watanabe1130/claude-code-trends
- 週次クラウドルーティン（月曜自動実行）が結果を push → `git pull` でローカルに反映
