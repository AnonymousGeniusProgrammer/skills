# Patterns and sequences

These sequences connect catalog entries into practical work. The complete 61-entry catalog, including inverse moves, is indexed in [SKILL.md](SKILL.md). Read the chapter mechanics for the selected transformation.

## Prepare, then extend

**When to use:** the imminent feature would duplicate logic or compound a tangle.

**How:** establish current behavior; make the smallest useful structural change; verify; switch hats and implement the new behavior with its appropriate checks.

**Trade-offs:** preparatory work must repay its cost through the needed change; defer unrelated cleanup. [Ch 1](chapters/ch01-first-example.md), [Ch 2](chapters/ch02-principles.md).

## Preserve understanding in code

**When to use:** reading reveals an intention hidden behind mechanics.

**How:** rename the concept; Extract Variable or Extract Function at the appropriate scope; verify; use the clearer code to inspect the next responsibility boundary.

**Trade-offs:** names must add meaning. Inline an unsuccessful abstraction or simplify its dependencies first. [Ch 6](chapters/ch06-first-refactorings.md).

## Unblock an extraction

**When to use:** several temporaries or interleaved effects make extraction awkward.

**How:** Split Variable for distinct roles; use Replace Temp with Query for stable derivations; Slide Statements to gather independent work; extract a coherent operation.

**Trade-offs:** preserve snapshots, aliasing, and evaluation order. Many output parameters may signal the wrong boundary. [Ch 6](chapters/ch06-first-refactorings.md), [Ch 7](chapters/ch07-encapsulation.md), [Ch 8](chapters/ch08-moving-features.md).

## Inline, regroup, and re-extract

**When to use:** existing function or class boundaries scatter related knowledge.

**How:** Inline Function or Inline Class to make related work visible; regroup it with safe moves; extract around better change boundaries.

**Trade-offs:** a temporary large unit can be useful, but preserve dispatch and keep each state working. [Ch 3](chapters/ch03-code-smells.md), [Ch 7](chapters/ch07-encapsulation.md).

## Separate preparation from consumption

**When to use:** calculation and formatting, or parsing and processing, change for different reasons.

**How:** Split Phase; move first-phase results into an intermediate record; make the second phase consume that record; extract the first phase. Use a transform to gather compatible derivations.

**Trade-offs:** prepared values can become stale if their source changes. A phase record need not become a behavior-heavy class. [Ch 1](chapters/ch01-first-example.md), [Ch 6](chapters/ch06-first-refactorings.md).

## Give a data clump a home

**When to use:** meaningful values repeatedly travel together.

**How:** Introduce Parameter Object or Preserve Whole Object; move shared rules into the resulting concept; use Extract Class when the same group appears as fields.

**Trade-offs:** group a real relationship and evaluate the callee's new dependency. Preserve endpoint, unit, and validation behavior during migration. [Ch 6](chapters/ch06-first-refactorings.md), [Ch 11](chapters/ch11-refactoring-apis.md).

## Control data before moving it

**When to use:** widespread readers and writers prevent a safe data reorganization.

**How:** Encapsulate Variable or Record; route updates through a narrow interface; protect collection membership and nested aliases as needed; Move Field or Rename Field behind that interface.

**Trade-offs:** getters can still leak mutable references. Check shared-target cardinality before moving storage. [Ch 7](chapters/ch07-encapsulation.md), [Ch 8](chapters/ch08-moving-features.md), [Ch 9](chapters/ch09-organizing-data.md).

## Remove redundant mutable state

**When to use:** a cache or accumulator duplicates authoritative data and is easy to leave stale.

**How:** identify all contributions; split independent sources; calculate a query; assert agreement with the stored result; migrate readers; remove obsolete writes.

**Trade-offs:** check nonzero initialization, mutable historical inputs, numeric equality, and query cost. Immutable enrichment is a legitimate alternative. [Ch 9](chapters/ch09-organizing-data.md).

## Migrate an interface with forwarding

**When to use:** all callers cannot safely change in one step.

**How:** introduce the new declaration; keep the old one delegating; migrate callers one at a time; retire the wrapper when compatibility permits. A factory similarly isolates creation changes.

**Trade-offs:** temporary dual interfaces add complexity; published clients may require long-term compatibility. [Ch 2](chapters/ch02-principles.md), [Ch 6](chapters/ch06-first-refactorings.md), [Ch 11](chapters/ch11-refactoring-apis.md).

## Separate loop responsibilities

**When to use:** a traversal computes unrelated outcomes.

**How:** Split Loop while partitioning side effects; Slide Statements to gather initialization; Extract Function for each job; optionally Replace Loop with Pipeline.

**Trade-offs:** retain ordering and traversal semantics. Measure actual cost instead of assuming one loop is necessary. [Ch 8](chapters/ch08-moving-features.md).

## Localize recurring variation

**When to use:** several operations repeat the same type dispatch.

**How:** establish a factory and a suitable class structure; Replace Conditional with Polymorphism one branch at a time. For shared missing-value behavior, Introduce Special Case instead.

**Trade-offs:** keep appropriate defaults; one isolated switch may be clearer. If a host's role changes, put the variation in a collaborator. [Ch 10](chapters/ch10-conditional-logic.md), [Ch 12](chapters/ch12-inheritance.md).

## Replace hierarchy coupling with delegation

**When to use:** inheritance exposes an unsuitable interface, confuses identity, or blocks independent variation.

**How:** hide creation; introduce a delegate; move behavior while retaining forwarders; preserve base behavior; remove the obsolete inheritance relationship after callers are compatible.

**Trade-offs:** delegation adds explicit plumbing. Distinguish Replace Subclass with Delegate from Replace Superclass with Delegate; examine `super` calls and identity. [Ch 12](chapters/ch12-inheritance.md).

## Tune after locating the bottleneck

**When to use:** measurements show a relevant performance shortfall.

**How:** profile representative behavior; isolate the hot spot; make a small optimization; check results and remeasure; keep changes that meet the performance need.

**Trade-offs:** optimization can reduce clarity, so justify it with evidence. Hard resource budgets apply throughout the work. [Ch 2](chapters/ch02-principles.md).
