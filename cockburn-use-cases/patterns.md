# Patterns

## Breadth-First Elaboration
**When to use**: Starting a use-case effort or recovering from premature detail.
**How**: Build the actor-goal list; draft briefs/MSSs; list failure conditions; then specify handling.
**Trade-offs**: Defers detail, but catches scope and priority errors before expensive elaboration (Ch 1, Ch 3).

## Stakeholder-Interest Audit
**When to use**: A scenario seems complete from the user's perspective but may omit compliance, audit, or ownership needs.
**How**: List every stakeholder and interest; trace each to an interaction, validation, state change, or explicit linked requirement.
**Trade-offs**: Adds analysis effort; prevents late change requests caused by silent stakeholders (Ch 2, Ch 4).

## Sea-Level Test
**When to use**: A use case feels too broad, too technical, or unhelpfully fragmented.
**How**: Ask whether one primary actor can complete meaningful work in one sitting and leave satisfied. Ask “why?” to move up and “how?” to move down.
**Trade-offs**: A user-goal focus improves readability; some reusable subfunctions still warrant extraction (Ch 5).

## Extension-First Failure Discovery
**When to use**: After a credible main success scenario exists.
**How**: For each step, enumerate detectable failure or alternative conditions before designing their handling. Route each extension to recovery, another success exit, or failure.
**Trade-offs**: Requires careful business decisions; exposes requirements early (Ch 1, Ch 8).

## Four-Scope Artifacts
**When to use**: Defining system boundaries.
**How**: Maintain a vision statement, scope/context drawing, in/out list, and actor-goal list together; revise all when one changes.
**Trade-offs**: More coordination artifacts; prevents different readers assuming different system boundaries (Ch 3).

## Sub-Use-Case Extraction
**When to use**: A scenario step is complex, reused, or harms readability.
**How**: Replace the step with an active goal name and specify the sub-use case separately. Let the caller depend only on its success or failure.
**Trade-offs**: Extraction reduces local detail but adds navigation and maintenance cost; do not extract trivial steps (Ch 10).

## Fully Dressed Use Case
**When to use**: A high-risk or precise behavioral requirement needs a consistent reviewable form.
**How**: Use name, context, scope, level, primary actor, stakeholders/interests, preconditions, minimal/success guarantees, trigger, MSS, extensions, variations, and related information.
**Trade-offs**: Strong completeness aid; more ceremony than a brief (Ch 11).
