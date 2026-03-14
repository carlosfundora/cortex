# Changelog

## 2.1.4

### Bug Fixes

- **Fix orphaned temp files leaking disk space (~37GB observed).**
  Atomic writes (`writeFileSync` → `renameSync`) left behind `memory.db.tmp.*`
  files when processes were killed (SIGTERM) between the write and rename.
  Added signal handlers (SIGTERM/SIGINT/SIGHUP) to clean up in-progress temp
  files, and startup cleanup to remove orphaned temp files from dead processes.

- **Fix `process.exit(1)` skipping `finally { closeDb() }` in main error handler.**
  Changed to `process.exitCode = 1` so the database is properly closed on errors.

- **Fix MCPResponse type to allow `null` id per JSON-RPC 2.0 spec.**
  Parse error responses must use `id: null` when the request ID is unknown.

## 2.1.3

### Bug Fixes

- **MCP server: ignore JSON-RPC notifications instead of sending invalid responses.**
  After the `initialize` handshake, Claude Code sends a `notifications/initialized`
  message (a JSON-RPC notification with no `id` field). The server incorrectly
  treated this as a request, responded with an error containing `id: undefined`,
  and caused Claude Code to drop the STDIO connection ("1 MCP server failed").
  Notifications are now silently ignored as required by the JSON-RPC 2.0 spec.
