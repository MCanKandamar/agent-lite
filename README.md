# agent-lite

**Your AI agent forgets. Your repo doesn't.**

agent-lite is a small working system for AI agents, made of Markdown files and git. The agent keeps its plan and progress in your repository, so the next session, or another agent, can pick up the work.

- **Continue anywhere.** Start a new session, or move from Claude Code to Codex when your quota runs out. Say "continue the project" and the work carries on.
- **Recover interrupted work.** If a session stops mid-task, the next one compares git with the notes, finds what was never recorded and checks it again.
- **Follow the work.** Bigger tasks are planned first and done in small steps, each with a reason and a check you can repeat.
- **Spend less on long sessions.** The state lives in files, so a fresh session can replace a huge one.
- **Use it beyond code.** It works for any repeatable work that fits in a folder: software, firmware, drawings, reports.

No server, no scripts, no hooks: your agent reads the files and follows them.

## See it work

A session was cut off yesterday. Today, in a new session (illustrative output):

```text
You:   continue the project

Agent: Read docs/WORK.md. Commit 5f82a1c is not recorded there, so the last session was cut off.
       - Matched it to the plan: "Send reset e-mail" (in progress)
       - Checked it again. verified: `npm test` ✓
       - Updated WORK.md and marked the step (recovered)

       Next step: Reset form. Shall I continue?
```

By default, each step of a planned task ends with a short report:

```text
Commit: 8c41e2d
Files:  src/mail/reset.ts, tests/reset.test.ts
Why:    reset links must expire, so the token stores its creation time
Check:  run `npm test`, then request a reset and open the link after 15 minutes
```

## Why it exists

**Agents lose context.** A session ends, a quota runs out or a tool crashes, and what the agent knew goes with it. Keeping one session open gets expensive instead: every request sends the whole context again. In the author's Claude Code setup, a new session starts near 76K tokens, so a 300K session carries about four times as much on every request. agent-lite itself adds roughly 2K tokens, plus your project notes.

**People lose track of the code.** This is often called [comprehension debt](https://addyosmani.com/blog/comprehension-debt/): the gap between how much code exists and how much of it anyone understands. In one [randomized trial](https://www.anthropic.com/research/AI-assistance-coding-skills) of 52 engineers, the group using AI scored 50% on a quiz about a new library, and the group coding by hand scored 67%. Small steps with a stated reason give you something you can actually read.

## How memory works

| Layer | Where | What it holds | When the agent reads it |
|---|---|---|---|
| Short-term | `docs/WORK.md` | Current task, plan, dead ends (target: 80 lines) | Every "continue" |
| Long-term | `docs/PROJECT.md` | Architecture, environment, decisions | Only the section a task needs |
| Archive | git history | Commits and earlier versions of WORK.md | During recovery, or when needed |
| Rules | `~/.agents/USER.md`, profiles | Your preferences, and the profiles a project lists | At the start of each session |

Each project also gets an `AGENTS.md` with its commands and the working protocol. Claude Code gets a small `CLAUDE.md` that imports it and your profiles.

## What makes it different

- **Git is the witness.** Notes can be out of date; git shows what really changed. The agent treats anything the notes do not record as unverified until it checks it.
- **It belongs to no tool.** Any agent that reads files and uses git can follow it, and one agent can take over from another.
- **Evidence instead of claims.** A check counts only if it ran after the last edit: `verified: npm test ✓`, `failed`, or `not run` with the reason.
- **Your rules follow you.** Write your preferences once. Profiles keep coding rules out of your CV and document rules out of your code.
- **It is small enough to read.** You can check every file before you install it.

## Safe with what you already have

Setup only adds. If you already use `AGENTS.md`, `CLAUDE.md`, Cursor rules, Memory Bank, planning-with-files, Spec Kit or something similar, it stays in place.

- It shows a diff and waits for your approval before it changes any existing file, inside or outside the project.
- It looks for other agent systems and lists what it finds. It never migrates or disables them.
- If `docs/WORK.md` or `docs/PROJECT.md` is already taken, it uses `docs/agent-lite/` instead.
- If your rules contradict the protocol, your existing rule wins and is recorded as an override.
- It never changes source code, build files or CI, and it never asks for secrets.
- Running it twice is safe: the second run says it is already installed.

agent-lite sends no data anywhere. The `npx skills` installer is a separate tool that sends anonymous install counts; set `DISABLE_TELEMETRY=1` to turn that off.

## Install

```bash
npx skills add MCanKandamar/agent-lite -g
```

This needs Node.js 22.20 or later, for the installer only. Then open your project in your agent and run the skill:

| Agent | Command |
|---|---|
| Claude Code, Antigravity | `/agent-lite` |
| Codex | `$agent-lite` |
| Any other | say "set up agent-lite in this project" |

If your agent has no skill support, paste `skills/agent-lite/setup-prompt.md` into the chat.

Setup asks one question at a time and skips what it can read from the repository. It keeps your preferences in `~/.agents/USER.md` and the included profiles in `~/.agents/profiles`.

<details>
<summary>Install without Node.js</summary>

Windows PowerShell:

```powershell
git clone https://github.com/MCanKandamar/agent-lite.git
New-Item -ItemType Directory -Force "$HOME\.agents\skills", "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse -Force ".\agent-lite\skills\agent-lite" "$HOME\.agents\skills"
Copy-Item -Recurse -Force ".\agent-lite\skills\agent-lite" "$HOME\.claude\skills"
```

macOS / Linux:

```bash
git clone https://github.com/MCanKandamar/agent-lite.git
mkdir -p ~/.agents/skills ~/.claude/skills
cp -R agent-lite/skills/agent-lite ~/.agents/skills/
cp -R agent-lite/skills/agent-lite ~/.claude/skills/
```

</details>

## Use

- **Daily:** say "continue the project". For bigger tasks, the agent writes the goal and plan to `docs/WORK.md` first.
- **Preferences:** "update my preferences". This also sets how closely you follow the work: report each step, or stop after each step.
- **Profiles:** "create a profile for <kind of work>" or "add this rule to the <name> profile".

## Profiles

A profile holds the rules for one kind of work. A project lists its profiles in `AGENTS.md`, for example `Profiles: code, embedded`. These four are included, and you can edit them freely:

| Profile | For |
|---|---|
| `code` | Writing or changing software: simple solutions, minimal diffs, no new dependencies without asking |
| `embedded` | Microcontroller firmware, with `code`: ask before flashing, and only hardware proves it works |
| `learning` | You do the work to learn; the agent teaches step by step and leaves your code alone |
| `docs` | CVs, letters, reports, presentations: never invent facts, keep the document's language |

For any other kind of work, setup asks up to five questions and drafts a profile. One mechanical engineer already uses agent-lite to have an agent produce drawings.

## Works with

| Agent | Status |
|---|---|
| Claude Code | Tested on Windows |
| Codex | Tested on Windows; reads `AGENTS.md` with no extra setup |
| Antigravity | Tested on Windows; reads `AGENTS.md` with no extra setup |
| Gemini CLI, Cursor, Copilot | Supported by the setup steps, not tested yet |

## Tips for saving tokens

- When a task is finished, start a new session instead of carrying on. `docs/WORK.md` holds what the next one needs.
- If the task is not finished and the context is large, compact it and say what to keep: the goal, decisions, failed attempts and the next step.
- In Claude Code, a `UserPromptSubmit` hook can read the token count from the session transcript and remind you at 200K and every 100K after that. This is optional and needs a script, so it is not part of agent-lite.

## Update

Run the install command again, then say "update agent-lite" in each project. This replaces only the protocol block in `AGENTS.md` and shows the diff first. Your profiles and `USER.md` stay as they are. If you installed the old `project-setup` skill, delete that folder from `~/.agents/skills` and `~/.claude/skills`.

## Uninstall

Delete the agent-lite `WORK.md` and `PROJECT.md`: they are in `docs/`, unless `AGENTS.md` › Map lists a `Path override`. Delete `AGENTS.md` too, or remove only the agent-lite sections if you had that file before. Then remove the `agent-lite` folders from `~/.agents/skills` and `~/.claude/skills`. `~/.agents/USER.md` and `~/.agents/profiles` are yours to keep or delete.

## License

MIT, see [LICENSE](LICENSE).
