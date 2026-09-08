# Chapter 10: Linking Use Cases

## Core Idea

Link use cases only when a named goal needs independent expansion, reuse, or separate ownership; otherwise retain the step in its caller.

## Frameworks Introduced

- **Sub-use case**: a named action step with its own success/failure behavior.
  - When to use: the goal is complex or reused enough to justify navigation cost.
  - How: reference it as an active goal; the caller depends only on its result.
- **Extension use case**: a separately maintained conditional activity that interrupts a base activity without making the base name every possible interruption.
  - When to use: independently developed optional behavior can interrupt a continuing base activity.
  - How: state its trigger as a condition in the base use case.

## Key Takeaways

1. Extraction improves readability but is not free.
2. A caller should not know the internal scenario stripes of a sub-use case.
3. Use extension use cases sparingly for genuine interruptible base activities.

## Connects To

- **Ch 5**: avoid extracting too-low subfunctions.
- **Ch 8**: extension use cases use the same condition-and-outcome logic.
