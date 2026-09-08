# Chapter 7: Error Handling

Source: printed pp. 103–112; PDF pp. 134–143. Michael Feathers.

## Core Idea

Design error handling so that it preserves a clear normal path and gives callers a usable failure contract. A tidy happy path is incomplete if failure leaves resources, state, or recovery ambiguous.

## Frameworks Introduced

### Use Exceptions Rather Than Return Codes

In the book's object-oriented setting, use exceptions to separate normal processing from error propagation when status checks dominate the algorithm. Keep recovery at a boundary that can act meaningfully.

This is a preference within the source's language context. For a language or project built around explicit result/error values, retain that idiom while applying the same goal: make both successful and failed outcomes explicit without burying domain behavior.

### Write Your Try-Catch-Finally Statement First

Use this when an operation can fail after acquiring resources or partially changing state.

1. Identify the externally visible guarantee required after success or failure.
2. Establish acquisition, normal work, cleanup, and recovery boundaries.
3. Start with a failing scenario that exercises the boundary.
4. Implement the smallest handling that satisfies it.
5. Add normal behavior while preserving cleanup and state guarantees.

Why it works: the error path shapes the operation instead of being appended after it is difficult to accommodate. A `try` block does not itself provide a database transaction or rollback; identify the mechanism that actually preserves the guarantee.

### Define Exception Classes in Terms of a Caller's Needs

Use this when several vendor exceptions lead to the same recovery behavior.

1. Group failures by what the caller can do about them.
2. Translate vendor-specific failures at an adapter boundary.
3. Keep relevant cause and operation context in the translated error.
4. Expose distinct types when callers genuinely handle them differently.

The chapter's `ACMEPort` example wraps multiple device-specific exceptions as one port failure. That reduces repeated catch logic and the dependency on the vendor's taxonomy. Preserve distinctions when retry, rejection, or escalation differ.

### Define the Normal Flow: Special Case Pattern

Use this when an apparently exceptional branch is actually a normal domain alternative.

1. Decide whether absence has a valid domain meaning.
2. Represent that meaning with an object or result that supports the ordinary operation.
3. Let clients use the common contract.
4. Keep real failures distinct from the normal alternative.

The chapter's meal-expense example treats “no claimed meal expense” as entitlement to a per-diem amount. A special-case expense object returns that amount. A failed database query, by contrast, is not evidence of no expenses.

### Avoid Unspecified Null Contracts

The book recommends exceptions or special-case objects instead of returning null, and an empty collection when “no items” is the valid result. Avoid passing null unless the API explicitly expects it.

Adapt this to the project's language using explicit optional/absence types where appropriate. That adaptation should be labeled when explaining the book: the underlying lesson is to give absence a defined meaning, not to replace every missing value with an arbitrary default.

## Worked Example

Reconstructing the meal-expense decision:

```java
MealExpenses expenses = expenseReports.mealsFor(employeeId);
total = total.plus(expenses.total());
```

The interface guarantees a valid expense representation after a successful lookup. For claimed meals, it returns their total; for the ordinary no-claim case, it returns the per-diem representation. This rewritten fragment omits the book's concrete types and implementations.

Test three different outcomes:

| Condition | Expected behavior | Why it matters |
|---|---|---|
| Valid expense record exists | Use the claimed amount | Preserve ordinary behavior |
| Successful lookup finds no claim | Use the defined per-diem amount | Verify the special case's domain meaning |
| Lookup fails | Propagate a meaningful failure | Prevent infrastructure failure being mistaken for absence |

The gain is a clearer normal algorithm with one authoritative definition of the special case. The cost is another type or explicit representation; use it when it removes repeated domain branching without concealing failure.

## Checked Exception Trade-off

The chapter favors unchecked exceptions for ordinary application development because checked exceptions can force declarations through otherwise unrelated calling layers. It also recognizes a potential benefit for critical library contracts. Present that as the author's trade-off, not a proof that every checked exception is wrong. Public API compatibility and a project's established error model matter during actual changes.

## Key Concepts

- **Exception boundary**: the place that translates, handles, or deliberately propagates failure.
- **Caller-oriented taxonomy**: error categories based on distinct recovery needs.
- **Special Case Pattern**: an ordinary interface implementation that encapsulates a defined exceptional-looking case.
- **Normal flow**: the domain algorithm without repeated incidental error plumbing.
- **Checked exception**: an exception participating in a Java method's declared contract.
- **Failure context**: the operation, cause, and relevant circumstances needed to interpret an error.
- **Empty collection**: a valid no-items result, distinct from failure to obtain the collection.

## Mental Models

- Classify errors by the decision the recipient can make.
- Separate domain absence from technical failure before simplifying branches.
- Treat cleanup and partial progress as part of the operation's contract.

## Anti-patterns

- **Swallow and continue**: logging alone does not restore a valid state.
- **Vendor taxonomy everywhere**: many clients repeat the same exception translation.
- **Null propagation**: each layer shifts interpretation and checking onto another caller.
- **Defaulting on failure**: replacing an unavailable result with zero or an empty list can falsify business facts.
- **Redundant catch blocks**: identical recovery is copied for exceptions the client does not need to distinguish.

## Key Takeaways

1. Define what remains true when an operation fails.
2. Translate external failures into a caller-usable contract.
3. Model valid special cases explicitly.
4. Keep absence and failure distinguishable.
5. Follow the language's error idiom while retaining clear boundaries.

## Connects To

- [Chapter 3](ch03-functions.md): separating orchestration and recovery.
- [Chapter 8](ch08-boundaries.md): wrappers and adapter tests.
- [Chapter 14](ch14-successive-refinement.md): separating parser errors into `ArgsException`.
