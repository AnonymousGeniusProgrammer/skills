# Chapter 6: Preconditions, Triggers, Guarantees

## Core Idea

The header establishes the scenario contract: what must already hold, what starts the interaction, and what stakeholders can rely on at every exit and at success.

## Frameworks Introduced

- **Precondition**: state expected to be true before the use case begins.
- **Trigger**: an event that starts the use case, including a time event.
- **Minimal guarantee**: protection delivered regardless of success or failure.
- **Success guarantee**: resulting state promised when the goal succeeds.

## How to Apply

State only conditions the use case is entitled to assume; if the system must establish or verify something, make it behavior in the scenario. Write guarantees in stakeholder terms, then ensure the scenario and extensions deliver them.

## Key Takeaways

1. Preconditions keep the use case from silently assuming unmodeled work.
2. Minimal guarantees make failures safe and accountable.
3. Success guarantees clarify when to stop writing.

## Connects To

- **Ch 2**: guarantees translate stakeholder interests into contract terms.
- **Ch 8**: extensions must preserve minimal guarantees.
