# 最新トレンドサマリー

最終更新: 2026-08-17

---

## 最新バージョン

**v2.1.222+**（前回: v2.1.222+、今週は新バージョン確認なし）

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

---

## 今週のホットトピック（2026-08-17）

1. **サブエージェントのデフォルトネスト深さは 3 層（ドキュメント明確化）**: 公式ドキュメントで「3 layers deep below main conversation」と明記。以前の知識ベースに「最大5段階」と誤記していた部分を訂正。`CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH` 環境変数で変更可能（1 で無効化）。
2. **同時実行サブエージェント上限が明文化**: デフォルト 20 同時実行。超過するとスポーン失敗。`CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS` 環境変数で変更可能。`ultracode` 有効時は免除。
3. **`/subtask` コマンドが公式確認**: 現在の会話履歴を継承したサブエージェントを起動するコマンド。`/fork` と同目的。`Agent(fork)` を deny すると禁止可能。
4. **バックグラウンドサブエージェントのツールセットが列挙**: MCP ツール＋19種の built-in ツールが明示（`Artifact`, `Monitor`, `SendMessage` 等を含む）。フォアグラウンドより制限あり。

---

## 前週（2026-08-10）との主要差分

| カテゴリ | 変更内容 |
|---------|---------|
| バージョン | v2.1.222+（変化なし、CHANGELOG 取得不可のため確認できず） |
| サブエージェント | ネストデフォルト深さが「3層」と明確化（以前は「5段階最大」と誤記）|
| サブエージェント | 同時実行上限デフォルト 20、`CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS` で設定可能 |
| サブエージェント | `/subtask` コマンドが公式確認（フォーク型サブエージェント起動） |
| バックグラウンド | バックグラウンドサブエージェントの利用可能ツールセット（19種）が明記 |
| 知識ベース | 誤記訂正: docs/02_design-patterns.md の「5段階」→「3層デフォルト」 |

---

## 前々週以前からの既知事項（変更なし）

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
