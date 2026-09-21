# 最新トレンドサマリー

最終更新: 2026-09-21

---

## 最新バージョン

**v2.1.277+**（前回: v2.1.267+）

新規判明マイルストーン（今週）:
- v2.1.271+: `omitClaudeMd` サブエージェントフロントマターフィールド
- v2.1.277+: **AGENTS.md ネイティブサポート**

その他補完:
- v2.1.233+: `claude plugin validate <path>` コマンド
- v2.1.242+: `/tasks` コマンドでモデル・エフォートレベル表示

主要マイルストーン（累積）:
- v2.1.178+: ネストディレクトリで同名エージェントはCWD最近傍が優先
- v2.1.195+: マッチャーhyphen対応・カンマ区切りリスト
- v2.1.196+: `prompt_id` フィールド（OpenTelemetry連携）
- v2.1.198+: symlink経由path-scoped rules対応 / Explore モデル継承変更 / `/agents` ウィザード廃止 / `background` フィールド追加・デフォルトでバックグラウンド化
- v2.1.199+: `$CLAUDE_CODE_BRIDGE_SESSION_ID`（Remote Control相関）
- v2.1.200+: `permissionMode: "manual"` エイリアス追加
- v2.1.203+: `isolation: worktree` Bash バグ修正
- v2.1.205+: `/doctor` コマンド・`--append-subagent-system-prompt` フラグ
- v2.1.206+: `/doctor` が CLAUDE.md トリム提案機能を追加
- v2.1.207+: プラグインフックの `${user_config.*}` 参照禁止 / 無効 glob パターンが「マッチなし」に変更
- v2.1.210+: MEMORY.md 書き込み後のサイズチェック強化 / `isolation: worktree` の WD チェックがリポジトリ全体に拡張 / サブエージェント出力スキャン（instruction-shaped パターン検出）
- v2.1.211+: MEMORY.md 測定からフロントマター/コメント除外 / `--setting-sources project` 除外でパス限定ルールも確実スキップ
- v2.1.213+: `/import` コマンド（他コーディングエージェント設定の移行）
- v2.1.214+: Auto Memory `modified` フロントマターフィールド / フック exit 2 の JSON 検証失敗時でも確実ブロック
- v2.1.216+: `/memory` がGUIエディタ開放中もセッション継続 / `isolation: worktree` の Bash コマンド自体の git リダイレクトチェック
- v2.1.217+: パス限定ルールのブレース展開予算制限（1,000パターン / 4 MiB）
- v2.1.218+: サブエージェントフロントマターフックへのワークスペーストラスト適用
- v2.1.219+: `CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH` 環境変数（バージョン確定）
- v2.1.222+: `availableModels` allowlist のブロックファミリーエイリアス処理改善
- v2.1.232+: `CLAUDE_CODE_FORK_SUBAGENT` インタラクティブモードでデフォルトON
- v2.1.233+: `claude plugin validate <path>` コマンド追加
- v2.1.234+: `CLAUDE_CODE_PROJECT_DIR_NAME` 環境変数（Auto Memory の複数リポジトリ間共有）
- v2.1.238+: `.claude/agents/` のインライン MCP サーバー定義にフォルダトラスト要件を追加
- v2.1.239+: `claudeMdExcludes` でシンボリックリンクのリンク元・リンク先両パスでマッチ可能に
- v2.1.242+: `/tasks` コマンドでモデルとエフォートレベルを表示
- v2.1.246+: `maxTurns` フロントマターフィールド / `maxTurns` 到達サブエージェントを「partial」としてマーク
- v2.1.248+: `experimental: { cacheTtl: '5m' | '1h' }` サブエージェント実験フィールド / exit 0 での JSON パース失敗を non-blocking エラーとして報告
- v2.1.251+: **モデル解決順序変更** – フロントマター `model` が `CLAUDE_CODE_SUBAGENT_MODEL` より優先される順序に変更
- v2.1.257+: `CLAUDE_CODE_SUBAGENT_MODEL_FORCE=1` 環境変数（全サブエージェントモデルの強制上書き）/ `scratchpad_dir` フック共通入力フィールド追加
- v2.1.261+: `--append-subagent-system-prompt-file` フラグ / 停止状態からのサブエージェント再開
- v2.1.267+: `bypassPermissions` サブエージェント permissionMode / `cloud_credential_error` StopFailure マッチャー
- v2.1.271+: `omitClaudeMd` サブエージェントフロントマターフィールド（user/project/local CLAUDE.md をスキップ）
- v2.1.277+: **AGENTS.md ネイティブサポート**（`/config` に「Project instructions」設定追加）

---

## 今週のホットトピック（2026-09-21）

1. **v2.1.277+: AGENTS.md ネイティブサポート**: 前回の v2.1.267+ から10バージョン進行。`AGENTS.md` を Claude Code がネイティブに直接読み込めるようになった。従来の `@AGENTS.md` インポートや symlink によるワークアラウンドが不要になった。`/config` パネルの「Project instructions」設定に4つの値（`claude-md-or-agents-md` / `claude-md-and-agents-md` / `claude-md` / `managed-only`）が追加された。`agents-md@builtin` プラグインが内部実装。
2. **v2.1.271+: `omitClaudeMd` サブエージェントフィールド**: サブエージェントが user/project/local CLAUDE.md をスキップして起動できるようになった。コンテキスト節約・分離実行に活用可能。
3. **StopFailure マッチャー全11種確定**: 前回「10種」と記録していたが、`unknown` が加わって11種に確定。他のエラータイプに分類されない全エラーをフォールバックでキャッチ可能。
4. **バックグラウンドサブエージェントのツール一覧更新**: `SubagentHandback` が加わり計20種となった。

---

## 前週（2026-09-14）との主要差分

| カテゴリ | 変更内容 |
|---------|---------|
| バージョン | **v2.1.277+** 確認（前回 v2.1.267+）。v2.1.271+・v2.1.277+ の2マイルストーン新規判明 |
| **AGENTS.md** | **v2.1.277+ でネイティブサポート開始**（最大の変更）。「Project instructions」設定追加 |
| サブエージェント | `omitClaudeMd: true` フロントマターフィールド（v2.1.271+）追加 |
| StopFailure | `unknown` マッチャーが確定し計11種に（前回10種） |
| バックグラウンドサブエージェント | `SubagentHandback` ツール追加（計20種） |
| Project instructions | `managed-only` 値でproject/local/user CLAUDE.md・AGENTS.md を全スキップ可能 |

---

## 前々週以前からの既知事項（変更なし）

- サブエージェントのデフォルトネスト深さは3層（`CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH` で変更可能）
- 同時実行サブエージェント上限デフォルト 20（`CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS` で変更可能）
- `/subtask` コマンドが公式確認（フォーク型サブエージェント起動、v2.1.212+）
- Claude 5 ファミリー（`claude-opus-5` / `claude-sonnet-5` / `claude-fable-5`）がサブエージェントで利用可能
- `Ctrl+G` でプランモードの計画をテキストエディタで直接編集
- `--no-session-persistence` フラグ: `-p` 実行はデフォルトでセッション保存
- フック入力 `effort` フィールドは `{ level: string }` オブジェクト型
- `/context` でCLAUDE.md読み込み確認、`/memory` でファイル編集
- `TeammateIdle` / `DirectoryAdded` / `UserPromptExpansion` フックイベント（v2.1.234+ 週確認）
- `/batch <instruction>` コマンド（5〜30サブエージェントへの自動分散）
- `CLAUDE_CODE_PROJECT_DIR_NAME` で複数リポジトリ間 Auto Memory 共有（v2.1.234+）
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
- ダイナミックワークフロー（v2.1.154+）
- Auto Memory（v2.1.59+）
- `SessionEnd` フックイベント（clear/resume/logout/prompt_input_exit/bypass_permissions_disabled/other でマッチ）
- `CwdChanged` フックイベント
- `InstructionsLoaded` フックイベント（session_start/nested_traversal/path_glob_match/include/compact でマッチ）
- `autoMemoryDirectory` でAuto Memory 保存場所カスタマイズ
- `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD` 環境変数
- スキル `disable-model-invocation: true` フィールド
- `claudeMdExcludes` でモノレポ CLAUDE.md を選択的除外
- `PreModelSwitch`/`PostModelSwitch` フックイベント（前回確認）
- `SessionStart` マッチャーに `fork` 追加（前回確認）
- `Notification` マッチャーに `elicitation_dialog`・`quota_auto_resume_fired`（前回確認）
- `allowedHttpHookUrls` / `httpHookAllowedEnvVars`（前回確認）
- `maxTurns` フロントマターフィールド（v2.1.246+、前回確認）
- `experimental.cacheTtl`（v2.1.248+、前回確認）
- `CLAUDE_CODE_SUBAGENT_MODEL_FORCE=1`（v2.1.257+、前回確認）
- `--append-subagent-system-prompt-file`（v2.1.261+、前回確認）
- 停止状態からのサブエージェント再開（v2.1.261+、前回確認）
- `permissionDecision: "skip"` 第3オプション（前回確認）
- `statusMessage` フィールド（前回確認）
- `disableAllHooks: false` プロジェクト→ユーザー上書き（前回確認）
- `claudeMdExcludes` symlink 両パスマッチ（v2.1.239+、前回確認）
- `bypassPermissions` サブエージェントモード（v2.1.267+、前回確認）
- `cloud_credential_error` StopFailure マッチャー（v2.1.267+、前回確認）
- `scratchpad_dir` フック共通入力フィールド（v2.1.257+、前回確認）
- Notification マッチャー9種（前回確認）
- StopFailure マッチャー（今回 `unknown` 追加で計11種確定）
- カスタムサブエージェント description の合計トークン上限: 15,000 tokens
- `CLAUDE_AGENT_SDK_DISABLE_BUILTIN_AGENTS=1`（非インタラクティブ/SDK でビルトインエージェント全無効化）

---

## 外部ソース取得状況

- GitHub系（gh CLI / MCPスコープ制限）: **引き続き取得不可**
- 公式ドキュメント4種: ✅ 継続取得中
