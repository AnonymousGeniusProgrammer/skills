---
name: cockburn-use-cases
description: "Knowledge base from Writing Effective Use Cases by Alistair Cockburn. Use when eliciting, scoping, writing, reviewing, or improving software and business-process use cases; applying actor-goal, stakeholder-interest, goal-level, scenario, extension, and guarantee techniques; or deciding what belongs outside a use case."
---

<!-- argument-hint: [topic, framework name, or chapter number] -->

# Writing Effective Use Cases
**Author**: Alistair Cockburn | **Chapters**: 22 | **Generated**: 2026-08-13

## How to Use This Skill

- **Without arguments** — apply the core method below.
- **With a topic** — load the relevant chapter for `scope`, `extensions`, `stakeholders`, or another indexed topic.
- **With a chapter** — ask for `ch05` to load that chapter.
- **For quick decisions** — consult [cheatsheet.md](cheatsheet.md).

## Core Frameworks & Mental Models

### Use case = behavioral contract

Treat a use case as the contract by which the system under discussion (SuD) serves a primary actor while protecting every stakeholder interest. It contains the scenarios for one actor goal: paths that succeed and paths that fail. Include an interaction, validation, or internal state change only when it advances a goal or protects an interest.

Do not treat a use case as the complete requirements document. Keep business rules, data definitions, interfaces, performance, organizational constraints, and other non-behavioral requirements in their own linked artifacts.

### Actors & Goals; Stakeholders & Interests

Use **Actors & Goals** to write readable action steps: an actor achieves a result or passes information. Use **Stakeholders & Interests** to find omissions: list everyone with a vested interest, including off-stage stakeholders such as owners, regulators, and auditors. Their interests commonly require validations, records, and state changes that no visible user asks for.

The primary actor calls on the SuD for a service. A supporting actor is external and supplies a service to the SuD. The SuD is normally a black box; mention internal components only for an intentional white-box use case.

### Scope and level are separate decisions

Name the design scope on every use case: the enterprise, system, or subsystem being treated as the black box. Then name the goal level:

- **Summary** — a long-running context or lifecycle that joins user goals.
- **User-goal (sea level)** — one primary actor can complete meaningful work in one sitting and leave satisfied.
- **Subfunction** — a supporting goal needed by a user goal; extract it only when reuse or readability justifies it.

When a use case seems wrong, ask *why is the actor doing this?* to move upward, and *how is that achieved?* to move downward. Prefer user-goal level. A list of user goals is the most compact expression of what a system is for.

### Work breadth-first, then add precision

Avoid detailing one use case before the set is right. Work through four precision levels:

1. Build and review the actor-goal list.
2. Draft a brief or main success scenario for selected goals.
3. Brainstorm all detectable failure conditions.
4. Specify the handling for those failures.

This sequence exposes scope and priority errors before high-detail work becomes waste. Maintain the connected scope artifacts: vision statement, scope/context drawing, in/out list, and actor-goal list.

### Main success scenario and extensions

Write a main success scenario of roughly 3–10 goal-oriented steps. Each step should show an actor reaching a result, passing information, validating a condition, or changing state; avoid UI choreography and embedded conditional prose. Then enumerate extension conditions exhaustively, and give each extension a route back to the main flow, a distinct success exit, or a failure exit.

Use **extensions** when behavior changes under a condition. Use **technology and data variations** when the same behavior is achieved through different channels or data representations. Use a **sub-use case** to name a complex reusable step; the calling use case cares only whether that subgoal succeeds or fails.

### Definition of done

A use case is done for its chosen purpose when the scope, level, actor goal, stakeholder interests, preconditions, guarantees, main success scenario, and meaningful extensions are clear enough for the intended readers. Match ceremony to risk and tolerance; readable, modestly detailed use cases are often more valuable than overformal artifacts that no one reads.

## Chapter Index

| # | Title | Key Frameworks |
|---|---|---|
| [ch01](chapters/ch01-introduction.md) | Introduction to Use Cases | precision, usage narratives, requirements boundary |
| [ch02](chapters/ch02-contract-for-behavior.md) | The Use Case as a Contract for Behavior | scenarios, stakeholder interests |
| [ch03](chapters/ch03-scope.md) | Scope | design scope, actor-goal list |
| [ch04](chapters/ch04-stakeholders-and-actors.md) | Stakeholders & Actors | primary/supporting/off-stage actors |
| [ch05](chapters/ch05-goal-levels.md) | Three Named Goal Levels | summary, user-goal, subfunction |
| [ch06](chapters/ch06-preconditions-triggers-guarantees.md) | Preconditions, Triggers, Guarantees | start/end contract |
| [ch07](chapters/ch07-scenarios-and-steps.md) | Scenarios and Steps | main success scenario, step style |
| [ch08](chapters/ch08-extensions.md) | Extensions | alternative paths, failure handling |
| [ch09](chapters/ch09-technology-data-variations.md) | Technology & Data Variations | same behavior, different mechanism |
| [ch10](chapters/ch10-linking-use-cases.md) | Linking Use Cases | sub-use cases, extension use cases |
| [ch11](chapters/ch11-use-case-formats.md) | Use Case Formats | fully dressed template |
| [ch12](chapters/ch12-when-done.md) | When Are We Done? | fit-for-purpose completeness |
| [ch13](chapters/ch13-scaling-many-use-cases.md) | Scaling Up to Many Use Cases | organizing a use-case set |
| [ch14](chapters/ch14-special-use-cases.md) | Two Special Use Cases | CRUD, parameterized use cases |
| [ch15](chapters/ch15-business-process-modeling.md) | Business Process Modeling | enterprise scope |
| [ch16](chapters/ch16-missing-requirements.md) | The Missing Requirements | non-behavioral requirements |
| [ch17](chapters/ch17-overall-process.md) | Use Cases in the Overall Process | lifecycle integration |
| [ch18](chapters/ch18-briefs-and-xp.md) | Use Case Briefs and eXtreme Programming | low-precision briefs |
| [ch19](chapters/ch19-mistakes-fixed.md) | Mistakes Fixed | review and repair |
| [ch20](chapters/ch20-each-use-case.md) | Each Use Case | per-use-case reminders |
| [ch21](chapters/ch21-use-case-set.md) | The Use Case Set | set-level reminders |
| [ch22](chapters/ch22-working-on-use-cases.md) | Working on the Use Cases | practical workflow |

## Topic Index

- **Actor-goal list** → ch01, ch03, ch13
- **Business process** → ch15
- **CRUD** → ch14
- **Design scope** → ch03
- **Extensions** → ch08, ch10
- **Goal levels** → ch05
- **Guarantees** → ch06
- **Main success scenario** → ch01, ch07
- **Missing requirements** → ch01, ch16
- **Parameterized use case** → ch14
- **Preconditions** → ch06
- **Stakeholders and interests** → ch02, ch04
- **Sub-use case** → ch05, ch10
- **Technology variation** → ch09
- **Use case brief** → ch03, ch18
- **Use case template** → ch11
- **Usage narrative** → ch01

## Supporting Files

- [glossary.md](glossary.md) — key terms and definitions
- [patterns.md](patterns.md) — reusable writing techniques
- [cheatsheet.md](cheatsheet.md) — decision rules and review prompts

## Scope & Limits

This skill captures Cockburn's method for behavioral requirements. Combine it with domain models, business rules, interface specifications, quality attributes, and project-specific standards for a complete requirements set.
