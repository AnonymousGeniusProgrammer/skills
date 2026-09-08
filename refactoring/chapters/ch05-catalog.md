# Chapter 5: Introducing the Catalog

Source: printed pp. 101-104; PDF pp. 123-126. This short chapter explains how to use the catalog; it introduces no substantial application example.

## Core idea

Treat a named refactoring as a reusable transformation with motivation, preconditions, and mechanics. A before/after sketch alone does not explain when it is appropriate or how to reach the result safely.

## Read the five parts with a purpose

| Catalog part | Use it to answer |
|---|---|
| Name, with aliases where supplied | What transformation are we discussing? |
| Sketch | Is this the operation I remember? |
| Motivation | Why do it here, and when might it be inappropriate? |
| Mechanics | What sequence and special cases should I check? |
| Examples | Why are the steps ordered this way, and how do dependencies change? |

The chapter describes the name, sketch, motivation, mechanics, and examples as the five parts; aliases accompany names. Graphics are memory aids rather than independent explanations.

## Apply the catalog

1. Start with the change obstacle, not a favorite transformation.
2. Find the catalog entry through its name or the smell map.
3. Read motivation before committing to the shape of the result.
4. Inspect variables, dispatch, callers, mutation, and other entry-specific preconditions.
5. Carry out a small step and verify behavior. If an attempted step fails, recover and reduce its size.
6. Stop at a useful working state. Compose additional refactorings only when they address the remaining obstacle.

The mechanics present a safe default sequence, not the sole legal implementation. Experienced users may combine steps where confidence is justified. The trickier the situation, the smaller the feedback interval should become.

## Catalog judgment

- Every refactoring has a conceptual inverse, but this catalog does not separately list all inverses. Opposite moves are useful under different constraints.
- Examples isolate one transformation and often leave other design problems in place. Do not treat their final state as a complete architecture recommendation.
- This is a selected catalog of useful operations, not a claim to enumerate every possible restructuring.
- Retain the second edition's names. For example, Extract Method is an alias of **Extract Function**, and Rename Function is covered by **Change Function Declaration**.

## Worked lookup (skill illustration)

A large expression is hard to understand. **Extract Variable** might name a local concept; **Extract Function** might be better if the concept has broader use. Read motivation and scope requirements before deciding. If evaluation has side effects, changing when or how often it runs may prevent a seemingly obvious extraction.

This lookup is a new illustration of catalog use, not a worked example claimed from the chapter.

## Takeaways and connections

Use names for precise communication, mechanics for safe execution, and examples for understanding. See [Ch 3](ch03-code-smells.md) to choose an entry; [Ch 6](ch06-first-refactorings.md) through [Ch 12](ch12-inheritance.md) contain all 61 named entries in this PDF's catalog.
