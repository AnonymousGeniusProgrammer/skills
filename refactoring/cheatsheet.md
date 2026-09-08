# Refactoring decision cheatsheet

Use this for quick judgment; open the chapter mechanics before implementing a move.

## Begin and stop

| If… | Then… | Because… |
|---|---|---|
| The task changes outputs or rules | Name it as feature/fix work; separate preparatory restructuring | The Two Hats give failures a clear meaning |
| Structure obstructs the next needed change | Refactor the obstruction first | The investment can repay itself immediately |
| A confusing module can remain behind a stable interface | Leave its internals alone for this task | Refactoring needs a practical benefit |
| Relevant feedback is missing | Add focused behavior protection or use a demonstrably safe restricted move | Small steps need a way to detect mistakes |
| A step breaks behavior | Recover that step and make it smaller | Narrow feedback beats prolonged debugging |
| The requested change is now easy and working | Finish the task | Possible cleanup is not an obligation to pursue all of it |

## Diagnose the direction

| Evidence | Starting move | Check before proceeding |
|---|---|---|
| One module has unrelated reasons to change | Split Phase or Extract Class | Are the contexts really independent? |
| One change edits many modules | Move Function/Field; possibly inline then re-extract | Which knowledge should have one home? |
| A fragment needs an intention name | Extract Function/Variable | Does the name explain more than the body? |
| Names merely repeat trivial forwarding | Inline or Remove Middle Man | Does the wrapper protect a useful interface? |
| A mutable record leaks updates | Encapsulate Record/Collection | Are nested aliases also controlled? |
| Two scalar bounds repeatedly travel together | Introduce Parameter Object | Preserve units and inclusive/exclusive endpoints |
| Stored data duplicates a calculation | Replace Derived Variable with Query | Include every original contribution and snapshot rule |
| Repeated type dispatch changes in lockstep | Replace Conditional with Polymorphism | Preserve defaults and select at one boundary |
| One exceptional path obscures ordinary work | Guard Clauses | Keep precedence, cleanup, and effects |
| Most clients repeat a missing-value fallback | Introduce Special Case | Preserve different clients and update behavior |
| A literal flag selects a distinct operation | Remove Flag Argument | Boolean domain data is a different case |

## Resolve competing choices

| Choice | Choose one side when… | Choose the other when… |
|---|---|---|
| Class / Transform | Queries must track changed source data | Consumers use a stable prepared result |
| Value / Reference | Independent immutable values or snapshots are needed | Collaborators must observe shared entity updates |
| Parameter → Query / Query → Parameter | The callee already appropriately knows the derivation | An internal dependency makes it coupled or unpredictable |
| Whole object / Scalar parts | The callee should know the domain object | That dependency would cross an unwanted boundary |
| Inheritance / Delegate | The subtype supports its parent contract and variation fits | Roles change, axes multiply, or the contract does not fit |
| Function / Command | An ordinary call is enough | A real lifecycle, undo, or execution context earns the extra structure |

## Defaults and checks

- **Rule of Three:** reconsider recurring duplication by its third occurrence; use judgment sooner when needed. (Ch 2)
- **Function size:** no mandatory line limit; extract useful intention, including a single line when warranted. (Ch 3, 6)
- **Tests:** use self-checking, risk-driven cases; coverage locates gaps but does not grade assertions. (Ch 4)
- **Ordering:** moving code must preserve read/write dependencies, short-circuiting, and relevant effects. (Ch 8)
- **Identity:** object replacement and canonical sharing have observable consequences. Check aliases first. (Ch 9)
- **Assertions:** express programmer invariants; expected bad input needs ordinary handling. (Ch 10)
- **Compatibility:** keep forwarding for independent clients until migration is established. (Ch 2, 6, 11)
- **Performance:** measure relevant behavior; loop count is not a performance result. (Ch 2, 8)

[Chapter and catalog index](SKILL.md) · [Sequences](patterns.md) · [Definitions](glossary.md)
