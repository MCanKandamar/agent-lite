# agent-lite

A small, tool-independent working system for AI coding agents (Claude Code, Codex, Gemini CLI and others).

- Start a new session, say "continue the project", and the agent picks up where the last one stopped.
- If an agent is cut off mid-task (quota, crash), the next one finds the unfinished work through git and recovers it.
- Your preferences are written once and shared by every agent. Rules for each kind of work live in **profiles** that you pick per project or create on the spot.
- Works for embedded projects too (STM32, ESP32, RTOS): flashing always needs your OK, and hardware results are never assumed.

No server, no dependencies: only Markdown files and one skill.

## How it works

```
~/.agents/USER.md              core preferences: language, reports, autonomy, git (every session)
~/.agents/profiles/<name>.md   rules for one kind of work: code, embedded, learning, docs, ...
~/.agents/skills/project-setup the setup skill (copied to ~/.claude/skills for Claude Code)

<your repo>/AGENTS.md          project rules, commands, working protocol, "Profiles: code, embedded"
<your repo>/docs/WORK.md       current task, plan, done, dead ends, open questions (state)
<your repo>/docs/PROJECT.md    architecture, environment, hardware, decisions (knowledge)
<your repo>/CLAUDE.md          @AGENTS.md plus one @-import per profile (Claude Code only)
```

- **Core vs. profiles.** USER.md holds only what is true everywhere. Coding rules don't leak into your CV, and document rules don't leak into your firmware.
- **Picking profiles.** A project names its profiles in AGENTS.md (`Profiles: code, embedded, learning`). Without a project (e.g. a quick session to edit a document), the agent picks one from the index in USER.md, or asks.
- **New kinds of work.** If no profile fits, setup asks up to five questions and drafts a new profile. After that, when you correct the agent on something that applies to all work of that kind, it proposes adding the rule to the profile.
- **Recovery.** On "continue", the agent compares git history with WORK.md. Commits or uncommitted changes that WORK.md does not record mean the last session was interrupted. The agent matches them to the plan, marks them unverified, re-runs the checks and tells you.

## Profiles included

| Profile | For |
|---|---|
| `code` | writing or changing software; the agent writes the code (KISS, minimal diffs, no new dependencies without asking, honest check results) |
| `embedded` | microcontroller firmware; use with `code` (no heap in ISRs, `built ✓, needs HW test`, ask before flashing) |
| `learning` | you write the code to learn it; the agent teaches step by step and does not touch your code |
| `docs` | CV, letters, reports, presentations (keep the document's language, never invent facts) |

Edit them freely; they are your rules.

## Install

Windows PowerShell, from the cloned repository folder:

```powershell
New-Item -ItemType Directory -Force "$HOME\.agents\skills", "$HOME\.agents\profiles", "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse -Force ".\skills\project-setup" "$HOME\.agents\skills"
Copy-Item -Recurse -Force ".\skills\project-setup" "$HOME\.claude\skills"
Get-ChildItem ".\profiles\*.md" | ForEach-Object { if (-not (Test-Path "$HOME\.agents\profiles\$($_.Name)")) { Copy-Item $_.FullName "$HOME\.agents\profiles\" } }
```

macOS / Linux:

```bash
mkdir -p ~/.agents/skills ~/.agents/profiles ~/.claude/skills
cp -R skills/project-setup ~/.agents/skills/ && cp -R skills/project-setup ~/.claude/skills/
cp -n profiles/*.md ~/.agents/profiles/
```

Existing profiles are never overwritten. Claude Code reads skills from `~/.claude/skills`, Codex from `~/.agents/skills`.

## Use

- **Set up a project:** open the repository in your agent and run the skill: `/project-setup` in Claude Code, `$project-setup` in Codex, or say "set up agent-lite in this project". Without skill support, paste `skills/project-setup/setup-prompt.md` into the chat. The first run also creates `~/.agents/USER.md` and links it for your agents.
- **Daily:** "continue the project". For bigger tasks the agent writes the goal and plan to `docs/WORK.md` first.
- **Preferences:** "update my preferences".
- **Profiles:** "create a profile for <kind of work>" or "add this rule to the <name> profile".

## Update

Pull the repository, run the install commands again, then say "update agent-lite" in each project. This replaces only the protocol block in AGENTS.md (between `<!-- agent-lite:protocol … -->` markers) and shows the diff first. Your profiles and USER.md are left alone.

## Uninstall

Delete `AGENTS.md`, `docs/WORK.md` and `docs/PROJECT.md` from a project, and the `project-setup` folders from `~/.agents/skills` and `~/.claude/skills`. `~/.agents/USER.md` and `~/.agents/profiles` are yours to keep or delete.

## Status

Developed and used with Claude Code and Codex on Windows. Gemini CLI, Cursor and Copilot are supported by the setup steps but not tested.

## License

MIT, see [LICENSE](LICENSE).
