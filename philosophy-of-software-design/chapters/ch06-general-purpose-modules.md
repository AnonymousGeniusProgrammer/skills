# Chapter 6: General-Purpose Modules are Deeper

Source: first edition (2018), PDF pages 50-55. Reference notes.

## Core idea

Build the functionality currently needed behind a somewhat general-purpose interface. The immediate benefit is a simpler separation of responsibilities; possible future reuse is an additional benefit.

## Framework: somewhat general-purpose

Let current needs determine what the module implements, while expressing its interface through concepts that support several uses. Keep caller-specific policy in the caller. Generality is excessive when today's use requires substantial glue code or an elaborate framework.

Use three questions to find the balance:

| Question | Evidence of a useful abstraction | Warning |
|---|---|---|
| What simple interface covers current needs? | Several special operations become a few reusable ones. | Fewer methods achieved by adding many flags or parameters. |
| In how many situations is an operation useful? | The same operation serves different current tasks. | Every operation corresponds to one button or one call site. |
| Is current usage easy? | Callers express intent with a small amount of direct code. | Callers must rebuild range handling or other basic mechanism. |

These are judgment questions, not method-count targets or a requirement to prove multiple existing callers.

## Source API comparison

The editor's text model originally mirrored UI commands: backspace, forward delete, and delete selection. That made the model depend on cursors, selections, and keyboard policy. A text-oriented interface instead supports insertion, deletion of ranges, and movement between positions.

Adapted API sketch:

```java
void insert(Position position, String newText);
void delete(Position start, Position end);  // [start, end)
Position changePosition(Position position, int numChars);
```

The UI determines which range corresponds to a key; the text module handles text and line boundaries. The range endpoint convention is part of the contract, even though the type signatures cannot express it. This sketch does not specify every boundary condition or a complete implementation.

## Mental models

- **Mechanism versus particular use**: ask who needs to know the policy. A keyboard command belongs with the UI even when a text primitive implements it.
- **Generality through fewer concepts**: use a reusable operation to remove specialized names and cross-module knowledge.
- **False abstraction**: concealing backspace behavior from the UI is unhelpful when the UI developer needs that behavior to be explicit.

## Anti-patterns and limits

- Speculating about future features and implementing capabilities no current requirement justifies.
- Mirroring every high-level feature as a new low-level method.
- Going so low-level that callers must loop over single-character operations for every insertion or deletion. The interface can be general and short while still omitting useful functionality.
- Equating a general-purpose module with one that combines unrelated mechanisms.

## Key takeaways

1. Seek generality in the interface, not speculative functionality.
2. Make caller-specific policy explicit at its natural level.
3. Preserve convenience for current tasks while removing unnecessary specialization.

## Connects to

- [Ch 5](ch05-information-hiding.md): reduce knowledge shared with callers.
- [Ch 9](ch09-together-or-apart.md): place mechanism and specialization.
- [Ch 11](ch11-design-it-twice.md): compare line, character, and range interfaces.
