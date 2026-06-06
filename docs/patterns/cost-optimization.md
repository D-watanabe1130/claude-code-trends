---
tags: [claude-code, cost, model, design-pattern]
updated: 2026-06-01
---

# モデル選択・コスト最適化

## タスク種別ごとの推奨モデル

| タスク | 推奨モデル | 理由 |
|--------|-----------|------|
| ファイル探索・grep・調査 | Haiku | 速度重視・コスト低 |
| コード生成・実装 | Sonnet | バランス型 |
| 設計レビュー・品質チェック | Opus | 精度重視 |
| 大量並列処理（CI 等） | Haiku / Sonnet | コスト管理 |
| 高速レスポンスが必要な Opus タスク | Fast Mode (Opus) | 速度と品質を両立 |

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
