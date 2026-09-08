# Chapter 12: Why Write Comments? The Four Excuses

Source: first edition (2018), PDF pages 102-106. Reference notes.

## Core idea

Comments preserve design knowledge that code does not express and complete the abstraction offered to readers. This chapter motivates documentation; the writing technique is in Chapter 13.

## The four excuses and their implications

| Excuse considered in the chapter | Ousterhout's response as a design rule |
|---|---|
| Good code documents itself. | Use clear code and names, then document behavior, assumptions, and rationale still missing from them. |
| There is no time. | Include documentation in continuous design investment; perpetual postponement produces permanent gaps. |
| Comments become stale. | Keep them close to their subjects, reduce duplication, and update them during review. |
| Existing comments are worthless. | Improve the information they carry instead of repeating obvious code. |

## Comments as part of abstraction

A method signature may give two positions named `start` and `end`, yet leave open whether the end is included and what happens when the interval is empty or reversed. Forcing callers to reconstruct those rules from implementation exposes the complexity the abstraction was supposed to hide.

Provide enough informal contract information that ordinary users can work without reading the implementation. Use implementation comments for reasoning a maintainer needs to understand the mechanism. Good names reduce documentation needs but cannot encode every useful relationship.

## Mental models

- **Designer knowledge**: preserve the facts and reasoning a future maintainer would otherwise need to rediscover or guess.
- **Cognitive load**: summarize the relevant contract so a reader can ignore implementation details.
- **Unknown unknowns**: make non-obvious obligations and relationships discoverable.

Documentation can explain a necessary dependency and remove obscurity. It does not, by itself, remove structural change amplification. Simplify a bad abstraction where possible rather than surrounding it with explanations.

## Anti-patterns and limits

- Requiring implementation reading as the normal way to discover an interface.
- Comment volume mistaken for information value.
- Saving essential design reasoning only for the original author's memory.
- Treating the chapter's estimated documentation costs as measured productivity guarantees.

## Key takeaways

1. Document what readers need but cannot infer reliably from declarations.
2. Preserve why a design works, not a narration of every statement.
3. Maintain comments as part of maintaining the design.

## Connects to

- [Ch 13](ch13-comments-and-abstractions.md): precision, intuition, and interface documentation.
- [Ch 15](ch15-comments-first.md): write contracts while designing.
- [Ch 16](ch16-modifying-existing-code.md): keep documentation accurate.
