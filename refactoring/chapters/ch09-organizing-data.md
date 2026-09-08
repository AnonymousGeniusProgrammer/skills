# Chapter 9: Organizing Data

Source: printed pp. 239-258; PDF pp. 261-280. Entry locators use printed pages; add 22 for PDF pages.

## Core idea

Give each variable a coherent role, keep an authoritative source for each fact, and choose value or reference semantics according to how collaborators must observe updates.

## Catalog mechanics

### Split Variable — p. 240

**Use when:** one variable is assigned different conceptual roles over its lifetime.

**How:** rename its first declaration and assignment; make it immutable where possible; redirect uses up to the next assignment; test; introduce the next role with its own declaration and repeat.

**Watch:** a loop index or accumulator naturally changes while retaining one role. Do not mechanically split every reassignment. Assigning a parameter to another conceptual value can obscure both roles; preserve the original input under its original meaning.

The source's motion example reuses `acc` for acceleration from an initial force and later for combined forces. Separate `primaryAcceleration` and `secondaryAcceleration`, keeping each use attached to the correct phase of motion.

### Rename Field — p. 244

**Use when:** a stored field or effective property name no longer communicates the data's meaning.

**How:** for a small private record, rename all accesses together. For broad access, Encapsulate Record first; rename the internal storage and adjust internal methods; test; migrate constructor input naming and external accessors as distinct steps.

**Watch:** a private field name, constructor input key, serialized field, and public property are different interfaces. They need not all change at once. Preserve external readers and persisted data according to their actual contracts.

### Replace Derived Variable with Query — p. 248

**Use when:** mutable state duplicates something already calculable from other authoritative data.

**How:** find every update and distinguish independent contributions; Split Variable if needed; implement a query; compare it with the stored result through an assertion at the observation point; test representative updates; migrate readers; delete obsolete storage and updates.

**Watch:** an initial value or external adjustment may not be present in the history used for recomputation. Keep it as an independent source. A cached immutable transform result can be appropriate; recomputation is not a universal requirement. Check equality and rounding when numeric operation order changes.

### Change Reference to Value — p. 252

**Use when:** a contained object is conceptually an immutable value, so updates should replace it instead of changing a shared identity.

**How:** make it immutable; move initialization into construction; replace owner-side mutations with construction of a changed value; remove setters; implement value equality and matching hash behavior where the language requires it.

**Watch:** first inspect aliases. If collaborators must observe mutations through the same reference, this transformation would change behavior. An immutable binding alone does not make a nested object immutable. In JavaScript, defining an `equals` method does not alter `===` or native Map key semantics.

### Change Value to Reference — p. 256

**Use when:** several records represent the same logical entity and updates should be seen consistently through one canonical instance.

**How:** establish an instance repository keyed by stable identity; make it reachable where host objects are built; obtain the related object from it instead of constructing independent copies; migrate construction paths incrementally and test.

**Watch:** repository lifetime and identity scope must fit the application. A global singleton is an example implementation, not the pattern's requirement. Verify behavior for duplicate IDs, missing entities, conflicting copied data, and callers that expected independent snapshots. These are application checks derived from the identity choice.

## Worked example: remove a production accumulator without losing its initial value

The source's ProductionPlan keeps adjustments and an accumulated production value. Summing the adjustments can eliminate a synchronization obligation, but a later example adds a nonzero initial production.

1. List all contributions: initial production and later adjustment amounts.
2. Separate the initial contribution from the running adjustment accumulator.
3. Add a query for the adjustment total and compare it with the old accumulator while existing tests run.
4. Switch the production query to initial production plus calculated adjustments.
5. Remove only the redundant accumulator and its writes. Keep the adjustment list and initial production.

Compact reconstruction of the resulting model:

```javascript
class ProductionPlan {
  constructor(initialProduction) {
    this.initialProduction = initialProduction;
    this.adjustments = [];
  }
  applyAdjustment(adjustment) {
    this.adjustments.push(adjustment);
  }
  get production() {
    return this.initialProduction +
      this.adjustments.reduce((sum, item) => sum + item.amount, 0);
  }
}
```

The snippet abbreviates encapsulation to expose the calculation. Check no adjustments, nonzero initialization, positive and negative adjustments, and repeated updates. If adjustment objects can later mutate, the query may observe a change the old accumulator never saw; establish ownership or preserve that behavior before completing the transformation.

## Value/reference decision table

| Need | Better starting point | Consequence |
|---|---|---|
| Share immutable information safely | Value | Copies are equivalent; replace on change |
| Preserve a historical snapshot | Value or explicit snapshot | Later entity updates do not alter the record |
| Reflect customer updates in all related orders | Reference | Orders share one entity identity |
| Recompute cheaply from mutable authoritative state | Query | Removes synchronization between source and cache |
| Share a prepared result whose inputs remain stable | Immutable transform | Stored derivations need no update protocol |

## Takeaways and connections

Separate semantic roles before changing storage. Establish equivalence before deleting a cache. Treat equality, identity, and update visibility as behavior, not incidental implementation details. See [Ch 6](ch06-first-refactorings.md) for transforms, [Ch 7](ch07-encapsulation.md) for protecting records, [Ch 10](ch10-conditional-logic.md) for assertions, and [Ch 12](ch12-inheritance.md) for separating entity from catalog identity.
