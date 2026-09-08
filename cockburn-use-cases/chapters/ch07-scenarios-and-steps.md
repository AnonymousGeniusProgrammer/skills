# Chapter 7: Scenarios and Steps

## Core Idea

Write scenarios as readable sequences of goal-progressing actions, with a main success scenario as the backbone and explicit alternatives elsewhere.

## Frameworks Introduced

- **Main Success Scenario (MSS)**: the simplest path in which the primary actor's goal succeeds.
  - How: use roughly 3–10 steps from trigger to success guarantee.
- **Goal-oriented step**: one actor achieves a result, passes information, validates a condition, or changes state.
  - How: state intent and result, not UI gestures or implementation detail.
- **Between-step commentary**: short prose only when it clarifies sequence or allowed ordering.

## Anti-patterns

- **UI script**: “click, select, type” without explaining the goal or result.
- **Compound conditional step**: hides several alternatives inside one long sentence.
- **Design leakage**: exposes internal components in a black-box requirement.

## Worked Example

Prefer “Customer identifies requested book; system presents matching offers; customer selects offer; system establishes payment; system confirms order” over a browser navigation script. Put card rejection or no inventory in extensions.

## Key Takeaways

1. One grammatical form makes use cases easier to scan and review.
2. Each step should contribute to satisfying an interest or goal.
3. A scenario can describe permitted unordered input plainly when exact order does not matter.

## Connects To

- **Ch 5**: stable goal level keeps scenarios readable.
- **Ch 8**: move conditional paths into extensions.
