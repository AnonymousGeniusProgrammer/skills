# Chapter 8: Moving Features

Source: printed pp. 197-238; PDF pp. 219-260. Entry locators use printed pages; add 22 for PDF pages.

## Core idea

Group elements that must be understood and changed together. Moves are behavior-sensitive: location affects scope, dispatch, object ownership, and execution order.

## Catalog mechanics

### Move Function — p. 198

**Use when:** another context better matches a function's data, collaborators, callers, or future use.

**How:** inspect dependencies and decide whether helpers should move first; inspect polymorphic bindings; copy the function into its new home; pass required source data or a source reference; adjust naming and receiver context; run static checks; make the old function delegate; test; inline the old wrapper if callers can reasonably use the destination directly.

**Watch:** moving a cluster from lower-level helpers outward can simplify dependencies. Alternatively, inline exclusive helpers, move the combined operation, then re-extract. A wrapper may remain when it preserves a useful or published interface.

### Move Field — p. 207

**Use when:** a field belongs with data or behavior in another object and the new relationship reflects the domain.

**How:** encapsulate it; create target storage/accessors; establish the source-to-target reference; redirect accessors; test; remove old storage.

**Watch:** multiple source objects may share one target. Their original values must be compatible before merging storage. A staged setter that writes both locations and an assertion comparing values can expose inconsistency during migration. Distinct per-source values cannot simply become one shared field.

### Move Statements into Function — p. 213

**Use when:** the same statements accompany calls and belong to the callee's responsibility.

**How:** slide the repeated statements next to the call. For one simple caller, transfer directly and check. For several callers, extract a temporary wrapper containing both the call and its accompanying statements; migrate callers; inline the old callee into the wrapper; choose the appropriate final name.

**Watch:** if the statements should accompany the call but have a distinct responsibility, the wrapper itself may be the useful endpoint. Check every caller before changing the shared callee's behavior.

### Move Statements to Callers — p. 217

**Use when:** behavior embedded in a shared function needs to vary by caller.

**How:** move the varying statements to an edge of the function. For simple cases, transfer them to callers. Otherwise extract the code that will stay, inline the old wrapper into callers, then rename the remaining function.

**Watch:** preserve execution order and exceptions. For overridden methods, align the remaining wrapper across implementations before removing dispatch. Large boundary changes may be easier through Inline Function and fresh extractions.

### Replace Inline Code with Function Call — p. 222

**Use when:** inline code has the same purpose and behavior as an existing operation.

**How:** confirm the match, replace it with the call, and test.

**Watch:** coincidentally similar expressions that represent different policies should not necessarily share an implementation. The function's name should make sense in the caller's domain. Verify a library function's actual semantics before substituting it.

### Slide Statements — p. 223

**Use when:** related statements are interleaved with unrelated ones, often blocking extraction.

**How:** identify the destination; inspect every intervening statement for interference; move only when dependencies permit it; test. On failure, recover and attempt a smaller move.

**Dependency rules:** preserve declaration-before-use and value availability. Do not move a read across a write that changes what it reads, or a write across a read or conflicting write whose behavior it changes. Include mutations inside called functions in this reasoning.

**Watch:** the move can also change observable side-effect or exception order. Superficially independent variable names do not establish independence if they alias the same object.

### Split Loop — p. 227

**Use when:** one traversal combines distinct jobs, making each difficult to understand or extract.

**How:** duplicate the loop structure; partition its responsibilities and eliminate duplicate side effects before executing the result; test; gather each initialization with its loop and consider Extract Function.

**Watch:** prove the jobs are separable. An interleaving of dependent per-item updates cannot automatically become two complete passes. Preserve input traversal semantics; a single-use iterator may need a different approach. Measure performance when traversal cost matters.

### Replace Loop with Pipeline — p. 231

**Use when:** selection, mapping, and aggregation would read more clearly as named collection operations.

**How:** introduce a collection variable; translate one part of the loop into a pipeline stage; test; continue until the loop has no behavior left; replace the accumulator with the pipeline result where appropriate.

**Watch:** keep ordering, short-circuiting, accumulator defaults, and side effects equivalent. Check whether the target operations are eager or lazy and whether multiple traversals are safe. A pipeline with an obscure reducer can be less clear than a focused loop.

### Remove Dead Code — p. 237

**Use when:** code has no remaining supported use and burdens understanding.

**How:** inspect callers and entrypoints, remove the unused code, and run appropriate checks. Version control preserves its history.

**Watch:** absence of text references alone does not establish that published functions, reflection targets, configuration entrypoints, or plugin hooks are dead. Commenting out obsolete implementation merely leaves the reading burden behind.

## Worked example: separate salary total from youngest age

The source loop calculates both a salary sum and the youngest person's age.

1. Begin with tests for both outcomes and an empty collection.
2. Duplicate the traversal, keeping salary accumulation in one loop and minimum-age calculation in the other. Remove the extra salary update before running, otherwise the total doubles.
3. Slide each initialization next to its respective traversal.
4. Extract `totalSalary` and `youngestAge` separately.
5. Optionally replace the salary loop with a sum pipeline and substitute a clearer minimum algorithm.

Compact reconstruction of the separated calculations:

```javascript
function totalSalary(people) {
  return people.reduce((sum, person) => sum + person.salary, 0);
}

function youngestAge(people) {
  let youngest = Infinity;
  for (const person of people) {
    if (person.age < youngest) youngest = person.age;
  }
  return youngest;
}
```

This reconstruction assumes ordinary numeric ages and salaries. It retains the source's empty-case minimum of `Infinity`; decide separately whether a product should expose something else. The source later uses `Math.min(...people.map(...))`; that concise form also requires attention to supported numeric inputs and argument-count limits for very large collections. The refactoring benefit is separate responsibilities, not compulsory use of spread syntax.

## Decision rules and anti-patterns

| Observation | Direction | Evidence needed |
|---|---|---|
| Every caller repeats the same companion operation | Move statements inward | It belongs to the callee and every migrated caller keeps the same effect |
| Callers need different companion operations | Move statements outward | Shared computation can remain coherent without the varying policy |
| A function is dominated by another context's data | Move function toward that context | Fewer meaningful dependencies, correct receiver/dispatch |
| A field is always passed with another object | Consider moving field | Correct ownership, cardinality, and shared-value semantics |
| A loop has two independently named outcomes | Split, then extract | No required per-item interleaving or doubled effects |

Avoid bundling a move with unrelated algorithm changes. Move one dependency boundary at a time, preserve behavior, and then improve the new local structure. See [Ch 6](ch06-first-refactorings.md) for extraction, [Ch 7](ch07-encapsulation.md) for access control, [Ch 9](ch09-organizing-data.md) for identity, and [Ch 12](ch12-inheritance.md) for dispatch-sensitive moves.
