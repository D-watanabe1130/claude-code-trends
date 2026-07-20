# 最新トレンドサマリー

最終更新: 2026-07-20

---

## 最新バージョン

**v2.1.211+**（メモリドキュメント・サブエージェントドキュメントより確認）

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

---

## 今週のホットトピック（2026-07-20）

1. **`/doctor` 機能拡張（v2.1.206+）**: 重複エージェントファイル検出に加え、CLAUDE.md のトリム提案が追加（コードベースから推測できる情報の削除提案）
2. **MEMORY.md サイズ管理の自動化（v2.1.210〜211+）**: 書き込み後に上限チェックしてリマインダー/エラーを返す仕組みが整備
3. **プラグインフック制約（v2.1.207+）**: シェル形式プラグインフックで `${user_config.*}` 参照不可に
4. **glob パターン堅牢化（v2.1.207+）**: `.claude/rules/` の無効 glob が全体エラーではなくそのパターンのみ「マッチなし」に
5. **`isolation: worktree` WD チェック強化（v2.1.210+）**: 起動ディレクトリだけでなくリポジトリ全体を対象に
6. **`--setting-sources` 挙動修正（v2.1.211+）**: `project` 除外でパス限定・ネストルールも確実除外

---

## 前週（2026-07-13）との主要差分

| カテゴリ | 変更内容 |
|---------|---------|
| バージョン | v2.1.205+ → v2.1.211+ |
| `/doctor` | CLAUDE.md トリム提案を追加（v2.1.206+） |
| フック | プラグインフックから `${user_config.*}` 参照を禁止（v2.1.207+） |
| ルール | 無効 glob パターンが「マッチなし」に変更（全エラーから改善）（v2.1.207+） |
| Auto Memory | MEMORY.md 書き込み後サイズチェック強化（リマインダー/エラー付き）（v2.1.210+） |
| サブエージェント | `isolation: worktree` の WD チェックがリポジトリ全体に拡張（v2.1.210+） |
| Auto Memory | MEMORY.md 測定からフロントマター/HTML コメントを除外（v2.1.211+） |
| 設定 | `--setting-sources project` 除外でパス限定ルールも除外されるよう修正（v2.1.211+） |

---

## 前々週（2026-07-13）以前からの既知事項（変更なし）

- `/doctor` コマンド（重複エージェントファイル診断）: v2.1.205+
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

---

## 外部ソース取得状況

- GitHub系（gh CLI / MCPスコープ制限）: **引き続き取得不可**
- 公式ドキュメント4種: ✅ 継続取得中
