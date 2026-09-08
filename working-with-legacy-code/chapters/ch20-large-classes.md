# Chapter 20: This Class Is Too Big and I Don't Want It to Get Any Bigger

Source: printed pp. 245–268; PDF pp. 268–291.

## Core idea

Find responsibilities inside a large class and extract them in manageable steps. Choose boundaries that clarify the current work; do not require a complete ideal decomposition before making progress.

The Single Responsibility Principle concerns a coherent purpose and reason to change. It does not mean one method per class. Distinguish a large public interface from a large implementation: delegation can simplify implementation before all callers can be migrated to smaller interfaces.

## Seven responsibility-finding heuristics

| Book's heuristic | Practical probe | Useful result |
|---|---|---|
| Group Methods | Group methods by names, callers, data, and apparent purpose; ask why each belongs here | Candidate responsibilities, including small groups |
| Look at Hidden Methods | Examine private and protected methods as possible operations of another object | A new public interface without exposing the original object's internals |
| Look for Decisions That Can Change | Find encoding, storage, library, or algorithm decisions already embedded in the code | An implementation choice that can be hidden behind intent-revealing methods |
| Look for Internal Relationships | Sketch methods' use of other methods and instance variables | Clusters and the few connections between them |
| Look for Primary Responsibility | Describe the class in one sentence and inspect extra clauses | Its central purpose and responsibilities that support or distract from it |
| When All Else Fails, Do Some Scratch Refactoring | Explore a candidate extraction in a disposable copy | Knowledge of a viable boundary, not an obligation to keep the experiment |
| Focus on Current Work | Ask which responsibility must change now | An extraction with immediate value and bounded scope |

Use several heuristics together. Names can be misleading, and shared data alone does not prove that all methods belong in one class. Do not force a full partition before extracting one useful responsibility.

## Feature sketches

For each relevant method, draw links to the methods and fields it uses. Constructors can obscure the picture because they initialize many fields; omit them initially when appropriate. Look for clusters that communicate through a small number of links.

An **effect sketch** follows what a change can affect. A **feature sketch** follows what a feature uses. The two drawings may contain similar nodes but answer different questions. A feature boundary is a design hypothesis; an effect sketch helps determine what must be observed when changing it.

## Worked example: reservation fees

Feathers's `Reservation` contains duration, daily rate, date, customer, and additional fees. Extending a reservation and computing its principal fee use the core reservation fields. Adding fees and totaling those additions form another cluster. The total-fee method connects the clusters.

1. Sketch the methods' field usage.
2. Try to name each cluster. A proposed extracted class containing nearly the entire meaning of a reservation suggests that the remaining class is the wrong side of the split.
3. Keep the primary reservation behavior in `Reservation` and extract additional-fee handling into a `FeeCalculator`.
4. Leave principal-fee calculation with the duration and daily rate. Pass the resulting base fee to the calculator's total operation.
5. Test the fee calculation separately and retain tests for the delegation and total.

Passing a calculated value avoids giving the calculator a broad back-reference to `Reservation`. Moving principal-fee calculation as well would pull in several reservation fields and weaken the boundary. Let data needs constrain the extraction instead of chasing an attractive class name.

## Separate interface and implementation decisions

A large `ScheduledJob` can remain a facade while its implementation delegates to smaller classes. This is useful when changing all clients at once is impractical. Client-specific interfaces, such as a controller exposing only run/pause operations, can later reduce the surface each caller knows.

The Interface Segregation Principle helps assess those client views. Merely introducing several interfaces while leaving one tangled implementation does not remove its internal coupling. Conversely, useful implementation extraction does not require an immediate public API migration. Test client changes before retiring old entry points.

## A conservative extraction when coverage is weak

Prefer covering the area first. When dependency structure makes a fully covered extraction impractical, the chapter describes a deliberately mechanical intermediate route:

1. Select one responsibility and gather the fields that belong to it into a recognizable section of the class.
2. Extract whole method bodies into uniquely named temporary helper methods, such as a `MOVING` prefix. Keep original methods as forwarding entry points with their original signatures.
3. Extract parts of mixed methods into the same section, using the [Extract Method checks](appendix-extract-method.md).
4. Search all uses of the fields and helpers, including subclasses. Identify reads, writes, visibility assumptions, and inherited members before moving them.
5. Move the gathered section to a new class. Add an instance in the old class and redirect the forwarding methods. Use compiler errors to expose unresolved dependencies, then inspect successful resolutions as well.
6. Preserve the original override entry points. Check construction, initialization order, ownership, and state sharing across the boundary.
7. Remove temporary naming and establish focused tests around the newly accessible class. Run the available broader checks.

This approach reduces the number of simultaneous decisions; it does not make an untested change risk-free. Keep the diff small enough to inspect each moved behavior and its new connection.

## Silent inheritance hazards

| Hazard | Why compilation may succeed | Required check |
|---|---|---|
| Move an overriding method out of a subclass | Calls can fall back to the base implementation | Retain an override that delegates to the extracted implementation |
| Move a field whose name also exists in a base class | An unqualified reference can bind to the inherited field | Search declarations and uses through the hierarchy; verify which state each expression accesses |
| Move behavior into a helper that needs the original object's mutable internals | Broad accessors or a back-reference can recreate the tangle | Prefer a narrow value/operation boundary; reconsider the split if it needs most of the original object |

The compiler catches missing names. It does not prove that names still resolve to the same members or that dispatch still selects the same implementation.

## Anti-patterns and takeaways

- Avoid a refactoring binge that postpones the requested feature until every class looks ideal.
- Avoid continued growth merely because the class already contains unrelated work. Sprout a tested class where an extraction is not yet feasible.
- Avoid automatically making hidden methods public on the existing class. A public operation on a cohesive extracted class may be the better interface.
- Avoid interpreting every cross-link in a feature sketch as a reason to keep everything together. Some dependencies can become explicit values or narrow calls.
- Discuss a few plausible responsibilities with maintainers, then validate one through code and tests. A diagram alone does not settle the design.

## Connects to

- [Chapter 6](ch06-not-much-time.md): Sprout Class prevents additional growth while preserving a local path forward.
- [Chapter 11](ch11-what-methods-to-test.md): effect sketches assess the behavioral consequences of extraction.
- [Chapter 16](ch16-understanding-code.md): scratch refactoring as investigation.
- [Chapter 23](ch23-not-breaking-anything.md): mechanical edits, compiler feedback, and focused review.
