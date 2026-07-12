# 最新トレンドサマリー

最終更新: 2026-07-06

---

## 最新バージョン

**v2.1.199+**（フックドキュメントより確認）

主要マイルストーン:
- v2.1.195+: マッチャーhyphen対応・カンマ区切りリスト
- v2.1.196+: `prompt_id` フィールド（OpenTelemetry連携）
- v2.1.198+: symlink経由path-scoped rules対応
- v2.1.199+: `$CLAUDE_CODE_BRIDGE_SESSION_ID`（Remote Control相関）

---

## 今週のホットトピック（2026-07-06）

1. **新規フックイベント4件確認**: `PostToolUseFailure` / `Notification` / `ConfigChange` / `ElicitationResult`
2. **マッチャー仕様の完全明文化**: `SessionStart`（ソース種別）/ `SubagentStart`/`Stop`（エージェントタイプ）/ `StopFailure`（エラータイプ）/ `FileChanged`（ファイル名）
3. **逆境的レビューの過剰エンジニアリング警告**: 公式ドキュメントに注意事項追加
4. **`shell`パラメータ**: command フックに追加
5. **v2.1.196+ `prompt_id`**: フック入力にOpenTelemetry相関ID追加
6. **v2.1.199+ `$CLAUDE_CODE_BRIDGE_SESSION_ID`**: Remote Control セッション追跡

---

## 前週（2026-06-29）との主要差分

| カテゴリ | 変更内容 |
|---------|---------|
| フックイベント | `PostToolUseFailure`, `Notification`, `ConfigChange`, `ElicitationResult` が新規確認 |
| マッチャー | SessionStart source / SubagentStart agent type / StopFailure error type マッチが明文化 |
| バージョン | v2.1.176 → v2.1.199+ へ更新 |
| ベストプラクティス | 逆境的レビューの過剰エンジニアリング注意が公式追加 |
| 公式推奨 | ステータスライン・`@`ファイル参照・パイプ入力が明文化 |
| CLAUDE.md | `.claude/CLAUDE.md` が公式配置場所として明記 |

---

## 前々週（2026-06-22）以前からの既知事項（変更なし）

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
