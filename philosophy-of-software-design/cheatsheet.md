# Decision cheatsheet

First-edition guidance. Chapter numbers resolve through [the chapter index](SKILL.md#chapter-index).

## Choose a direction

| When you see... | Consider... | Because... |
|---|---|---|
| One decision forces many edits | Localize its knowledge. (2, 5) | Scattered decisions amplify change. |
| A small task needs much background | Reduce caller obligations; expose necessary facts. (2, 4) | Short code can still have high cognitive load. |
| Two modules share the same format rules | Merge related work or give those rules one owner. (5, 9) | Separate stages can duplicate knowledge. |
| Every caller must coordinate several steps | A complete operation with a simpler contract. (4, 8, 9) | The module can own shared sequencing. |
| A low-level API mirrors individual features | General operations plus caller-owned policy. (6, 9) | Generality can simplify current usage. |
| A configuration knob needs expert guessing | Automatic selection or a useful default. (8) | Transferring a question does not solve it. |
| Callers know more than the implementation | Expose the meaningful choice. (5, 8) | Necessary information cannot be hidden. |
| The first design seems inevitable | Sketch a structurally different alternative. (11) | Comparison reveals assumptions. |
| Documentation resists a clear contract | Revisit the abstraction, then explain it completely. (13, 15) | Brevity created by missing facts is misleading. |
| An unfamiliar requirement prompts a tiny patch | Compare a coherent redesign within constraints. (16) | Minimal diffs can accumulate special cases. |
| A test or pattern suggests a design | Check the resulting abstraction and dependencies. (19) | Technique adoption does not prove simplicity. |
| Code is too slow | Measure, seek a structural fix, simplify the hot path. (20) | Intuition can target the wrong cost. |

## Split or combine? (9)

- Shared knowledge or simpler combined use -> consider combining.
- Independent general mechanism versus special-purpose policy -> separate their knowledge.
- Extractable subtask -> split if parent and child remain independently understandable.
- Every caller must invoke both resulting methods and transfer state -> reconsider the split.
- Only evidence is method length -> inspect contracts and readability before choosing.

## Handle an exceptional condition? (10)

- A natural broader contract achieves the intended result -> define the error away.
- The module can recover while preserving its promise -> mask locally.
- Several errors require one response -> aggregate at that response boundary.
- Recovery is not viable -> evaluate explicit termination against the system's obligations.
- Callers require the failure information -> expose it.

## Red flags: first investigation

| Flag | Inspect |
|---|---|
| Shallow Module | Interface cost versus useful hidden complexity. (4) |
| Information Leakage | Multiple owners of one design decision. (5) |
| Temporal Decomposition | Knowledge duplicated across runtime stages. (5) |
| Overexposure | Rare choices imposed on common usage. (5) |
| Pass-Through Method | What the extra layer contributes. (7) |
| Repetition | Missing abstraction or duplicated control flow. (9) |
| Special-General Mixture | Policy embedded in a reusable mechanism. (9) |
| Conjoined Methods | Dependencies defeating independent understanding. (9) |
| Comment Repeats Code | Missing precision or higher-level purpose. (13) |
| Implementation Documentation Contaminates Interface | Details unnecessary for callers. (13) |
| Vague Name | Important distinctions the identifier omits. (14) |
| Hard to Pick Name | Unclear purpose or combined concepts. (14) |
| Hard to Describe | Complex or incomplete abstraction. (15) |
| Nonobvious Code | The fact a reader needs to infer behavior correctly. (18) |

## Defaults and qualifications

- Invest continuously; **10-20%** is Ousterhout's heuristic, not a required quota. (3)
- Measure depth through caller burden; use **no universal line-count limit**. (4, 9)
- Choose somewhat general interfaces for **current needs**, not speculative features. (6)
- Prefer complete, precise comments to either verbosity or cryptic brevity. (13)
- For a bug fix, first demonstrate the failure with a test. (19)
- Remove optimization complexity without a demonstrated benefit, unless it independently simplifies design. (20)
