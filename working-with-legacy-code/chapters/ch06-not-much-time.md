# Chapter 6: I Don't Have Much Time and I Have to Change It

Source: printed pp. 57-76; PDF pp. 80-99.

## Core idea

First try to put the relevant existing class under test; the obstacle may be smaller than expected. If that cannot fit the change, place new behavior in a tested method or class and make a small, explicit connection to the old code. Tests for the addition do not automatically cover that connection.

## Choose a technique

| Situation | Technique | Structural result |
|---|---|---|
| A distinct new step fits within an existing algorithm | **Sprout Method** | Existing method calls a tested new method |
| The original class cannot be instantiated or compiled easily, or the addition is a new responsibility | **Sprout Class** | A separate tested object supplies the new behavior |
| New work belongs before/after the existing operation | **Wrap Method** | A coordinating method invokes new work and the old body |
| Added behavior is independent, should apply through an object boundary, or would further overload a large class | **Wrap Class** | A tested wrapper delegates to the original object |

Sprouting inserts a new step into the old algorithm. Wrapping makes the old operation one step in a larger algorithm. Pick by that relationship, not by a preference for more methods or classes.

## Sprout Method

1. Find the insertion point and sketch a descriptive call there.
2. Determine required local inputs and the result the caller needs back.
3. Develop the new method under tests, without first intermingling it with the old code.
4. Enable the call and verify the inputs, result, and position in the old algorithm.

If construction prevents testing, a parameterized static method can be a temporary staging point for independent logic. Several such methods with shared inputs may later reveal a class. Keep the purpose and exit path clear; adding a static global dependency would undermine this benefit.

**Why it works:** the new decision is separately testable, and its interface exposes how it relates to the legacy method. **Cost:** the original method remains unprotected and may look uneven, with one extracted responsibility amid tangled code.

## Worked example: filter entries before posting

Reconstruction of `TransactionGate.postEntries`: the old code posts dates for incoming entries and adds them to a bundle. The addition should exclude entries already present in that bundle.

Develop `uniqueEntries(incoming)` separately, with cases for an empty input, all-new entries, and entries already in the bundle. Then connect it:

```java
List<Entry> entriesToAdd = uniqueEntries(entries);
for (Entry entry : entriesToAdd) {
    entry.postDate();
}
transactionBundle.getListManager().add(entriesToAdd);
```

This is a compact adaptation of the source example. Confirm what "unique" means here: the source checks existing bundle membership; it does not by itself establish deduplication among repeated values in the incoming batch. Preserve the required semantics rather than inferring them from the method name.

The helper tests verify filtering. The connection still needs evidence that dates are posted only on the filtered entries and that the filtered collection reaches `add`. Inspect or test that route at the closest feasible boundary.

## Sprout Class

1. Identify the isolated new responsibility or calculation.
2. Sketch construction and invocation at the insertion point.
3. Pass the necessary values through a constructor or method boundary.
4. Expose the result the old method needs.
5. Develop the class under tests, then connect it to production.

In the source's quarterly-report example, a difficult report generator needs an HTML header row. A small tested header generator supplies the string. It can later become one member of a family of HTML generators, but that later design is optional. The immediate justification is a tested addition with limited intrusion.

**Trade-off:** a small new class may initially look disproportionate or fragment an existing abstraction. Prefer a responsibility-revealing name and revisit its role as related changes occur. In C++, keeping a new dependency in an implementation file can also avoid expanding an existing header's compilation burden.

## Wrap Method

There are two forms:

- **Preserve the old entry point:** rename the old body, keep the original public signature on a new wrapper, and have that wrapper invoke the new tested behavior and the old operation.
- **Introduce an opt-in entry point:** leave the old method as it is and add a new method that invokes both operations. Update only callers intended to acquire the new behavior.

The source's payment example wraps a payment operation with logging. Choose the order deliberately. Logging before payment differs from logging after success; exception behavior and repeat calls also matter. The wrapper's contract must specify the intended sequence.

A wrapper works only when the addition can sit around the old operation. A change interwoven with an internal loop needs another seam or direct protected editing. Renaming the old body can expose a naming problem: a method called `dispatchPayment` may still calculate the amount. Further extraction can improve this after protection exists.

## Wrap Class

1. Create a wrapper that accepts the old object through an appropriate interface or abstract boundary.
2. Test the independent new behavior using a controllable wrapped collaborator.
3. Delegate the old operation and preserve any other operations the wrapper promises.
4. Instantiate the wrapper at the places intended to acquire the behavior.

Use a **Decorator** when callers should use the wrapper through the same interface as the wrapped object. Use a plainly named coordinator when only a few call sites need a combined action. Multiple decorator layers can compose optional behavior, but they also make execution order and navigation harder to understand.

## Judgment under time pressure

The chapter's choice is not a guarantee that tests always pay back within the current edit. Their benefits include later changes, understanding, and fault localization. State the trade-off for the actual change: protection obtained, integration still untested, and any temporary structural compromise.

A wrapper or sprout is a way to deliver tested new behavior while reducing the size of the initial incision. Repeatedly using it should eventually make the old area easier to approach, rather than becoming a reason to abandon that area permanently.

## Connects to

- [Ch 8](ch08-adding-features.md): develop new behavior and then integrate its design.
- [Ch 9](ch09-class-into-test-harness.md): try construction before assuming it is too expensive.
- [Ch 20](ch20-large-classes.md): move from temporary sprouts to explicit responsibilities.
