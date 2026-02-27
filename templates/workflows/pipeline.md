# Pipeline Workflow

> Label-driven issue pipeline that routes work through 6 specialized agents from backlog to done.

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{REPO}}` | GitHub repository (owner/name) | `acme/my-app` |

## Template

---begin template---

## Team Workflow — Label-Driven Pipeline

Issues flow through these stages via GitHub label changes. Each label has a clear owner.

### Pipeline Stages

```
backlog → needs-architecture → ready-for-dev → in-progress → needs-testing → needs-review → done
```

| Label | Meaning | Owner |
|-------|---------|-------|
| `backlog` | New issue, not yet prioritized | PM |
| `needs-architecture` | Needs Architect review before dev | Architect |
| `ready-for-dev` | Architecture approved, ready to build | Engineer |
| `in-progress` | Engineer actively working | Engineer |
| `needs-testing` | PR created, needs QA | Tester |
| `needs-review` | QA passed, needs founder/maintainer approval | PM |
| `done` | Merged and complete | PM |

### Agent Responsibilities

**PM (Orchestrator):**
- Creates issues and adds to backlog
- Delegates by assigning appropriate labels
- Monitors pipeline via agent update triggers (reactive)
- Escalates to founder only for approvals and blockers
- Moves issues to `done` after merge

**Architect:**
- Picks up issues labeled `needs-architecture`
- Reviews and adds architectural guidance as a comment
- Updates label: `needs-architecture` → `ready-for-dev`
- Reports completion to PM with issue number

**Engineer:**
- Picks up issues labeled `ready-for-dev`
- Updates label: `ready-for-dev` → `in-progress` when starting
- Implements feature and creates PR
- Updates label: `in-progress` → `needs-testing` when PR ready
- Reports completion to PM with PR number

**Tester:**
- Picks up issues labeled `needs-testing`
- Tests the PR thoroughly
- If PASS: Updates label `needs-testing` → `needs-review`
- If FAIL: Updates label `needs-testing` → `in-progress` (back to Engineer)
- Reports result to PM

**Marketing:**
- Creates content issues with `marketing` label
- Reports completion when content ready for approval
- Publishes after founder approval

**Community:**
- Monitors support channels
- Creates issues for recurring problems
- Reports completion after responding to users

### Label Update Commands

Every agent updates labels when they complete their work:

```bash
# Remove old status, add new status
gh issue edit {NUMBER} --repo {{REPO}} \
  --remove-label "{old-status}" \
  --add-label "{new-status}"
```

### Event-Driven Flow

1. Agent completes work → Updates GitHub label → Writes response
2. Orchestrator detects update → Parses response → Delegates to next agent
3. Next agent picks up → Cycle continues

### Automation Levels

The team can operate in one of four modes:

| Proactive | Auto-Merge | Mode | Behavior |
|-----------|------------|------|----------|
| OFF | OFF | **Manual** | Event-driven, manual merge approval |
| ON | OFF | **Supervised** | Proactive pipeline, manual merge |
| OFF | ON | **Reactive Auto-Merge** | Event-driven, auto-merge after QA |
| ON | ON | **Fully Autonomous** | Proactive pipeline + auto-merge |

### Escalation Rules

**Escalate to PM:**
- Blockers you cannot resolve
- Ambiguous requirements
- Dependencies on other agents

**PM escalates to Founder/Maintainer:**
- Content approvals
- PR merge approvals
- Budget/strategic decisions
- Critical blockers

### Response Protocol

When completing work, agents report with this format:

```
DONE: {brief summary}
ISSUE: #{number}
LABEL-UPDATED: {old-label} → {new-label}
PR: #{number} (if applicable)
```

---end template---

## Customization Guide

- **Add or remove pipeline stages** by modifying the label table. Common additions: `needs-design`, `blocked`, `in-review`.
- **Adjust automation levels** based on your trust in the agent system. Start with Manual, graduate to Supervised.
- **Simplify for smaller teams** — if you don't have an Architect, remove the `needs-architecture` stage and go directly from `backlog` to `ready-for-dev`.
- **The PM is always the orchestrator** — this is the key design principle. All other agents report to PM, never directly to users.
