# Chapter 23: How Do I Know That I'm Not Breaking Anything?

Source: printed pp. 309–317; PDF pp. 332–340.

## Core idea

Reduce uncertainty at each edit. Distinguish behavior changes from structure-preserving work, pursue one immediate objective, preserve details mechanically, and use tests, compiler feedback, and review for different kinds of evidence.

## Hyperaware Editing

Before an edit, be able to say whether it should change behavior and how feedback will detect an unintended effect. Fast tests shorten the gap between an action and its consequences. The aim is deliberate work with external feedback, not mentally simulating an entire system.

Treat the chapter's claim that unchanged code does not wear out as a model of source edits, not an operational guarantee: changing inputs, dependencies, environments, and accumulated state can still cause failures. For a refactoring, specify the behavior and conditions being preserved.

## Single-Goal Editing

1. Name the next concrete objective: expose a collaborator, extract a body, add one case, or correct one behavior.
2. When another improvement appears, record it rather than starting an overlapping edit.
3. Complete the immediate change and run the relevant checks.
4. Reassess the next item with a coherent working state.

A feature may require multiple changes. The discipline concerns overlapping unfinished transformations, not a ban on a cohesive feature diff. Keep preparatory dependency breaking distinguishable from the intentional behavior change so failures can be traced to a small step.

## Preserve Signatures

When moving behavior to make it testable, retain names, types, order, passing modes, and return contracts where possible. Copy a declaration carefully and derive the forwarding call from it. Do not simultaneously introduce new parameter objects, rename arguments, or change units.

Reconstructed from Feathers's processing example:

```text
process(orders, dailyTarget, interestRate, compensationPercent):
    processOrders(orders, dailyTarget, interestRate, compensationPercent)
```

The new method initially accepts the same four values with the same declared types. Wrapping them in new objects and multiplying the rate by 100 during this move would mix a data transformation with dependency breaking. Make that later change only with appropriate tests and a concrete reason.

Preserving a signature reduces transcription risk. It does not prove that the new method name avoids inherited-member collisions or that the forwarding call uses the intended receiver.

## Lean on the Compiler

The procedure has two parts:

1. Alter a declaration so references that need migration become compile errors.
2. Use those errors to locate and update the references, then recompile.

For example, encapsulating global exchange-rate fields in an `Exchange` object causes unqualified uses to fail; those sites can be redirected to the object's fields. Changing a variable from a concrete class to an interface similarly reveals methods required by its clients.

Compiler errors are a navigation aid, not a complete dependency inventory. Search and inspect names that might silently resolve differently. In particular:

| Situation | False inference | Better check |
|---|---|---|
| Removing an override produces no errors | The method was unused | Calls may now resolve to the superclass method |
| Moving a field produces no errors | No code still needs that state | A same-named inherited field may now be selected |
| A signature change compiles | Values retain their meaning | Check conversions, argument ordering, overload selection, and units |
| One target builds | All clients were updated | Check relevant build targets and configurations |

If compilation is slow, targeted search may provide quicker navigation. Improve the build boundary where it materially obstructs feedback; do not repeat a large build merely to discover one obvious reference at a time.

## Pair Programming and focused review

Feathers strongly recommends pairing for dependency-breaking edits made before coverage exists. A second reader can track the narrow objective, notice semantic changes hidden in mechanical moves, and challenge assumptions about inheritance or initialization.

When applying this guidance as an agent, use available project review practices and be explicit about residual uncertainty. The book's recommendation does not grant authority to contact people, create tasks, or expand the user's requested workflow.

For a focused review, compare the original and new bodies, then inspect the connections: receiver, arguments, return assignment, call count, ordering, initialization, and exceptional paths. Review the exact transformation rather than broadly asking whether the code looks cleaner.

## Anti-patterns and takeaways

- Avoid doing cleanup, parameter redesign, dependency breaking, and new behavior in one unverified step.
- Avoid keeping a growing mental stack of half-finished changes; write down discoveries and finish one operation.
- Avoid treating successful compilation as behavioral equivalence.
- Avoid making production changes merely to make a failing characterization test green; first establish why it failed.
- Use the smallest useful feedback loop and expand validation to the affected integration boundary.

## Connects to

- [Chapter 2](ch02-working-with-feedback.md): feedback makes controlled change practical.
- [Chapter 20](ch20-large-classes.md): field and override hazards during class extraction.
- [Chapter 25](ch25-dependency-breaking-techniques.md): conservative preparatory transformations.
- [Extract Method appendix](appendix-extract-method.md): a worked mechanical move.
