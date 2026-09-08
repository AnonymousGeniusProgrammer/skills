# Chapter 3: Bad Smells in Code

By Kent Beck and Martin Fowler. Source: printed pp. 71-84; PDF pp. 93-106.

## Core idea

Use a smell as a hypothesis about a maintenance problem. Identify the concrete change it obstructs, then consult the suggested refactoring's motivation and mechanics. The authors deliberately avoid universal line-count or field-count thresholds.

## Diagnostic map: all 24 smells

| Smell | Evidence to look for | Candidate moves and judgment |
|---|---|---|
| Mysterious Name | A reader must inspect implementation to discover intent | Change Function Declaration, Rename Variable, Rename Field; a hard-to-find name can expose confused responsibility |
| Duplicated Code | The same knowledge must be checked or changed in several places | Extract Function; Slide Statements to align fragments; Pull Up Method for shared subclass behavior |
| Long Function | Purpose is obscured by implementation detail | Extract Function; reduce obstructing temps and parameters first; Split Loop if a loop has multiple jobs |
| Long Parameter List | Calls are difficult to understand or values travel together | Replace Parameter with Query, Preserve Whole Object, Introduce Parameter Object, Remove Flag Argument, Combine Functions into Class |
| Global Data | Many places can alter shared state and its writers are hard to find | Encapsulate Variable, then narrow scope and control updates; enforced immutable global data has lower risk |
| Mutable Data | Distant updates invalidate assumptions | Encapsulate Variable, Split Variable, Separate Query from Modifier, Remove Setting Method; eliminate derivable state |
| Divergent Change | One module changes for unrelated reasons | Split Phase for sequential contexts; Extract Function and Move Function, or Extract Class, for interleaved responsibilities |
| Shotgun Surgery | One conceptual change requires edits across many modules | Move Function and Move Field to gather knowledge; Combine Functions into Class or Transform; inline then re-extract if current boundaries obstruct progress |
| Feature Envy | A function obtains much of its data from another module | Move Function, possibly after Extract Function; prioritize things that change together over a mechanical getter count |
| Data Clumps | The same meaningful group appears as fields and parameters | Extract Class, Introduce Parameter Object, Preserve Whole Object; ask whether removing one member makes the others nonsensical |
| Primitive Obsession | Strings/numbers carry domain rules scattered across callers | Replace Primitive with Object; type-code behavior may warrant subclasses and polymorphism |
| Repeated Switches | The same classification is dispatched in several places | Replace Conditional with Polymorphism to localize changes when a category is added |
| Loops | Traversal syntax conceals selection and transformation | Replace Loop with Pipeline; split unrelated jobs first |
| Lazy Element | A function, class, or hierarchy adds little explanation or behavior | Inline Function, Inline Class, Collapse Hierarchy |
| Speculative Generality | Unused hooks and parameters complicate current needs | Remove unused parameters, Inline Function/Class, Collapse Hierarchy, Remove Dead Code after checking actual use |
| Temporary Field | Some state exists only for one situation or computation | Extract Class with associated behavior; Introduce Special Case where absent-state behavior is consistent |
| Message Chains | Clients depend on an object graph's navigation structure | Hide Delegate at a useful boundary, or extract and move the consuming behavior toward the data |
| Middle Man | An interface is dominated by forwarding without useful separation | Remove Middle Man or inline individual wrappers; retain delegation that protects a valuable boundary |
| Insider Trading | Modules rely excessively on one another's internal details | Move Function/Field, introduce a home for common knowledge, Hide Delegate; reconsider inheritance coupling |
| Large Class | Field groups, duplicated code, or client-specific subsets reveal several responsibilities | Extract Class, Extract Superclass, or Replace Type Code with Subclasses according to the actual relationship |
| Alternative Classes with Different Interfaces | Equivalent capabilities cannot substitute because protocols differ | Change Function Declaration and Move Function; consider Extract Superclass after behavior aligns |
| Data Class | Other objects repeatedly manipulate a holder's data in detail | Encapsulate Record, remove unnecessary setters, move behavior toward the data; effectively immutable phase-result records are a useful exception |
| Refused Bequest | A subclass inherits behavior or interface that does not fit | Push down genuinely specialized members; use a delegate when the subtype cannot support the inherited interface |
| Comments | Explanations compensate for obscure names or tangled code | Extract Function, rename, or Introduce Assertion; retain rationale, uncertainty, and other information code cannot express well |

## Distinctions that determine the move

### Divergent Change versus Shotgun Surgery

Draw a small map of change reasons to affected modules. If one module serves several independent reasons, separate those contexts. If one reason touches scattered modules, gather that knowledge. Both can coexist. Splitting a module simply because it is long can worsen shotgun surgery; gathering everything simply because files are numerous can worsen divergent change.

### Feature Envy versus intentional variation

Data proximity is a heuristic. Strategy and Visitor intentionally separate behavior so a particular dimension can vary independently. Ask which parts change together and whether moving the function would couple previously independent policies. Extract only the genuinely envious fragment if the surrounding function has a different home.

### Message Chains versus Middle Man

Hide navigation when a client should be insulated from relationship changes. If hiding every link creates a forwarding bureaucracy, expose a stable collaborator or move the operation closer to the end of the chain. A fluent pipeline is not automatically an object-navigation smell; identify the knowledge the chain exposes.

### Repeated Switches versus one conditional

The second edition deliberately focuses on repeated dispatch. A single switch can be the clearest local expression of a choice, including a factory that selects a subtype. Polymorphism pays when it gathers a recurring dimension of variation; it has its own class and indirection costs.

### Data Class versus a phase result

The intermediate data produced by Split Phase may appropriately be a plain, effectively immutable record. It need not acquire methods merely to satisfy an object-oriented preference. A mutable record whose callers duplicate its rules is a different situation.

## Worked example: diagnose a change before splitting files

Reconstruction of the chapter's database/financial-instrument scenario:

1. A module needs edits for both a database change and a financial-instrument change. Record those as two change reasons: this suggests Divergent Change.
2. If fetching precedes financial calculation, separate the phases with an intermediate record. If they are interleaved, extract and move the responsible functions first.
3. Now inspect a single instrument change. If it also touches duplicated rules across several other modules, those edits suggest Shotgun Surgery. Gather that rule independently of the first split.
4. Verify that each resulting module has a coherent reason to change and that the refactoring preserves behavior.

The result is a responsibility boundary justified by change patterns, not a predetermined number of classes.

## Anti-patterns and takeaways

- Do not turn this diagnostic vocabulary into a rule that every occurrence must be removed.
- Good names allow small functions to explain their callers. Arbitrary fragmentation with weak names adds navigation cost.
- An inlining step that temporarily creates a large function can enable a better subsequent extraction.
- Comments about why a choice was made can remain valuable after the code is clear.
- Refusing a superclass implementation is often tolerable; refusing its public contract is a stronger reason to replace inheritance.

Read [Ch 6](ch06-first-refactorings.md) for initial decomposition, [Ch 7](ch07-encapsulation.md) for boundaries, [Ch 8](ch08-moving-features.md) for responsibility moves, [Ch 10](ch10-conditional-logic.md) for dispatch, and [Ch 12](ch12-inheritance.md) for subtype relationships.
