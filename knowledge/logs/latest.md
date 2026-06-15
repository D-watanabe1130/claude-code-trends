# 最新トレンド（直近調査）

**調査日**: 2026-06-15（第8回調査）
**参照ファイル**: [2026-06-15.md](2026-06-15.md)

## 現在の最新バージョン
- Claude Code: **v2.1.176**（最新）
- Claude Fable 5: **v2.1.170**より利用可能
- サブエージェント5段階ネスト: **v2.1.172**より
- `enforceAvailableModels` マネージド設定: **v2.1.175**より
- 多言語セッションタイトル / `footerLinksRegexes`: **v2.1.176**より
- Opus 4.8 / ダイナミックワークフロー: **v2.1.154**より
- fallbackModel 設定: **v2.1.166**より
- `/fork` コマンドのデフォルト有効化: **v2.1.161**
- MCP制限のサブエージェント適用: **v2.1.153**
- Task → Agent ツール改名: **v2.1.63**
- Auto Memory 最低必要バージョン: **v2.1.59**

## 今回の主要確認事項（2026-06-15 第8回）

### 新規確認（公式ドキュメント詳細）
1. **フックイベント全29種** の公式一覧確認（`PermissionDenied`・`UserPromptExpansion`・`PostToolBatch`・`MessageDisplay`・`TeammateIdle`・`InstructionsLoaded`・`ConfigChange`・`CwdChanged`・`FileChanged`・`Elicitation`・`ElicitationResult` 等）
2. **Stop フック 8連続ブロック制限** の明文化
3. **Explore/Plan サブエージェントの CLAUDE.md スキップ** 仕様確認
4. **`context: fork` フォークサブエージェント** の公式説明確認
5. **Agent SDK 課金変更**（2026-06-15 施行）: サブスクリプションプランに月次 Agent SDK クレジット新設、インタラクティブ使用とは別枠
6. **エージェントチームの計画承認フロー**（リード承認必須モード）詳細
7. **CLAUDE.md のHTMLコメント自動除去** 機能
8. **`CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD`** 環境変数（追加ディレクトリの CLAUDE.md 読み込み）
9. **`claudeMdExcludes`** 設定（モノレポでの CLAUDE.md スキップ）
10. **MCP stdio サーバーの `CLAUDE_PROJECT_DIR`** 自動設定
11. **MCP WebSocket (`ws`) サポート**
12. **`/init` AGENTS.md 自動読み込み**（Cursor/Devin 等の既存設定も統合）
13. **クロスモデルワークフロー4種**（Router/Plugin/MCP 型）が体系化

### 前回（第7回）からの主要17項目（参考）
1. Claude Fable 5（v2.1.170、"Mythos-class model"）
2. サブエージェント5段階ネスト（v2.1.172）
3. `enforceAvailableModels` マネージド設定（v2.1.175）
4. 多言語セッションタイトル / `footerLinksRegexes`（v2.1.176）
5. Bedrock 認証情報キャッシュ改善・`~/.aws` 読み取り
6. マーケットプレイスプラグイン検索
7. `/sandbox` コマンド（OS レベル分離）
8. `/loop` / `/schedule` コマンド（定期タスク）
9. `<important if="...">` CLAUDE.md タグ
10. コンテキスト劣化の数値目標（300-400k トークン / 30%以下）
11. `context: fork` スキルフィールド
12. `CLAUDE_AGENT_SDK_DISABLE_BUILTIN_AGENTS=1` 環境変数
13. プラグインサブエージェントのスコープ識別子
14. CLAUDE.md コンパクト後の動作の明確化

## AI オーケストレーション設計パターン（最新）

### オーケストレーター / ワーカー責務分離
```
Command → Agent（オーケストレーター） → Skill（ワーカー）
```
- **オーケストレーター**: タスク分解・サブエージェント起動・結果統合
- **Explore サブエージェント（ワーカー）**: Haiku・読み取り専用・CLAUDE.md スキップ（コスト最適化）

### 並列 vs 直列の判断基準
**並列（エージェントチーム/並列サブエージェント）が有効:**
- 複数観点の調査・レビュー
- 独立した新モジュール
- 競合仮説のデバッグ
- クロスレイヤー調整

**直列（単一セッション/サブエージェント）が有効:**
- 順序依存タスク・同一ファイル編集・依存関係が多い作業

**エスカレーション:** コンテキスト限界到達 or エージェント間通信が必要 → エージェントチームへ

### 品質ゲート4層
1. プロンプト内検証
2. `/goal` 条件（評価エージェントが毎ターン後にチェック）
3. Stop フック（スクリプトゲート・8連続ブロックで強制終了）
4. 検証サブエージェント（別モデルが反証）

## CLAUDE.md 設計ベストプラクティス（最新）

### スコープ別使い分け
- **Managed Policy**: 組織全体強制（IT/DevOps 管理）
- **`~/.claude/CLAUDE.md`**: 個人全プロジェクト共通
- **`./CLAUDE.md`**: チーム共有（バージョン管理）
- **`./CLAUDE.local.md`**: 個人プロジェクト固有（gitignore）

### 記述原則
- 200行以下を目標
- HTMLコメント `<!-- -->` でメンテナーメモ（コンテキストに非注入）
- `@path` インポート（最大4ホップ）
- `.claude/rules/` でパス限定ルールを分離
- `CLAUDE_CODE_NEW_INIT=1` でインタラクティブ `/init` モード

### 読み込み順序（優先順位低→高）
1. Managed Policy（組織全体）
2. `~/.claude/CLAUDE.md`（ユーザー）
3. ファイルシステムルート→作業ディレクトリ（上から下）
4. `./.claude/rules/`（パス非限定）
5. `./CLAUDE.local.md`（最後）

## ホットトピック
- **Agent SDK 課金変更（2026-06-15 施行）**: インタラクティブ使用と別の月次クレジット
- **フックイベント体系の充実**: 29種類で自動化の粒度が大幅向上
- **エージェントチームの成熟**: 計画承認フロー・複数表示モード完備
- **クロスモデルワークフロー**: Router/Plugin/MCP 型で Claude Code + 他モデル統合が体系化
- **コミュニティエコシステムの急拡大**: 主要リポジトリが 100k+ スターに到達
