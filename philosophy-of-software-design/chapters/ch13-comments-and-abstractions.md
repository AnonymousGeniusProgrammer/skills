# Chapter 13: Comments Should Describe Things that Aren't Obvious from the Code

Source: first edition (2018), PDF pages 107-128. Reference notes.

## Core idea

Add information at a different level from the adjacent code: precise meanings that declarations omit, or an abstract explanation that makes implementation details understandable. Preserve the reader's ability to use an abstraction without reconstructing its implementation.

## Choose the kind of comment

| Kind | Information to capture | Natural location |
|---|---|---|
| **Interface** | Capability, result, arguments, effects, failures, and caller obligations | With the class or operation declaration/documentation |
| **Data structure member** | Meaning, units, ownership, allowed values, and invariants | Beside the member declaration |
| **Implementation** | Purpose of a substantial block and reasons for non-obvious choices | Inside the implementation, near the relevant block |
| **Cross-module** | Shared decision, dependencies, and required coordinated changes | At the natural change point, or one central explanation with local pointers |

Use established documentation conventions and tools. The author advocates interface comments for classes and methods and comments for class variables, while acknowledging declarations with nothing useful to add. Most simple local variables and short method bodies need no separate implementation narration.

## Lower-level precision

For a value or parameter, establish the information a reader otherwise has to infer from all its uses:

- Units and coordinate system.
- Inclusive or exclusive endpoints.
- Meaning of null, empty, absent, and sentinel values.
- Resource ownership and who closes or frees it.
- Invariants and relationships to other values.

Describe what a variable **represents**, rather than listing every statement that changes it. A heartbeat flag can mean that a heartbeat has arrived since the last timer reset; this definition is clearer than a procedural account of each thread toggling it.

## Higher-level intuition

For a block of code, state the goal that explains the steps. In the source's RPC example, the useful abstraction is finding an unsent request to the right server that can accept more work. Repeating each conditional test gives the reader detail without that organizing idea.

For unusual paths, explain the conditions that bring execution there and why the work is necessary. Comments on longer loops can describe what one iteration accomplishes.

## Interface documentation checklist

Begin with caller-visible behavior, then specify meaningful argument and result constraints, side effects, exceptions, preconditions, and sequencing obligations. Keep internal data structures and algorithms out unless a property they imply is needed for correct use.

In the `IndexLookup` example:

- A caller needs the key-comparison semantics of its range query.
- Internal RPC message formats and index storage structures stay hidden.
- Performance characteristics may deserve a high-level explanation when they affect use; that does not require internal protocol details.
- A readiness method must explain both blocking behavior and any requirement to call it to advance the operation.
- Failure behavior belongs in the contract when callers can observe it. Transparent recovery mechanisms need not be exposed as implementation recipes.

Illustrative comment, newly written to show the technique:

```java
// Pixels reserved on each side of a rendered text line.
private int horizontalMargin = 4;
```

The added information is the unit and whether the value applies per side. Merely restating the variable name would not help.

## Cross-module decisions

Put coordinated-change guidance where a maintainer must go, such as an enum declaration when adding a status value. If no natural center exists, use one labeled design note and short references at affected sites. The book's `designNotes` example is an organizational option; the essential property is discoverability without duplicated explanations.

## Red flags and limits

**Comment Repeats Code**: someone could write it by mechanically paraphrasing the adjacent statements. **Implementation Documentation Contaminates Interface**: callers must wade through details they do not need. Missing documentation is not cured by cryptic or incomplete brevity.

## Key takeaways

1. Add precision below the declaration or intuition above the implementation.
2. Document the complete caller contract separately from internal reasoning.
3. Make cross-module dependencies discoverable at their change points.
4. Treat a reader's confusion as evidence to improve the code or explanation.

## Connects to

- [Ch 4](ch04-deep-modules.md): formal and informal interfaces.
- [Ch 15](ch15-comments-first.md): comments reveal design weaknesses early.
- [Ch 16](ch16-modifying-existing-code.md): single-source documentation and maintenance.
