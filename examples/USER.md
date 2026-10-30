# User preferences (agent-lite)
<!-- Core: applies to every session and project. A project's AGENTS.md may override.
Change only with the user's consent. Lives at ~/.agents/USER.md. -->

## About me
- <!-- role and experience in one line, e.g. "Junior developer, learning while building" -->
- Handle: <!-- optional short name for shared projects, e.g. "alex"; default: your git user.name -->

## Communication
- Conversation: <!-- your language -->. Files: the language the active profile says; agent files (AGENTS.md, WORK.md) in English.
- Reports: short bullets, one line per check with its result (done / verified / not run + reason).
- Commands: complete and ready to paste for <!-- your shell, e.g. Windows PowerShell -->. After them, say what output to expect and what to send back.

## Autonomy
- Ask before any change outside the agreed plan. Reading, searching, building and running tests need no approval.

## Review
- Report every completed plan step; stop for my OK at the end of the task.

## Git (only in git repositories)
- Commit locally on a feature branch after each completed plan step. Never push without asking.
- Commits and PRs in my name only; no AI co-author or "Generated with" lines.

## Profiles
- A project lists its profiles in AGENTS.md (`Profiles: code, embedded`). Read each from `~/.agents/profiles/<name>.md` before starting.
- No project or no `Profiles:` line: pick the profile that fits the task from the index below. If unsure, ask. If none fits, use this core only; if this kind of work will repeat, offer to create a profile for it.
- When my feedback would apply to every task of a profile, propose adding it to that profile (show the change, wait for my OK).

Index (one line per installed profile):
- code — writing or changing software; the agent writes the code
- embedded — microcontroller firmware; use with code
- learning — I do the work to learn it; the agent teaches
- docs — documents for people: CV, letters, reports, presentations
