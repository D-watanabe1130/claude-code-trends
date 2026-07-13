# 最新トレンドサマリー

最終更新: 2026-07-13

---

## 最新バージョン

**v2.1.205+**（サブエージェントドキュメントより確認）

主要マイルストーン:
- v2.1.178+: ネストディレクトリで同名エージェントはCWD最近傍が優先
- v2.1.195+: マッチャーhyphen対応・カンマ区切りリスト
- v2.1.196+: `prompt_id` フィールド（OpenTelemetry連携）
- v2.1.198+: symlink経由path-scoped rules対応 / Explore モデル継承変更 / `/agents` ウィザード廃止 / `background` フィールド追加・デフォルトでバックグラウンド化
- v2.1.199+: `$CLAUDE_CODE_BRIDGE_SESSION_ID`（Remote Control相関）
- v2.1.200+: `permissionMode: "manual"` エイリアス追加
- v2.1.203+: `isolation: worktree` Bash バグ修正
- v2.1.205+: `/doctor` コマンド・`--append-subagent-system-prompt` フラグ

---

## 今週のホットトピック（2026-07-13）

1. **`/doctor` コマンド（v2.1.205+）**: 重複サブエージェントファイルを自動検出・解決提案
2. **`--append-subagent-system-prompt` フラグ（v2.1.205+）**: 全サブエージェント（ネスト含む）のシステムプロンプト末尾に一括追記
3. **Explore モデル継承変更（v2.1.198+）**: Haiku 固定 → メイン会話のモデルを継承（API上限Opus）
4. **`background` フィールド追加・デフォルト変更（v2.1.198+）**: サブエージェントがデフォルトでバックグラウンド実行に
5. **`permissionMode: "manual"` エイリアス（v2.1.200+）**: `"default"` の人間可読エイリアス
6. **`/agents` ウィザード廃止（v2.1.198+）**: CLIウィザード廃止、ファイル直接編集に移行
7. **`PermissionDenied` フックの `retry: true` パターン**: Auto Mode 拒否後のリトライ制御

---

## 前週（2026-07-06）との主要差分

| カテゴリ | 変更内容 |
|---------|---------|
| バージョン | v2.1.199+ → v2.1.205+ |
| サブエージェント | `/doctor` コマンド追加（重複エージェントファイル診断） |
| サブエージェント | `--append-subagent-system-prompt` フラグ追加 |
| サブエージェント | Explore のモデル継承変更（Haiku固定 → 親会話のモデル、API上限Opus） |
| サブエージェント | `background` フィールド追加、v2.1.198+でデフォルトがバックグラウンドに |
| サブエージェント | `permissionMode: "manual"` エイリアス追加（v2.1.200+） |
| サブエージェント | `isolation: worktree` バグ修正（v2.1.203+） |
| サブエージェント | `/agents` ウィザード廃止（v2.1.198+） |
| フック | `PermissionDenied` フックの `retry: true` パターン明文化 |
| フック | 出力文字数上限 10,000 文字が明文化 |

---

## 前々週（2026-07-06）以前からの既知事項（変更なし）

- 新規フックイベント4件: `PostToolUseFailure` / `Notification` / `ConfigChange` / `ElicitationResult`
- マッチャー仕様: SessionStart（ソース）/ SubagentStart/Stop（エージェントタイプ）/ StopFailure（エラータイプ）
- v2.1.196+ `prompt_id` フィールド（OpenTelemetry連携）
- v2.1.199+ `$CLAUDE_CODE_BRIDGE_SESSION_ID`（Remote Control相関）
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
