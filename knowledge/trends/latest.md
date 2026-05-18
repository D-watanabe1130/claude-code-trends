# 最新トレンド（直近調査）

**調査日**: 2026-05-18
**参照ファイル**: [2026-05-18.md](./2026-05-18.md)

## 現在の最新バージョン
- Claude Code: **v2.1.142以降**（2026-05-18時点、CHANGELOG取得失敗のため詳細不明）
- Fast Mode モデル: **Opus 4.7**（v2.1.142から）

## 今週の主要変更
1. **フックイベントが大幅拡張**（PostToolBatch, SubagentStart/Stop, TaskCreated/Completed, PermissionRequest, PermissionDenied, PreCompact/PostCompact, Elicitation, WorktreeCreate/Remove など）
2. **AGENTS.md 相互運用性**が公式ガイドに追加（他エージェントフレームワークとの共存）
3. **CLAUDE.md のHTMLコメント自動除去**（`<!-- notes -->`がコンテキスト注入前に削除）
4. **`asyncRewake: true`** フックオプション追加（バックグラウンド実行+exit code 2でClaude起動）
5. **`once: true`** フックフィールド追加（セッション中1回のみ実行）
6. **サブエージェントのAuto Memory** が公式ドキュメントに明記
7. **`/btw` コマンド**（会話履歴に残らないサイドクエスチョン）
8. **`autoMemoryDirectory`** 設定（Auto Memory の保存場所カスタマイズ）

## ホットトピック
- **フックシステム完全版**: 20以上のイベントタイプが文書化
- **Agent View** (`claude agents`): 全セッション一覧・管理
- **AGENTS.md 互換**: `/init` が自動的に他エージェントの設定ファイルを認識
- **asyncRewake**: 長時間バックグラウンドモニタリングパターン
- **サブエージェント Auto Memory**: 専門エージェントの知識蓄積
- **/btw コマンド**: コンテキスト非汚染の軽量確認ツール

## 前回比較（2026-05-15 → 2026-05-18）

### 新規判明
- フックイベント約15種類が新たに文書化
- `asyncRewake`, `once` フックオプション
- HTTPフックの `allowedEnvVars`
- MCPツールフックの `input` テンプレート構文
- プラグインフック（`hooks/hooks.json`）
- AGENTS.md 相互運用ガイド
- CLAUDE.md HTMLコメント除去機能
- `claudeMd` キー（managed-settings.json）
- `autoMemoryDirectory` 設定
- サブエージェントの独自 Auto Memory
- `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD` 環境変数
- `CLAUDE_EFFORT`, `$CLAUDE_CODE_REMOTE` 環境変数
- `/btw` コマンド（サイドクエスチョン）
- `AskUserQuestion` ツールパターン（公式推奨）
- `--append-system-prompt` フラグ
- `/sandbox` の第3権限手段としての明示化

### 変化なし
- コアバージョン: v2.1.142（推定）
- 主要エコシステムリポジトリ
- 基本的なワークフローパターン（Explore→Plan→Implement→Commit）
