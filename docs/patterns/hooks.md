---
tags: [claude-code, hooks, design-pattern]
updated: 2026-06-01
---

# フックパターン

## 基本フック

### PreToolUse（危険コマンドブロック）
```json
{ "matcher": "Bash(rm *)", "hooks": [{ "type": "command", "command": "block-rm.sh" }] }
```

### PostToolUse（自動lint）
```json
{ "matcher": "Edit|Write", "hooks": [{ "type": "command", "command": "lint-check.sh" }] }
```

## 高度なフック

### exec形式（v2.1.139+）
シェルを介さず安全にコマンドを起動。
```json
{ "type": "command", "command": "my-script", "args": ["--flag", "${tool_input.file_path}"] }
```

### continueOnBlock（v2.1.139+）
PostToolUse でブロック後、拒否理由を Claude にフィードバックしてターン継続。

### プロンプトフック（2026-05-25+）
Claude に yes/no 判断を委ねる。AIによる動的ゲートキーピング。
```json
{ "type": "prompt", "prompt": "このBashコマンドは安全ですか？ yes または no で答えてください。" }
```

### Setup フックイベント（2026-06-01+）
`--init-only` フラグと組み合わせて初回のみ実行。

### SessionStart フック高機能化（2026-06-01+）
```json
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "sessionTitle": "auth-refactor",
    "initialUserMessage": "前回のissue #42の続きを始めます",
    "reloadSkills": true
  }
}
```

### mcp_tool フック型（2026-06-01+）
MCP サーバーのツールをフックから直接呼び出す。

### asyncRewake（長時間バックグラウンドモニタリング）
```json
{ "type": "command", "command": "background-monitor.sh", "async": true, "asyncRewake": true }
```
exit code 2 で Claude を再起動。CIビルド監視・テスト完了待機に最適。

### PostToolBatch（並列ツール呼び出し後の検証）
複数ツールが並列実行された後、次のモデル呼び出し前にブロックできる。

### PermissionRequest / PermissionDenied（権限の自動制御）
`retry-check.sh` が `{retry: true}` を返すと操作をリトライ。

### HTTPフック with allowedEnvVars
```json
{
  "type": "http",
  "url": "https://api.company.com/hooks",
  "headers": { "Authorization": "Bearer $CI_TOKEN" },
  "allowedEnvVars": ["CI_TOKEN"]
}
```

### once: true（セッション中1回のみ実行）
### watchPaths（FileChanged監視登録）
### CLAUDE_ENV_FILE（環境変数永続化）
### terminalSequence（デスクトップ通知）
