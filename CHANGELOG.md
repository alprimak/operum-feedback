# Changelog

All notable changes to Operum Desktop are documented here.

## [0.18.0] - 2026-04-11

### New Features

- **Schedule & Todo tools for agents** — Agents can now create, edit, and delete schedules and todo items natively via Operum MCP tools
- **Context window utilization** — Agents page now shows how much of each agent's context window is in use
- **Auto-focus command input** — Mission Control command input is focused automatically on open
- **Escape key support** — Press Esc to dismiss the support chat or todo panel from anywhere
- **Per-agent token usage stats** — Agents page shows input/output token breakdown per agent
- **Keyboard navigation** — Alt+1–8 to jump between sidebar sections; Alt+0 for command input; overlay shows available shortcuts

### Improvements

- **Onboarding wizard** — Substep indicators, in-app Claude login flow, and auto-advance after each step
- **Auto-merge reliability** — Fixed cases where QA-approved PRs were not auto-merging after the flag was toggled on
- **Agent performance** — Agent memory and log management improved for long-running sessions
- **GitHub references** — Issues and PRs are now consistently labeled as "Issue #N" or "PR #N" throughout the app

### Bug Fixes

- Fixed token usage stats being reset on periodic team sync
- Fixed command input losing draft content when navigating between pages
- Fixed blocking operations causing UI freezes on slower machines
- Fixed duplicate padding when support chat panel was open

---

## [0.17.0] - 2026-04-07

### New Features

- **Operum MCP Server** — Native git and GitHub operations for agents without requiring system CLI tools
- **Auto-approve Operum tools** — Claude Code automatically approves Operum MCP tool calls without prompting

### Improvements

- Redesigned landing pages for individuals and organizations
- Keyboard-accessible How It Works multi-page section with sidebar navigation
- Mobile UX improvements across all landing pages

### Bug Fixes

- Fixed integration credentials leaking between teams
- Fixed activity feed showing duplicate entries
- Fixed Twitter rate limiting to prevent account suspension

---

## [0.15.0] - 2026-04-03

### New Features

- **Agent chat history** — Support chat now loads conversation history from the activity journal

### Bug Fixes

- Fixed MCP tools not included in Claude Code's allowed tools list
- Fixed How It Works mobile tabs not staying fixed below the navbar
- Fixed sequential PR enforcement incorrectly blocking docs/marketing PRs

---

*For older release notes, see previous versions in the app.*
