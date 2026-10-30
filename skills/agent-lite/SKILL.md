---
name: agent-lite
description: "Gives any AI agent memory that lives in your repo, so work continues across sessions and tools and interrupted work is recovered from git. Installs or updates agent-lite in a repository (AGENTS.md, docs/WORK.md, docs/PROJECT.md). Keeps existing rules and other agent systems untouched. Runs a one-question-at-a-time setup interview that skips what it can read from the repo, stores cross-project user preferences once (~/.agents/USER.md), and picks or creates work profiles (~/.agents/profiles: code, embedded, learning, docs, or new ones through a short interview). Use when the user asks to set up, install, initialize, adopt or update agent-lite or the project working files, to change their agent preferences, or to create or change a profile."
---

agent-lite keeps an AI agent's plan, progress and project knowledge in your repository as plain Markdown, so any agent can continue the work in a new session. Git covers whatever was left unrecorded.

What setup touches:
- In the project: adds `AGENTS.md`, `docs/WORK.md`, `docs/PROJECT.md` and import files such as `CLAUDE.md`. Source code, build files and CI stay as they are.
- In your home folder: adds `~/.agents/USER.md` and profiles and, with your approval, links `USER.md` from your agents' global instruction files, such as `~/.claude/CLAUDE.md`.
- It shows a diff and waits for your approval before it changes any existing file. Project rules win over these preferences. Setup sends no data anywhere.

Read `setup-prompt.md` in this skill's directory and follow it exactly. It is the single source of the setup steps and templates.
