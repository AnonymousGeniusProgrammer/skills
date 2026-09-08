# Chapter 6: A First Set of Refactorings

Source: printed pp. 105-160; PDF pp. 127-182. Entry locators below are printed pages; add 22 for PDF pages.

## Core idea

Name intent, control access, and gather related data and calculations before attempting larger reorganizations. Extraction and inlining are complementary tools for finding useful boundaries.

## Catalog mechanics

Each entry summarizes motivation, the main sequence, and the constraint most likely to change the decision. Check behavior after meaningful steps and each caller migration.

### Extract Function — p. 106

**Use when:** a fragment's purpose is clearer as a named operation than as inline implementation. Length and reuse are secondary signals.

**How:** name the target after intent; copy the fragment; identify out-of-scope reads and writes; move declarations used only within the fragment; pass remaining inputs; return a changed value when appropriate; perform static checks; replace the original fragment with a call; test. Look for genuinely equivalent fragments that can then call it.

**Watch:** several assigned outer variables often signal that dependencies need simplifying first with Split Variable or Replace Temp with Query. Nested functions can reduce initial parameter plumbing but may later need moving. An extraction whose name explains no more than its body can be undone.

### Inline Function — p. 115

**Use when:** the body communicates as well as the name, forwarding adds no useful separation, or a group needs combining before re-extraction.

**How:** check overrides and dispatch; locate callers; substitute the body into one caller while adapting parameters and locals; test; repeat; remove the unused declaration.

**Watch:** preserve argument evaluation, returns, receiver context, and local names. A polymorphic method cannot simply be replaced with one implementation. Recursion or complex control flow may make another move preferable.

### Extract Variable — p. 119

**Use when:** part of an expression deserves a name meaningful within the current function.

**How:** confirm the expression is free of side effects; introduce an immutable local at the appropriate evaluation point; replace one occurrence; test; migrate other equivalent occurrences incrementally.

**Watch:** a name useful outside this scope may belong in a function. Combining multiple evaluations is safe only when the expression has the same relevant value at those points.

### Inline Variable — p. 123

**Use when:** the variable adds no useful explanation or blocks another transformation.

**How:** verify that the initializer has no side effects; make the binding immutable to expose later assignments; replace references with the expression one at a time; test; remove the declaration.

**Watch:** immutability of the binding does not make its referenced inputs stable. Inlining a snapshot into later queries can change results when those inputs change.

### Change Function Declaration — p. 124

**Use when:** a name or parameter arrangement miscommunicates the operation or makes callers awkward. This entry includes Rename Function and Change Signature.

**Simple route:** make a removed parameter unnecessary in the body, change the declaration, update all references, and verify. Separate a rename from a parameter change when that simplifies feedback.

**Migration route:** prepare and extract the body into a function with the desired interface, retaining the old function as a forwarding wrapper. Supply new parameters, test, and move callers individually. Remove the old function and temporary naming when appropriate.

**Watch:** account for each polymorphic binding. A published API may need the wrapper indefinitely; finding no local callers does not establish that independent clients have migrated.

### Encapsulate Variable — p. 132

**Use when:** broadly accessed mutable data is difficult to move or its updates are hard to control.

**How:** create reading and updating functions; run static checks; move each access behind them; restrict direct visibility; test. Consider Encapsulate Record when the value has structure.

**Watch:** this controls access to the binding. Returning a mutable object can still expose its internal updates. Separate reassignment protection from protection of the referred-to data.

### Rename Variable — p. 137

**Use when:** a variable's name conceals or misstates its role, especially over a wide scope.

**How:** use semantic rename where suitable; otherwise identify and update all references. Encapsulate a widely used variable first when that permits gradual migration. An unchanged value can sometimes be copied under a new name while callers move.

**Watch:** external references to a published mutable variable prevent an ordinary local rename. A second mutable binding would create a synchronization problem.

### Introduce Parameter Object — p. 140

**Use when:** a meaningful group repeatedly travels through parameters.

**How:** create or reuse a suitable structure, often a value class; add it as a parameter; migrate callers to construct or pass it; replace each old parameter's use with a field or query on the object; remove old parameters one by one.

**Watch:** group a domain concept, not unrelated arguments placed in a bag. The resulting object can become a home for related behavior; merely shortening a signature is the smaller benefit.

### Combine Functions into Class — p. 144

**Use when:** a set of functions shares a common record and benefits from a persistent common context.

**How:** group the common data if necessary; encapsulate the record in a class; move related functions into it; remove parameters now available from instance data; extract and move remaining related calculations.

**Watch:** a class is especially useful when source data changes and derived queries must remain consistent. An instance is not inherently safe if it also caches values without maintaining them.

### Combine Functions into Transform — p. 149

**Use when:** several derivations of the same input are repeated or difficult to discover, and consumers can use a prepared data result.

**How:** introduce a transform initially returning an independent copy; move a calculation into it as an enriched field; update its clients; test; repeat. Extract complex calculations before moving them.

**Watch:** ensure copying protects the input to the depth actually mutated. Test that the source remains unchanged. Stored derived fields become stale if inputs are subsequently changed; use an appropriate class/query model when live updates are required.

### Split Phase — p. 154

**Use when:** two distinct concerns form a sequence and the later concern can consume a clearer intermediate representation.

**How:** extract the second phase; add an intermediate record parameter; move shared inputs and first-phase results into that record incrementally; remove raw inputs the second phase no longer needs; extract the first phase to return the record.

**Watch:** the intermediate structure should express what the second phase needs. Passing every original input through it without separating responsibility does not create the intended boundary. See the statement example in Ch 1.

## Worked example: turn temperature bounds into a range

The source filters station readings using minimum and maximum operating temperatures. Reconstruct the migration this way:

1. Create `NumberRange` with the same bounds, initially adding no validation or changed numeric policy.
2. Add a range parameter while keeping the old bounds in use. Update callers to pass the correct range.
3. Read the maximum from the range; remove that scalar parameter after verification. Repeat for the minimum.
4. Move the membership rule into the range. Then callers ask for containment rather than knowing its representation.

```javascript
class NumberRange {
  constructor(min, max) {
    this.min = min;
    this.max = max;
  }
  contains(value) {
    return value >= this.min && value <= this.max;
  }
}

function readingsOutsideRange(station, range) {
  return station.readings.filter(reading => !range.contains(reading.temp));
}
```

This compact reconstruction exposes fields for brevity; the source uses getters and no updating methods. The example assumes ordinary numeric readings. The original outside predicate and negated containment differ for `NaN`, so verify the input contract before using this rewrite on unrestricted JavaScript numbers. Adding support or validation for malformed data is a separate behavior decision.

Test ordinary inside/outside values and both endpoints. Preserve which endpoints belong to the range; an innocent-looking `<`/`<=` change changes behavior.

## Choice table

| Decision | Favor the first when | Favor the second when |
|---|---|---|
| Extract Variable / Extract Function | The name belongs only to a local calculation | The concept deserves reuse or a broader domain name |
| Extract Function / Inline Function | A name hides meaningful implementation | Indirection adds no explanation or obstructs better boundaries |
| Class / Transform | Inputs change and queries must reflect them | Consumers use an effectively immutable prepared result |
| Parameter Object / Preserve Whole Object | A meaningful group has no suitable object | A suitable existing object already supplies the required values |

## Takeaways and connections

Preserve evaluation semantics while changing names and boundaries. Choose class versus transform using mutation requirements. Use temporary duplication or forwarding only as part of a coherent migration. See [Ch 7](ch07-encapsulation.md) for deep data boundaries, [Ch 8](ch08-moving-features.md) for moving operations, and [Ch 11](ch11-refactoring-apis.md) for parameter tradeoffs.
