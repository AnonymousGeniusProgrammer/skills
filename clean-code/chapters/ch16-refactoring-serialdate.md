# Chapter 16: Refactoring SerialDate

Source: printed pp. 267–284; PDF pp. 298–315. Robert C. Martin. Case study: David Gilbert's `org.jfree.date.SerialDate` from JCommon.

## Core Idea

Understand and test a legacy component before changing its structure. Use failures, coverage gaps, naming problems, and misplaced responsibilities to identify specific improvements, while distinguishing bug fixes and contract changes from behavior-preserving refactoring.

## Frameworks Applied

### First, Make It Work; Then Make It Right

1. Read the public behavior and existing tests.
2. Use coverage and call searches to find unexplored operations.
3. Add independent behavioral examples for important gaps and boundaries.
4. Classify failures: implementation defect, unsupported desired behavior, or unresolved requirement.
5. Resolve justified behavior changes explicitly.
6. Refactor structure while running the relevant wider suite.

The source increases initial coverage from about half the executable statements to over ninety percent before the main structural pass. Those are observations about this example, not required targets. Near the end, the reported percentage falls because the class has shrunk and a few uncovered statements now carry more weight. Read the uncovered behavior, not only the percentage.

### Tests Can Reveal Ambiguity

The author proposes case-insensitive parsing and additional abbreviations. He leaves some abbreviation tests unresolved because the contract is uncertain. An intuitive expectation is evidence to discuss, not authority to silently change a public library's behavior.

For actual project work, distinguish:

| Finding | Next action |
|---|---|
| Behavior contradicts an established contract | Add a reproducer and fix the defect |
| Behavior is undocumented and callers may rely on it | Inspect usage and clarify the contract |
| A preferred new behavior expands accepted input | Treat it as a feature or compatibility decision |
| A test records an unanswered question | Keep the uncertainty visible and resolve it |

### Put Concepts at the Right Abstraction Level

The abstract date represents a calendar day. Its name should not advertise the serial/ordinal representation of one implementation. Martin selects `DayDate` to distinguish day-only meaning from other Java date types.

Move implementation-specific bounds, lookup tables, and calculations into the implementation that needs them. Keep generic operations in the abstraction only when their dependencies are genuinely generic.

### Replace Integer Codes with Domain Types

The source introduces types for months, weekdays, interval inclusion, week-in-month selection, and relative weekday searches.

1. Identify the set of meaningful values and their external representation.
2. Introduce a type that expresses the valid choices.
3. Migrate callers deliberately.
4. Move related behavior, such as month-to-quarter conversion, to the relevant concept.
5. Remove redundant internal validation while retaining validation where raw external input enters.

Why it works: callers cannot casually confuse unrelated integer domains. Failure mode: changing types or numeric encodings breaks serialized data, public APIs, or integrations that were not included in the migration.

### Make Logical Dependencies Physical

The day-of-week calculation appears generic but depends on the weekday associated with ordinal zero. Moving its code without that assumption would create a hidden implementation dependency.

The source introduces an abstract operation supplying the weekday of the ordinal origin. The common algorithm can then use that explicit input. Search for assumptions about units, origins, ranges, and ordering before moving an algorithm to a more general layer.

### Separate Construction from the Abstract Model

The original abstract date's creation path constructs `SpreadsheetDate`. A factory separates representation choice from the general date contract. The abstraction no longer needs to select its own concrete derivative directly.

## Worked Example: Following Weekday

The source finds that asking for the Saturday following 25 December 2004 returns the same date. Under the intended strictly-following contract, the answer is 1 January 2005.

Reconstructed arithmetic:

```text
offset = targetWeekday - currentWeekday
if offset <= 0:
    offset += 7
return baseDate plus offset days
```

The equality case is the important boundary. A method named “on or after” would have a different contract; the algorithm must match the actual promise.

Test all seven target weekdays from several base dates, including transitions across month and year boundaries. For “nearest weekday,” test both past and future targets; the case study discovers an unreachable future-adjustment branch through a pattern of failing tests and uncovered code.

## Worked Example: A Clear Immutable Operation

The source changes date arithmetic into instance methods, then notices an ambiguity: `date.addDays(7)` can imply mutation even though a new date is returned.

```java
DayDate nextWeek = date.plusDays(7);
```

This source-derived naming choice makes the value-producing operation clearer. Verify that callers retain the return value where needed and that the original instance remains unchanged according to its contract. Renaming alone cannot establish immutability.

## Refactoring Map

| Finding | Transformation | Why |
|---|---|---|
| `SerialDate` exposes implementation in an abstract name | Rename around the day concept | Match the abstraction |
| Month constants inherited through an interface | Use a month enum | Express type meaning without false inheritance |
| Month-to-quarter logic in the date class | Move behavior to the month concept | Reduce feature envy |
| Generic-looking algorithm assumes ordinal origin | Add an explicit origin contract | Expose the logical dependency |
| Abstract base constructs a concrete date | Introduce a factory | Separate construction choice |
| Several arithmetic operations repeat end-of-month adjustment | Extract that shared rule | Keep one definition of the boundary behavior |
| Method has no callers except tests for that method | Investigate whether the operation is part of a real contract | Avoid preserving unnecessary behavior solely because it has a test |

## Context-Sensitive Choices in the Source

The chapter includes decisions to remove an explicit `serialVersionUID`, reduce uses of `final`, favor wildcard imports, and delete unused methods/tests. These are contested or context-dependent choices, not automatic actions for a generated skill.

- Preserve serialization compatibility unless changing it is part of the task and verified with representative stored data.
- Preserve language constraints and project conventions that provide a useful guarantee.
- Before deleting a library method, inspect its actual compatibility surface, including external, reflective, generated, and framework-driven callers where applicable. A local search alone does not prove that a public API is unused.
- Separate deliberate behavior changes from structural transformations in the report.

These application boundaries prevent a historical case-study decision from being mistaken for authorization in another codebase.

## Key Concepts

- **DayDate**: the case study's name for a calendar-day abstraction.
- **Ordinal day**: a numeric representation relative to an origin.
- **Feature envy**: behavior relying heavily on another concept's information.
- **Logical dependency**: an assumption about another component not expressed through its interface.
- **Boundary defect**: an error at equality, limits, or transitions.
- **Coverage pattern**: the shape of executed and unexecuted code, useful beyond a single percentage.

## Mental Models

- Use failing-case patterns to infer the mistaken boundary or assumption.
- Move behavior with the concept that explains it.
- Distinguish compatibility decisions from improvements in internal organization.

## Anti-patterns

- Refactoring before understanding important uncovered behavior.
- Calling a desired new behavior a bug without establishing the contract.
- Moving code upward while leaving representation assumptions implicit.
- Deleting external contracts based only on local “find usages.”
- Treating coverage percentage as a quality ranking independent of code size and behavior.

## Key Takeaways

1. Establish behavior before structural cleanup.
2. Resolve ambiguity explicitly.
3. Use types and ownership to clarify the domain.
4. Make hidden representation assumptions explicit.
5. Preserve compatibility unless its change is intended and verified.

## Connects To

- [Appendix B](appendix-b-serialdate.md): the source listing inventory and study route.
- [Chapter 9](ch09-unit-tests.md): calendar tests and single-concept examples.
- [Chapter 17](ch17-smells-and-heuristics.md): the IDs used throughout the case study.
