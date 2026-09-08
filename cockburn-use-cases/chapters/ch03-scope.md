# Chapter 3: Scope

## Core Idea

Every use case needs an explicit design scope—the system, subsystem, or organization treated as the system under discussion—plus a functional scope expressed by its actor goals.

## Frameworks Introduced

- **In/out list**: record boundary decisions explicitly.
  - When to use: competing assumptions about what the team is building.
  - How: note candidates and their inclusion/exclusion decision, revisiting as context changes.
- **Actor-goal list**: list primary actors and system-supported goals.
  - When to use: early scoping, estimation, prioritization, or release planning.
  - How: brainstorm actors across the system lifetime, then exhaust their goals.
- **Four scope artifacts**: vision, scope drawing, in/out list, actor-goal list.
  - When to use: defining an evolving product boundary.
  - How: maintain them together because a change to one usually changes the others.

## Key Concepts

- **Functional scope**: what the system is meant to do, expressed by actor goals.
- **Design scope**: the named black box being designed.
- **Outermost use case**: a summary use case at the widest scope where its primary actor remains outside.
- **Use case brief**: a 2–6 sentence low-precision description for communication and estimation.

## Worked Example

If an invoicing application sends print jobs to an established printing system, the printing system is a supporting actor outside scope. The use case describes preparing and sending a job plus handling the response—not designing the printer internals.

## Key Takeaways

1. Incorrect scope can radically distort cost and design.
2. Name scope on every use case; do not assume it is obvious.
3. Actor-goal lists are valuable before detailed prose exists.

## Connects To

- **Ch 4**: scope determines who is an external actor.
- **Ch 5**: outermost use cases are normally summary level.
