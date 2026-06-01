# 最新トレンド（直近調査）

**調査日**: 2026-06-01
**参照ファイル**: [2026-06-01.md](./2026-06-01.md)

## 現在の最新バージョン
- Claude Code: **v2.1.142以降**（CHANGELOG取得失敗のため詳細不明）
- Auto Memory 最低必要バージョン: **v2.1.59**（公式ドキュメントで明記）
- Fast Mode モデル: **Opus 4.7**

## 今週の主要変更
1. **ビルトインサブエージェント2種追加が公式ドキュメントに明記**（`statusline-setup`: Sonnet、`claude-code-guide`: Haiku）
2. **`--agents` CLIフラグ**（JSONでセッションスコープのサブエージェントを直接定義）
3. **プラグインエージェントのスコープ識別子仕様確認**（`agents/` サブフォルダパスが識別子の一部に）
4. **SessionStart フック新出力フィールド**（`sessionTitle`, `initialUserMessage`, `reloadSkills`）
5. **`Setup` フックイベント**（`--init-only` フラグで一回限りの初期化）
6. **`mcp_tool` フック型の詳細ドキュメント化**（MCPサーバーツールをフックから直接呼び出し）
7. **フック並列実行と重複排除**（マッチしたフックは並列実行、同一ハンドラーは自動排除）
8. **プロンプトフックに `model` フィールド追加**（使用モデル指定が可能に）
9. **`${CLAUDE_PLUGIN_DATA}` パスプレースホルダー追加**（プラグイン永続データディレクトリ）
10. **新環境変数3種**（`CLAUDE_CODE_DISABLE_AUTO_MEMORY`, `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD`、`--append-system-prompt` CLIフラグ）
11. **`claudeMd` 設定キー**（managed-settings.jsonにCLAUDE.md内容を直接埋め込み）
12. **逆境的レビューパターン**（Adversarial Review）が公式ベストプラクティスに追加
13. **`--add-dir` ディレクトリはサブエージェントスキャン対象外**であることが明確化
14. **Explore/Planサブエージェント**: CLAUDE.mdとgit statusをスキップして実行（コスト削減）

## ホットトピック
- **ビルトインサブエージェントの全容**: 計7種（Explore, Plan, general-purpose, statusline-setup, claude-code-guide + Explore thoroughness levels）
- **SessionStart フック高機能化**: セッション名・初期メッセージ・スキル再ロードをフックから制御
- **Adversarial Reviewパターン**: 公式ベストプラクティスに追加された品質保証の新標準
- **CLIサブエージェント定義**: `--agents` JSONでCI/CDへの統合が簡単に
- **mcp_tool フック型**: MCPサーバーツールをフック内で直接使用可能

## 前回比較（2026-05-25 → 2026-06-01）

### 新規判明（14項目）
- ビルトインサブエージェント `statusline-setup`（Sonnet）/ `claude-code-guide`（Haiku）の公式ドキュメント掲載
- Explore/Plan が CLAUDE.md と git status をスキップする仕様の確認
- `--agents` フラグによる JSON でのセッションスコープサブエージェント定義
- プラグイン `agents/` サブフォルダパスが識別子に含まれる仕様
- SessionStart フック: `sessionTitle`, `initialUserMessage`, `reloadSkills` フィールド
- `Setup` フックイベント（`--init-only` フラグ連携）
- `mcp_tool` フック型の詳細確認
- フック並列実行と重複排除の公式確認
- プロンプトフック `model` フィールド
- `${CLAUDE_PLUGIN_DATA}` パスプレースホルダー
- `CLAUDE_CODE_DISABLE_AUTO_MEMORY=1` 環境変数
- `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1` 環境変数
- `claudeMd` managed-settings.jsonキー
- Adversarial Review パターン（逆境的レビュー）の公式ベストプラクティス追加

### 変化なし
- コアバージョン: v2.1.142（推定）
- 基本ワークフロー（Explore→Plan→Implement→Commit）
- サブエージェントの11フロントマターフィールド
- Agent Teams / フォークモード / プラグイン機能
- Auto Memory 基本仕様（200行/25KB）
- フックイベント30種（前回確定分）
