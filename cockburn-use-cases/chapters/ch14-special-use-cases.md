# Chapter 14: Two Special Use Cases

## Core Idea

CRUD and parameterized use cases are shortcuts for recurring patterns, but should not hide genuinely different stakeholder goals or outcomes.

## Frameworks Introduced

- **CRUD use case**: create, retrieve, update, or delete information when those operations are meaningful actor goals.
- **Parameterized use case**: one generalized use case whose variation is defined by a controlled parameter.

## Key Takeaways

1. Use a combined pattern only when the behavior and interests truly align.
2. Split cases when different operations have different actors, policies, extensions, or guarantees.
3. Keep a user-goal lens; technical data operations alone may be too low-level.

## Connects To

- **Ch 5**: test the goal level before extracting a CRUD case.
- **Ch 19**: repair common misuses.
