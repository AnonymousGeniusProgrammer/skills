# Chapter 11: Design it Twice

Source: first edition (2018), PDF pages 99-101. Reference notes.

## Core idea

For an important design decision, compare meaningfully different possibilities before committing. Comparison exposes weaknesses and often suggests a better combination than the first idea.

## Technique

1. Sketch at least two structurally different approaches. For an interface, a few central operations and representative calls may be enough.
2. Trace current tasks through each candidate.
3. Compare caller effort, simplicity of the contract, generality, and implementation efficiency.
4. Use the weaknesses to refine a candidate, combine strengths, or develop another option.
5. State the resulting choice and the tradeoff it accepts.

Use sketches proportional to the decision. The principle does not demand two complete implementations. Even a candidate you expect to reject can clarify why another design is better.

## Source comparison

| Text interface | Caller burden | Design implication |
|---|---|---|
| Whole lines | Callers split and join lines for arbitrary edits. | Internal storage leaks into higher-level operations. |
| Individual characters | Callers loop for insertion or deletion of larger regions. | Very small primitives omit needed capability and may be inefficient. |
| Arbitrary text ranges | Callers specify the affected range. | The text abstraction owns the mechanics needed by ordinary tasks. |

The lesson is to compare concrete usage, not assume the fewest or lowest-level methods produce the simplest interface.

## Mental models and limits

Separate **interface design** from **implementation design**. Ease of use dominates the former; internal simplicity and performance matter strongly to the latter. A range-based text API can be compared independently from storage choices such as lines, blocks, or a gap buffer.

Apply the technique to major decomposition and UI decisions as well. The source's suggestion that a small class design might take an hour or two is illustrative, not a required time budget.

## Anti-patterns

- Treating the first workable idea as the only serious option.
- Comparing cosmetic variations that retain the same underlying weakness.
- Implementing every alternative fully before learning from a simpler sketch.
- Choosing through abstract preference without tracing actual caller needs.

## Key takeaways

1. Make alternatives different enough to teach you something.
2. Compare the obligations they impose on callers.
3. Let weaknesses guide the next design.

## Connects to

- [Ch 6](ch06-general-purpose-modules.md): range-based text abstraction.
- [Ch 15](ch15-comments-first.md): interface comments as inexpensive design artifacts.
- [Ch 20](ch20-performance.md): compare and measure performance-sensitive designs.
