# Chapter 11: Refactoring APIs

Source: printed pp. 305-348; PDF pp. 327-370. Entry locators use printed pages; add 22 for PDF pages. Here API includes ordinary internal function interfaces, not only network endpoints.

## Core idea

Make calls communicate intent while locating dependencies and variability where they belong. A shorter signature is useful only if it does not hide inappropriate coupling.

## Catalog mechanics

### Separate Query from Modifier — p. 306

**Use when:** a caller must perform an update just to obtain information, or a returned value obscures a function's effects.

**How:** copy the operation into a clearly named query; remove observable side effects from that query; run static checks; for each caller using the result, call the query and then the original modifier in the order that preserves behavior; test; remove the modifier's return value; consolidate any duplicated computation appropriately.

**Watch:** the queried result must still represent what the old operation returned. A returned post-update value may need a different decomposition. Atomic operations and concurrent updates may prevent a naive two-call split. This is command-query separation, a design guideline; Fowler does not treat it as absolute.

### Parameterize Function — p. 310

**Use when:** operations perform the same conceptual algorithm with different literal values.

**How:** select one function; add a parameter for a varying literal and pass the old literal at its callers; substitute the parameter in the body; test; migrate each related function's callers to the generalized operation.

**Watch:** preserve differences that represent genuinely separate policies. Parameterize demonstrated variation; speculative parameters burden callers. Descriptive wrappers can remain useful when they explain particular domain operations.

### Remove Flag Argument — p. 314

**Use when:** a literal boolean, string, or enum passed by callers chooses an operation whose intent would be clearer as a named call.

**How:** provide an explicit operation for each relevant choice, through branch extraction or wrappers; migrate callers that pass literal choices; test.

**Watch:** a boolean that is domain data is not automatically a flag. A tangled implementation can remain behind explicit wrappers. Avoid multiplying functions for every combination of several flags; first consider whether the operation has separable responsibilities.

### Preserve Whole Object — p. 319

**Use when:** callers repeatedly unpack a coherent existing object merely to pass several parts to an operation.

**How:** introduce a wrapper accepting the whole object and forwarding its parts to the old function; migrate callers; inline the old function into the wrapper; remove unnecessary unpacking and choose the final name.

**Watch:** the callee now knows the whole object's interface. Do not add that coupling across a boundary where scalar parameters were deliberately sufficient. Passing a whole object also changes when its fields are read; preserve snapshot and mutation semantics.

### Replace Parameter with Query — p. 324

**Use when:** a parameter is readily derivable from data the callee already appropriately knows.

**How:** extract the derivation if needed; replace parameter uses with the same query incrementally; test; remove the parameter with Change Function Declaration.

**Watch:** querying another parameter is often the safest case. Querying mutable global state may destroy referential transparency and testability. Check that all callers used the same derivation and that query timing remains equivalent.

### Replace Query with Parameter — p. 327

**Use when:** a function internally accesses a dependency that should belong to its caller, such as environmental state.

**How:** extract the query result to a variable; extract the remaining body into a function receiving that value; inline the temporary and old wrapper into callers; give the new function the intended name.

**Watch:** this shifts work outward and may lengthen parameter lists. Preserve how often and when the query executes. The gain is a clearer dependency boundary, often a deterministic calculation that is easier to test.

### Remove Setting Method — p. 331

**Use when:** a field should be fixed after construction and external setters mainly support initialization scripts.

**How:** supply the field through construction; migrate initialization callers away from setters; inline the setter's initialization work into construction; remove it; make the field immutable where supported; test.

**Watch:** abandon or redesign the move if callers legitimately update a shared reference object and cannot replace it. Removing a setter does not freeze an object reachable through the field. Preserve any required initialization behavior and ordering.

### Replace Constructor with Factory Function — p. 334

**Use when:** creation needs a clearer name, subtype selection, or flexibility unavailable from direct construction.

**How:** add a factory initially calling the existing constructor; migrate callers and test after each; restrict direct constructor access where feasible.

**Watch:** initially preserve the same allocation, identity, initialization, and failure behavior. Returning cached instances or a different subtype is not automatically safe just because a factory permits it.

### Replace Function with Command — p. 337

**Use when:** a function needs a richer lifecycle, undo, customizable behavior, or a shared execution context that helps decompose a difficult calculation.

**How:** create a class; move the function into its execution method while retaining the original forwarding function; consider moving arguments into constructor fields; then extract internal operations using the shared context.

**Watch:** command objects add state and indirection. Define whether an instance may execute more than once and reset per-execution state accordingly. Creating one fresh command per original call is the simplest way to preserve independent calls. Fowler usually prefers a plain function unless the command's facilities earn their cost.

### Replace Command with Function — p. 344

**Use when:** a command's lifecycle and state add no benefit over invoking a straightforward operation.

**How:** wrap command construction plus execution in a function; inline supporting methods into execution as appropriate; move constructor parameters into the execution signature; replace field reads with parameters one at a time; inline creation and execution into the wrapper; remove the unused class.

**Watch:** confirm that callers do not rely on retained identity, multiple entrypoints, staged initialization, or repeated execution. Re-extract local helpers afterward when they improve clarity.

## Worked example: make delivery intent visible

The source has `deliveryDate(order, true)` and `deliveryDate(order, false)`. The flag chooses rush versus regular rules, each with its own processing and regional delivery times.

1. Extract the two branches into `rushDeliveryDate` and `regularDeliveryDate` without changing any rule.
2. Let the original function delegate to them while callers remain valid.
3. Replace each literal-true caller with the rush operation and each literal-false caller with the regular one.
4. Inspect callers passing calculated data separately; they may still need a selection point.
5. Remove the old public flag-based operation only if its callers and compatibility obligations permit it.

```javascript
function deliveryDate(order, isRush) {
  return isRush ? rushDeliveryDate(order) : regularDeliveryDate(order);
}
```

This intermediate wrapper is a compact reconstruction, with branch bodies intentionally omitted. It preserves behavior while providing a route for callers to migrate. Verify region boundaries and resulting dates in both modes. Refactoring the interface does not authorize changing delivery policy.

The source's insurance-scoring example demonstrates a different pressure: too many interacting locals can be gathered into a `Scorer` command, then broken into methods. That is a decomposition aid, not a requirement to turn every calculation into an object.

## Resolve the opposing moves

| Tradeoff | Favor this | When |
|---|---|---|
| Fewer arguments vs explicit dependencies | Replace Parameter with Query | The dependency already belongs in the callee |
| Fewer arguments vs explicit dependencies | Replace Query with Parameter | The dependency makes the callee impure or unnecessarily coupled |
| Whole object vs parts | Preserve Whole Object | The domain object is a legitimate callee dependency |
| General operation vs named choices | Parameterize Function | Values vary within the same algorithm |
| General operation vs named choices | Remove Flag Argument | Callers choose conceptually distinct operations |
| Function vs command | Plain function by default | The command adds no concrete lifecycle benefit |

## Takeaways and connections

Design the call from the caller's perspective, then account for the dependencies its convenience introduces. Keep wrappers during gradual migration. Preserve query timing, allocation, and update semantics. See [Ch 2](ch02-principles.md) for published APIs, [Ch 6](ch06-first-refactorings.md) for declaration migration, [Ch 9](ch09-organizing-data.md) for reference semantics, and [Ch 12](ch12-inheritance.md) for factory-supported hierarchy changes.
