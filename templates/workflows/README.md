# Workflow Templates

> Coordination protocols that connect agents into a functioning team.

## Overview

Workflows define *how* agents work together. While agent templates define individual roles, workflow templates define the system-level patterns that enable coordination.

## Workflow Summary

| Workflow | Template | Purpose |
|----------|----------|---------|
| **Pipeline** | [`pipeline.md`](pipeline.md) | Label-driven issue stages from backlog to done |
| **IPC** | [`ipc.md`](ipc.md) | File-based inter-agent communication protocol |
| **Branching** | [`branching.md`](branching.md) | Git strategy with agent home branches |
| **Release** | [`release.md`](release.md) | Semantic versioning and release process |

## How Workflows Connect

```
Pipeline (labels)     →  Defines WHAT stage work is in
IPC (trigger/response) →  Defines HOW agents communicate
Branching (git)       →  Defines WHERE code changes happen
Release (tags)        →  Defines WHEN code ships
```

## Dependency Order

If you're implementing from scratch, add workflows in this order:

1. **Pipeline** — the foundation. Without labels, nothing routes.
2. **IPC** — enables agents to hand off work to each other.
3. **Branching** — agents need a git strategy before writing code.
4. **Release** — needed when you're ready to ship.

## Key Concepts

### Event-Driven Coordination

The primary flow is event-driven:
1. Agent completes work → updates label → writes response
2. Orchestrator detects response → delegates to next agent
3. Next agent picks up → cycle continues

Polling/self-assessment is a backup mechanism, not the primary coordination method.

### Typed Response Prefixes

All agent responses use typed prefixes for machine-readable parsing:

| Prefix | Meaning |
|--------|---------|
| `DONE:` | Task completed successfully |
| `REQUEST:` | Needs decision or resource |
| `ERROR:` | Something went wrong |
