# Chapter 15: Write The Comments First

Source: first edition (2018), PDF pages 137-141. Subtitle: Use Comments As Part Of The Design Process. Reference notes.

## Core idea

Use comments to design and evaluate an abstraction before implementation detail distracts from its contract. Difficulty writing a clear, complete description is evidence about the design itself.

## Comments-first technique

1. Describe the overall abstraction of a new class or module.
2. Sketch the main public signatures and their interface comments, leaving bodies for later.
3. Refine the contracts until responsibilities and ordinary usage are coherent.
4. Declare important state and describe what it represents.
5. Implement the operations, adding comments for non-obvious internal reasoning.
6. As new methods and state emerge, describe their contracts or meanings when introducing them. Revise descriptions as implementation and testing reveal better decisions.

The result is a design artifact that evolves with the implementation, without an accumulating documentation backlog. The sequence is the author's preferred technique, not a requirement to freeze every detail before coding.

## Mental model: a cheap abstraction test

Compare the interface explanation with the implementation. If the explanation must enumerate all major internal steps, the method may be shallow. If it can express a useful capability simply while retaining all necessary constraints, the abstraction is more promising.

**Hard to Describe** is the red flag when a method or variable resists a simple, complete account. Reconsider responsibilities, state decomposition, sequencing, special cases, or naming rather than merely shortening the prose.

Completeness matters. An undocumented precondition or a cryptic one-liner can make a complicated contract look simple; that does not demonstrate depth.

## Why the timing matters

Design rationale is freshest while choosing the interface. Writing after implementation encourages paraphrasing the code and forgetting the decisions behind it. Early comments also reveal awkwardness before substantial implementation depends on it.

The book's estimates of typing and comment-writing time are informal arguments about cost, not measured universal percentages.

## Anti-patterns

- Waiting until code is stable before documenting the contract.
- Treating initial comments as immutable specifications despite new evidence.
- Erasing necessary details to make documentation look short.
- Describing every internal step in an interface comment to compensate for a weak abstraction.

## Key takeaways

1. Write a usable contract before its method body.
2. Use explanation difficulty to investigate design complexity.
3. Evolve comments and code together until they agree.

## Connects to

- [Ch 11](ch11-design-it-twice.md): compare candidate contracts.
- [Ch 13](ch13-comments-and-abstractions.md): what belongs in each comment.
- [Ch 16](ch16-modifying-existing-code.md): maintain the contract during later changes.
