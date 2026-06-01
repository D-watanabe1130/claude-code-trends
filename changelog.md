# 更新履歴

---

## 2026-06-01

公式ドキュメント（best-practices, sub-agents, hooks, memory）の調査により、前週比14項目の新規判明事項を記録。主な発見は：ビルトインサブエージェントとして `statusline-setup`（Sonnet）と `claude-code-guide`（Haiku）の2種が公式ドキュメントに追加され計7種のビルトインの全容が明確化されたこと；SessionStart フックに `sessionTitle`, `initialUserMessage`, `reloadSkills` の3フィールドが追加され自動化の幅が広がったこと；逆境的レビューパターン（Adversarial Review）が公式ベストプラクティスに追加され独立コンテキストでのサブエージェントレビューが推奨手法として位置づけられたこと；`--agents` CLIフラグによるJSONでのセッションスコープサブエージェント定義とCI/CD統合が容易になったこと。なお、GitHub系ソース（gh CLI）は環境に未インストールのため全て取得不可。

---

## 2026-05-25

サブエージェント・フック・メモリの大幅拡張を記録（30項目の新規判明）。主な発見は：サブエージェントに11の新フロントマターフィールドが追加（disallowedTools, permissionMode, maxTurns, skills, mcpServers, memory, background, effort, isolation, color, initialPrompt）；`/agents` TUIコマンドでインタラクティブなサブエージェント管理が可能に；フォークモード（`CLAUDE_CODE_FORK_SUBAGENT=1` / `/fork`）で親会話履歴を丸ごと継承するサブエージェントが利用可能に；フックイベントが30種に確定し `if` フィールドや `prompt` タイプが追加；Agent Teams（実験的）でエージェント間の協調通信が可能に；プラグイン機能（`/plugin`）の正式導入。

---

## 2026-05-18

フックシステムの大幅強化とUX改善を記録。主な発見は：exec形式フック（`args: string[]`）でシェルを介さない安全なコマンド起動が可能に（v2.1.139+）；`continueOnBlock: true` でブロック後も理由をClaudeにフィードバックしてターン継続（v2.1.139+）；`terminalSequence` でフックからデスクトップ通知が送信可能に（v2.1.141+）；`asyncRewake: true` で長時間バックグラウンド処理とClaudeの再起動連携；`PostToolBatch` イベントで並列ツール実行後の一括検証が可能に；`/btw` コマンドでコンテキストを汚染しないサイドクエリが可能に；AskUserQuestion インタビューパターンの公式推奨化。

---

## 2026-05-15

初回調査。Claude Code v2.1.139 の主要機能を記録。基本ワークフロー（Explore→Plan→Implement→Commit）、CLAUDE.md 設計原則、サブエージェント基本設計、フックパターン（PreToolUse/PostToolUse）、Auto Mode、並列実行パターン（Fan-out）を文書化。
