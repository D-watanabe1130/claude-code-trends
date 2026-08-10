# 更新履歴

---

## 2026-08-10

公式ドキュメント4種（best-practices/sub-agents/hooks/memory）を取得。外部GitHubソースはMCPスコープ制限により今週も取得不可。バージョンが **v2.1.222+** まで進んでいることをサブエージェントドキュメントの記述から確認（前回: v2.1.218+）。最大の新規判明事項は **`/import` コマンド（v2.1.213+）** の公式ドキュメントへの詳細記載で、Cursor・Copilot・AGENTS.md・Devin・Windsurf・Cline など他のコーディングエージェントの設定（指示ファイル・MCPサーバー・コマンド・サブエージェント・スキル）を Claude Code へ一括移行できる。これは `@AGENTS.md` インポートや symlink と異なり、ワンタイムコピーとして取り込む仕組み。CLAUDE.md 分野では **ブロックレベル HTML コメント（`<!-- ... -->`）がコンテキスト注入前に除去される仕様**が明記された。保守者向けのメモや廃止理由などをコンテキストコストなしに記録できるようになる実用的な改善。サブエージェント分野では **v2.1.222+** で組織の `availableModels` allowlist がサブエージェントのモデルファミリーエイリアス（例: `model: opus`）をブロックする場合に、以前は継承モデルにフォールバックするだけだったのが、allowlist が許可する最新バージョンへ正しく代替するよう改善された。`CLAUDE_CODE_NEW_INIT=1` の詳細仕様についても拡充が確認され、AGENTS.md・Devin/Windsurf/Cline ルールも読み込んでサブエージェントによるコードベース探索と承認フローを経てファイルを書き込むインタラクティブ版の挙動が明確化された。

---

## 2026-08-03

公式ドキュメント4種（best-practices/sub-agents/hooks/memory）を取得。外部GitHubソースはMCPスコープ制限により今週も取得不可。バージョンは **v2.1.218+** のまま（前回から進行確認なし）。最大の新規判明事項は **Claude 5 ファミリーのサブエージェントドキュメントへの公式登場** で、`model` フィールドのフルIDとして `claude-opus-5` / `claude-sonnet-5` が公式例として記載された。UX 改善として **`Ctrl+G` でプランモードの計画をテキストエディタで直接編集**できる手順が公式ベストプラクティスに明記された。CLI 面では `claude -p` の非インタラクティブ実行がデフォルトでセッション保存されること・使い捨て実行には **`--no-session-persistence`** が必要であること、`--output-format json` は `result` フィールドを含む単一JSONオブジェクトで `stream-json` は init イベントから始まる複数行JSONとなる仕様が明記された。フック面では共通入力フィールド `effort` が `{ level: string }` 形式のオブジェクト型として公式ドキュメントに明文化された。また CLAUDE.md 読み込み確認には **`/context`**、ファイル編集には **`/memory`** と役割が整理・明確化された。

---

## 2026-07-27

公式ドキュメント4種（best-practices/sub-agents/hooks/memory）を取得。外部GitHubソースはMCPスコープ制限により今週も取得不可。バージョンが **v2.1.218+** まで進んでいることを確認（前回: v2.1.211+）。最大の新規判明事項は **Auto Memory ファイルへの `modified` タイムスタンプ自動記録（v2.1.214+）** で、Claude が YAML フロントマターを含む memory ファイルを書き込む際に ISO 8601 形式の `modified` フィールドが自動付与され、ファイルの鮮度が可視化されるようになった。安全性分野では **`isolation: worktree` の Bash コマンド自体のチェック（v2.1.216+）** が追加され、ワーキングディレクトリチェックに加えて `git -C`・`--git-dir`・`GIT_DIR`/`GIT_WORK_TREE` 変数・メインチェックアウトへの `cd` チェーンなど、コマンド内容でメインチェックアウトへリダイレクトするパターンを直接検出してエラーにするようになった。UX 改善として **`/memory` がノンブロッキング化（v2.1.216+）** し、VS Code などの GUI エディタでメモリファイルを開いてもセッションを継続できるようになった。ルール分野では **パス限定ルールのブレース展開に予算制限（v2.1.217+）** が追加され、1,000パターン / 4 MiB を超えると CLI がクラッシュする旧来の問題が解消された。セキュリティ面では **サブエージェントフロントマターフックへのワークスペーストラスト適用（v2.1.218+）** が確認された。また新規文書化として `autoMemoryDirectory` 設定・`CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD` 環境変数・スキル `disable-model-invocation: true` の用途・`AGENTS.md` との互換パターン（`@AGENTS.md` インポートまたは symlink）・フック exit 2 の JSON 検証失敗時でも確実ブロックする修正（v2.1.214+）が今週初めて確認された。

---

## 2026-07-20

公式ドキュメント4種（best-practices/sub-agents/hooks/memory）を取得。外部GitHubソースはMCPスコープ制限により今週も取得不可。バージョンが **v2.1.211+** まで進んでいることを確認（前回: v2.1.205+）。最大の新規判明事項は **`/doctor` コマンドの機能拡張（v2.1.206+）** で、従来の重複エージェントファイル検出に加え、CLAUDE.md のトリム提案機能が追加された（コードベースから推測可能なディレクトリ構成・依存リスト等を削除候補として提示し、落とし穴・規約・ツールデフォルト外の設定のみを残す）。Auto Memory 分野では **v2.1.210** で MEMORY.md への書き込み後に 200行/25KB 上限チェックが強化され、上限に近いとリマインダー、超過するとエラーが返るようになった。さらに **v2.1.211** でこの測定がロード後コンテンツ（YAML フロントマター・ブロックレベルHTMLコメントを除去後）を対象とするよう変更され、コメントが多くても誤検知しなくなった。フック分野では **v2.1.207+** でシェル形式のプラグインフックから `${user_config.*}` 変数の参照が禁止された。また `.claude/rules/` のパス限定ルールで `[` を含む無効 glob パターンが、以前は Read ツール全体をエラーにしていたが、v2.1.207 以降は当該パターンのみ「マッチなし」となり他パターンは継続動作するよう改善された。サブエージェント分野では `isolation: worktree` のワーキングディレクトリ安全チェックが **v2.1.210+** でリポジトリ全体（モノレポのサブディレクトリから起動した場合のリポジトリルートを含む）に拡張。設定面では **v2.1.211+** で `--setting-sources` の `project` 除外が、パス限定ルールやネストした `.claude/rules/` のルールにも確実に適用されるよう修正された（以前は project 除外でも読み込まれ続けていた）。

---

## 2026-07-13

公式ドキュメント4種（best-practices/sub-agents/hooks/memory）を取得。外部GitHubソースはMCPスコープ制限により今週も取得不可。バージョンが **v2.1.205+** まで進んでいることを確認（前回: v2.1.199+）。最大の新規判明事項は **`/doctor` コマンド（v2.1.205+）** の追加で、同一 `.claude/agents/` ディレクトリ内で同じ `name` を持つファイルを自動検出してリネーム・削除を提案する。また **`--append-subagent-system-prompt` フラグ（v2.1.205+）** が追加され、非インタラクティブモードで全サブエージェント（ネストを含む）のシステムプロンプト末尾に一括追記できるようになった。サブエージェント関連では **v2.1.198+ から Explore サブエージェントのモデルが Haiku 固定からメイン会話のモデルの継承に変更**（Claude API 上限は Opus）、**`background` フィールドが追加**されてデフォルト動作もバックグラウンド実行に変更、**`/agents` コマンドの対話型ウィザードが廃止**されファイル直接編集方式に移行した。さらに **v2.1.200+** で `permissionMode: "manual"` エイリアス（`"default"` と同義）、**v2.1.203+** で `isolation: worktree` の Bash コマンドがワークツリー削除後にメインチェックアウトへサイレント fallback するバグを修正、**v2.1.178+** でネスト `.claude/agents/` ディレクトリで同名エージェントが重複する場合は CWD に最近傍の定義が優先されるルールが明文化。フック分野では `PermissionDenied` フックの `retry: true` パターンとフック出力の 10,000 文字上限が明文化された。

---

## 2026-07-06

公式ドキュメント4種（best-practices/sub-agents/hooks/memory）を取得。外部GitHubソースはMCPスコープ制限により今週も取得不可。フックドキュメントからバージョン **v2.1.199+** を確認（前回はv2.1.176）。最大の新規判明事項は**フックイベント4件の新規確認**: `PostToolUseFailure`（ツール失敗後）・`Notification`（通知送信時、`permission_prompt`/`auth_success`等でマッチ）・`ConfigChange`（設定ファイル変更時）・`ElicitationResult`（MCP Elicitation 回答後）。また**マッチャー仕様の詳細が完全明文化**され、`SessionStart`はソース種別（startup/resume/clear/compact）でフィルタ可能、`SubagentStart`/`Stop`はエージェントタイプ名でマッチ、`StopFailure`はエラータイプ（rate_limit/overloaded/authentication_failed）でマッチ、`FileChanged`はリテラルファイル名でマッチすることが確認された。バージョン別新機能として v2.1.195+ でハイフン付きツール名完全一致・カンマ区切りマッチャーリスト対応、v2.1.196+ で `prompt_id` フィールド（OpenTelemetry連携）、v2.1.198+ でsymlink経由path-scoped rules対応、v2.1.199+ で `$CLAUDE_CODE_BRIDGE_SESSION_ID` が追加された。ベストプラクティス面では**逆境的レビューの過剰エンジニアリング警告**が公式追加（「ギャップを探せ」指示は常に何かを報告するため正確性・要件に関わるもののみ報告と明示すること）、ステータスライン・`@`ファイル参照・パイプ入力が公式推奨として明文化された。

---

## 2026-06-29

公式ドキュメント6種（best-practices/sub-agents/hooks/memory/features-overview/artifacts）を取得。外部GitHubソースはMCPスコープ制限により今週も取得不可。最大の新規判明事項は**Artifacts機能（ベータ）の公式ドキュメント確認**。Team/Enterpriseプランで、セッション出力をプライベートWebページとして`claude.ai`に公開・組織内共有できる機能で、`Ctrl+]`で再オープン、`CLAUDE_CODE_ARTIFACT_AUTO_OPEN=0`で自動起動を無効化、Compliance APIで組織管理が可能。また**Code intelligence（LSPツール）がExtensionの公式カテゴリに昇格**し、コードインテリジェンスプラグインによるシンボルナビゲーションがファイル全読み込みに代わるコンテキスト節約手段として公式推奨になった。features-overviewページが大幅更新され**機能ごとのコンテキストコスト表とロードタイミング図**が追加、また`skillOverrides`設定によりファイル編集不要でスキル可視性を上書き可能になったことが確認された。さらに**Subagents / Background agents（agent-view）/ Agent teams の3並列化オプション**が公式に明確区分された。ウェブ検索ではsafe mode・`/cd`コマンド・post-session hookが報告されているが公式ドキュメントでの詳細は来週確認予定。

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
