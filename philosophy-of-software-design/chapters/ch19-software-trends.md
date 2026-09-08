# Chapter 19: Software Trends

Source: first edition (2018), PDF pages 159-165. Reference notes.

## Core idea

Evaluate a development practice by its effect on complexity. Familiarity, popularity, and a pattern name do not establish that it fits the current problem.

## Evaluation table

| Practice | Benefit identified by Ousterhout | Risk or qualification |
|---|---|---|
| **Interface inheritance** | Reuses one abstraction across different implementations. | The contract must capture what those implementations share. |
| **Implementation inheritance** | Removes repeated implementation and associated change amplification. | Shared mutable state and overrides can require understanding an entire hierarchy. |
| **Composition** | Can supply shared functionality through an independent helper abstraction. | Judge the resulting boundary rather than assuming any helper is deep. |
| **Agile development** | Iteration lets design incorporate experience. | Feature-by-feature pressure can encourage tactical patches. |
| **Unit tests** | Increase confidence in structural changes and expose regressions. | Passing tests is not itself a measure of abstraction quality. |
| **Test-driven development** | Tests clarify expected behavior; a failing test is useful before a bug fix. | The author criticizes building a design one passing feature test at a time. |
| **Design patterns** | Reuse a known solution and readers' existing understanding. | Forcing an ill-fitting pattern adds complexity. |
| **Getters and setters** | Can mediate access that must be exposed. | One accessor pair per field can reveal representation and create shallow interfaces. |

## Abstractions as increments

Wait until a capability is needed, then invest in a coherent, somewhat general-purpose abstraction rather than a growing set of unrelated feature hooks. This does not mean implementing every imagined future feature. It means designing the necessary core operations to fit together.

## Inheritance decision

Before sharing an implementation through inheritance, examine composition. If inheritance is the best fit, keep ownership of state clear: parent-managed state should be controlled through the parent's operations rather than freely mutated across the hierarchy. Ask how many implementations a maintainer must understand to change one safely.

Interface reuse is different from pass-through layers. Multiple implementations can provide different useful behavior behind a common contract, reducing the number of concepts callers learn.

## Testing and the author's TDD position

Ousterhout strongly supports tests for refactoring. The Tcl interpreter-to-bytecode transition illustrates how an existing suite can reveal regressions during a major implementation change.

His criticism of TDD is a design opinion in this edition: he argues that chasing individual passing tests can displace attention to a coherent abstraction. He explicitly supports writing a failing regression test before fixing a bug, so the test demonstrates the failure it is meant to detect.

When using this reference, attribute that criticism rather than presenting it as settled consensus. A user's requested test-first workflow and project conventions still govern the work; evaluate abstraction quality within them.

## Anti-patterns and limits

- Treating object orientation, patterns, or tests as automatic proof of good design.
- Using an inheritance tree whose shared state defeats information hiding.
- Adding accessors because fields exist, without asking which operations callers need.
- Interpreting abstraction-first development as speculative feature building.

## Key takeaways

1. Identify the complexity a practice removes and the dependencies it adds.
2. Favor coherent abstractions within incremental development.
3. Use tests to make design improvement feasible.
4. Apply patterns and inheritance when their contracts fit.

## Connects to

- [Ch 3](ch03-working-code-isnt-enough.md): tactical versus strategic work.
- [Ch 6](ch06-general-purpose-modules.md): scope of a useful general interface.
- [Ch 7](ch07-layers-and-abstractions.md): useful interface reuse.
- [Ch 16](ch16-modifying-existing-code.md): refactoring with confidence.
