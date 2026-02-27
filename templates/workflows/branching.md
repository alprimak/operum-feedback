# Git Branching Strategy

> Agent home branches, feature branch conventions, and conflict resolution for multi-agent AI teams.

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{AGENT_NAME}}` | Current agent's identifier | `engineer` |
| `{{REPO}}` | GitHub repository (owner/name) | `acme/my-app` |

## Template

---begin template---

## Git Branching Strategy

All agents follow a standardized git workflow with dedicated home branches and short-lived feature branches.

### Agent Home Branches

Each agent uses a persistent `agent/{name}` branch as its idle/home state:

| Agent | Home Branch |
|-------|-------------|
| Engineer | `agent/engineer` |
| Tester | `agent/tester` |
| Architect | `agent/architect` |

Home branches track `main` via fetch + fast-forward merge at the start of each task. They exist because `main` cannot be checked out in multiple worktrees simultaneously.

**Rule:** `agent/*` home branches must NEVER be used for feature work. Always create a properly named feature branch before writing code.

### Branch Naming Convention

**Format:** `<type>/issue-<number>-<short-description>`

| Type | Purpose | Example |
|------|---------|---------|
| `feat/` | New features | `feat/issue-42-user-auth` |
| `fix/` | Bug fixes | `fix/issue-17-login-error` |
| `refactor/` | Code refactoring | `refactor/issue-55-api-cleanup` |
| `docs/` | Documentation | `docs/issue-33-readme-update` |
| `test/` | Tests | `test/issue-28-integration-tests` |
| `chore/` | Maintenance | `chore/issue-61-deps-update` |

### Workflow

**1. Start work (sync home branch first):**
```bash
git checkout agent/{{AGENT_NAME}}
git fetch origin main
git merge origin/main --ff-only
git checkout -b feat/issue-{NUMBER}-description
```

**2. Make changes and commit:**
```bash
git add <files>
git commit -m "feat: description (relates to #NUMBER)"
```

**Commit message format:**
- `fix: <desc>` — Bug fixes
- `feat: <desc>` — Features
- `refactor: <desc>` — Refactoring
- `docs: <desc>` — Documentation
- `test: <desc>` — Tests

Always include `(relates to #NUMBER)` or `(closes #NUMBER)`.

**3. Rebase before push (mandatory):**
```bash
git fetch origin main
git rebase origin/main
```

**4. Push and create PR:**
```bash
git push -u origin feat/issue-{NUMBER}-description
gh pr create --title "feat: Description (#NUMBER)" \
  --body "## Summary\n\nCloses #NUMBER\n\n## Changes\n- Change 1"
```

**5. Return to home branch after task:**
```bash
git checkout agent/{{AGENT_NAME}}
```

### Conflict Resolution

If your branch conflicts with main:

```bash
git checkout agent/{{AGENT_NAME}}
git fetch origin main
git merge origin/main --ff-only
git checkout feat/issue-{NUMBER}-description
git rebase agent/{{AGENT_NAME}}
# Resolve conflicts, then:
git add <resolved-files>
git rebase --continue
git push -f origin feat/issue-{NUMBER}-description
```

### Rules

**DO:**
- Create feature branches from your synced home branch
- Use descriptive names with issue numbers
- Keep branches short-lived (< 1 week)
- Rebase if stale
- Delete after merge
- One branch per issue
- Return to `agent/{name}` after every completed task

**DON'T:**
- Create branches without issue numbers
- Leave branches open after work is done
- Reuse old branch names
- Work on multiple issues in same branch
- Push directly to `main`
- Do feature work directly on `agent/*` branches

### Agent-Specific Notes

- **Engineer:** Always create branch before starting work. Push early and often. Return to home branch after task completion.
- **Tester:** Use `gh pr checkout` to test PR branches. Return to home branch after task completion.
- **Architect:** Design/RFC branches can live up to 2 weeks for review. Return to home branch after task completion.

---end template---

## Customization Guide

- **Home branch naming** — `agent/{name}` is the convention. Alternatives: `bot/{name}`, `ai/{name}`. Pick one and be consistent.
- **Worktree usage** — if agents run concurrently, use `git worktree` so each agent has its own working directory. Home branches enable this.
- **Non-code agents** (Marketing, Community) can use `docs/` or `content/` prefixes and batch multiple updates.
- **Simplify for solo agent** — if you only have one agent, you can skip home branches and work directly on feature branches from `main`.
