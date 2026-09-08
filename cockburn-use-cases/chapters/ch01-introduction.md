# Chapter 1: Introduction to Use Cases

## Core Idea

Use cases are readable behavioral requirements built around actor goals. Start with a useful sketch, then increase precision only when the project needs it.

## Frameworks Introduced

- **Four precision levels**: actor-goal list, brief/MSS, failure conditions, and failure handling.
  - When to use: planning requirements work without exhausting the team on premature detail.
  - How: finish and review each layer before investing in the next.
- **Usage narrative**: a concrete, situated story that helps people discover and visualize behavior.
  - When to use: unfamiliar domains or early elicitation.
  - How: describe one particular person, motive, context, interaction, and desired outcome briefly.

## Key Concepts

- **Use case**: all scenarios for one primary actor's goal.
- **Functional requirements**: the behavioral part of requirements, not the whole specification.
- **Precision**: how much is said; it is distinct from accuracy.
- **Failure condition**: a detectable way the expected path can be interrupted or rejected.

## Mental Models

- Work breadth-first when agreement about scope and priorities matters more than detail.
- Treat use cases as a hub connecting other requirements and project information, not a container for everything.

## Worked Example

Before specifying an ATM withdrawal, sketch a narrative: a named customer needs a fast cash amount on the way to work, receives the correct notes and receipt, and leaves without exposing the account. Generalize that story into a user-goal use case; then add alternative conditions such as wrong PIN, insufficient funds, unavailable network, or unavailable cash.

## Key Takeaways

1. Readability is a baseline quality requirement.
2. Stabilize goals before elaborating failures and handling.
3. Failure brainstorming is where hidden requirements often appear.
4. Keep non-behavioral requirements in linked artifacts.

## Connects To

- **Ch 3**: use actor-goal lists to establish scope.
- **Ch 7–8**: turn an MSS into scenario extensions.
