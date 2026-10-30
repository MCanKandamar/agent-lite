# agent-lite setup (v5.1)

You are installing **agent-lite** into the current repository: a small set of files that lets any AI agent continue this project across sessions and tools. Talk to the user in their language (see `~/.agents/USER.md` if it exists; otherwise use the language of their messages). Agent files (AGENTS.md, docs/WORK.md, docs/PROJECT.md) are written in English unless USER.md says otherwise; section headings and labels always stay in English.

Preferences come in two layers, both outside the repository:
- `~/.agents/USER.md` — the core: language, reports, autonomy, git. Applies to every session.
- `~/.agents/profiles/<name>.md` — one file per kind of work (e.g. `code`, `embedded`, `learning`, `docs`). A project lists the profiles it uses in AGENTS.md › Project as `Profiles: code, embedded`. Each profile starts with `# Profile: <name>` and a `When:` line that says what kind of work it is for.

A file belongs to agent-lite only if it carries a marker: `<!-- agent-lite` in its text, the title `# User preferences (agent-lite)`, or, for docs/WORK.md and docs/PROJECT.md, a protocol block in the repo's AGENTS.md. Any other file belongs to the user or to another system.

## Modes
Decide the mode yourself, then tell the user in one line. Check in this order; the first match wins:
- **update**: agent-lite files exist and the user asked to update → replace only the text between `<!-- agent-lite:protocol v… -->` and `<!-- /agent-lite:protocol -->` in AGENTS.md with the current template: show the diff, apply it after approval, and stop. If AGENTS.md has a `Learning mode: on` line (v1), offer to replace it with `learning` in the `Profiles:` line. If AGENTS.md has no `## File access` section, offer to add it: find read-only candidates as in Step 2, show them, and let the user decide.
- **preferences**: the user asked to change their preferences → run Steps 0 and 1 only.
- **profile**: the user asked to create or change a profile → run Steps 0 and 3 only (no repository needed).
- **team**: the user asked to share the project between several people → run Step 7 only.
- **resume**: `TODO(setup)` markers exist in agent-lite's AGENTS.md, WORK.md or PROJECT.md → continue from the first unanswered item; never re-ask answered items.
- **installed**: AGENTS.md has the protocol block and no `TODO(setup)` remains → say that agent-lite is already installed, offer update, preferences or a profile, and change nothing.
- **existing**: there is code or documentation, and AGENTS.md has no `<!-- agent-lite:protocol` block.
- **new**: the repo has little or no source code.

## Hard rules
1. Never modify source code, build files, CI, dependency manifests or generator project files (e.g. `.ioc`) during setup.
2. Never rewrite existing content in AGENTS.md, CLAUDE.md, USER.md, profiles or other instruction files. Add sections; show a diff before changing any existing file and wait for approval.
3. Ask one question per message. Put your recommended option first, marked "(recommended)". Accept "you decide" (= recommended) and "skip" (= recorded as an open question); neither counts as approval of a diff. Never ask for secrets; ask only where credentials come from.
4. Never ask what you can read from the repo. Show what you inferred, with its source (file or command), and ask for one confirmation.
5. Right after Step 2b, create or extend the agent-lite files as Step 5 says (its Finishing bullet runs last). Write each answer into its target file right after it is given, so an interrupted setup can resume from the remaining `TODO(setup)` markers.
6. If the folder is not a git repository, recommend `git init` and ask. Crash recovery depends on git. If the work is mostly binary files (CAD, images, Office files), tell the user that git shows which files changed but not what changed inside them, so WORK.md step status matters more.
7. Profiles and USER.md hold general rules only. Project facts (commands, paths, hardware) go into AGENTS.md or docs/PROJECT.md.
8. Setup only adds. Never overwrite, rename, migrate or disable another system's files or settings. Setup must be safe to run twice: before adding a line, section or link, check that it is not already there.

## Step 0 — Bundled profiles (every mode except update and installed)
If this skill's folder contains `profiles/`, copy each file to `~/.agents/profiles/` (create the folder if missing) only if no file with that name exists there. Never overwrite a profile. Say in one line what was added and what was kept. If you received this prompt without the skill folder, skip this step; Step 3 can create profiles through the interview.

## Step 1 — User preferences (only if `~/.agents/USER.md` is missing, or in preferences mode)
If `~/.agents/USER.md` exists without the agent-lite title, it is not ours: do not touch it, use `~/.agents/agent-lite/USER.md` in its place everywhere below, and tell the user in one line. Skip this step if that file already exists.

Ask, one at a time:
1. Language: for talking to you, and for prose in agent files (recommended: your language for conversation, English for agent files).
2. Report style: short bullets with one result line per check (recommended) / detailed / minimal.
3. Autonomy: ask before any change outside the agreed plan (recommended) / do routine work alone and ask only for the "Always ask first" list.
4. Git: commit locally after each completed plan step on a feature branch, never push without asking (recommended) / never commit, the user commits / ask before every commit.
5. Review pace, so the user can follow the work: report every step and stop at the end of the task (recommended) / stop for the user's OK after every step / report only at the end of the task.
6. Shell for the commands you give them (e.g. Windows PowerShell, bash).

Build USER.md from the template at the end of this file. The template already holds the recommended answers; change a line only if the user chose differently. In its Profiles index, list only the profiles that exist in `~/.agents/profiles/`. Show it and ask for approval. Then write it, and with approval link it for the platforms the user uses:
- Claude Code: append `@~/.agents/USER.md` to `~/.claude/CLAUDE.md` (create the file if missing; keep existing content).
- Codex: in `~/.codex/AGENTS.md`, between `<!-- agent-lite:user -->` and `<!-- /agent-lite:user -->` (keep other content), write: "Before your first reply in every session, read `<absolute path to ~/.agents/USER.md>` and follow it. If the project's AGENTS.md has a `Profiles:` line, also read each listed profile from `<absolute path to ~/.agents/profiles>/<name>.md`." If `~/.codex/AGENTS.override.md` exists, warn that it hides AGENTS.md.
- Gemini CLI: append `@<absolute path to ~/.agents/USER.md>` to `~/.gemini/GEMINI.md`.
- Cursor / Copilot: they have no user-level file; give the user the text to paste into their settings.
- Any other agent that reads AGENTS.md needs no link: the Working protocol tells it to read USER.md and the profiles.

## Step 2 — Inspect the project (read-only)
Read only what is needed: README; manifests and build files (package.json, pyproject.toml, Cargo.toml, go.mod, CMakeLists.txt, Makefile, *.csproj, platformio.ini); lint/format configs; CI files; existing instruction files (AGENTS.md, CLAUDE.md, GEMINI.md, .cursor/rules, .github/copilot-instructions.md, CONTRIBUTING.md); `docs/`; `git log --oneline -20`; number of contributors.

**Embedded detection.** Treat the project as embedded if you find any of: `*.ioc` (STM32CubeMX), linker scripts (`*.ld`, `*.icf`, `*.sct`), `startup_*.s`, `arm-none-eabi` / `riscv*-elf` toolchain references, `FreeRTOSConfig.h`, Zephyr (`prj.conf`, `west.yml`), ESP-IDF (`sdkconfig`, `idf_component.yml`), `platformio.ini`, Keil (`*.uvprojx`), IAR (`*.ewp`), STM32CubeIDE/Eclipse (`.cproject`), `/* USER CODE BEGIN */` markers. From these, infer where possible: MCU/board, toolchain, build system, code generator and generated folders, RTOS, flash/RAM sizes (from the linker script), clock and pin setup (from the `.ioc` file, read-only).

**Existing systems.** Look for other agent setups: instruction files (listed above, plus `.windsurfrules`, `.kiro/`), memory or plan systems (`memory-bank/`, `task_plan.md`, `.planning/`, `.specify/`, `_bmad/`, or similar), and target files that exist without an agent-lite marker (`docs/WORK.md`, `docs/PROJECT.md`). Read the existing instruction files for rules that contradict the Working protocol or USER.md (commits, branches, plans, language, checks).

**Read-only candidates.** Look for files the agent should read but not change: third-party code (`vendor/`, `third_party/`, `external/`, `libs/`, submodules in `.gitmodules`, `Drivers/` and `Middlewares/` in STM32 projects), generated files (headers such as `DO NOT EDIT` or `@generated`), and reference material (datasheets, customer documents, approved drawings). Skip what git already ignores, such as `node_modules/` or build output. Keep the candidates for Step 4, question 2b.

Present the other findings in one list with sources, and ask for confirmation once.

## Step 2b — Coexistence (only if Step 2 found something)
- **File name clash.** Leave the existing file as it is. Recommend a separate path (`docs/agent-lite/WORK.md`, `docs/agent-lite/PROJECT.md`) and record it in AGENTS.md › Map as `Path override: docs/WORK.md = docs/agent-lite/WORK.md`. Do the same for a USER.md moved in Step 1.
- **Another memory or plan system.** Keep it. Recommend: WORK.md holds only the current task and step, and PROJECT.md › References links to the other system's files instead of copying them. Ask one question to confirm.
- **Contradicting rules.** Show each one in a line. Recommend: the existing rule wins. Record it in AGENTS.md › Conventions as `Override: <rule>`, and leave the protocol block unchanged so that updates stay clean.

## Step 3 — Profiles
1. List `~/.agents/profiles/*.md` with each file's `When:` line.
2. **Profiles exist:** show the list and recommend a set from Step 2 (e.g. embedded detected → `code, embedded`). Ask which ones this project uses; several are allowed. Then ask whether the user does the work to learn it (recommended: no; yes → add `learning`, if that profile exists).
3. **No profiles, or the work the user describes fits none:** say so and create one with a short interview, one question per message, at most five:
   1. What kind of work is it, and what is the result (e.g. firmware, web app, CV, presentation)?
   2. Who does the work: the agent, or the user while the agent teaches?
   3. What must always be true of the result (quality bar, checks, style, language)?
   4. What must the agent never do, or always ask first?
   5. Anything you often have to correct agents about in this kind of work?
   Draft `~/.agents/profiles/<short-name>.md` from the answers: `# Profile: <name>`, a `When:` line, then only the rules the answers support, as short bullets. Show it, write it after approval, and add one line for it to the index in USER.md › Profiles. Tell the user: "This profile is a first draft. Whenever you correct me on something that applies to all work of this kind, I will propose adding it here."
4. Write the chosen names into AGENTS.md › Project as `Profiles: <names>`. For Claude Code, add `@~/.agents/profiles/<name>.md` to CLAUDE.md for each profile, below `@AGENTS.md`. Tell the user that Claude Code asks once per project to allow these imports from outside the repository; until they allow it, the agent reads the profiles with a tool instead.

## Step 4 — Questions (ask only what is still unknown, in this order)
1. Purpose: what the project is, who uses it, its stage (prototype / active / production / maintenance). → AGENTS.md › Project
2. Working principles: show the default "Always ask first" list and ask what to add for this project (e.g. migrations, payment code, public API, safety-critical code). → AGENTS.md › Always ask first
   2b. Read-only files: show the candidates from Step 2, each with its reason, as "I recommend not touching these". The user decides: accept them (recommended), remove some, or add others. → AGENTS.md › File access
3. Architecture: preferred structure, constraints, things to avoid. → PROJECT.md › Architecture
4. Project-specific conventions that neither the tools nor the chosen profiles cover (naming, error handling, MISRA or similar rules, allowed language features). → AGENTS.md › Conventions
5. Development environment: confirm detected build/test/lint commands; ask for missing ones and for anything needed to run the project that is not in the repo (services, environment variable names, tools). → AGENTS.md › Commands, PROJECT.md › Environment

   **Embedded questions (only for embedded projects, only what is still unknown):**
   - E1. Target MCU and board (and hardware revision if it matters). → PROJECT.md › Hardware & toolchain
   - E2. Toolchain and build system (CMake / Make / IDE project / CubeMX / PlatformIO / ESP-IDF / Zephyr), the exact build command, and how to check memory usage (e.g. `arm-none-eabi-size build/app.elf`). → AGENTS.md › Commands, PROJECT.md
   - E3. Code generator boundaries: which tool generates code, which files and folders it owns, whether regeneration is expected, and the rule for editing generated files (e.g. only inside `USER CODE BEGIN/END`). → AGENTS.md › Conventions, PROJECT.md
   - E4. Hardware constraints: pins and peripherals that are reserved or must not change, clock source and frequency, power or timing limits. → PROJECT.md › Hardware & toolchain
   - E5. RTOS and memory budget, if relevant: RTOS name, task and stack conventions, flash/RAM limits or reserved headroom. → PROJECT.md
   - E6. Hardware testing: who tests on the board (recommended: the user, after the agent reports `built ✓, needs HW test`) and whether a serial log or test command exists. → AGENTS.md › Verification labels note
6. Verification expectations: what must pass before a step counts as done (e.g. unit tests, build without warnings, lint). → AGENTS.md › Conventions
7. Git for this project, only if it differs from USER.md (branch naming, commit message style; infer from CONTRIBUTING.md or git log first). → AGENTS.md › Git

## Step 5 — Create or extend the files
Use the templates below.
- **New projects:** create AGENTS.md, docs/WORK.md and docs/PROJECT.md. Add CLAUDE.md if Claude Code is used. Add `.gemini/settings.json` if Gemini CLI is used.
- **Existing projects:**
  - If AGENTS.md exists, append the missing sections and leave the old content untouched.
  - If CLAUDE.md exists, add `@AGENTS.md` as its first line.
  - Link existing docs from PROJECT.md › References instead of copying them.
  - Show diffs before writing to existing files.
- **Embedded tags:** in the templates below, any line containing `[embedded]` is embedded-only. For embedded projects keep the line and delete the tag; for other projects delete the whole line. Keep the Hardware & toolchain section of PROJECT.md only for embedded projects.
- **Finishing:** fill every `TODO(setup)` you have an answer for. Move unanswered items to WORK.md › Open questions and delete their markers. In WORK.md › Next, put "Confirm open questions" or the user's first task.

## Step 6 — Finish
- Check that AGENTS.md, WORK.md and PROJECT.md exist (at their `Path override` paths, if any), that every profile in the `Profiles:` line exists in `~/.agents/profiles/`, and that no `TODO(setup)` markers remain. A listed profile that is missing: offer to create it with the Step 3 interview, or to remove it from the line.
- Show the user: the decisions taken (and whether each was inferred or answered), the profiles used or created, the files created or changed, and the open questions.
- Suggest a commit (`chore: add agent-lite working files`) and wait for approval if the Git rules require it.
- If Step 2 found more than one contributor, offer to share the project (Step 7).

## Step 7 — Shared project (only in team mode)
Needs protocol v4 or later in AGENTS.md; if it is older, run update first. Each step that changes a file follows hard rule 2.
1. Work out the handle (see Working protocol › Sources), show it, and ask for one confirmation.
2. Move the current WORK.md with `git mv` to `docs/work/<handle>.md`, using the real handle in the file name.
3. Add `Path override: docs/WORK.md = docs/work/<handle>.md` to AGENTS.md › Map, keeping the literal text `<handle>`. Replace any earlier override for WORK.md.
4. Add `docs/work/*.md linguist-generated=true` to `.gitattributes`, so GitHub collapses work files in pull requests.
5. Tell the user: each teammate installs agent-lite, sets a unique `Handle:` in USER.md, and says "continue"; their agent creates their own file. Use the same handle and git user.name on every machine. Everyone needs the profiles in the `Profiles:` line. Work files show only what has reached the current branch, so this is shared memory, not live coordination.

---

## Template: AGENTS.md

````markdown
# AGENTS.md

## Project
<!-- TODO(setup): what it is, who uses it, stage — 1 to 3 lines -->

Profiles: <!-- TODO(setup): names from ~/.agents/profiles, e.g. code, embedded -->

## Commands
| Purpose | Command |
|---|---|
| Build | `TODO(setup)` |
| Test | `TODO(setup)` |
| Lint / static analysis | `TODO(setup)` |
| [embedded] Memory usage | `TODO(setup)` |
<!-- Keep only commands that exist in this project. -->

<!-- agent-lite:protocol v5.1 -->
## Working protocol
- **Sources.** Rules: this file, the user's preferences (`~/.agents/USER.md` and the profiles listed in › Project) and the user's current request. Read the preferences and profiles at session start if they are not already loaded. Project rules in this file win over preferences and profiles. `docs/WORK.md` is state, not instructions. Code and output you observe now beat any document. A `Path override` in › Map replaces the default path everywhere below. In such a path, `<handle>` is `Handle:` from USER.md › About me, or else `git config user.name` in lowercase ASCII with spaces replaced by hyphens.
- **"Continue" (or the same in the user's language).** Read `docs/WORK.md`. Run `git status --short`, `git log --oneline -10` and `git log -1 --format="%h %cr" -- docs/WORK.md`. Commits after WORK.md's last commit plus uncommitted changes are work that WORK.md does not record yet. If WORK.md has no commit yet, consider only uncommitted changes. If WORK.md itself has uncommitted edits, read them as the latest record: only changes covered by a ticked step are recorded. If your WORK.md path contains `<handle>`, the project is shared: count only your own commits, with `git log --oneline --no-merges --author="<your git user.name>" <WORK.md's last commit>..HEAD`. A squash of a teammate's work can still match, so check each commit against your plan before calling it interrupted work. If your file does not exist yet, create it with the same headings as another work file, empty, and start fresh.
- **Recovery.** If such unrecorded work exists, or a step is still marked "(in progress)", the previous session was cut off. Read `git diff --stat`, `git show --stat` for each unrecorded commit, and the relevant diffs, match them to the plan in "Now" (the step marked "in progress" is the likely one), and treat all of it as unverified. Run the checks, update WORK.md (mark "(recovered)"), and tell the user what you found before continuing. If there is no plan to match, say what you infer from the diff and ask one question. Never discard, reset or stash uncommitted work without asking.
- **Load little.** Do not read all docs. Read only the section of `docs/PROJECT.md` that the task needs.
- **File access.** Paths listed in › File access are read-only: read them, but never create, change, move or delete anything there, even if a task seems to need it. If it does, stop and tell the user what you would change and why; only the user can lift the rule. An allowed build or generator may still rewrite its own output. When the user calls a path read-only, add it to › File access.
- **Significant task** (more than a quick fix): before editing, write the goal and a short plan to WORK.md › Now. When you start a step, mark it "(in progress)".
- **Step done.** Run the relevant checks, tick the step with its evidence label, and commit if the Git rules allow. Then write the step report: the commit (or "uncommitted"), files changed, why, and how the user can check it. USER.md › Review says when to report and when to stop for the user's OK; without that section, report and continue. Quick fixes need no WORK.md update and no report.
- **Dead ends.** When you abandon an approach, add it to WORK.md › Dead ends with the reason. Check Dead ends before proposing an approach. In a shared project, a dead end that others could hit also goes to PROJECT.md › Decisions & constraints as a `Constraint:`.
- **Task done.** Move it to Done with evidence, choose Next[1] or ask the user, and keep WORK.md within its size rule.
- **Knowledge.** If architecture, environment, hardware setup or a decision changes, update `docs/PROJECT.md` in the same change. If code and a document disagree, the code wins; fix the document.
- **Honesty.** Never claim a check you did not run. Report every check as passed, failed or not run (with the reason). A pass before later edits does not count. Use the labels below.
<!-- /agent-lite:protocol -->

## Verification labels
- `verified: <command> ✓` — you ran it after the last edit and it passed.
- `built ✓` — it compiles and links.
- [embedded] For firmware, add memory use: `built ✓ (flash 41.2/64 KB, RAM 9.8/20 KB)`.
- `needs HW test` — [embedded] only real hardware can prove it. Write `built ✓, needs HW test`, and never assume it works on the board.
- `HW verified (<who>, <date>)` — [embedded] only if the user reported the result on hardware, or you ran an on-target test the user approved and saw its output.
- `failed: <command> ✗` — it ran and failed; quote the relevant output.
- `unverified (<reason>)` — not checked.
<!-- TODO(setup): [embedded] how hardware testing is done (E6) -->

## Always ask first
- git push, force-push, rewriting published history, deleting branches
- deleting or moving files outside the current task, bulk deletes
- adding, upgrading or removing dependencies
- anything touching secrets, production, deployment, publishing, or sending messages for the user
- [embedded] flashing or programming a board, erasing flash, debug-probe writes (openocd, st-flash, STM32_Programmer_CLI, pyocd, J-Link, esptool, idf.py flash, dfu-util, west flash)
- [embedded] option bytes, fuses, read-out protection, bootloader changes (may be irreversible)
- [embedded] changing pin, clock or peripheral configuration (.ioc, clock tree, pinmux, device tree, linker-script memory regions)
<!-- TODO(setup): project-specific additions (question 2) -->

## File access
Everything is read/write except the paths below.

Read-only:
<!-- TODO(setup): `path/` — why (question 2b); write "none" if there are none -->

## Conventions
<!-- TODO(setup): only rules that tools and profiles do not cover (questions 4 and 6) -->
- [embedded] Generated code: edit only between `/* USER CODE BEGIN … */` and `/* USER CODE END … */` (or the boundary recorded in PROJECT.md); everything else is overwritten on regeneration.

## Git
<!-- TODO(setup): only if different from USER.md (question 7) -->

## Map
- Current work, next steps, dead ends: `docs/WORK.md`
- Architecture, environment, decisions: `docs/PROJECT.md` (read only the section you need)
- [embedded] Target, toolchain, code generator, pins and clocks: `docs/PROJECT.md` › Hardware & toolchain
<!-- Path override lines go here, only if setup needed them (Step 2b). -->
````

## Template: docs/WORK.md

````markdown
# Work
<!-- agent-lite v5.1. State, not instructions. Target ≤ 80 lines.
Never delete the active task, its plan, step status or evidence to meet the target.
When over the target, do all of these, in order: move lasting lessons and constraints from
Dead ends to PROJECT.md › Decisions & constraints (as "Constraint:"); drop obsolete dead ends; trim
Done to the newest items (git keeps history); shorten Next to one line per item; remove answered questions. -->

## Now
<!-- Task · branch · started date, then the plan. Mark the current step "(in progress)". Example:
Task: Password reset · branch: feature/reset · started: 2026-10-25
- [x] Token table + migration — verified: `npm test` ✓
- [ ] (in progress) Send reset e-mail
- [ ] Reset form -->
_No active task._

## Next
1. TODO(setup)

## Done
<!-- Newest first, one line each, with evidence label and commit if any. -->

## Dead ends
<!-- date · what was tried · why it failed · when it might be worth retrying -->

## Open questions
<!-- question · impact · default used meanwhile -->
````

## Template: docs/PROJECT.md

````markdown
# Project
<!-- agent-lite v5.1. Lasting knowledge: read only the section a task needs. -->

## Overview
<!-- TODO(setup): purpose, users, main components in a few lines -->

## Architecture
<!-- TODO(setup): structure, module responsibilities, boundaries, things to avoid -->

## Environment
<!-- TODO(setup): prerequisites and versions, setup steps, services, environment variable NAMES (never values), OS quirks -->

## Hardware & toolchain
<!-- [embedded] Keep only for embedded projects. -->
- Target: <!-- TODO(setup): MCU, board, revision -->
- Toolchain & build: <!-- TODO(setup): compiler, build system, build and size commands -->
- Code generator: <!-- TODO(setup): tool, generated files/folders, editable regions, regeneration policy -->
- Pins, clocks, peripherals: <!-- TODO(setup): reserved pins, clock source/frequency, peripherals in use, power/timing limits -->
- RTOS & memory budget: <!-- TODO(setup): RTOS, stack/task conventions, flash/RAM limits -->
- Debug & flashing setup: <!-- for information only; flashing needs the user's approval every time -->

## Decisions & constraints
<!-- Newest first. "YYYY-MM-DD · Decision: … · Why: …" or "YYYY-MM-DD · Constraint: … · Learned from: …".
Mark replaced entries "(superseded by YYYY-MM-DD)" instead of deleting them. -->

## References
<!-- Links to existing docs (datasheets, reference manuals, design docs) instead of copies -->
````

## Template: CLAUDE.md (only if Claude Code is used)

````markdown
@AGENTS.md
@~/.agents/profiles/<name>.md
````
<!-- One `@~/.agents/profiles/<name>.md` line per profile in the Profiles: line. -->

## Template: .gemini/settings.json (only if Gemini CLI is used)
If the file exists, add only the `context.fileName` entry and show the diff first.

````json
{
  "context": {
    "fileName": ["AGENTS.md", "GEMINI.md"]
  }
}
````

## Template: ~/.agents/USER.md

````markdown
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
````
