---
tags: [claude-code, mcp, design-pattern]
updated: 2026-06-01
---

# MCP 設計パターン

## カスタム MCP vs 既存 MCP の選択基準

| 状況 | 選択 |
|------|------|
| 公式・コミュニティ製サーバーが存在する | 既存を使う |
| 社内 API・独自サービスへのアクセスが必要 | カスタムを作る |
| 認証要件が複雑（OAuth 等） | 既存サーバーで対応できるか確認してから判断 |
| プロトタイプ段階 | 既存で試してから不足を特定する |

## ツールスキーマ設計原則

- ツール名は動詞+名詞で意図を明確に（例: `search_files`, `create_page`）
- 引数は必要最小限に絞る（Claude が推論で補える情報は省く）
- エラーレスポンスは Claude が次のアクションを判断できる情報を含める
- 冪等性を意識する（同じ引数で複数回呼ばれても安全な設計）

## スコープ設定

```json
// プロジェクト固有: .claude/settings.json
// ユーザー全体: ~/.claude/settings.json
// 特定セッションのみ: claude --mcp-config で渡す
```

## mcp_tool フック型（2026-06-01+）

フックから MCP ツールを直接呼び出せる。
```json
{
  "type": "mcp_tool",
  "server": "my_server",
  "tool": "security_scan",
  "input": { "file_path": "${tool_input.file_path}" }
}
```
