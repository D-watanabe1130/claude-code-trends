# 最新トレンドサマリー

最終更新: 2026-08-24

---

## 最新バージョン

**v2.1.234+**（前回: v2.1.222+、memory ドキュメントに `CLAUDE_CODE_PROJECT_DIR_NAME` が v2.1.234+ 必須と明記）

主要マイルストーン:
- v2.1.178+: ネストディレクトリで同名エージェントはCWD最近傍が優先
- v2.1.195+: マッチャーhyphen対応・カンマ区切りリスト
- v2.1.196+: `prompt_id` フィールド（OpenTelemetry連携）
- v2.1.198+: symlink経由path-scoped rules対応 / Explore モデル継承変更 / `/agents` ウィザード廃止 / `background` フィールド追加・デフォルトでバックグラウンド化
- v2.1.199+: `$CLAUDE_CODE_BRIDGE_SESSION_ID`（Remote Control相関）
- v2.1.200+: `permissionMode: "manual"` エイリアス追加
- v2.1.203+: `isolation: worktree` Bash バグ修正
- v2.1.205+: `/doctor` コマンド・`--append-subagent-system-prompt` フラグ
- v2.1.206+: `/doctor` が CLAUDE.md トリム提案機能を追加
- v2.1.207+: プラグインフックの `${user_config.*}` 参照禁止 / 無効 glob パターンが「マッチなし」に変更（Read ツール失敗から改善）
- v2.1.210+: MEMORY.md 書き込み後のサイズチェック強化 / `isolation: worktree` の WD チェックがリポジトリ全体に拡張
- v2.1.211+: MEMORY.md 測定からフロントマター/コメント除外 / `--setting-sources project` 除外でパス限定ルールも確実スキップ
- v2.1.213+: `/import` コマンド（他コーディングエージェント設定の移行）
- v2.1.214+: Auto Memory `modified` フロントマターフィールド / フック exit 2 の JSON 検証失敗時でも確実ブロック
- v2.1.216+: `/memory` がGUIエディタ開放中もセッション継続 / `isolation: worktree` の Bash コマンド自体の git リダイレクトチェック
- v2.1.217+: パス限定ルールのブレース展開予算制限（1,000パターン / 4 MiB）
- v2.1.218+: サブエージェントフロントマターフックへのワークスペーストラスト適用
- v2.1.222+: `availableModels` allowlist のブロックファミリーエイリアス処理改善（許可済み最新バージョンへ代替）
- v2.1.234+: `CLAUDE_CODE_PROJECT_DIR_NAME` 環境変数（Auto Memory の複数リポジトリ間共有）

---

## 今週のホットトピック（2026-08-24）

1. **バージョン v2.1.234+ 確認**: memory ドキュメントに「`CLAUDE_CODE_PROJECT_DIR_NAME` は Claude Code v2.1.234 以降が必要」と明記。v2.1.222+ から少なくとも12バージョン進行したことが確認された。
2. **新規フックイベント3件確認**: `TeammateIdle`（Agent team チームメイトがアイドル状態になったとき）/ `DirectoryAdded`（セッション途中でディレクトリ追加時）/ `UserPromptExpansion`（コマンドがプロンプトに展開されたとき）が公式ドキュメントに新規掲載。
3. **`/batch` コマンド**: git リポジトリ内で変更を5〜30サブエージェントに自動分散し、各ワークツリーでPRを開く新コマンドが公式ベストプラクティスに明記。Fan-out パターンの新標準。
4. **`CLAUDE_CODE_PROJECT_DIR_NAME` 環境変数**: `CLAUDE_CONFIG_DIR` と合わせて設定すると、どのリポジトリで起動しても同じ Auto Memory を共有できる（v2.1.234+）。

---

## 前週（2026-08-17）との主要差分

| カテゴリ | 変更内容 |
|---------|---------|
| バージョン | **v2.1.234+** 確認（前回は v2.1.222+ まで） |
| フック | `TeammateIdle` / `DirectoryAdded` / `UserPromptExpansion` の3イベント新規確認 |
| コマンド | `/batch <instruction>` — 5〜30サブエージェントへの自動分散処理（公式ベストプラクティスに追加） |
| Auto Memory | `CLAUDE_CODE_PROJECT_DIR_NAME` で複数リポジトリ間共有が可能（v2.1.234+） |
| Setup フック | matcher 値 `init` / `maintenance` が公式確認 |

---

## 前々週以前からの既知事項（変更なし）

- サブエージェントのデフォルトネスト深さは3層（`CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH` で変更可能）
- 同時実行サブエージェント上限デフォルト 20（`CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS` で変更可能）
- `/subtask` コマンドが公式確認（フォーク型サブエージェント起動）
- バックグラウンドサブエージェントの利用可能ツールセット（19種）が明記
- Claude 5 ファミリー（`claude-opus-5` / `claude-sonnet-5` / `claude-fable-5`）がサブエージェントで利用可能
- `Ctrl+G` でプランモードの計画をテキストエディタで直接編集（公式ベストプラクティスに明記）
- `--no-session-persistence` フラグ: `-p` 実行はデフォルトでセッション保存
- フック入力 `effort` フィールドは `{ level: string }` オブジェクト型
- `/context` でCLAUDE.md読み込み確認、`/memory` でファイル編集
- Auto Memory に `modified` タイムスタンプ（ISO 8601）（v2.1.214+）
- フック exit 2 が JSON 検証失敗でも確実ブロック（v2.1.214+）
- `/memory` がGUIエディタ開放中もセッション継続（v2.1.216+）
- `isolation: worktree` の Bash コマンド git リダイレクト検出（v2.1.216+）
- パス限定ルールのブレース展開予算制限（v2.1.217+）
- サブエージェントフロントマターフックのワークスペーストラスト（v2.1.218+）
- `/doctor` コマンド（重複エージェントファイル診断 + CLAUDE.md トリム提案）: v2.1.205〜206+
- `--append-subagent-system-prompt` フラグ: v2.1.205+
- Explore モデル継承変更（Haiku固定 → 親会話モデル、API上限Opus）: v2.1.198+
- `background` フィールド追加・デフォルトバックグラウンド: v2.1.198+
- `permissionMode: "manual"` エイリアス: v2.1.200+
- `isolation: worktree` Bash バグ修正: v2.1.203+
- `/agents` ウィザード廃止: v2.1.198+
- `PermissionDenied` フックの `retry: true` パターン
- フック出力 10,000 文字上限
- 新規フックイベント4件: `PostToolUseFailure` / `Notification` / `ConfigChange` / `ElicitationResult`
- マッチャー仕様: SessionStart（ソース）/ SubagentStart/Stop（エージェントタイプ）/ StopFailure（エラータイプ）
- Artifacts機能（ベータ）: Team/Enterprise, `Ctrl+]`, 16MiB制限
- Code intelligence（LSP Tool）: Extension公式カテゴリ
- `skillOverrides`: `modelInvocable` フィールドでスキル可視性制御
- MCP tool search: デフォルト有効
- 並列化3段階: Subagents / Background agents / Agent teams
- `fallbackModel`: 最大3つのフォールバック
- ネストサブエージェント5段階（v2.1.172+）
- ダイナミックワークフロー（v2.1.154+）
- Auto Memory（v2.1.59+）
- `SessionEnd` フックイベント（clear/resume/logout/prompt_input_exit/bypass_permissions_disabled/other でマッチ）
- `CwdChanged` フックイベント
- `InstructionsLoaded` フックイベント（session_start/nested_traversal/path_glob_match/include/compact でマッチ）
- `autoMemoryDirectory` でAuto Memory 保存場所カスタマイズ
- `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD` 環境変数
- スキル `disable-model-invocation: true` フィールド
- `AGENTS.md` 互換パターン（`@AGENTS.md` インポートまたは symlink）
- `claudeMdExcludes` でモノレポ CLAUDE.md を選択的除外

---

## 外部ソース取得状況

- GitHub系（gh CLI / MCPスコープ制限）: **引き続き取得不可**
- 公式ドキュメント4種: ✅ 継続取得中
