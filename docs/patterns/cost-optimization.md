---
tags: [claude-code, cost, model, design-pattern]
updated: 2026-06-08
---

# モデル選択・コスト最適化

## タスク種別ごとの推奨モデル

| タスク | 推奨モデル | 理由 |
|--------|-----------|------|
| ファイル探索・grep・調査 | Haiku | 速度重視・コスト低 |
| コード生成・実装 | Sonnet | バランス型 |
| 設計レビュー・品質チェック | Opus 4.8 | 精度重視 |
| 大量並列処理（CI 等） | Haiku / Sonnet | コスト管理 |
| 高速レスポンスが必要な高精度タスク | Fast Mode (Opus 4.8) | 速度と品質を両立（2x コスト、2.5x速度） |
| 最難タスク（複雑な設計・深い推論） | Opus 4.8 + /effort xhigh | 最大精度 |

## サブエージェントへの割り当てパターン

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

## コスト管理の原則

- オーケストレーター（判断役）: Sonnet または Opus
- ワーカー（実行役）: タスクの複雑さに応じて Haiku か Sonnet
- `allowedTools` を絞るとコンテキスト消費を抑えられる

## Opus 4.8 の高速モード（v2.1.154+）

| モード | コスト | 速度 |
|--------|--------|------|
| 標準（Opus 4.8） | 1x | 1x |
| Fast Mode（/fast） | 2x | 2.5x |
| Effort xhigh | 高 | 低（最高精度） |

```bash
/fast          # Opus 4.8 高速モード（2倍コスト・2.5倍速）
/effort xhigh  # 最大努力（最難タスク向け）
```

## fallbackModel によるコスト/信頼性トレードオフ（v2.1.166+）

```json
{
  "fallbackModel": ["claude-sonnet-4-5", "claude-haiku-4-5"]
}
```

- プライマリモデル障害時にフォールバック（コスト低下を許容して可用性を確保）
- `--fallback-model` CLIフラグでインタラクティブセッションにも適用可
- 非リトライ可能なAPIエラー時に自動的にフォールバックモデルで1回リトライ

## 思考無効化によるコスト削減（v2.1.166+）

```bash
MAX_THINKING_TOKENS=0 claude    # Opus 4.8 等でも思考を無効化（コスト削減）
--thinking disabled              # CLIフラグでも可
```

思考を必要としないルーティンタスクでは無効化してコストを抑える。

## CLAUDE_CODE_SUBAGENT_MODEL によるモデル一括制御（v2.1.161+）

```bash
CLAUDE_CODE_SUBAGENT_MODEL=claude-haiku-4-5 claude  # 全サブエージェントをHaikuで実行
```

サブエージェントのデフォルトモデルを環境変数で一括オーバーライド。テスト・開発時のコスト削減に有効。
