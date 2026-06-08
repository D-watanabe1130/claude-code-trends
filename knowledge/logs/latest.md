# 最新トレンド（直近調査）

**調査日**: 2026-06-08（第3回更新）
**参照ファイル**: [2026-06-08-3rd.md](2026-06-08-3rd.md)

## 現在の最新バージョン
- Claude Code: **v2.1.168**（最新）
- Opus 4.8: **v2.1.154**より利用可能
- ダイナミックワークフロー: **v2.1.154**より利用可能
- fallbackModel 設定: **v2.1.166**より利用可能
- `/fork` コマンドのデフォルト有効化: **v2.1.161**
- MCP制限のサブエージェント適用: **v2.1.153**
- Task → Agent ツール改名: **v2.1.63**
- Auto Memory 最低必要バージョン: **v2.1.59**

## 今回の主要変更（2026-06-08 第3回）

### 新規機能（今回初確認）
1. **Ultrareview** (beta): `/code-review ultra` または `claude ultrareview`、マルチエージェント分析でタスクトラッキング対応
2. **Devcontainers**: `.devcontainer/` による一致した分離環境
3. **Channels** (beta): `--channels` / plugin-based で外部イベントをセッションにプッシュ（CI結果、Slack通知等）
4. **Ultraplan** (beta): `/ultraplan` コマンド（計画専用モード）
5. **No Flicker Mode** (beta): `/tui fullscreen`、`CLAUDE_CODE_NO_FLICKER=1`
6. **Agent SDK `AgentDefinition` 完全フィールド**: `background`、`effort`、`permissionMode`、`initialPrompt`、`skills`（プリロード）、`disallowedTools`
7. **エージェントチーム専用フック**: `TeammateIdle`、`TaskCreated`、`TaskCompleted`
8. **Managed Policy CLAUDE.md**: `/Library/Application Support/ClaudeCode/CLAUDE.md`（macOS）など（除外不可・全ユーザー適用）
9. **`claudeMd` マネージドセッティングキー**: `managed-settings.json` で CLAUDE.md 内容を直接管理
10. **`CLAUDE_CODE_NEW_INIT=1`**: `/init` のインタラクティブマルチフェーズフロー（CLAUDE.md + スキル + フックを段階的セットアップ）
11. **`claudeMdExcludes`**: モノレポで他チームの CLAUDE.md を除外
12. **`CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1`**: `--add-dir` ディレクトリからも CLAUDE.md をロード
13. **Stop フックの `CLAUDE_CODE_STOP_HOOK_BLOCK_CAP`** 環境変数: デフォルト8回のブロック上限を変更可能

### CLAUDE.md ベストプラクティス詳細化（公式ドキュメント）
- **ロード順**: Managed Policy → User → Project → Local（後ろが優先）
- **200行以下**が目標（超えると遵守率低下）
- **HTMLコメント** `<!-- -->` はコンテキストから自動除去
- **`@path/to/import`** 構文で他ファイルをインポート（最大4ホップ）
- **`.claude/rules/`** でパス別条件ロードルール（シンボリックリンク対応）
- **`AGENTS.md` 互換**: `@AGENTS.md` インポートまたはシンリンク

### AI オーケストレーション最適化パターン（公式ドキュメントから抽出）
- **最小権限原則**: `AgentDefinition.tools` で読み取り専用制限
- **コンテキスト渡し**: 親→サブエージェントは Agent ツールのプロンプト文字列のみ
- **コスト最適化**: `AgentDefinition.model` でタスク別モデル（opus/sonnet/haiku）割り当て
- **品質ゲート3層**: Stop フック（決定論）+ `/goal`（自律継続）+ `additionalContext`（フィードバック）
- **並列 vs 直列**: 同一ファイル編集なし・独立タスクは並列、依存関係あり・同一ファイルは直列

## ホットトピック
- **Agent SDK の成熟**: `AgentDefinition` フィールドが充実し、プログラマティックなマルチエージェント設計が本格化
- **Ultrareview/Ultraplan**: ネイティブのマルチエージェントレビュー・計画機能が正式beta化
- **コスト最適化**: タスク別モデル割り当てが標準パターンとして確立
- **Managed Policy**: 組織全体に CLAUDE.md を配布・強制する仕組みが公式化
- **品質ゲート設計**: 3層パターン（Stop/goal/additionalContext）が公式ベストプラクティスに

## 前回比較（2026-06-08 第2回 → 第3回）

### 新規判明（主要13項目）
- Ultrareview (beta) / Ultraplan (beta)
- Channels (beta) / No Flicker Mode (beta) / Devcontainers
- Agent SDK AgentDefinition 完全フィールド（background/effort/permissionMode/initialPrompt）
- エージェントチーム専用フック（TeammateIdle/TaskCreated/TaskCompleted）
- Managed Policy CLAUDE.md（全ユーザー強制適用）
- claudeMd マネージドセッティングキー
- CLAUDE_CODE_NEW_INIT=1 インタラクティブ /init
- claudeMdExcludes / CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD
- CLAUDE.md 設計詳細（200行制限・HTMLコメント除去・インポート構文・4ホップ上限）
- AGENTS.md 相互運用ガイド（@インポートまたはシンリンク）
- コスト最適化（タスク別モデル割り当てパターン）
- 品質ゲート3層設計（Stop/goal/additionalContext）
- コミュニティスター数更新（Superpowers 221k、ECC 210k、anthropics/skills 147k）

### 変化なし
- CHANGELOG 最新版は v2.1.168（Bug fixes のみ）
- フック基本5タイプ（command/http/mcp_tool/prompt/agent）
- コアワークフロー（Explore→Plan→Implement→Commit）
- Auto Memory 基本仕様（200行/25KB）
