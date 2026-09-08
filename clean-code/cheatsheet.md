# Clean Code Decision Cheatsheet

Use these rules to decide what to inspect or change. Chapter numbers refer to [SKILL.md](SKILL.md).

| When you see… | Do this… | Because… |
|---|---|---|
| A name requires a comment to decode | Establish the actual meaning, then rename | The next reader needs the same context. Ch 2 |
| A function mixes policy with string/path details | Extract a meaningful lower-level operation | Sibling steps should have one abstraction level. Ch 3 |
| A “check” also initializes or mutates | Separate the behavior or name the whole atomic operation | Callers need to predict effects. Ch 3, 13 |
| Many independent parameters | Inspect ownership and domain grouping | Fewer visible parameters are useful only if dependencies stay clear. Ch 3 |
| A mode flag chooses distinct workflows | Consider explicit operation names | The call should reveal the chosen behavior. Ch 3, 17 |
| Several copies of one business rule | Give the shared knowledge one home | Future changes should not require synchronized edits. Ch 12 |
| Similar code with different reasons to change | Keep the concepts separate until a real commonality exists | A generic selector can create artificial coupling. Ch 12, 17 |
| A comment explains a non-obvious constraint | Verify and preserve its rationale | Names cannot express every reason. Ch 4 |
| A comment repeats syntax or stale history | Remove or relocate it | Redundant claims drift and obscure useful information. Ch 4 |
| Different formatting from neighboring code | Use the team formatter and conventions | Consistency reduces reading effort. Ch 5 |
| Getters expose an object graph | Ask what job the client really needs | Behavior can hide representation. Ch 6 |
| A failed lookup becomes an empty list | Separate absence from failure | A default must not invent a successful result. Ch 7 |
| Vendor errors dominate callers | Translate at an owned boundary | Recovery needs should shape the internal contract. Ch 7 |
| A fake passes but integration is uncertain | Test the actual adapter | A substitute only confirms modeled assumptions. Ch 8 |
| Test setup overwhelms the scenario | Use Build-Operate-Check and domain helpers | The test should explain one behavioral concept. Ch 9 |
| A small class still has unrelated jobs | Split by demonstrated reasons to change | Method count does not measure responsibility. Ch 10 |
| Business work constructs concrete services | Move assembly to startup or supply a factory | Construction and use change for different reasons. Ch 11 |
| Individually synchronized calls form a sequence | Guard the full invariant in an atomic operation | Safety of each call does not make their composition safe. Ch 13 |
| A concurrency test fails intermittently | Preserve the failure evidence and investigate schedules | A successful rerun does not invalidate a race. Ch 13 |
| A refactor changes accepted inputs or outputs | Classify and verify the contract change separately | Structural cleanup does not justify new behavior. Ch 16 |

## Representation Choice

| Dominant expected change | Favor | Cost to acknowledge |
|---|---|---|
| New types with stable operations | Polymorphic objects | New operations affect implementations |
| New operations over stable types | Procedures over explicit data | New types affect procedures |

## Resolve Competing Rules

1. Preserve the required behavior and run the relevant tests.
2. Remove duplicated knowledge.
3. Make intent clear.
4. Remove unnecessary classes and methods.

This is the book's ordered Simple Design framework. It counterbalances both oversized units and excessive fragmentation. Ch 12.

## Defaults to Interpret, Not Enforce Blindly

| Source preference | Useful interpretation |
|---|---|
| Very small functions; rarely twenty lines | Inspect mixed responsibilities and abstraction levels |
| Zero, one, then two arguments preferred | Reduce call-site interpretation without hiding state |
| Roughly 200-line files, often below 500 | Inspect organization when navigation becomes difficult |
| Lines around 100–120 characters at most | Follow the project formatter and maintain readable expressions |
| One assertion preferred | One concept matters more than mechanically counting assertions |
| Wildcard imports and other Java-era preferences | Retain project conventions and verify compatibility |

## Quick Verification Route

- **Refactoring** → behavior before/after; relevant callers; unchanged error and ordering contracts.
- **Algorithm fix** → equality and limits; empty/invalid input; cases adjacent to the defect.
- **Boundary change** → consumer tests plus actual adapter behavior.
- **Concurrency change** → complete invariant; resource ownership; exhaustion; failure; shutdown.
- **Review finding** → observed code → practical consequence → smallest justified improvement.
