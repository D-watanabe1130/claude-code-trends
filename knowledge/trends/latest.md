# 最新トレンドサマリー

最終更新: 2026-08-03

---

## 最新バージョン

**v2.1.218+**（前回から変化なし確認済み）

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
- v2.1.214+: Auto Memory `modified` フロントマターフィールド / フック exit 2 の JSON 検証失敗時でも確実ブロック
- v2.1.216+: `/memory` がGUIエディタ開放中もセッション継続 / `isolation: worktree` の Bash コマンド自体の git リダイレクトチェック
- v2.1.217+: パス限定ルールのブレース展開予算制限（1,000パターン / 4 MiB）
- v2.1.218+: サブエージェントフロントマターフックへのワークスペーストラスト適用

---

## 今週のホットトピック（2026-08-03）

1. **Claude 5 モデルファミリーがサブエージェントドキュメントに公式例示**: `model` フィールドのフルIDとして `claude-opus-5` / `claude-sonnet-5` が公式ドキュメントに登場。Claude 5 世代（Opus 5, Sonnet 5, Fable 5）がサブエージェントで利用可能。
2. **`Ctrl+G` でプランをエディタ直接編集（公式ベストプラクティスに明記）**: プランモードの Plan ステップで `Ctrl+G` を押すと Claude の計画をテキストエディタで編集してから実装に渡せる。
3. **`--no-session-persistence` フラグが公式明記**: `-p` フラグでの非インタラクティブ実行はデフォルトでセッション保存される。使い捨て実行は `--no-session-persistence` で抑制。
4. **フック入力 `effort` フィールドがオブジェクト型として明文化**: `{ level: "high" }` 形式のオブジェクトとして受け取ることが公式文書化。
5. **`/context` コマンドの役割が公式明確化**: CLAUDE.md の読み込み確認には `/context`、ファイル編集には `/memory` と役割が整理。

---

## 前週（2026-07-27）との主要差分

| カテゴリ | 変更内容 |
|---------|---------|
| バージョン | 変化なし（v2.1.218+） |
| モデル | Claude 5（`claude-opus-5` / `claude-sonnet-5` / `claude-fable-5`）がサブエージェントドキュメントに公式例示 |
| UX | `Ctrl+G` でプランをエディタ編集する手順が公式ベストプラクティスに明記 |
| CLI | `--no-session-persistence` フラグの存在・用途が公式明記 |
| フック | `effort` 入力フィールドがオブジェクト型 `{ level: string }` として明文化 |
| デバッグ | `/context` vs `/memory` の役割分担が公式で明確化 |
| 出力形式 | `--output-format json` は `result` フィールドを含む単一JSON、`stream-json` は init イベントから始まる複数行JSON |

---

## 前々週以前からの既知事項（変更なし）

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
