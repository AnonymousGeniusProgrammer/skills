# Decisions while changing legacy code

## Start here

| If… | Then… | Because… |
|---|---|---|
| The change location is uncertain | Read/sketch before restructuring | A seam chosen too early may protect the wrong behavior |
| The class looks impossible to construct | Try a minimal construction test | Actual failures reveal the necessary dependency work |
| You know what will change | Trace returns, mutation, aliases, globals, external effects | Edited lines do not identify all required observations |
| Many changes share an area | Look for a useful interception/pinch point | One broader test may supply the first cover economically |
| The old behavior surprises you | Characterize it; keep correction explicit | Observed behavior and desired behavior answer different questions |
| New helper tests pass | Check the production connection | Wrong arguments or an unused helper can still ship |

## Select the incision

| Need | Prefer | Main cost/check |
|---|---|---|
| New step inside an algorithm | Sprout Method | Original body and connection remain weakly covered |
| Independently testable new responsibility | Sprout Class | Additional boundary; verify conversion and integration |
| Work before/after an operation | Wrap Method | Preserve ordering and exception behavior |
| Optional behavior around an object | Wrap Class / Decorator | Delegation completeness and wrapper ordering |
| Replace one awkward call | Existing override seam, then Extract and Override Call | Do not override the logic being tested |
| Replace an internally created object | Parameterize Constructor/Method | Preserve original entry point, lifetime, and creation order |
| Replace an owned awkward type | Extract Interface | C++ virtual dispatch can silently change |
| Replace a wide external parameter | Adapt Parameter | Adapter translation needs checks |
| Several uses of one global | Replace Global Reference with Getter | Other clients still use the global |
| A singleton must change globally | Introduce Static Setter | Shared state, reliable cleanup, ownership, concurrency |
| Procedural external calls block testing | Link Substitution or function pointers | Correct production resolution or pointer initialization |

## Constructor decision

1. Can a caller supply the collaborator? → Parameterize Constructor.
2. Can a constructor-time factory override work under the language's dispatch and initialization rules? → Consider Extract and Override Factory Method.
3. Can creation be delayed without changing required timing? → Consider Extract and Override Getter; route all uses through it.
4. Is original construction acceptable, with only later behavior needing a fake? → Supersede Instance Variable may fit.
5. Does construction itself perform unwanted work? → Break the dependency before it executes. A later setter cannot undo it.

## Restructuring judgment

| Observation | Next move |
|---|---|
| Bulleted method | Seek a meaningful sequence, but inspect locals crossing chunks |
| Snarled method | Skeletonize control flow; name predicates and operations separately |
| Tiny understood fragment with weak coverage | Extract What You Know, then test; prefer low coupling count |
| Too many local values | Consider a method object with per-invocation state |
| Large class with several purposes | Use method groups and feature sketches; extract the responsibility needed now |
| Similar methods with meaningful differences | Extract the differences, then share the common operation |
| Removed member produces no compiler error | Check inherited fallback and name binding before calling it unused |
| Another improvement interrupts an edit | Record it; finish and check the current transformation |

## Defaults and stop signs

- Preserve signatures and move intact bodies while opening a seam; redesign interfaces after tests provide cover.
- Feathers's manual-extraction heuristic is 2–3 lines, at most 5. It is a starting aid, not a method-size rule.
- Coupling count measures values crossing the new interface. Zero does not imply no side effects.
- Keep the feedback loop fast enough to use after small edits; historical timing examples are not project requirements.
- A fake test covers the production logic it executes, not the substituted integration. Check that integration separately when affected.
- An expectation update needs an understood behavior change. Never automatically bless new output to clear a failure.

For procedures and page locators, use the [chapter index](SKILL.md#chapter-index) and [catalog](chapters/ch25-dependency-breaking-techniques.md).
