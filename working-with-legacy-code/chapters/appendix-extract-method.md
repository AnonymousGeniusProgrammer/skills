# Appendix: Refactoring — Extract Method

Source: printed pp. 415–419; PDF pp. 438–442.

## Core idea

Move a coherent fragment into a named method while preserving its behavior and connection to the caller. Extract Method creates reusable operations, separates responsibilities, and makes large methods easier to understand and test.

## Procedure with tests in place

1. Select the exact fragment and identify the tests that exercise it. Keep a recoverable comparison with the original; Feathers temporarily comments out the old fragment.
2. Choose an intent-revealing name and create the new method.
3. Put its call where the original fragment executed.
4. Copy the fragment into the method without changing its logic.
5. Inspect variable declarations and data flow. Use compiler errors as an aid to identifying inputs and outputs, while checking for names that still compile through fields or inheritance.
6. Add parameters and a result as needed. Preserve types, passing semantics, conversions, and assignments at the caller.
7. Run the relevant tests and inspect the transformation.
8. Remove the temporary commented copy once the move is verified.

An automated refactoring tool can carry out these mechanics when it supports the language and construct. Verify its result and avoid coupling the extraction with unrelated edits.

## Worked example: premium handling fee

In Feathers's reservation example, amounts below 100 use the base-fee branch. The other branch adds a premium calculation to the result. Extract only the premium expression first:

```java
// Reconstructed example; constants and other methods are omitted.
int getPremiumFee(int amount) {
    return (amount * PREMIUM_RATE_ADJ) + SURCHARGE;
}
```

The caller's premium branch becomes `result += getPremiumFee(amount);`. The branch condition and accumulated result remain with the caller. The helper takes `amount` because it is an input; it returns the calculated contribution because the caller owns the accumulation.

Verify both sides of the original branch, especially the boundary at 100, and the contribution's assignment. A test of `getPremiumFee` alone will not catch a missing call, an inverted branch, or replacing `+=` with an incorrect assignment.

## Extraction checks

| Check | Typical mistake |
|---|---|
| Local values read by the fragment | Missing input silently binds to an instance field |
| Values written and used afterward | New local loses the original output |
| Parameter and result types | Implicit conversion changes range or precision |
| Member lookup and dispatch | New name unintentionally overrides or hides a method |
| Control flow | Early return, loop exit, or exception changes its target or timing |
| Evaluation and side effects | A repeated call is cached, reordered, or evaluated on a different path |

The last control-flow and side-effect checks are implementation guidance extending the appendix's mechanics; apply the actual language rules. If the fragment has several interdependent outputs, reconsider the boundary or use a method object rather than guessing at a new interface.

## Anti-patterns and takeaways

- Avoid extracting and moving to another class in the same weakly covered step.
- Avoid treating compiler silence as proof that all locals were passed correctly.
- Avoid leaving duplicated live implementations or temporary commented bodies after verification.
- Use [Chapter 22](ch22-monster-methods.md) when adequate tests are not yet possible and [Chapter 23](ch23-not-breaking-anything.md) for conservative editing discipline.
