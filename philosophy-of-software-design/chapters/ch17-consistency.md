# Chapter 17: Consistency

Source: first edition (2018), PDF pages 148-151. Reference notes.

## Core idea

Make similar things recognizable through similar representations and behavior, while preserving distinctions between different things. Consistency reduces relearning and makes assumptions safer.

## Where consistency helps

- Names that keep one meaning across the system.
- Coding and documentation conventions that make structure predictable.
- Multiple implementations that honor a common interface.
- Familiar patterns applied to problems they actually fit.
- **Invariants** that keep a property true and remove special cases, such as a chosen representation rule for stored text lines.

The aim is reliable transfer of knowledge. Visual similarity is harmful when it suggests behavioral equivalence that is absent.

## Establish and maintain conventions

1. Find existing examples before making a new local design choice.
2. Record shared conventions where developers will discover them; keep local invariants near their code.
3. Use automation for conventions that can be checked mechanically and review for those requiring judgment.
4. Help newcomers recognize the established patterns.

The source's line-ending example uses a pre-commit check to prevent whole-file formatting churn. It illustrates enforcement of an agreed convention; it is not a requirement for a particular hook or line-ending setting in every project.

## Changing a convention

Before introducing a different approach, consider whether there is significant new information and whether the improvement is large enough to justify updating old uses coherently. A personal preference alone rarely outweighs the cost of inconsistency.

Ousterhout describes agreement on a coordinated change, followed by a consistent result. Treat that as guidance for convention changes; applying the skill does not authorize a repository-wide migration.

## Mental models and limits

- **Reusable understanding**: readers should learn a concept once and apply it safely elsewhere.
- **Recognizable difference**: unlike concepts should remain distinguishable in names, representations, and patterns.
- **Invariants instead of branches**: a stable representation can make operations simpler across the system.

## Anti-patterns

- Introducing an isolated style improvement that creates a second convention.
- Forcing a new problem into an existing pattern solely for uniformity.
- Using one generic name for values with different semantics.
- Recording conventions without making them discoverable or checking adherence.

## Key takeaways

1. Follow established patterns when their semantics fit.
2. Distinguish genuinely different cases.
3. Make conventions visible and checkable.

## Connects to

- [Ch 14](ch14-choosing-names.md): consistent vocabulary.
- [Ch 18](ch18-obvious-code.md): reader expectations.
- [Ch 19](ch19-software-trends.md): evaluate pattern fit.
