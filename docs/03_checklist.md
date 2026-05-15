# Claude Code 設計チェックリスト

最終更新: 2026-05-15

---

## プロジェクト初期設定

- [ ] `/init` で CLAUDE.md を自動生成（`CLAUDE_CODE_NEW_INIT=1` でインタラクティブ版）
- [ ] CLAUDE.md を 200行以内に保つ（超えると指示が無視されやすくなる）
- [ ] `.claude/rules/` でパススコープルールを整理（コンテキスト節約）
- [ ] 権限設定: `/permissions` で allowlist を設定、または Auto Mode を検討
- [ ] フックで必須チェックを自動化（eslint、typecheck等）

---

## サブエージェント設計

- [ ] `description` は Claude が自動委任できる十分に具体的な説明か
- [ ] `tools` は最小限の権限に絞っているか
- [ ] 調査系タスクには `haiku`、品質系には `opus` を指定したか
- [ ] 同じワーカーを繰り返し使う場合は `.claude/agents/` に定義したか
- [ ] 調査にはサブエージェントを使い、メインコンテキストを守っているか

---

## スキル設計

- [ ] SKILL.md frontmatter に `name` と `description` があるか
- [ ] 副作用のあるワークフローは `disable-model-invocation: true` を設定したか
- [ ] CLAUDE.md に毎回入れる必要のない情報はスキルに移動したか
- [ ] スキルは単一責任（一つのことだけ）になっているか

---

## フック設計

- [ ] 危険なコマンドは `PreToolUse` でブロックしているか
- [ ] ファイル編集後に必要なチェック（lint/typecheck）は `PostToolUse` に設定したか
- [ ] `SessionStart` で開発コンテキストを自動ロードしているか
- [ ] exec形式（`args: string[]`）を使い安全なコマンド起動にしているか（v2.1.139+）
- [ ] `continueOnBlock: true` でブロック理由を Claude にフィードバックしているか（v2.1.139+）

---

## セッション管理

- [ ] 無関係タスク間に `/clear` を使用しているか
- [ ] 長い探索には `use subagents to investigate X` を使っているか
- [ ] 2回以上同じ修正をしたら `/clear` して新しいプロンプトで再開しているか
- [ ] Checkpoint を活用してリスクある変更を試みているか（`/rewind` または `Esc+Esc`）
- [ ] 長期タスクは `/rename` でセッション名をつけて管理しているか

---

## 並列実行・スケール

- [ ] 独立したタスクはバックグラウンドエージェント（`--bg`）で並列実行しているか
- [ ] CI/CD には `claude -p "prompt" --allowedTools` を使用しているか
- [ ] Fan-out パターンで大規模マイグレーションを自動化しているか
- [ ] `/goal` コマンドで条件達成まで自律継続させているか（v2.1.139+）
- [ ] `claude agents` でバックグラウンドセッションを一覧管理しているか（v2.1.139+）

---

## 品質・安全

- [ ] Claude に自己検証手段（テスト・スクリーンショット・expected output）を提供しているか
- [ ] CLAUDE.md に「IMPORTANT」「YOU MUST」で重要ルールを強調しているか
- [ ] 過去に Claude が犯した同じミスがあれば CLAUDE.md に追記したか
- [ ] 検証なしにコードをシップしていないか（「信頼して検証」ルール）

---

## 新機能チェック（2026-05-15時点）

- [ ] `/goal` コマンドで完了条件を設定した自律実行を試したか
- [ ] `claude agents` でバックグラウンドセッションを管理しているか
- [ ] Auto Mode（`Shift+Tab`）で繰り返し承認を省略しているか
- [ ] `/ultrareview` で大規模 PR のレビューを自動化しているか
- [ ] `/ultraplan` で複雑な計画を自動化しているか
- [ ] Fast Mode（`/fast`）が Opus 4.7 になったことを確認したか
