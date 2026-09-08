# Chapter 1: Changing Software

Source: printed pp. 3-8; PDF pp. 26-31.

## Core idea

Define a change by the behavior it intentionally changes and the behavior it must preserve. Even a feature request usually places much more existing behavior at risk than it intends to alter.

## Decision framework

Use Feathers' four reasons for change to separate goals:

| Goal | Intended change | Principal invariant |
|---|---|---|
| Add a feature | New functionality, usually structure | Existing functionality outside the addition |
| Fix a bug | A specific existing behavior | Other behavior, including neighboring cases |
| Improve design | Structure and maintainability | Functional behavior |
| Optimize | Time, memory, or another resource | Functional behavior |

These are distinctions of intent, not claims that other dimensions cannot move. Refactoring can affect performance; optimization can require restructuring. If timing is part of a contract, treat it as behavior to preserve or change explicitly.

Before editing, establish three things: the required change, evidence that the change works, and evidence that surrounding behavior remains intact. A ticket label such as "bug" does not answer these questions. A user can regard a changed requirement as a defect while a developer calls it a feature; settle the behavior without letting terminology obscure it.

## Worked example

Reconstruction of the chapter's CD-player example: adding `replaceTrackListing(name, track)` to a class does not by itself give the running application a new feature. A caller must reach it. Adding a UI button introduces the call, but also changes layout and potentially display timing.

For that task, write a compact change boundary:

- New behavior: the selected track listing can be replaced.
- Preserved behavior: adding tracks, selecting tracks, and playback still work.
- Deliberate UI difference: the replacement control is now available.
- Relevant observations: replacement result, the unchanged neighboring operations, and the new route through the UI.

The lesson is to trace an addition through its callers. A diff containing only added lines can still change existing behavior through wiring, overload selection, layout, or resource use. Those last examples are applications of the chapter's distinction, not additional named book frameworks.

## Mental models and failure modes

**Preservation dominates the task.** Think of the requested alteration as a small region surrounded by functionality users already rely on. Code left untouched can still behave differently because something it calls or reads has changed.

**Avoidance creates future risk.** Repeatedly appending code to an existing method because extracting another method feels risky makes the next change harder to understand. Caution is useful when it produces feedback and small steps; permanent avoidance erodes both structure and skill.

**Care needs instruments.** More scrutiny or more developers can help analysis, but neither provides the rapid behavioral evidence of tests around a change.

## Apply it

1. State the observable before/after behavior in the user's terms.
2. List the surrounding behaviors that must remain stable.
3. Identify plausible propagation paths beyond the edited lines.
4. Choose observations for both intentional change and preservation.
5. Keep design improvement and functional change distinguishable while executing them.

Completion evidence is a concrete behavior boundary and a way to observe it. A small diff alone does not establish low risk.

## Connects to

- [Ch 2](ch02-working-with-feedback.md): turn preservation into a feedback loop.
- [Ch 11](ch11-what-methods-to-test.md): trace the reach of a change.
- [Ch 23](ch23-not-breaking-anything.md): make the initial unprotected edits more controlled.
