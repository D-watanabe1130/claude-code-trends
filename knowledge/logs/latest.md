# 最新トレンド（直近調査）

**調査日**: 2026-06-08
**参照ファイル**: [2026-06-08.md](../trends/2026-06-08.md)

## 現在の最新バージョン
- Claude Code: **v2.1.161+**（`/fork` コマンドのデフォルト有効化バージョン）
- フォークサブエージェント 最低必要バージョン: **v2.1.117**
- MCP制限のサブエージェント適用: **v2.1.153**
- Task → Agent ツール改名: **v2.1.63**
- Auto Memory 最低必要バージョン: **v2.1.59**

## 今週の主要変更
1. **`/fork` コマンドがデフォルト有効化**（v2.1.161+、以前は `CLAUDE_CODE_FORK_SUBAGENT=1` が必要）
2. **Agent フック型**（実験的）: フックからサブエージェントを直接スポーンして検証
3. **新規フックイベント10種+**: `MessageDisplay`, `Elicitation`, `ElicitationResult`, `TeammateIdle`, `WorktreeCreate`, `WorktreeRemove`, `PostCompact`, `StopFailure`, `TaskCreated`, `TaskCompleted`, `UserPromptExpansion`
4. **Stop フックの決定論的ゲート仕様**（最大8回連続ブロック後に強制終了）
5. **フックイベント別タイムアウトデフォルト**: `UserPromptSubmit` は30秒、`MessageDisplay` は10秒に短縮
6. **フック入力に `agent_id` / `agent_type` / `CLAUDE_EFFORT`** フィールド追加
7. **サブエージェントの自動圧縮**（95%でトリガー、`CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` でカスタマイズ）
8. **新環境変数**: `CLAUDE_CODE_SUBAGENT_MODEL`, `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE`, `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS`
9. **Managed Subagents**: 組織管理者が全ユーザー向けにサブエージェントを展開可能（優先度最高）
10. **Task ツール → Agent ツール改名**（v2.1.63、`Task(...)` はエイリアスとして継続）
11. **サブエージェントファイルの直接編集にはセッション再起動が必要**（`/agents` 経由は即時反映）
12. **CLAUDE.md はシステムプロンプト後のユーザーメッセージとして注入**（アーキテクチャ明確化）
13. **圧縮後のサバイバル仕様**: プロジェクトルートCLAUDE.mdは再注入、ネストCLAUDE.mdは非自動
14. **公式失敗パターン文書化**: キッチンシンクセッション、過剰指定CLAUDE.md、無限探索など5パターン
15. **`CLAUDE_CODE_NEW_INIT=1`** でインタラクティブな多段階 `/init` フロー

## ホットトピック
- **フォーク正式化**: 実験的から本番デフォルトへ、パラレル実験の標準パターンに
- **フックライフサイクルの完全体**: MessageDisplay/Elicitation/Worktree系 で企業級制御が可能に
- **決定論的Stop フック**: テスト自動パスまでターン終了をブロック、最大8回制限で安全
- **Managed Subagents**: 組織デプロイメント機能の成熟
- **コンテキスト管理の精緻化**: CLAUDE.md注入方式・圧縮サバイバル仕様の明確化

## 前回比較（2026-06-01 → 2026-06-08）

### 新規判明（主要18項目）
- `/fork` コマンドのデフォルト有効化（v2.1.161+）
- Agent フック型（実験的）
- MessageDisplay フックイベント（displayContent でテキスト上書き可能）
- Elicitation / ElicitationResult フックイベント（MCPサーバー入力要求の制御）
- TeammateIdle / WorktreeCreate / WorktreeRemove フックイベント
- PostCompact / StopFailure / TaskCreated / TaskCompleted / UserPromptExpansion フックイベント
- フックイベント別タイムアウト短縮（UserPromptSubmit: 30秒、MessageDisplay: 10秒）
- フック入力 `agent_id` / `agent_type` フィールド、`CLAUDE_EFFORT` 環境変数
- サブエージェント自動圧縮（95%、CLAUDE_AUTOCOMPACT_PCT_OVERRIDE）
- CLAUDE_CODE_SUBAGENT_MODEL 環境変数
- CLAUDE_CODE_DISABLE_BACKGROUND_TASKS 環境変数
- Managed Subagents（最高優先度）
- Task → Agent ツール改名（v2.1.63）
- サブエージェントファイル直接編集 → セッション再起動必要
- CLAUDE.md のシステムプロンプト後ユーザーメッセージ注入仕様
- 圧縮後サバイバル仕様（プロジェクトルート CLAUDE.md は再注入）
- 公式ベストプラクティスへの5失敗パターン追加
- CLAUDE_CODE_NEW_INIT=1 インタラクティブ init

### 変化なし
- コアワークフロー（Explore→Plan→Implement→Commit）
- サブエージェント基本フロントマター11フィールド
- プラグイン機能
- Agent Teams 実験的機能
- Auto Memory 基本仕様（200行/25KB）
