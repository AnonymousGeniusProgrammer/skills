# Patterns and Techniques

Use this file to select a technique. Read the linked chapter for its worked example, prerequisites, and limits. The list includes design patterns and concrete refactoring/testing methods; it does not imply that every named method is a formal pattern.

## Boy Scout Cleanup
**When to use**: A requested change touches code with nearby avoidable confusion.
**How**: Protect the affected behavior, improve one name or structural problem, then verify the changed area.
**Trade-offs**: Keep the improvement proportionate to the current work. [Ch 1](chapters/ch01-clean-code.md)

## Intention-Revealing Rename
**When to use**: A name requires decoding or misstates the entity's role.
**How**: Establish its actual meaning, select the domain/technical term, and review the renamed call sites.
**Trade-offs**: Public names and serialized identifiers may require compatibility handling. [Ch 2](chapters/ch02-meaningful-names.md)

## Extract a Concept; Stepdown Organization
**When to use**: A function mixes abstraction levels or contains a separately meaningful operation.
**How**: Name the lower-level concept, extract it, and arrange the main operation so its immediate steps read coherently.
**Trade-offs**: Avoid forwarding fragments that add navigation without meaning. [Ch 3](chapters/ch03-functions.md)

## Argument Object and Explicit Operations
**When to use**: Several inputs represent one concept, or a selector hides distinct workflows.
**How**: Give related data a domain type; split differently purposed operations into clear entry points.
**Trade-offs**: Preserve meaningful independent inputs and avoid shared mutable state as a shortcut. [Ch 3](chapters/ch03-functions.md)

## Abstract Factory and Polymorphic Dispatch
**When to use**: Many operations repeat the same type selection, or consumers need control over creation timing without construction details.
**How**: Define a common behavioral contract, implement variants, and centralize concrete construction.
**Trade-offs**: Adding operations across stable data types can fit procedures better. [Ch 3](chapters/ch03-functions.md), [Ch 6](chapters/ch06-objects-and-data-structures.md), [Ch 11](chapters/ch11-systems.md)

## Intent/Rationale Comment
**When to use**: A necessary explanation cannot be expressed clearly by names and structure.
**How**: State the invariant, reason, or consequence close to the controlling code and verify its accuracy.
**Trade-offs**: Comments create another maintained claim; remove redundant narration. [Ch 4](chapters/ch04-comments.md)

## Behavior-Oriented Encapsulation
**When to use**: Clients traverse an object's representation to perform a job.
**How**: Identify the intended job and let its natural owner expose that operation.
**Trade-offs**: Plain transfer data can remain explicit; avoid pass-through method proliferation. [Ch 6](chapters/ch06-objects-and-data-structures.md)

## Exception Translation Wrapper
**When to use**: External failures leak a taxonomy callers do not need.
**How**: Translate failures into categories matching caller recovery while retaining cause and context.
**Trade-offs**: Keep distinct failures when they require distinct actions. [Ch 7](chapters/ch07-error-handling.md)

## Special Case and Empty Collection
**When to use**: Absence represents a valid domain alternative.
**How**: Supply an object or explicit result implementing the normal contract; return an empty collection for a successful no-items result.
**Trade-offs**: Never convert a failed lookup into fictional absence. [Ch 7](chapters/ch07-error-handling.md)

## Adapter, Fake, and Learning Tests
**When to use**: A dependency is broad, unfamiliar, unstable, or not ready.
**How**: Define the needed interface, experiment with actual behavior, develop consumers using a fake, and verify the real adapter independently.
**Trade-offs**: A fake cannot establish actual integration compatibility. [Ch 8](chapters/ch08-boundaries.md)

## TDD; Build-Operate-Check; Test DSL
**When to use**: Implementing behavior through short feedback cycles or clarifying noisy tests.
**How**: Begin with a failing example, add sufficient behavior, and refine. Structure tests around setup, action, and checks; extract repeated domain helpers.
**Trade-offs**: Keep one concept visible and avoid a framework that hides scenario facts. [Ch 9](chapters/ch09-unit-tests.md)

## Extract Class by Responsibility and Cohesion
**When to use**: Independent reasons to change or separate state/method clusters share a class.
**How**: Identify the meaningful group, move its state and behavior together, and give it a clear contract.
**Trade-offs**: Split for demonstrated need; minimal class count remains a counterweight. [Ch 10](chapters/ch10-classes.md)

## Dependency Injection; Separation of Main
**When to use**: Domain work constructs or locates concrete dependencies.
**How**: Assemble collaborators in startup and supply them through constructors, setters, or a needed factory.
**Trade-offs**: Keep actual wiring and lifecycle independently verified; a container is optional. [Ch 11](chapters/ch11-systems.md)

## Proxy, Decorator, and Aspect-Like Separation
**When to use**: Behavior needs wrapping or a concern consistently intersects many domain operations.
**How**: Use an appropriate wrapper/interception mechanism and define where it applies. Decorator is referenced as established naming vocabulary; the systems chapter develops proxies and aspects.
**Trade-offs**: Additional indirection and hidden interception can complicate reasoning. [Ch 11](chapters/ch11-systems.md), [Ch 17](chapters/ch17-smells-and-heuristics.md)

## Template Method or Strategy
**When to use**: Related algorithms share a stable structure or a replaceable algorithmic policy.
**How**: Use a common skeleton with selected variable steps, or isolate the varying algorithm behind a supplied strategy.
**Trade-offs**: Confirm shared reasons to change; inheritance and configurability are not free. [Ch 12](chapters/ch12-emergence.md), [Ch 17 G5](chapters/ch17-smells-and-heuristics.md)

## Incremental Migration
**When to use**: New variants make an existing implementation increasingly tangled.
**How**: Introduce a seam, migrate one case, verify, consolidate, and remove obsolete paths. Args demonstrates typed marshalers and separated diagnostics.
**Trade-offs**: Transitional duplication needs a bounded destination. [Ch 14](chapters/ch14-successive-refinement.md)

## Explain Temporaries and Normalize Bounds
**When to use**: An algorithm contains opaque expressions, repeated offsets, or implicit ordering.
**How**: Name intermediate meanings, distinguish counts from indexes, expose prerequisites, and verify equality/empty/overlap cases.
**Trade-offs**: Renaming without understanding the arithmetic can worsen ambiguity. [Ch 15](chapters/ch15-junit-internals.md), [Ch 16](chapters/ch16-refactoring-serialdate.md)

## Concurrency Ownership and Atomic Operations
**When to use**: Several workers share data or depend on a compound check/update sequence.
**How**: Partition state first; otherwise give the shared owner an operation guarding the complete invariant. Consider producer-consumer queues, reader-writer coordination, or ordered resource acquisition according to the model.
**Trade-offs**: Account for contention, starvation, shutdown, and access bypassing the owner. [Ch 13](chapters/ch13-concurrency.md), [App A](chapters/appendix-a-concurrency-ii.md)

## CAS, Schedule Variation, and Deadlock Analysis
**When to use**: Evaluating a single-value atomic update, rare interleavings, or resource cycles.
**How**: Use a fitting atomic library operation; test varied schedules and retain failure conditions; analyze mutual exclusion, lock-and-wait, no preemption, and circular wait.
**Trade-offs**: Atomic fields do not protect compound invariants; stress tests do not prove correctness. [App A](chapters/appendix-a-concurrency-ii.md)

## Algorithm Study Routes
**When to use**: Studying a concrete algorithm rather than selecting a structural pattern.
**How**: Use the prime-sieve rationale in [Ch 4](chapters/ch04-comments.md), prime generation/pagination in [Ch 10](chapters/ch10-classes.md), string prefix/suffix compaction in [Ch 15](chapters/ch15-junit-internals.md), and calendar/interval arithmetic in [Ch 16](chapters/ch16-refactoring-serialdate.md) and [App B](chapters/appendix-b-serialdate.md).
**Trade-offs**: These are reconstructed teaching examples; consult the PDF for exact original listings. Command and Visitor appear as naming/pattern references, not full implementation tutorials in this book.
