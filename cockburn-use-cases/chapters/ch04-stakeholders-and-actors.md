# Chapter 4: Stakeholders & Actors

## Core Idea

Separate actors, who have behavior, from stakeholders, who have an interest in correct behavior. Both are necessary for a complete use case.

## Frameworks Introduced

- **Stakeholder-interest listing**: surface visible and off-stage concerns.
  - When to use: drafting a full use case or reviewing late change requests.
  - How: list each stakeholder and the state, validation, protection, or information they need.
- **Actor classification**: primary, supporting, SuD, and internal actors.
  - When to use: clarifying interaction direction and system boundaries.
  - How: identify who calls the SuD, who supplies it services, and whether internals should remain hidden.

## Key Concepts

- **Primary actor**: calls upon the SuD to deliver a service.
- **Supporting actor**: externally provides a service to the SuD.
- **Off-stage stakeholder**: cares about outcome but may never appear in a scenario step.
- **White-box use case**: intentionally reveals the SuD's internal actors.

## Worked Example

An ATM customer requests cash, but the account owner, bank, operations team, and regulators may require balance validation, transaction logging, a durable record, and a safe outcome if the network fails. These are contractual obligations even when only the customer touches the screen.

## Key Takeaways

1. Primary actors are stakeholders, but not all stakeholders are primary actors.
2. Missing interests often become expensive post-release changes.
3. Default to black-box system use cases for requirements.

## Connects To

- **Ch 2**: stakeholder interests define the behavioral contract.
- **Ch 6**: guarantees express stakeholder protection.
