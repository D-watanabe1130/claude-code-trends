# Claude Code 設計パターン集

最終更新: 2026-05-15

## アーキテクチャパターン

### Command → Agent → Skill（オーケストレーション）

```
/コマンド → エージェント → スキル
```

- **Command**: ユーザーが呼び出すエントリーポイント（`.claude/commands/`）
- **Agent**: 特定タスクに特化した専門AIアシスタント（`.claude/agents/`）
- **Skill**: 再利用可能なワークフロー・ドメイン知識（`.claude/skills/`）

### Writer / Reviewer パターン

- Session A（Writer）: 実装
- Session B（Reviewer）: コードレビュー（異なるコンテキストで偏りなし）

### Fan-out パターン（大規模処理）

```bash
for file in $(cat files.txt); do
  claude -p "Migrate $file" --allowedTools "Edit,Bash(git commit *)"
done
```

---

## コンテキスト管理

### 基本原則

- コンテキストウィンドウが最重要リソース
- CLAUDE.md は 200行以内に保つ
- タスク間は `/clear` でリセット
- 調査・探索にはサブエージェントを使用

### 推奨ワークフロー

1. **Explore**（Plan Mode）: ファイル読み込みのみ、変更なし
2. **Plan**: 詳細な実装計画作成（`Ctrl+G` でエディタ編集）
3. **Implement**: コーディング＋テスト検証
4. **Commit**: コミット＆PR作成

---

## サブエージェント設計

```yaml
# .claude/agents/専門名.md
---
name: security-reviewer
description: Reviews code for security vulnerabilities
tools: Read, Grep, Glob, Bash
model: opus
---
```

- `description` が自動委任の判断基準（具体的に書くほど精度が上がる）
- `tools` で権限を最小化
- `model` で用途別コスト最適化（調査 → haiku、品質レビュー → opus）

---

## フックパターン

### PreToolUse（危険コマンドブロック）

```json
{
  "matcher": "Bash(rm *)",
  "hooks": [{ "type": "command", "command": "block-rm.sh" }]
}
```

### PostToolUse（自動lint）

```json
{
  "matcher": "Edit|Write",
  "hooks": [{ "type": "command", "command": "lint-check.sh" }]
}
```

### exec形式（v2.1.139+）

シェルを介さず安全にコマンドを起動。パス中の特殊文字のクォート不要。

```json
{
  "type": "command",
  "command": "my-script",
  "args": ["--flag", "${tool_input.file_path}"]
}
```

### continueOnBlock（v2.1.139+）

PostToolUse でブロック後、拒否理由を Claude にフィードバックしてターン継続。

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [{ "type": "command", "command": "validate.sh", "continueOnBlock": true }]
      }
    ]
  }
}
```

### terminalSequence（v2.1.141+）

フックからデスクトップ通知を送信。

```json
{
  "terminalSequence": "\033]777;notify;Claude Code;処理完了\007"
}
```

---

## Auto Mode

```bash
claude --permission-mode auto -p "fix all lint errors"
```

- 分類モデルがコマンドを事前評価
- スコープ拡大・未知インフラ・悪意ある指示をブロック
- `-p` との組み合わせで完全自律実行（CI/CDに最適）

---

## メモリ設計

### CLAUDE.md ヒエラルキー（優先順）

```
マネージドポリシー > ~/.claude/CLAUDE.md > ./CLAUDE.md > CLAUDE.local.md
```

### パススコープルール（.claude/rules/）

特定ファイルパターンに一致する場合のみロード。コンテキスト節約に有効。

```yaml
---
paths:
  - "src/api/**/*.ts"
---
# このルールはAPIファイル編集時のみロード
- すべてのAPIエンドポイントには入力バリデーションを含めること
```

### Auto Memory

- 保存先: `~/.claude/projects/<project>/memory/MEMORY.md`
- 先頭200行 または 25KB をセッション開始時にロード
- `/memory` コマンドで閲覧・編集・トグル

---

## MCP 設計パターン

> ★ 未取得 — 次回 trendupdate で `code.claude.com/docs/en/mcp` および `modelcontextprotocol.io` を調査して補完する

### カスタム MCP vs 既存 MCP の選択基準

| 状況 | 選択 |
|------|------|
| 公式・コミュニティ製サーバーが存在する | 既存を使う |
| 社内 API・独自サービスへのアクセスが必要 | カスタムを作る |
| 認証要件が複雑（OAuth 等） | 既存サーバーで対応できるか確認してから判断 |
| プロトタイプ段階 | 既存で試してから不足を特定する |

### ツールスキーマ設計原則

- ツール名は動詞+名詞で意図を明確に（例: `search_files`, `create_page`）
- 引数は必要最小限に絞る（Claude が推論で補える情報は省く）
- エラーレスポンスは Claude が次のアクションを判断できる情報を含める
- 冪等性を意識する（同じ引数で複数回呼ばれても安全な設計）

### スコープ設定

```json
// プロジェクト固有: .claude/settings.json
// ユーザー全体: ~/.claude/settings.json
// 特定セッションのみ: claude --mcp-config で渡す
```

---

## モデル選択・コスト最適化

> ★ 未取得 — 次回 trendupdate で公式ドキュメント・CHANGELOG から補完する

### タスク種別ごとの推奨モデル（暫定）

| タスク | 推奨モデル | 理由 |
|--------|-----------|------|
| ファイル探索・grep・調査 | Haiku | 速度重視・コスト低 |
| コード生成・実装 | Sonnet | バランス型 |
| 設計レビュー・品質チェック | Opus | 精度重視 |
| 大量並列処理（CI 等） | Haiku / Sonnet | コスト管理 |
| 高速レスポンスが必要な Opus タスク | Fast Mode (Opus) | 速度と品質を両立 |

### サブエージェントへの割り当てパターン

```yaml
# .claude/agents/researcher.md
---
model: haiku  # 調査系はコスト優先
tools: Read, Glob, Grep, WebSearch
---

# .claude/agents/reviewer.md
---
model: opus   # レビュー系は精度優先
tools: Read, Glob
---
```

### コスト管理の原則

- オーケストレーター（判断役）: Sonnet または Opus
- ワーカー（実行役）: タスクの複雑さに応じて Haiku か Sonnet
- `allowedTools` を絞るとコンテキスト消費を抑えられる

---

## エラーリカバリー・冪等性設計

> ★ 未取得 — 次回 trendupdate で公式ドキュメント・コミュニティソースから補完する

### 冪等性の原則

- 同じ操作を複数回実行しても結果が変わらない設計にする
- ファイル生成前に「既に存在するか」を確認する
- git commit 前に「変更差分があるか」を確認する（空コミット防止）
- 外部 API 呼び出しは成功済みかをチェックしてからリトライする

### 部分失敗時のリカバリー

```
タスク全体
  ├── ステップ1: 完了 ✓（スキップしてよい）
  ├── ステップ2: 失敗 ✗ ← ここから再開
  └── ステップ3: 未実行
```

- 長いタスクはステップ単位でチェックポイントを設ける
- 完了済みステップは冪等性を担保してスキップできるようにする
- 失敗したステップのエラー内容を上位（オーケストレーター/ユーザー）に明示する

### サイレント失敗の防止

- エラーを握りつぶして処理を続けない
- 失敗時は「何が失敗したか」「次に何をすべきか」をユーザーに伝える
- ワーカーエージェントは失敗をオーケストレーターに返し、自己判断で続行しない

### チェックポイント設計（長時間タスク）

- trendupdate の場合: 調査完了後に `knowledge/trends/YYYY-MM-DD.md` を保存してから承認フローへ
- 承認フロー失敗でも調査結果は保持され、次回は承認フローから再開できる

### タイムアウト・リトライ原則

- 外部 API（WebFetch・GitHub API）は一時障害を想定してリトライする
- リトライ回数は最大3回、間隔は指数バックオフ（1s → 2s → 4s）
- タイムアウト後は「取得できなかったソース」として記録し、残りを続行する

---

## スキル設計思想（Skill Design）

> ★ 未取得 — 次回 trendupdate で公式ドキュメント・コミュニティソースから抽出して補完する

### スキル / コマンド / エージェントの使い分け

| 種別 | 使う場面 |
|------|---------|
| **Skill** | 複数プロジェクトをまたいで再利用する手順・ドメイン知識 |
| **Command** | プロジェクト固有の繰り返し作業（`/deploy`, `/review` 等） |
| **Agent** | 単一責務の専門タスクに特化した常駐アシスタント |

### `description` の書き方原則

- 自動呼び出しのトリガーとなるキーワードを含める
- 「何をするスキルか」より「いつ使うか」を優先して書く
- 例: `「〇〇したいとき」「/xxx と言われたら」` 形式が有効

### スコープ定義（1スキル1責務）

- スキルが扱う入力・出力・副作用を明示する
- 複数の責務が混在したら分割を検討する
- 依存スキルは `## 関連ファイル` セクションに列挙し循環依存を避ける

### 入出力の契約

- スキルが受け取る前提情報（ファイルパス・ユーザー回答等）を冒頭に定義する
- 生成物（ファイル・コミット・レポート等）を完了条件として明示する
- エラー時はサイレントに続行せず、ユーザーへの確認または停止を選ぶ

---

## 実行基盤の選択：Agent SDK vs Managed Agents

> ★ 未取得 — 次回 trendupdate で公式ドキュメントを取得して補完する

### 選択基準

| 観点 | Agent SDK | Managed Agents |
|------|-----------|---------------|
| **実行場所** | 自分のプロセス・インフラ | Anthropic クラウド |
| **インターフェース** | Python / TypeScript ライブラリ | REST API |
| **作業対象** | 自分のファイルシステム・サービス | Anthropic 管理サンドボックス |
| **セッション状態** | ローカル JSONL | Anthropic ホストイベントログ |
| **向いている用途** | ローカル開発・自社インフラ上のエージェント | 外部ユーザー向けプロダクション製品 |

### 使い分けの原則

- **自社インフラ完結・ファイル操作中心** → Agent SDK
- **外部ユーザー向け・サンドボックスが必要** → Managed Agents
- **プロトタイプ段階** → Agent SDK で開発 → 本番移行時に Managed Agents へ

### Claude Code との関係

```
Claude Code CLI（対話型開発）
    ↕ 同じ能力・異なるインターフェース
Agent SDK（自動化・CI/CD・プログラム制御）
    ↕ 実行基盤のみ異なる
Managed Agents（Anthropic ホスト型・REST API）
```

---

## エージェントシステム設計（Agent SDK）

> ★ 未取得 — 次回 trendupdate で `code.claude.com/docs/en/agent-sdk` 等を調査して補完する

### オーケストレーター / ワーカー分離

- **オーケストレーター**: タスクの分解・委任・結果の統合を担う。直接ファイル操作しない
- **ワーカー**: 単一責務に特化。ツールセットを最小化し、スコープ外は拒否する
- 委任の判断基準は `description` の精度に依存（具体的なトリガー条件を書く）

### ハンドオフ設計

- 渡すべきもの: タスクの目的・制約・完了条件
- 渡さないもの: 不要な過去コンテキスト（コンテキストウィンドウ汚染を防ぐ）
- 完了シグナルを明示的に定義する（ファイル生成・コミット・レポート出力等）

### ガードレール

- **入力側**: オーケストレーターがワーカーに渡す前にスコープを検証
- **出力側**: ワーカーの出力をオーケストレーターが受け取る前に検証
- **エスカレーション**: ワーカーが判断できない場合はオーケストレーターに差し戻す（サイレント失敗しない）

### ツール設計原則

- **最小権限**: 各エージェントに必要なツールのみ付与（`tools:` で明示）
- **冪等性**: 同じ操作を複数回実行しても結果が変わらない設計
- **エラーの明示**: 失敗時はサイレントに続行せず、エラー内容をオーケストレーターに返す

---

## スケールパターン

### バックグラウンドエージェント（v2.1.139+）

```bash
claude --bg -p "run test suite and report failures"
claude agents  # 全セッション一覧
```

### /goal による自律継続（v2.1.139+）

```
/goal all tests pass
```

条件達成まで Claude が自動でターンを継続。経過時間・ターン数・トークン数をリアルタイム表示。

### Fan-out + allowedTools でCI安全実行

```bash
claude -p "fix lint errors" \
  --allowedTools "Edit,Bash(npm run lint)" \
  --permission-mode auto
```
