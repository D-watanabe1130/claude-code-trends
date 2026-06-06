---
tags: [claude-code, memory, claude-md, design-pattern]
updated: 2026-06-01
---

# メモリ設計・CLAUDE.md 設計

## CLAUDE.md ヒエラルキー（優先順）

```
マネージドポリシー > ~/.claude/CLAUDE.md > ./CLAUDE.md > CLAUDE.local.md
```

## CLAUDE.md 設計原則

- **200行以内**に保つ（コンテキスト節約）
- HTMLコメントはコンテキスト注入前に自動除去される（メンテナーノートに活用）
- `/btw` コマンド：回答が会話履歴に残らないディスミス可能オーバーレイ

## パススコープルール（.claude/rules/）

特定ファイルパターンに一致する場合のみロード。コンテキスト節約に有効。

```yaml
---
paths:
  - "src/api/**/*.ts"
---
# このルールはAPIファイル編集時のみロード
```

## ユーザーレベルルール（2026-05-25+）

```
~/.claude/rules/
├── preferences.md   # 全プロジェクトに適用する個人設定
└── workflows.md     # 個人ワークフロー
```

プロジェクトルールより先にロードされるため、プロジェクトルールが優先。

## Auto Memory

- 保存先: `~/.claude/projects/<project>/memory/MEMORY.md`（デフォルト）
- 先頭200行 または 25KB をセッション開始時にロード
- サブエージェントも独自の Auto Memory を持てる
- カスタム保存場所: `autoMemoryDirectory` を `~/.claude/settings.json` に設定
- 無効化: `CLAUDE_CODE_DISABLE_AUTO_MEMORY=1`

## claudeMdExcludes（モノレポ向け）（2026-05-25+）

```json
{ "claudeMdExcludes": ["**/monorepo/CLAUDE.md"] }
```

## AGENTS.md 互換パターン

```markdown
<!-- CLAUDE.md -->
@AGENTS.md
## Claude Code 固有設定
```

または `ln -s AGENTS.md CLAUDE.md`（Unix のみ）
