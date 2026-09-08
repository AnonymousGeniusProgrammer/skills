# Chapter 16: I Don't Understand the Code Well Enough to Change It

Source: printed pp. 209-213; PDF pp. 232-236.

## Core idea

Externalize what you are learning instead of keeping the entire change in working memory. Small sketches, annotated listings, and disposable refactoring experiments can turn uncertainty into specific questions and evidence.

## Notes and sketching

Write down the important entity just encountered, the next one, and their relationship. Record questions and paths to revisit. Informal shapes are enough when they support the current reasoning; a complete UML model is not required.

A sketch serves as memory and a conversational object. The chapter's recreated dispatcher sketch is not independently intelligible, but it was useful to the people examining the code. For notes retained beyond the session, add the few names and explanations a later reader will need.

## Listing Markup

Choose marks according to the question:

| Question | Markup |
|---|---|
| Which statements share a responsibility? | Use one symbol/color per candidate group |
| Where do nested blocks start and end? | Match boundaries from the inside outward |
| Which block is a promising extraction? | Circle it and note the coupling count from Ch 22 |
| What can a proposed change affect? | Mark the edited values, then their readers and subsequent effects |

Use paper or an equivalent temporary view. Keep navigational annotations distinct from production changes so the exploration remains easy to discard.

## Scratch Refactoring

Use **Scratch Refactoring** to learn in an isolated disposable copy:

1. Begin from a known snapshot, keeping the user's current edits intact.
2. Extract methods, move variables, and reshape the code to expose relationships.
3. Record discoveries and hypotheses, including uncertain behavior assumptions.
4. Discard the experimental edits.
5. Reimplement any chosen production improvement from the original code with appropriate protection.

The point is understanding; the experiment is not an untested production patch. Two failures matter: an incorrect experiment can teach a false model, and attachment to its endpoint can prevent a better design later. Verify important conclusions against original behavior.

## Worked example

Reconstruction of the chapter's method-analysis workflow: a long routine mixes several responsibilities and its nested structure is difficult to follow. Mark matching blocks first. Group statements by the state they manipulate, circle a promising extraction, and record which values enter or leave it. In a scratch copy, extract that block and inspect the resulting interface.

The artifact worth retaining might be: "this calculation needs only these inputs, while the display operation depends on shared state." Return to the original version and use that finding to choose tests and a smaller safe extraction. The scratch implementation itself remains disposable.

## Delete Unused Code

When code is demonstrably unused, removing it reduces the material a maintainer must understand; version control preserves its history. Establish usage with the mechanisms relevant to the system. Dynamic discovery, configuration, generated calls, and reflective access can make textual search incomplete.

This is a scoped cleanup technique, not permission to delete unfamiliar code or discard another person's changes.

## Apply it

Finish the investigation with a concrete change location, the inputs/effects involved, and the unresolved questions that influence safety. A useful sketch or verified hypothesis is progress even before production code changes.

## Connects to

- [Ch 11](ch11-what-methods-to-test.md): effect reasoning.
- [Ch 17](ch17-system-structure.md): develop the broader architectural story.
- [Ch 22](ch22-monster-methods.md): coupling count and extraction mechanics.
