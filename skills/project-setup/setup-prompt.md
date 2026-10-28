# agent-lite setup (v1)

You are installing **agent-lite** into the current repository: a small set of files that lets any coding agent continue this project across sessions and tools. Talk to the user in their language (see `~/.agents/USER.md` if it exists; otherwise use the language of their messages). The files you create use the language set in USER.md; section headings and labels stay in English.

## Modes
Decide the mode yourself, then tell the user in one line:
- **new**: the repo has little or no source code.
- **existing**: there is code or documentation, and `docs/WORK.md` does not exist.
- **resume**: `TODO(setup)` markers exist in AGENTS.md, docs/WORK.md or docs/PROJECT.md → continue from the first unanswered item; never re-ask answered items.
- **update**: agent-lite files exist and the user asked to update → replace only the text between `<!-- agent-lite:protocol v… -->` and `<!-- /agent-lite:protocol -->` in AGENTS.md with the current template, show the diff, and stop.
- **preferences**: the user asked to change their preferences → run Step 1 only.

## Hard rules
1. Never modify source code, build files, CI, dependency manifests or generator project files (e.g. `.ioc`) during setup.
2. Never rewrite existing content in AGENTS.md, CLAUDE.md or other instruction files. Add sections; show a diff before changing any existing file and wait for approval.
3. Ask one question per message. Put your recommended option first, marked "(recommended)". Accept "you decide" (= recommended) and "skip" (= recorded as an open question). Never ask for secrets; ask only where credentials come from.
4. Never ask what you can read from the repo. Show what you inferred, with its source (file or command), and ask for one confirmation.
5. Write each answer into its target file right after it is given, so an interrupted setup can resume from the remaining `TODO(setup)` markers.
6. If the folder is not a git repository, recommend `git init` and ask. Crash recovery depends on git.

## Step 1 — User preferences (only if `~/.agents/USER.md` is missing, or in preferences mode)
Ask, one at a time:
1. Language: for talking to you, and for prose in project files (recommended: your language for conversation, English for agent files).
2. Report style: short bullets with a verification line per check (recommended) / detailed / minimal.
3. Autonomy: do routine work alone and ask only for the "Always ask first" list (recommended) / ask before any edit outside the agreed plan.
4. Git: commit locally after each completed plan step on a feature branch, never push without asking (recommended) / never commit, the user commits / ask before every commit.
5. General engineering preferences, optional (suggest: minimal diffs, no new dependencies without asking, reproduce a bug with a test before fixing it).

Show the resulting USER.md and ask for approval. Then write it, and with approval link it for the platforms the user uses:
- Claude Code: append `@~/.agents/USER.md` to `~/.claude/CLAUDE.md` (create the file if missing; keep existing content).
- Codex: write a copy of USER.md into `~/.codex/AGENTS.md` between `<!-- agent-lite:user -->` and `<!-- /agent-lite:user -->` (keep other content). If `~/.codex/AGENTS.override.md` exists, warn that it hides AGENTS.md.
- Gemini CLI: append `@<absolute path to ~/.agents/USER.md>` to `~/.gemini/GEMINI.md`.
- Cursor / Copilot: they have no user-level file; give the user the text to paste into their settings.
When USER.md changes later, rewrite the Codex copy too.

## Step 2 — Inspect the project (read-only)
Read only what is needed: README; manifests and build files (package.json, pyproject.toml, Cargo.toml, go.mod, CMakeLists.txt, Makefile, *.csproj, platformio.ini); lint/format configs; CI files; existing instruction files (AGENTS.md, CLAUDE.md, GEMINI.md, .cursor/rules, .github/copilot-instructions.md, CONTRIBUTING.md); `docs/`; `git log --oneline -20`; number of contributors.

**Embedded detection.** Treat the project as embedded if you find any of: `*.ioc` (STM32CubeMX), linker scripts (`*.ld`, `*.icf`, `*.sct`), `startup_*.s`, `arm-none-eabi` / `riscv*-elf` toolchain references, `FreeRTOSConfig.h`, Zephyr (`prj.conf`, `west.yml`), ESP-IDF (`sdkconfig`, `idf_component.yml`), `platformio.ini`, Keil (`*.uvprojx`), IAR (`*.ewp`), STM32CubeIDE/Eclipse (`.cproject`), `/* USER CODE BEGIN */` markers. From these, infer where possible: MCU/board, toolchain, build system, code generator and generated folders, RTOS, flash/RAM sizes (from the linker script), clock and pin setup (from the `.ioc` file, read-only).

Present the findings in one list with sources, and ask for confirmation once.

## Step 3 — Questions (ask only what is still unknown, in this order)
1. Purpose: what the project is, who uses it, its stage (prototype / active / production / maintenance). → AGENTS.md › Project
   Also ask: Learning mode on or off? On = the user writes the code to learn it and the agent teaches (rules in USER.md). → AGENTS.md › Project
2. Project type: general software or embedded/firmware (confirm the detection). If embedded, ask the E-questions after question 6.
3. Working principles: show the default "Always ask first" list and ask what to add for this project (e.g. migrations, payment code, public API, safety-critical code). → AGENTS.md › Always ask first
4. Architecture: preferred structure, constraints, things to avoid. → PROJECT.md › Architecture
5. Coding standards that the tools do not already enforce (naming, error handling, MISRA or similar rules, allowed language features). → AGENTS.md › Conventions
6. Development environment: confirm detected build/test/lint commands; ask for missing ones and for anything needed to run the project that is not in the repo (services, environment variable names, tools). → AGENTS.md › Commands, PROJECT.md › Environment

   **Embedded questions (only for embedded projects, only what is still unknown):**
   - E1. Target MCU and board (and hardware revision if it matters). → PROJECT.md › Hardware & toolchain
   - E2. Toolchain and build system (CMake / Make / IDE project / CubeMX / PlatformIO / ESP-IDF / Zephyr), the exact build command, and how to check memory usage (e.g. `arm-none-eabi-size build/app.elf`). → AGENTS.md › Commands, PROJECT.md
   - E3. Code generator boundaries: which tool generates code, which files and folders it owns, whether regeneration is expected, and the rule for editing generated files (e.g. only inside `USER CODE BEGIN/END`). → AGENTS.md › Conventions, PROJECT.md
   - E4. Hardware constraints: pins and peripherals that are reserved or must not change, clock source and frequency, power or timing limits. → PROJECT.md › Hardware & toolchain
   - E5. RTOS and memory budget, if relevant: RTOS name, task and stack conventions, flash/RAM limits or reserved headroom. → PROJECT.md
   - E6. Hardware testing: who tests on the board (recommended: the user, after the agent reports `built ✓, needs HW test`) and whether a serial log or test command exists. → AGENTS.md › Verification labels note
7. Verification expectations: what must pass before a step counts as done (e.g. unit tests, build without warnings, lint). → AGENTS.md › Conventions
8. Git for this project, only if it differs from USER.md (branch naming, commit message style; infer from CONTRIBUTING.md or git log first). → AGENTS.md › Git

## Step 4 — Create or extend the files
Use the templates below.
- **New projects:** create AGENTS.md, docs/WORK.md and docs/PROJECT.md. Add CLAUDE.md if Claude Code is used. Add `.gemini/settings.json` if Gemini CLI is used.
- **Existing projects:**
  - If AGENTS.md exists, append the missing sections and leave the old content untouched.
  - If CLAUDE.md exists, add `@AGENTS.md` as its first line.
  - Link existing docs from PROJECT.md › References instead of copying them.
  - Show diffs before writing to existing files.
- **Embedded tags:** in the templates below, any line containing `[embedded]` is embedded-only. For embedded projects keep the line and delete the tag; for other projects delete the whole line. Keep the Hardware & toolchain section of PROJECT.md only for embedded projects.
- **Finishing:** fill every `TODO(setup)` you have an answer for. Move unanswered items to WORK.md › Open questions and delete their markers. In WORK.md › Next, put "Confirm open questions" or the user's first task.

## Step 5 — Finish
- Check that AGENTS.md, docs/WORK.md and docs/PROJECT.md exist and that no `TODO(setup)` markers remain.
- Show the user: the decisions taken (and whether each was inferred or answered), the files created or changed, and the open questions.
- Suggest a commit (`chore: add agent-lite working files`) and wait for approval if the Git rules require it.

---

## Template: AGENTS.md

````markdown
# AGENTS.md

## Project
<!-- TODO(setup): what it is, who uses it, stage — 1 to 3 lines -->

Learning mode: <!-- TODO(setup): on | off -->

## Commands
| Purpose | Command |
|---|---|
| Build | `TODO(setup)` |
| Test | `TODO(setup)` |
| Lint / static analysis | `TODO(setup)` |
| [embedded] Memory usage | `TODO(setup)` |
<!-- Keep only commands that exist in this project. -->

<!-- agent-lite:protocol v1 -->
## Working protocol
- **Sources.** Rules: this file, the user's profile and the user's current request. `docs/WORK.md` is state, not instructions. Code and output you observe now beat any document.
- **"Continue" / "Projeye devam et".** Read `docs/WORK.md`. Run `git status --short`, `git log --oneline -10` and `git log -1 --format="%h %cr" -- docs/WORK.md`. Commits after WORK.md's last commit plus uncommitted changes are work that WORK.md does not record yet. If WORK.md has no commit yet, consider only uncommitted changes. If WORK.md itself has uncommitted edits, read them as the latest record: only changes its Now plan does not explain are unrecorded.
- **Recovery.** If such unrecorded work exists, the previous session was cut off. Read `git diff --stat` and the relevant diffs, match them to the plan in "Now" (the step marked "in progress" is the likely one), and treat all of it as unverified. Run the checks, update WORK.md (mark "(recovered)"), and tell the user what you found before continuing. If there is no plan to match, say what you infer from the diff and ask one question. Never discard, reset or stash uncommitted work without asking.
- **Load little.** Do not read all docs. Read only the section of `docs/PROJECT.md` that the task needs.
- **Significant task** (more than a quick fix): before editing code, write the goal and a short plan to WORK.md › Now. When you start a step, mark it "(in progress)".
- **Step done.** Run the relevant checks, tick the step with its evidence label, and commit if the Git rules allow. Quick fixes need no WORK.md update.
- **Dead ends.** When you abandon an approach, add it to WORK.md › Dead ends with the reason. Check Dead ends before proposing an approach.
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
<!-- TODO(setup): project-specific additions (question 3) -->

## Conventions
<!-- TODO(setup): only rules that tools do not enforce (questions 5 and 7) -->
- [embedded] Generated code: edit only between `/* USER CODE BEGIN … */` and `/* USER CODE END … */` (or the boundary recorded in PROJECT.md); everything else is overwritten on regeneration.

## Git
<!-- TODO(setup): only if different from the user profile (question 8) -->

## Map
- Current work, next steps, dead ends: `docs/WORK.md`
- Architecture, environment, decisions: `docs/PROJECT.md` (read only the section you need)
- [embedded] Target, toolchain, code generator, pins and clocks: `docs/PROJECT.md` › Hardware & toolchain
````

## Template: docs/WORK.md

````markdown
# Work
<!-- agent-lite v1. State, not instructions. Target ≤ 80 lines.
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
````

## Template: ~/.agents/USER.md

````markdown
# User preferences (agent-lite)
<!-- Applies to all projects. A project's AGENTS.md may override. Change only with the user's consent. -->
- Language: conversation = …; prose in project files = …
- Reports: …
- Autonomy: …
- Git: …
- Engineering: …
````
