# claude-code-trends

Claude Code の最新トレンドを週次で追跡・記録するナレッジベース。

## リポジトリ構造

```
knowledge/trends/latest.md        # 最新サマリー（前回調査の基準）
knowledge/trends/YYYY-MM-DD.md   # 週次詳細レポート
docs/02_design-patterns.md        # 設計パターン集（インデックス）
docs/03_checklist.md              # 新機能チェックリスト
docs/patterns/                    # カテゴリ別の詳細パターンファイル
changelog.md                      # 週次更新サマリー（1段落形式）
SKILL.md                          # /trendupdate スキルの定義
```

## 週次スケジュールタスク

毎週月曜 9:00 JST にクラウドルーティンが自動実行する。
スケジュールタスクは**ユーザー不在で動く**ため、承認なしで docs/ を更新してよい。

## ソース取得の制約

| ソース | 手段 | 利用可否 |
|-------|------|---------|
| 公式ドキュメント（code.claude.com） | WebFetch | ✅ |
| 外部 GitHub リポジトリ（shanraisshan 等） | MCP github | ❌ スコープ制限 |
| anthropics/claude-code CHANGELOG | MCP github | ❌ スコープ制限 |
| `gh` CLI | Bash | ❌ 環境に未インストール |

取得不可のソースはレポートに「❌ 取得不可（理由）」として記録して続行する。

## 運用ルール

- 日本語で記述する
- 前回との差分にフォーカスし、変更のない情報は繰り返さない
- 全変更は `git add -A && git commit -m "Weekly trend update YYYY-MM-DD" && git push origin main`
- 重要な新規発見があれば PushNotification でオーナー（d.watanabe.marketing@gmail.com）に通知する
- 発見がなければ通知しない（静かにする）
