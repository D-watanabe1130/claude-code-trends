---
tags: [claude-code, orchestration, agent, design-pattern]
updated: 2026-06-08
---

# AI オーケストレーション

## アーキテクチャパターン

### Command → Agent → Skill

```
/コマンド → エージェント → スキル
```

- **Command**: ユーザーが呼び出すエントリーポイント（`.claude/commands/`）
- **Agent**: 特定タスクに特化した専門AIアシスタント（`.claude/agents/`）
- **Skill**: 再利用可能なワークフロー・ドメイン知識（`.claude/skills/`）

### Writer / Reviewer パターン
- Session A（Writer）: 実装
- Session B（Reviewer）: 独立コンテキストでレビュー（実装バイアスなし）

### Fan-out パターン（大規模処理）
```bash
for file in $(cat files.txt); do
  claude -p "Migrate $file" --allowedTools "Edit,Bash(git commit *)"
done
```

## オーケストレーター / ワーカー分離

- **オーケストレーター**: タスクの分解・委任・結果の統合を担う。直接ファイル操作しない
- **ワーカー**: 単一責務に特化。ツールセットを最小化し、スコープ外は拒否する
- 委任の判断基準は `description` の精度に依存（具体的なトリガー条件を書く）

## ハンドオフ設計

- 渡すべきもの: タスクの目的・制約・完了条件
- 渡さないもの: 不要な過去コンテキスト（コンテキストウィンドウ汚染を防ぐ）
- 完了シグナルを明示的に定義する

## ガードレール

- **入力側**: オーケストレーターがワーカーに渡す前にスコープを検証
- **出力側**: ワーカーの出力をオーケストレーターが受け取る前に検証
- **エスカレーション**: ワーカーが判断できない場合はオーケストレーターに差し戻す

## Agent Teams パターン（実験的・2026-05-25+）

```bash
CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1 claude
```

サブエージェント間を `SendMessage` ツールで通信。チームリードが複数ワーカーを協調制御。

## 逆境的レビューパターン（Adversarial Review・2026-06-01+）

実装後に独立コンテキストのサブエージェントでレビュー。公式ベストプラクティスに追加。

```text
Use a subagent to review the rate limiter diff against PLAN.md.
Report gaps, not style preferences.
```

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
条件達成まで Claude が自動でターンを継続。

## ダイナミックワークフロー（v2.1.154+）

数十〜数百のバックグラウンドエージェントを並列オーケストレーションする新機能。

```bash
/effort ultracode  # ダイナミックワークフローをトリガー
/workflows         # ワークフロー一覧
```

- `.claude/workflows/` ディレクトリでワークフローを管理
- Deep Research などのバンドルワークフローが利用可能
- 個人の生産性向上から組織スケールのタスク実行へ

## クロスセッションメッセージングの制限（v2.1.166+）

SendMessage でリレーされたメッセージはユーザー権限を持たない（セキュリティ強化）:
- 受信側はリレーされた権限リクエストを拒否
- Auto モードではリレーメッセージをブロック

この制限を理解した上でエージェント間通信を設計する。

## 推奨ワークフロー

1. **Explore**（Plan Mode）: ファイル読み込みのみ、変更なし
2. **Plan**: 詳細な実装計画作成
3. **Implement**: コーディング＋テスト検証
4. **Commit**: コミット＆PR作成
