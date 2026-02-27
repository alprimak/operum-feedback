# Inter-Agent Communication (IPC)

> File-based communication protocol enabling AI agents to coordinate through trigger files, response files, and typed message prefixes.

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{IPC_DIR}}` | Base directory for IPC files | `../shared` |
| `{{FILE_TOOL}}` | Locking file-write utility | `./file-tool.sh` |
| `{{AGENT_NAME}}` | Current agent's identifier | `engineer` |

## Template

---begin template---

## Inter-Agent Communication

Agents communicate through file-based IPC using a trigger/response pattern.

### File Structure

```
{{IPC_DIR}}/
├── triggers/
│   ├── pm.trigger
│   ├── architect.trigger
│   ├── engineer.trigger
│   ├── tester.trigger
│   ├── marketing.trigger
│   └── community.trigger
├── responses/
│   ├── pm.response
│   ├── architect.response
│   ├── engineer.response
│   ├── tester.response
│   ├── marketing.response
│   └── community.response
└── context/
    └── team-log.md
```

### Communication Flow

1. **Trigger** — PM writes a task to `{{IPC_DIR}}/triggers/{agent}.trigger`
2. **Agent reads** — Agent picks up the trigger file on session start
3. **Agent works** — Completes the task
4. **Response** — Agent writes result to `{{IPC_DIR}}/responses/{agent}.response`
5. **PM reads** — PM picks up the response and routes to next agent

### Response Format Prefixes

All responses MUST begin with one of these typed prefixes:

| Prefix | Meaning | When to Use |
|--------|---------|-------------|
| `DONE:` | Task completed successfully | Work is finished, ready for next step |
| `REQUEST:` | Needs decision or resource | Blocked, needs human or PM input |
| `ERROR:` | Encountered an error | Something went wrong, needs attention |

### Structured Response Format

```
DONE: {brief summary}
ISSUE: #{number}
LABEL-UPDATED: {old-label} → {new-label}
PR: #{number} (if applicable)
SUMMARY: {key changes and details}
```

### File Locking (Critical)

When multiple agents run concurrently, shared files can be corrupted. Use a locking wrapper for all writes to shared files:

```bash
# Write to a file (exclusive lock)
{{FILE_TOOL}} write {{IPC_DIR}}/responses/{{AGENT_NAME}}.response "DONE: Task completed"

# Append to a file (exclusive lock)
{{FILE_TOOL}} append {{IPC_DIR}}/context/team-log.md "## Entry\n- Details here"

# Read a file (shared lock, non-blocking)
{{FILE_TOOL}} read {{IPC_DIR}}/triggers/{{AGENT_NAME}}.trigger
```

> **Implementation note:** Build your file tool around `flock` (Linux/macOS) or equivalent OS-level file locking. The interface should support `read`, `write`, and `append` operations with automatic lock acquisition and release.

### Shared Files That Require Locking

- `{{IPC_DIR}}/triggers/*.trigger` — Agent trigger files
- `{{IPC_DIR}}/responses/*.response` — Agent response files
- `{{IPC_DIR}}/context/team-log.md` — Shared activity log

Files inside your code repository (managed by git) do not require the file tool — git handles conflicts.

### Activity Logging

All agents log their actions to a shared team log:

```bash
{{FILE_TOOL}} append {{IPC_DIR}}/context/team-log.md \
  "\n## $(date '+%Y-%m-%d %H:%M') - {{AGENT_NAME}}\n- [Action description here]"
```

### Startup Protocol

On session start, every agent should:
1. Read their trigger file for pending tasks
2. Check the team log for recent activity
3. If PM: Conduct a team status check
4. If not PM: Report status to PM

---end template---

## Customization Guide

- **Choose your IPC directory** — any shared filesystem location works. The key requirement is that all agents can read/write to it.
- **Add custom prefixes** — extend beyond DONE/REQUEST/ERROR if your workflow needs them (e.g., `BLOCKED:`, `WAITING:`, `DELEGATED:`).
- **File tool implementation** — the minimum viable file tool is a shell script wrapping `flock`. Example interface: `file-tool.sh {read|write|append} {path} [content]`.
- **Polling vs. watching** — agents can either poll trigger files on startup, or use filesystem watchers (`inotifywait`, `fswatch`) for real-time response.
- **Scale considerations** — file-based IPC works well for up to ~10 agents. Beyond that, consider message queues or databases.
