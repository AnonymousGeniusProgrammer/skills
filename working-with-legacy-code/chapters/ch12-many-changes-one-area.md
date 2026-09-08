# Chapter 12: I Need to Make Many Changes in One Area. Do I Have to Break Dependencies for All the Classes Involved?

Source: printed pp. 173-184; PDF pp. 196-207.

## Core idea

Several related change points can sometimes be protected through one nearby boundary. Find an **interception point** where an effect is observable, or a **pinch point** where effects from many relevant changes converge into a small set of observations.

## Select the boundary

1. Enumerate the proposed changes before searching for a convenient test.
2. Trace each change's effects as in [Ch 11](ch11-what-methods-to-test.md).
3. Identify accessible observations along those paths.
4. Prefer a close public observation when it gives the necessary coverage economically.
5. Consider a shared, higher-level boundary when it substantially reduces dependency breaking for the whole cluster.
6. Check that every relevant outgoing effect reaches one of the chosen observations.
7. Verify that the selected cases can actually distinguish the changes that might go wrong.

A pinch point is relative to a set of changes, not an intrinsic label for a class. It can consist of a couple of methods, and it need not be the system's highest-level API.

## Worked example: invoice and statement

Reconstruction of the chapter's billing example:

- For a shipping-calculation change inside `Invoice`, `getValue()` is a close useful interception point. Checking only a new `ShippingPricer` object would miss incorrect connection into the invoice total.
- If the work also changes `Item.shippingCarrier` and per-carrier statement formatting, `BillingStatement.makeStatement()` may cover all those effects through one result.
- An unrelated inventory use of `Item.needsReorder()` does not invalidate that choice if the shipping change cannot affect it.
- If a new supplier field affects both billing and inventory, the statement no longer observes every effect. Add an inventory observation; the pair may still be a useful narrowing compared with testing every intermediate variable independently.

Start with a small, characterized invoice-and-item arrangement, then vary the dimensions that distinguish the affected calculations and formatting. Preserve this behavior while extracting the shipping responsibility. Add desired new behavior in separate cases.

## Compare candidates

| Candidate | Benefit | Limitation |
|---|---|---|
| Internal value or newly extracted helper | Small and easy to inspect | May miss whether callers use it correctly |
| Public method on the changed class | Short effect path and useful client contract | Can be expensive to instantiate |
| Public method on a containing cluster | Covers several changes and permits internal restructuring | More setup, slower execution, less precise failures |
| System boundary | Broad integration evidence | Often distant, fragile, and hard to drive through a particular path |

Each extra step between change and observation adds an assumption to the argument that the test protects the change. If that argument is unclear, temporarily alter the change point in a controlled way and check whether the test detects it, then restore the code. Execution coverage alone is weaker than detecting a relevant difference.

## Design insight

A pinch point can reveal a natural encapsulation boundary. In the parser example, token-related methods hide a current position and the string being parsed. Grouping those fields and methods suggests a `Tokenizer` collaborator even before a formal class exists.

Temporarily disregard names in an effect sketch and inspect clusters. Then name the responsibility inside a useful boundary and verify it against the domain. The graph suggests a candidate; it does not prove the correct abstraction.

## Pinch Point Traps

Higher-level cover is a foothold for safer work. It can become a trap if every future test uses the entire cluster and its setup grows indefinitely.

As classes become separable, add smaller focused tests for their responsibilities. Retire a covering test only when its useful evidence is supplied elsewhere or no longer matters. Some integration observations remain valuable; smaller tests do not automatically replace all collaboration checks.

If no useful pinch point exists, reduce the change set or use several close observations. Forcing all effects through one test can produce a worse suite than acknowledging separate paths.

## Apply it

Record the set of changes, their observation paths, the selected boundary or boundaries, and the effects that remain outside cover. Begin refactoring only to the extent supported by those tests and other available evidence.

## Connects to

- [Ch 11](ch11-what-methods-to-test.md): build effect sketches.
- [Ch 13](ch13-characterization-tests.md): choose discriminating inputs and assertions.
- [Ch 20](ch20-large-classes.md): turn hidden boundaries into explicit responsibilities.
