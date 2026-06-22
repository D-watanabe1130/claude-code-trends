# 最新トレンド（直近調査）

**調査日**: 2026-06-22（第9回調査）
**参照ファイル**: [2026-06-22.md](2026-06-22.md)

## 現在の最新バージョン
- Claude Code: **v2.1.185**（最新、2026-06-21）
- Claude Fable 5: **v2.1.170** より利用可能
- サブエージェント5段階ネスト: **v2.1.172** より
- `enforceAvailableModels` マネージド設定: **v2.1.175** より
- 多言語セッションタイトル / `footerLinksRegexes`: **v2.1.176** より
- Opus 4.8 / ダイナミックワークフロー: **v2.1.154** より
- fallbackModel 設定: **v2.1.166** より
- `/fork` コマンドのデフォルト有効化: **v2.1.161**
- MCP制限のサブエージェント適用: **v2.1.153**
- Task → Agent ツール改名: **v2.1.63**
- Auto Memory 最低必要バージョン: **v2.1.59**

## 今回の主要確認事項（2026-06-22 第9回）

### 新規確認（v2.1.177〜v2.1.185 の変更）
1. **Auto Mode 安全強化**（v2.1.183）: 破壊的 git コマンド（`git reset --hard` 等）・`terraform destroy` 等を自動ブロック
2. **`/config key=value` 構文**（v2.1.181）: プロンプトから任意設定を変更可能
3. **エージェントチーム仕様変更**（v2.1.178）: `TeamCreate`/`TeamDelete` ツール廃止、暗黙チームが自動作成
4. **`Tool(param:value)` パーミッション構文**（v2.1.178）: `Agent(model:opus)` で Opus サブエージェントをブロック等
5. **Workflow プロンプトキーワード制限**（v2.1.178）: "run a workflow" 等の明示フレーズのみトリガー
6. **`attribution.sessionUrl` 設定**（v2.1.183）: コミット/PR からセッションリンクを省略
7. **`sandbox.allowAppleEvents` 設定**（v2.1.181）: macOS Apple Events 許可
8. **`CLAUDE_CLIENT_PRESENCE_FILE` 環境変数**（v2.1.181）: モバイルプッシュ通知抑制
9. **Bun 1.4 バンドル**（v2.1.181）
10. **ネスト `.claude/skills`**（v2.1.178）: 名前衝突時 `<dir>:<name>` 形式で両方利用可能
11. **フックイベント32種**（完全一覧確認: `Setup`/`UserPromptExpansion`/`PermissionDenied`/`PostToolUseFailure`/`StopFailure`/`PreCompact`/`PostCompact`/`Elicitation`/`ElicitationResult` 等を含む）
12. **公式 best-practices.md の記述基準表**（CLAUDE.md に含める/除外する内容を表形式で明確化）
13. **段階的セットアップ8トリガー**（features-overview.md: CLAUDE.md → Skill → MCP → Subagent → Hook → Plugin の移行判断基準）
14. **コミュニティ Hot 機能一覧更新**（Ultrareview/Ultraplan/Channels/Advisor/Computer Use/Routines 等）

### 前回（第8回）からの主要確認事項（参考）
1. フックイベント全29種の公式一覧確認
2. Stop フック8連続ブロック制限の明文化
3. Explore/Plan サブエージェントの CLAUDE.md スキップ仕様
4. Agent SDK 課金変更（2026-06-15 施行）
5. エージェントチームの計画承認フロー
6. CLAUDE.md のHTMLコメント自動除去機能
7. `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD` 環境変数
8. MCP WebSocket (`ws`) サポート
9. クロスモデルワークフロー4種（Router/Plugin/MCP 型）

## AI オーケストレーション設計パターン（最新）

### オーケストレーター / ワーカー責務分離
```
Command → Agent（オーケストレーター） → Skill（ワーカー）
```
- **オーケストレーター**: タスク分解・サブエージェント起動・結果統合
- **Explore サブエージェント（ワーカー）**: Haiku・読み取り専用・CLAUDE.md スキップ（コスト最適化）

### エージェントチーム構成（v2.1.178〜）
```
セッション開始 → 暗黙チーム自動作成
→ Agent(name="worker1") / Agent(name="worker2") で直接スポーン
→ チームメイト間で直接メッセージ通信
→ 共有タスクリストで自律協調
```

### 並列 vs 直列の判断基準
**並列（エージェントチーム/並列サブエージェント）が有効:**
- 複数観点の調査・レビュー
- 独立した新モジュール
- 競合仮説のデバッグ
- クロスレイヤー調整（フロントエンド/バックエンド/テスト）

**直列（単一セッション/サブエージェント）が有効:**
- 順序依存タスク・同一ファイル編集・依存関係が多い作業

**サブエージェント → エージェントチームへの移行点**:
- 並列サブエージェントでコンテキスト限界到達
- サブエージェント間での通信が必要

### コスト最適化（モデル別割り当て）
- **Explore**: Haiku（高速・低コスト・読み取り専用）
- **Plan**: 親会話モデル継承
- **General-purpose**: 親会話モデル継承
- **Opus サブエージェントのブロック**: `Agent(model:opus)` パーミッション構文

### Auto Mode 安全設計（v2.1.183〜）
**自動ブロック対象**（明示的ユーザー指示なき場合）:
- `git reset --hard`, `git checkout -- .`, `git clean -fd`, `git stash drop`
- `git commit --amend`（エージェント自身のコミット以外）
- `terraform destroy` / `pulumi destroy` / `cdk destroy`（特定スタック指定なし）

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

### 記述すべき内容（公式確認）
**✅ 含める**:
- Claude が推測できない Bash コマンド
- デフォルトと異なるコードスタイルルール
- テスト指示・優先テストランナー
- リポジトリエチケット（ブランチ命名・PR規約）
- プロジェクト固有のアーキテクチャ決定
- 開発環境の特殊事項（必須環境変数等）
- 非自明な動作・ハマりポイント

**❌ 除外**:
- Claude がコードを読めば分かること
- 標準的な言語規約
- 頻繁に変更される情報
- 詳細な API ドキュメント（リンクで代替）
- 長い説明・チュートリアル
- "write clean code" のような自明な慣行

### 記述原則
- **200行以下**を目標（超えると遵守率低下）
- HTMLコメント `<!-- -->` でメンテナーメモ（コンテキストに非注入）
- `@path` インポート（最大4ホップ）
- `.claude/rules/` でパス限定ルールを分離
- `CLAUDE_CODE_NEW_INIT=1` でインタラクティブ `/init` モード
- "IMPORTANT" / "YOU MUST" 等の強調語でルール遵守率を向上
- `claudeMdExcludes` でモノレポの不要 CLAUDE.md スキップ

### 読み込み順序（優先順位低→高）
1. Managed Policy（組織全体）
2. `~/.claude/CLAUDE.md`（ユーザー）
3. ファイルシステムルート→作業ディレクトリ（上から下）
4. `./.claude/rules/`（パス非限定）
5. `./CLAUDE.local.md`（最後）

### 段階的セットアップのトリガー
1. Claude が同じ間違いを2回 → CLAUDE.md 追記
2. 同じプロンプトを繰り返し入力 → Skill 化
3. 同じ手順書を3回貼り付け → Skill 化
4. ブラウザタブからデータをコピーし続ける → MCP サーバー接続
5. 記号定義探索が遅い → Code Intelligence プラグイン
6. サイドタスクが会話を汚染 → Subagent へルーティング
7. 毎回必ず実行したいアクション → Hook 作成
8. 別のリポジトリでも同じセットアップが必要 → Plugin 化

## ホットトピック
- **Auto Mode の産業利用対応**: 破壊的操作の自動ブロックにより無人実行の信頼性が大幅向上
- **エージェントチームの簡素化**: TeamCreate/Delete 廃止、暗黙チームで設定オーバーヘッド激減
- **コスト制御の精緻化**: `Tool(param:value)` 構文でモデル別パーミッション設計が可能に
- **スキルエコシステムの爆発的成長**: anthropics/skills 153k、mattpocock/skills 138k スター
- **Claude Code v2.1.185 の安定化**: 大量バグ修正・UX 改善でプロダクション品質向上
- **フックイベント32種体系**: 完全な自動化粒度が確立
