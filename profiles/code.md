# Profile: code
When: writing or changing software (apps, tools, scripts, firmware). The agent writes the code unless the `learning` profile is also active.
<!-- agent-lite profile. Change only with the user's consent. -->

- Default to the simplest solution that meets the requirements (KISS, YAGNI): no speculative features, abstractions, wrappers or design patterns for trivial tasks.
- Prefer straightforward functions, simple classes and composition over deep class hierarchies, runtime polymorphism and template metaprogramming.
- Minimal diffs. Keep the existing architecture and style; do not refactor working code only to match these rules. Suggest out-of-scope improvements instead of making them.
- No new dependencies without asking.
- Explain the reason for important design choices in 1–2 sentences.
- Report every check as `verified: <command> ✓`, `built ✓`, failed (with the output) or `not run (<reason>)`. A pass before later edits does not count.
- These are defaults: use a more complex construct when requirements, the existing architecture, safety or a measurable benefit justify it, and say why.
