---
description: GitHubトレンドと公式ドキュメントを調査してknowledgeベースを更新する。「トレンド調査」「ベストプラクティス更新」「/trend-update」と言われたら使用する。
---

# /trend-update

GitHubトレンドと公式ドキュメントの最新情報を取得し、設計パターンを自己更新するコマンド。
週1回の定期実行を推奨する。

## 手順

1. **リサーチ開始**
   - trend-researcher エージェントを呼び出す

2. **並列調査**
   以下のソースを同時に調査する:
   - GitHub: shanraisshan/claude-code-best-practice
   - GitHub: FlorianBruniaux/claude-code-ultimate-guide
   - GitHub: hesreallyhim/awesome-claude-code
   - GitHub: anthropics/claude-code（CHANGELOG.md）
   - 公式: https://code.claude.com/docs/en/best-practices
   - 公式: https://code.claude.com/docs/en/sub-agents
   - 公式: https://code.claude.com/docs/en/hooks
   - 公式: https://code.claude.com/docs/en/memory

3. **差分検出・記録**
   - 前回調査との差分を検出する
   - knowledge/trends/YYYY-MM-DD.md に詳細を保存する
   - knowledge/trends/latest.md を更新する
   - knowledge/changelog.md に差分サマリーを追記する

4. **更新案提示・承認フロー**
   - docs/02_design-patterns.md の更新案を提示する
   - docs/03_checklist.md の更新案を提示する
   - ユーザーの明示的な承認を待つ
   - 承認後に反映する

## 制約

MUST: ユーザーの承認を得てから docs/ を更新する
MUST NOT: 承認なしに自動上書きする

## 実行推奨頻度

週1回（毎週同じ曜日に実行することを推奨）
