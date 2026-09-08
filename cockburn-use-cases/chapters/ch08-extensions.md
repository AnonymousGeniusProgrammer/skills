# Chapter 8: Extensions

## Core Idea

Extensions capture all meaningful alternate and failure paths without duplicating the main success scenario.

## Frameworks Introduced

- **Extension condition**: a condition at or after a main step that changes behavior.
  - How: number from the triggering step and branch the detail beneath it.
- **Three extension exits**: return to the MSS, reach a separate success outcome, or end in failure.
  - How: state the chosen exit explicitly.
- **Failure-first discovery**: enumerate conditions before designing all responses.
  - How: ask what the SuD can detect and what it must handle at every MSS step.

## Anti-patterns

- Writing alternate paths as complete duplicated scenarios.
- Designing the first discovered recovery before searching for the rest of the failure conditions.
- Leaving an extension with no stated outcome.

## Worked Example

At “system validates payment,” an invalid entry can return for correction; a declined account can lead to another payment method; a network outage may preserve an audit record and end in failure. Each protects a different stakeholder interest.

## Key Takeaways

1. Failure handling is a rich source of hidden rules and actors.
2. Extensions keep the main path readable.
3. Every branch needs a clear destination.

## Connects To

- **Ch 1**: failure conditions precede costly detail.
- **Ch 6**: all exits must satisfy minimal guarantees.
