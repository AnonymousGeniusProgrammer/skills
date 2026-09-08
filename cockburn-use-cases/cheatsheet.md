# Use-Case Decision Guide

| If you observe… | Prefer… | Because… |
|---|---|---|
| The reader cannot tell what is being designed | Name the design scope | Scope determines which actors are external and what behavior belongs in the use case. |
| A title is “click button” or “validate field” | Move upward with “why?” | It is probably below user-goal level and should be a step, not a standalone use case. |
| A title spans days, months, or multiple user goals | Use a summary use case | It provides context and a table of contents for sea-level goals. |
| A visible flow omits logging, validation, or legal obligations | Audit stakeholder interests | Off-stage stakeholders create real behavioral requirements. |
| The same intent can happen via web, phone, or kiosk | Use a technology/data variation | The behavior is unchanged; only its channel or representation differs. |
| A condition changes behavior | Use an extension | It captures an alternate or failure scenario tied to a condition. |
| A step is complex or repeatedly needed | Consider a sub-use case | The caller can depend on success/failure while details remain separately readable. |
| The team has not agreed on what to build | Return to the actor-goal list | It is the cheapest useful precision level for scope and prioritization. |

## Per-use-case review

1. Is the scope named and consistently black-box or intentionally white-box?
2. Is the primary actor outside the SuD and pursuing a named goal?
3. Is the level summary, user-goal, or subfunction—and is that level useful?
4. Are stakeholder interests represented by actions, validations, or guarantees?
5. Does the MSS show 3–10 goal-progressing steps without UI detail?
6. Have meaningful failure conditions been listed before their handling was designed?
7. Does every extension return, finish in a distinct success, or fail cleanly?
8. Are non-behavioral requirements linked rather than forced into the prose?

## Defaults

- Default to a **system black box** and **user-goal level**.
- Default to **active verb + object** titles: “Register arrival”, not “Arrival registration”.
- Default to **simple action sentences**, not prose paragraphs or embedded conditionals.
- Default to **briefs** when learning or estimating, and fully dressed use cases when precision is worth the cost.
