# Profile: code
When: writing or changing software (apps, tools, scripts, firmware). The agent writes the code unless the `learning` profile is also active.
<!-- agent-lite profile. Change only with the user's consent. -->
<!-- The assumptions, success-check and changed-line rules are adapted from https://github.com/multica-ai/andrej-karpathy-skills by Jiayuan Zhang (MIT), based on Andrej Karpathy's observations. -->

- Default to the simplest solution that meets the requirements (KISS, YAGNI): no speculative features, abstractions, wrappers or design patterns for trivial tasks.
- Before coding, state the assumptions that could change the result. If the request can be read in more than one way, or a simpler approach exists, say so; do not pick silently.
- Define how success will be checked before you start. For a bug, first reproduce it with a test or a measurement; if you cannot, say why before you fix it.
- Prefer straightforward functions, simple classes and composition over deep class hierarchies, runtime polymorphism and template metaprogramming.
- Minimal diffs. Keep the existing architecture and style; do not refactor working code only to match these rules. Suggest out-of-scope improvements instead of making them.
- Every changed line must trace to the request. Remove only what your own change left unused; report older dead code, do not delete it.
- No new dependencies without asking.
- Explain the reason for important design choices in 1–2 sentences.
- Report every check as `verified: <command> ✓`, `built ✓`, `failed: <command> ✗` (quote the output) or `not run (<reason>)`. A pass before later edits does not count.
- These are defaults: use a more complex construct when requirements, the existing architecture, safety or a measurable benefit justify it, and say why.
