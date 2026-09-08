# Chapter 16: Modifying Existing Code

Source: first edition (2018), PDF pages 142-147. Reference notes.

## Core idea

Use each change to move toward the design you would choose if the new requirement had been known from the start. Evaluate that ideal within actual time, compatibility, and coordination constraints.

## Strategic maintenance

1. Understand the abstraction the affected code currently provides.
2. Ask whether the new requirement still fits that design.
3. Compare a local patch with a change to the relevant abstraction.
4. Choose the cleanest feasible design under current constraints, accounting for both implementation risk and accumulated complexity.
5. Update the contract and rationale wherever behavior or assumptions changed.

The author explicitly allows compromises: a three-month refactor may be impractical when a two-hour fix is required, and broad incompatibilities can rule out a redesign. Look for intermediate options instead of assuming either the smallest diff or the largest cleanup is best.

## Documentation maintenance rules

| Situation | Action |
|---|---|
| A behavior changes | Update the interface contract and related assumptions. |
| A block's mechanism changes | Inspect comments beside that block. |
| A subtle fix is explained in the commit message | Preserve the enduring rationale near the code too. |
| One decision affects several locations | Keep one authoritative explanation and references at affected sites. |
| Existing external documentation already explains a protocol or command | Point to it and document only local behavior and obligations. |

Position detailed implementation comments at the narrowest scope covering their subject. A method overview can describe its strategy; phase-specific details belong at the phases. As distance from the code increases, explanations should generally become more abstract.

## Author's location preference

For languages with separate declarations and implementation files, Ousterhout favors placing method documentation near the implementation so maintainers see it during changes, with documentation tools serving callers. This is a contextual preference to weigh against the project's established documentation tooling and conventions.

## Mental models and checks

- **Designed with the change in mind**: use this as a comparison target, not an instruction to rewrite unrelated systems.
- **Single source of truth**: duplicated prose can silently disagree; a broken reference is more discoverable than a plausible stale copy.
- **Diff review**: scan the completed changes for invalidated documentation and explanations missing from the maintained code.

Higher-level statements of behavior usually survive low-level refactoring better than narration of implementation steps. Preserve precise low-level comments where units, ownership, or invariants require them.

## Anti-patterns

- Assuming the smallest textual change produces the simplest long-term system.
- Repeating one module's entire contract beside every call.
- Keeping an essential bug-prevention rationale only in repository history.
- Using strategic programming as a reason to ignore compatibility or scope constraints.

## Key takeaways

1. Reassess the relevant abstraction when requirements change.
2. Make a feasible improvement rather than accumulating special cases by default.
3. Keep rationale discoverable and documentation singular.
4. Review changed behavior and comments together.

## Connects to

- [Ch 3](ch03-working-code-isnt-enough.md): sustained design investment.
- [Ch 13](ch13-comments-and-abstractions.md): cross-module documentation.
- [Ch 19](ch19-software-trends.md): tests support refactoring.
