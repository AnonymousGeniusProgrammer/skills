# Chapter 1: Introduction (It's All About Complexity)

Source: first edition (2018), PDF pages 13-17. Reference notes.

## Core idea

Treat design as a continuous effort to keep a system understandable and modifiable. This introductory chapter establishes the evaluation lens; it does not supply a mechanical recipe for good design.

## Decision guidance

- **Eliminate complexity** when a special case, inconsistency, or unnecessary mechanism can disappear entirely.
- **Encapsulate complexity** when necessary details can live behind an interface that other developers can use without learning those details.
- **Incremental design**: implement a manageable portion, learn from it, revise the design, then extend it. Plan to revisit decisions as evidence changes.
- **Red flags**: treat a symptom as a reason to explore another design. Compare the alternatives by the complexity they expose, rather than checking compliance with a slogan.

## Key concepts and mental models

**Modular design** divides software into units whose implementations can be understood largely independently. **Continuous redesign** uses experience to improve boundaries throughout the software's life. **Reader perspective** makes another developer's understanding an important test of simplicity.

Use code review as design practice: identify a concrete source of complexity in someone else's code, propose a different organization, and explain what knowledge a future reader would no longer need.

## Anti-patterns and limits

- Freezing the entire design before implementation leaves later discoveries to be patched around.
- Treating each increment solely as added functionality misses opportunities to improve the underlying abstractions.
- Applying any principle at its extreme can create a worse design. Balance competing considerations; investigate red flags rather than treating them as automatic defects.

## Key takeaways

1. Ask what a developer must understand to make the next change.
2. Prefer removing unnecessary complexity; contain the necessary remainder.
3. Use concrete alternatives and review feedback to improve judgment.

## Connects to

- [Ch 2](ch02-nature-of-complexity.md): symptoms and causes to look for.
- [Ch 3](ch03-working-code-isnt-enough.md): investment in continuous improvement.
- [Ch 11](ch11-design-it-twice.md): comparing alternative designs.
