# Starter Kit

> Bootstrap a new multi-agent project in minutes.

## What's Included

| File | Purpose |
|------|---------|
| [`CLAUDE.md.example`](CLAUDE.md.example) | Complete CLAUDE.md template with all system sections |
| [`project.toml`](project.toml) | Project metadata that agents reference on startup |
| [`goals.toml`](goals.toml) | Near-term and long-term goal tracking |

## Setup Guide

### 1. Create your project structure

```bash
mkdir -p .operum/knowledge
mkdir -p shared/triggers shared/responses shared/context
touch shared/context/team-log.md
```

### 2. Configure project metadata

Copy and edit the config files:

```bash
cp project.toml .operum/knowledge/project.toml
cp goals.toml .operum/knowledge/goals.toml
```

Edit `project.toml` with your project name, repo URL, and tech stack.

### 3. Build your CLAUDE.md

Start from `CLAUDE.md.example`. For each agent:

1. Copy the example as the base
2. Replace all `{{PLACEHOLDER}}` variables
3. Paste the relevant agent template from [`../agents/`](../agents/)
4. Paste the workflow templates from [`../workflows/`](../workflows/) that the agent needs

### 4. Create agent home branches

```bash
git checkout -b agent/engineer
git push -u origin agent/engineer

git checkout -b agent/tester
git push -u origin agent/tester

git checkout -b agent/architect
git push -u origin agent/architect

git checkout main
```

### 5. Set up GitHub labels

Create the pipeline labels in your repository:

```bash
REPO="owner/repo"
gh label create "status:backlog" --repo "$REPO" --color "6b7280"
gh label create "status:needs-architecture" --repo "$REPO" --color "8b5cf6"
gh label create "status:ready-for-dev" --repo "$REPO" --color "3b82f6"
gh label create "status:in-progress" --repo "$REPO" --color "9333ea"
gh label create "status:needs-testing" --repo "$REPO" --color "f59e0b"
gh label create "status:needs-review" --repo "$REPO" --color "f97316"
gh label create "status:done" --repo "$REPO" --color "22c55e"
```

### 6. Implement a file-locking tool

Agents need a locking wrapper for concurrent file writes. Minimum implementation:

```bash
#!/bin/bash
# file-tool.sh — flock-based atomic file operations
ACTION="$1"; FILE="$2"; CONTENT="$3"
case "$ACTION" in
  read)   flock -s "$FILE.lock" cat "$FILE" 2>/dev/null ;;
  write)  flock "$FILE.lock" bash -c "echo \"$CONTENT\" > \"$FILE\"" ;;
  append) flock "$FILE.lock" bash -c "echo \"$CONTENT\" >> \"$FILE\"" ;;
esac
```

This is a minimal example. Production implementations should handle edge cases (missing directories, permissions, Windows compatibility).

## Platform Notes

- **Linux/macOS:** Templates work as-is. Use `flock` for file locking.
- **Windows:** Substitute `.sh` → `.cmd` or `.ps1`. Use file locking equivalent (e.g., .NET `FileStream` with `FileShare.None`).
- **Path separators:** Forward slashes work in most contexts on all platforms.

## What's Next

After setup, create your first issue and let the pipeline handle it:

```bash
gh issue create --repo "owner/repo" \
  --title "feat: my first feature" \
  --label "status:backlog"
```

The PM agent will pick it up and route it through the pipeline.
