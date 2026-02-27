# Agent Templates

> Role definitions, responsibilities, and workflows for a 6-agent AI team.

## Overview

Each agent has a specialized role with clear boundaries. The PM orchestrates; all other agents report to the PM and operate within their defined pipeline stages.

```
PM (Orchestrator)
├── Architect  — Reviews before dev starts
├── Engineer   — Implements and creates PRs
├── Tester     — Tests PRs and gates quality
├── Marketing  — Drives growth and content
└── Community  — Handles support and engagement
```

## Agent Summary

| Agent | Template | Pipeline Labels | Key Action |
|-------|----------|----------------|------------|
| **PM** | [`pm.md`](pm.md) | All stages | Delegates, monitors, escalates |
| **Architect** | [`architect.md`](architect.md) | `needs-architecture` → `ready-for-dev` | Posts design review |
| **Engineer** | [`engineer.md`](engineer.md) | `ready-for-dev` → `in-progress` → `needs-testing` | Creates PR |
| **Tester** | [`tester.md`](tester.md) | `needs-testing` → `needs-review` (or back) | Approves or rejects |
| **Marketing** | [`marketing.md`](marketing.md) | `marketing` label | Creates content |
| **Community** | [`community.md`](community.md) | `community` label | Responds to users |

## How to Use

1. Pick the agents you need (minimum: PM + Engineer)
2. Copy the template content into each agent's CLAUDE.md
3. Replace `{{PLACEHOLDER}}` variables with your project values
4. Reference the [workflow templates](../workflows/) for coordination protocols

## Scaling Down

Not every project needs all 6 agents. Common configurations:

- **Solo dev (2 agents):** PM + Engineer
- **Small team (4 agents):** PM + Architect + Engineer + Tester
- **Full team (6 agents):** All roles

Remove agents you don't need and simplify the pipeline accordingly.

## GitHub Comment Identity

Each agent identifies itself in GitHub comments using HTML comments and signatures:

```markdown
<!-- operum-agent: {role} -->
## {Title}

{Comment content}

---
*{Role Title} — AI Agent Team*
```

This helps maintainers track which agent made each comment.
