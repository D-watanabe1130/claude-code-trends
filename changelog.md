# 更新履歴

---

## 2026-06-22

公式ドキュメント4種（best-practices/sub-agents/hooks/memory）を取得。外部GitHubソースはMCPスコープ制限により今週も取得不可。最大の新規判明事項は**スキルとプラグインが公式ベストプラクティスの中核機能として明確に格上げ**されたこと。スキルは「オンデマンドロードでコンテキストを節約する再利用可能ワークフロー」として位置づけられ、CLAUDE.mdとの役割分担が公式に整理された。フック分野では5タイプ（command/http/mcp_tool/prompt/agent）の完全ドキュメントと、フックプレースホルダー変数（`${CLAUDE_PROJECT_DIR}` 等）および `additionalContext` を含む JSON 出力フォーマットが詳細明文化された。また Auto Mode を `-p` フラグと組み合わせた非インタラクティブ実行では、分類モデルが繰り返しブロックすると自動中断（abort）する仕様が確認された（CI/CDでの注意事項）。`/goal` 条件が「別の評価エージェントが毎ターン後に条件を再チェックする」仕組みであることも明文化された。

---

## 2026-06-15

CHANGELOG（v2.1.176まで）・公式ドキュメント4種・コミュニティリポジトリ3種の完全取得に成功し、前回（v2.1.168）から8バージョン分の更新を記録。最大の発見は **Claude Fable 5 の登場**（v2.1.170、"Mythos-class model"）と **サブエージェントの5段階ネスト対応**（v2.1.172）。また `/sandbox` コマンド（OS レベル分離・パーミッションプロンプト約84%削減）・`/loop` と `/schedule`（定期タスク）・`<important if="...">` CLAUDE.md タグ・コンテキスト劣化の具体的しきい値（30%以下維持推奨・300-400k トークンで劣化）など、公式ドキュメントから複数の未記録機能が判明した。セキュリティ面ではコミュニティが28件の CVE と655件の悪意あるスキルを追跡しており、MCP ソース審査の重要性が増している。

---

## 2026-06-08

公式ドキュメント（best-practices, sub-agents, hooks, memory）の詳細再調査により、前週比18項目以上の新規判明事項を記録。主な発見は：`/fork` コマンドが v2.1.161 からデフォルト有効化され実験的フラグが不要になったこと；新たなフック型 `"type": "agent"`（実験的）でフックからサブエージェントを直接スポーンできるようになったこと；`MessageDisplay`, `Elicitation`, `ElicitationResult`, `TeammateIdle`, `WorktreeCreate`, `WorktreeRemove`, `PostCompact`, `StopFailure`, `TaskCreated`, `TaskCompleted`, `UserPromptExpansion` の11以上の新規フックイベントが確認されたこと；`Stop` フックの「決定論的ゲート」仕様（最大8回連続ブロック後に強制終了）が明確化されたこと；`CLAUDE_CODE_SUBAGENT_MODEL` や `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` などの新環境変数が追加されたこと；CLAUDE.md がシステムプロンプト後のユーザーメッセージとして注入されるアーキテクチャが公式に明記されたこと；組織管理者が全ユーザー向けサブエージェントを展開できる Managed Subagents が確認されたこと。なお、GitHub系ソースはセッションのリポジトリスコープ制限により全て取得不可。

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
