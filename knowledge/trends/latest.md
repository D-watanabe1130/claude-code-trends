# 最新トレンド（直近調査）

**調査日**: 2026-05-25
**参照ファイル**: [2026-05-25.md](./2026-05-25.md)

## 現在の最新バージョン
- Claude Code: **v2.1.142以降**（CHANGELOG取得失敗のため詳細不明）
- Fast Mode モデル: **Opus 4.7**

## 今週の主要変更
1. **サブエージェントフロントマターが大幅拡張**（11フィールド追加: disallowedTools, permissionMode, maxTurns, skills, mcpServers, memory, background, effort, isolation, color, initialPrompt）
2. **`/agents` コマンド**（TUIインターフェースでサブエージェントを作成・管理）
3. **フォークモード**（`CLAUDE_CODE_FORK_SUBAGENT=1` / `/fork` コマンド）— 親会話履歴を丸ごと継承するサブエージェント
4. **`--agent <name>` CLIフラグ** — セッション全体を特定サブエージェントとして動作させる
5. **Agent Teams**（`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`）— 複数セッションの自動協調
6. **プラグイン機能**（`/plugin`）— スキル・フック・サブエージェント・MCPをバンドルしてインストール
7. **フックイベント30種確定**（新規: TeammateIdle, StopFailure, PostToolUseFailure, UserPromptExpansion, InstructionsLoaded, CwdChanged, ConfigChange, ElicitationResult, Setup）
8. **フックタイプ `prompt` と `agent`** が追加（計5タイプ）
9. **`claudeMdExcludes`** — モノレポで不要な CLAUDE.md をスキップ
10. **`~/.claude/rules/`** — ユーザーレベルのパーソナルルール

## ホットトピック
- **サブエージェントの永続メモリ**: `memory: user/project/local` でスコープ指定
- **フォークモード**: 親コンテキストを継承して並列実験、プロンプトキャッシュ共有で安価
- **Agent Teams**: `SendMessage` ツールでエージェント間通信、大規模並列タスクへの対応
- **プラグインマーケットプレイス**: コードインテリジェンスプラグインでシンボルナビゲーション強化
- **Worktrees**: 独立gitチェックアウトで並列セッションを安全に実行
- **`/agents` TUI**: サブエージェントの作成・管理が視覚的に操作可能

## 前回比較（2026-05-18 → 2026-05-25）

### 新規判明（主要30項目）
- サブエージェントの11新フロントマターフィールド
- `/agents` TUI管理インターフェース
- フォークモード（`CLAUDE_CODE_FORK_SUBAGENT=1` / `/fork`）
- `--agent` / `--agents` CLIフラグ
- `Agent(agent_type)` 構文でサブエージェント生成制限
- @-メンションによるサブエージェント明示呼び出し
- サブエージェント再開（`SendMessage` ツール）
- ビルトインサブエージェント詳細（Explore=Haiku/Plan=継承/general-purpose=全ツール）
- フックタイプ `prompt` と `agent`（実験的）
- 新フックイベント9種
- フックの `if` / `shell` フィールド
- `CLAUDE_ENV_FILE` 環境変数
- `watchPaths`（FileChanged用）
- `claudeMdExcludes` 設定
- `~/.claude/rules/` ユーザーレベルルール
- `.claude/rules/` シンボリックリンク対応
- `InstructionsLoaded` フック（デバッグ）
- プラグイン機能（`/plugin`）
- Agent Teams（実験的）
- `/rewind` の "Summarize from here" / "Summarize up to here"
- `/compact <instructions>` カスタム圧縮
- `claude --continue` / `claude --resume`
- Worktrees基盤
- コンテキストウィンドウ可視化ページ
- Chrome拡張機能UI自動テスト
- `CLAUDE_CODE_SUBAGENT_MODEL` / `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` 環境変数
- `cleanupPeriodDays` 設定
- `Ctrl+B` バックグラウンド化
- `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=1`

### 変化なし
- コアバージョン: v2.1.142（推定）
- 基本ワークフロー（Explore→Plan→Implement→Commit）
- 主要フックパターン（asyncRewake, once, PostToolBatch等）
- Auto Memory 基本仕様（200行/25KB）
