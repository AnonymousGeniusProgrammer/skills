# Chapter 14: Choosing Names

Source: first edition (2018), PDF pages 129-136. Reference notes.

## Core idea

A name is a small abstraction: it should create an accurate image of the thing it denotes. Precision and consistency let readers make correct assumptions without repeatedly examining implementations.

## Naming technique

1. State what the entity represents and what similar concepts it must be distinguished from.
2. Consider the name in isolation, away from its declaration and documentation.
3. Choose words that convey the most important distinction.
4. Check existing uses of those words for the same meaning.
5. If no short, intuitive name fits, revisit whether the entity has a coherent purpose.

## Quick distinctions

| Naming situation | Better direction | Why |
|---|---|---|
| Logical file block versus physical disk block | `fileBlock` versus `diskBlock` | Prevents substituting different coordinate systems. |
| Character position versus screen coordinates | `charIndex`, `lineIndex` | Identifies the text abstraction instead of generic `x`, `y`. |
| Boolean state | A predicate such as `cursorVisible` | Makes the meaning of true inferable. |
| Sentinel | Name the state it means | A word meaning only "special value" leaves the contract obscure. |
| Generic range operation | `range`, rather than `selection` | Avoids implying a UI restriction the operation does not have. |
| Source and destination of one concept | Shared term with role prefixes | Preserves the concept while distinguishing roles. |

The source's filesystem bug used `block` for both logical and physical block numbers. Readers repeatedly assumed the intended meaning and missed the incorrect substitution. The lesson is to make a dangerous distinction visible wherever the values are used.

## Consistency has three parts

- Use the chosen name for the same purpose throughout the relevant scope.
- Reserve it for that purpose.
- Keep the purpose narrow enough that instances have the same meaning and expected behavior.

This allows knowledge from one location to transfer safely to another. Superficial consistency that gives different things one name has the opposite effect.

## Scope and precision

Short names such as `i` can be clear when their complete usage is visible in a small loop. As the distance between declaration and use grows, descriptive names become more valuable. A `result` variable can be reasonable when it really holds the documented return value; otherwise it conceals the computed concept.

Avoid both vague names and overspecific ones. Precision means correctly describing the abstraction, not including every implementation detail or arbitrarily maximizing length.

## Red flags

**Vague Name**: many different meanings plausibly fit. **Hard to Pick Name**: the entity may combine concepts that need separate representations, or the operation may lack a clear abstraction.

The chapter contrasts Ousterhout's preferences with short-name conventions in Go. Preserve that attribution; actual reader comprehension and the project's conventions matter more than universal naming-length rules.

## Key takeaways

1. Encode the distinction most likely to prevent misuse.
2. Use predicate names for booleans and meaning-based names for sentinels.
3. Make common vocabulary both consistent and unambiguous.
4. Treat persistent naming difficulty as a possible design problem.

## Connects to

- [Ch 13](ch13-comments-and-abstractions.md): details names cannot carry.
- [Ch 17](ch17-consistency.md): conventions preserve meaning.
- [Ch 18](ch18-obvious-code.md): correct first impressions.
