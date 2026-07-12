---
description: GitHubトレンドと公式ドキュメントを調査してknowledgeベースを更新する。「トレンド調査」「ベストプラクティス更新」「/trend-update」と言われたら使用する。
---

# /trendupdate

GitHubトレンドと公式ドキュメントの最新情報を取得し、設計パターンを自己更新するスキル。
週1回の定期実行を推奨（毎週月曜 9:00 JST にクラウドルーティンが自動実行）。

## 手順

### 1. 前回調査の確認

`knowledge/trends/latest.md` を読み込んで前回との差分検出基準を把握する。
ファイルが存在しない場合は「初回実行」として全情報を新規記録する。

### 2. 並列調査

以下のソースを**同時に**調査する:

**公式ドキュメント（WebFetch）:**
- https://code.claude.com/docs/en/best-practices
- https://code.claude.com/docs/en/sub-agents
- https://code.claude.com/docs/en/hooks
- https://code.claude.com/docs/en/memory

**GitHub（MCP github ツール）:**
- `shanraisshan/claude-code-best-practice` README（スコープ制限で取得不可の場合は記録して続行）
- `FlorianBruniaux/claude-code-ultimate-guide` README（同上）
- `hesreallyhim/awesome-claude-code` README（同上）
- `anthropics/claude-code` CHANGELOG（同上）

> 注意: クラウド環境では MCP github スコープが `d-watanabe1130/claude-code-trends` のみ。
> `gh` CLI は未インストール。外部リポジトリは取得不可のため、取得できたソースのみで記録を続行する。

**Agent SDK / Managed Agents（WebFetch）:**
- https://code.claude.com/docs/en/agent-teams
- https://code.claude.com/docs/en/agent-view

**MCP 設計（WebFetch、余力があれば）:**
- https://code.claude.com/docs/en/mcp

### 3. 差分検出・記録

前回調査との差分を検出し、以下のファイルを更新する:

**`knowledge/trends/YYYY-MM-DD.md`（新規作成）:**

```yaml
---
tags: [ClaudeCode, トレンド調査]
updated: YYYY-MM-DD
---
```

- 最新バージョン
- 新規確認事項（前回からの差分のみ）
- 変更なし項目（前回確認済みとして箇条書き）
- 取得できなかったソースと理由

**`knowledge/trends/latest.md`（全文置換）:**

```markdown
# 最新トレンドサマリー

最終更新: YYYY-MM-DD

## 最新バージョン
vX.X.X（確認ソース）

## 今週のホットトピック
- **機能名**: 概要
（変化した上位10項目のみ）

## 前週との主要差分
（表形式）

## 外部ソース取得状況
```

**`changelog.md`（先頭に追記）:**
今週の発見を1段落で追加。

### 4. docs/ の更新

スケジュールタスク（自動実行）の場合は承認なしで更新してよい。
手動実行（ユーザーと対話中）の場合は更新案を提示してユーザーの承認を待つ。

**新規デザインパターンが見つかった場合:**
- `docs/02_design-patterns.md` の「直近の主要変更」セクションを更新
- 該当する `docs/patterns/` ファイルに詳細を追記

**新機能チェック項目が見つかった場合:**
- `docs/03_checklist.md` に新しいセクションを追加

### 5. コミット・プッシュ

```bash
git add -A
git commit -m "Weekly trend update $(date +%Y-%m-%d)"
git push origin main
```

### 6. 通知

重要な新規発見があれば PushNotification でオーナーに通知する。
発見がなければ通知しない（静かにする）。

## 制約

スケジュールタスク: 承認なしで docs/ を更新してよい
手動実行: ユーザーの明示的な承認を得てから docs/ を更新する

## ナレッジベース

- 前回調査: `knowledge/trends/latest.md`
- 詳細ログ: `knowledge/trends/YYYY-MM-DD.md`
- リモート: https://github.com/D-watanabe1130/claude-code-trends
