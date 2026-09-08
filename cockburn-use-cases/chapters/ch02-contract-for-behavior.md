# Chapter 2: The Use Case as a Contract for Behavior

## Core Idea

A use case is a behavioral contract: it serves a primary actor's goal while protecting all stakeholder interests across success and failure scenarios.

## Frameworks Introduced

- **Actors & Goals model**: organize behavior around actors pursuing goals.
  - When to use: writing individual steps and identifying the primary actor.
  - How: state goal-progressing interactions in simple sentences.
- **Stakeholders & Interests model**: decide which behavior belongs in the contract.
  - When to use: reviewing for omitted validations, audit trails, or state changes.
  - How: list interests and ensure every important one is protected by the use case.
- **Striped trousers model**: one goal binds multiple scenario stripes, some ending in success and some failure.
  - When to use: distinguishing a use case from a single happy-path sequence.

## Key Concepts

- **Scenario**: one path under a particular set of conditions.
- **Main success scenario**: the first, normal success stripe.
- **Goal failure**: a valid end state that must be described when relevant.
- **Partial ordering**: steps need not imply an arbitrary strict order when the domain does not require one.

## Worked Example

“Place order” may call “Establish credit.” The outer scenario should not reproduce the credit process: it only needs the outcome—credit established or not. A rejected credit result can lead to an alternative payment route or to failure to place the order.

## Key Takeaways

1. A scenario is a path; a use case collects the paths for one goal.
2. Do not omit internal checks merely because a visible actor does not request them.
3. Model failure as behavior, not an afterthought.

## Connects To

- **Ch 4**: identify stakeholders and actors.
- **Ch 7–8**: write scenarios and extensions.
