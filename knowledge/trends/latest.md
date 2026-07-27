# 最新トレンドサマリー

最終更新: 2026-07-27

---

## 最新バージョン

**v2.1.218+**（前回: v2.1.211+）

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

## 今週のホットトピック（2026-07-27）

1. **Auto Memory に `modified` タイムスタンプ（v2.1.214+）**: Claude が memory ファイルを書き込む際、ISO 8601 形式の `modified` フロントマターフィールドが自動記録される。ファイルの鮮度が人間にも Claude にも可視化される。
2. **`isolation: worktree` の Bash コマンド内容チェック（v2.1.216+）**: WD チェックに加え、Bash コマンド自体が git リダイレクト（`git -C` / `--git-dir` / `GIT_DIR` / `GIT_WORK_TREE` / メインチェックアウトへの `cd`）でメインチェックアウトにアクセスするパターンを検出してエラーにする
3. **`/memory` コマンドのノンブロッキング化（v2.1.216+）**: GUI エディタ（VS Code 等）でメモリファイルを開いてもセッションが停止しなくなった
4. **`CLAUDE_CODE_NEW_INIT=1` の文書化**: `/init` のインタラクティブ多段階フロー（AGENTS.md / Devin / Windsurf / Clinerules 読み込み）が公式ドキュメントに記載
5. **`claudeMdExcludes` 設定の文書化**: モノレポで他チームの CLAUDE.md をスキップする glob パターン設定が公式化
6. **`disable-model-invocation: true` スキルフィールドの文書化**: 副作用スキルをモデル自動呼び出しから保護する仕組みが公式記載

---

## 前週（2026-07-20）との主要差分

| カテゴリ | 変更内容 |
|---------|---------|
| バージョン | v2.1.211+ → v2.1.218+ |
| Auto Memory | `modified` フロントマターフィールド追加（ISO 8601）（v2.1.214+） |
| フック | exit 2 が JSON 検証失敗でも確実ブロック（v2.1.214+） |
| `/memory` | GUIエディタ開放中もセッション継続（v2.1.216+） |
| サブエージェント | `isolation: worktree` の Bash コマンド自体の git リダイレクト検出（v2.1.216+） |
| ルール | ブレース展開予算制限（1,000パターン / 4 MiB）でCLIクラッシュを防止（v2.1.217+） |
| サブエージェント | フロントマターフック定義にワークスペーストラスト適用（v2.1.218+） |
| `/init` | `CLAUDE_CODE_NEW_INIT=1` で多段階インタラクティブフロー（新規文書化） |
| 設定 | `claudeMd` キーで managed-settings.json にCLAUDE.mdを直接埋め込み（新規文書化） |
| 設定 | `claudeMdExcludes` でモノレポ CLAUDE.md を選択的除外（新規文書化） |
| Auto Memory | `autoMemoryDirectory` で保存場所カスタマイズ可能（新規文書化） |
| 環境変数 | `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD`（新規文書化） |
| スキル | `disable-model-invocation: true` フィールド（新規文書化） |

---

## 前々週以前からの既知事項（変更なし）

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

---

## 外部ソース取得状況

- GitHub系（gh CLI / MCPスコープ制限）: **引き続き取得不可**
- 公式ドキュメント4種: ✅ 継続取得中
