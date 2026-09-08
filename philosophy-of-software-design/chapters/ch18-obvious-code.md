# Chapter 18: Code Should be Obvious

Source: first edition (2018), PDF pages 152-158. Reference notes.

## Core idea

Code is obvious when a reader's quick interpretation is likely to be correct. Evaluate this through readers and reviews; the author's familiarity with the implementation is not the relevant test.

## Three ways to improve obviousness

1. **Reduce the information needed** through abstractions and fewer special cases.
2. **Reuse information readers already have** through consistent conventions and familiar behavior.
3. **Present necessary new information** with precise names, clear structure, and useful comments.

Use these to diagnose a confusing passage: identify the fact a reader lacks, then choose the least burdensome way to make it unnecessary or visible.

## Common obscurity cases

| Case | Missing information | Response |
|---|---|---|
| Event callback | What invokes it, under which conditions, and on which thread | Document invocation conditions in the handler contract. |
| Generic pair used for domain results | Meaning of each position or generic field | Use a named result structure with meaningful members. |
| Dense formatting | Boundaries between parameters, phases, or expressions | Use whitespace to expose structure and group related work. |
| Constructor creates long-lived background work | Why the application continues after the visible entrypoint ends | Make lifecycle behavior explicit in the contract and where readers would otherwise infer incorrectly. |
| Concrete representation affects required behavior | Relevant performance or concurrency properties | Make the necessary guarantees discoverable. |

The book favors a specific container over a `Pair` whose fields have names such as key and value unrelated to their actual domain meaning. The problem is obscured meaning, not the mere presence of two values.

## Types and expectations

Ousterhout also recommends matching a variable's declared and allocated types when using a broad declared type would hide relevant concrete behavior. This is his position in the chapter. For application to a codebase, distinguish a hidden guarantee the reader actually needs from an implementation detail that a sufficient interface contract can legitimately conceal.

The event-driven programming discussion recognizes that indirect control flow may still be useful. Improve its documentation and discoverability rather than treating all callbacks as defects.

## Reader feedback technique

When a reviewer finds code non-obvious, identify what they inferred and what information was missing. Clarify the design, name, or comment so a future reader can make the correct inference without the same conversation.

## Red flag and limits

**Nonobvious Code** signals that quick reading fails to reveal meaning or behavior. A comment can supply unavoidable context, but repeated extensive explanation may indicate an opportunity to simplify the design itself.

## Key takeaways

1. Optimize the reading experience across future maintainers.
2. Use domain names where generic positions hide meaning.
3. Expose control-flow and lifecycle information when it affects understanding.
4. Investigate unexpected interpretations instead of defending writer familiarity.

## Connects to

- [Ch 2](ch02-nature-of-complexity.md): obscurity and cognitive load.
- [Ch 13](ch13-comments-and-abstractions.md): invocation and contract comments.
- [Ch 17](ch17-consistency.md): safe transfer of reader expectations.
