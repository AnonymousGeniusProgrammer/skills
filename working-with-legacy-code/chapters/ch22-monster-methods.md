# Chapter 22: I Need to Change a Monster Method and I Can't Write Tests for It

Source: printed pp. 289–307; PDF pp. 312–330.

## Core idea

Make a monster method testable through small, carefully checked extractions. Separate dependencies, expose observations, and learn the structure before attempting major reorganization.

## Recognize the shape

- **Bulleted method:** a long sequence of chunks, often separated by comments or whitespace. Temporary values can couple chunks despite their visual separation.
- **Snarled method:** deeply nested control flow hides operations behind difficult input conditions. A method can contain both shapes.

For bulleted code, look for a meaningful sequence of operations. For snarled code, initially expose control structure and isolate predicates. These are starting hypotheses, not mutually exclusive prescriptions.

## Choose a starting technique

| Available evidence | Technique | Limit |
|---|---|---|
| A trusted refactoring tool supports the exact extraction | Use tool-supported transformations to create seams | Tool support does not cover surrounding manual edits |
| You can execute the method but cannot easily see an intermediate result | Introduce Sensing Variables | Temporary instrumentation can overfit tests to internals |
| Only a tiny, clearly understood fragment is extractable | Extract What You Know | Low parameter count reduces some errors, not all behavioral risk |
| Critical logic is testable while ancillary dependencies obstruct progress | Gleaning Dependencies | Explicitly leaves some behavior less protected |
| Many local variables make extraction awkward | Break Out Method Object | Requires preserving data flow and connection to the original object |

Sprout Method or Sprout Class can isolate new behavior when dismantling the entire method is unnecessary for the current change.

## Tool-supported extraction

Confirm what the project's refactoring tool actually preserves for this language and code shape. Use it to extract predicates, operations, and external calls. During a phase that relies on the tool's analysis, avoid interleaving unsupported edits such as statement reordering, expression splitting, or manual renaming. Establish tests before such changes.

In Feathers's commodity-panel example, extraction exposes readiness checking, filtering, clearing the display, and displaying one result. Tests can then override the display operations while exercising the actual filtering logic. Later, a `CommodityFilter` can own the selection rules and the panel can retain display responsibilities.

Do not substitute the filtering predicate in the test meant to protect it. Separate the awkward display boundary and sense what the filter sends through it.

## Introduce Sensing Variables

1. Identify the internal event or value needed to judge the next extraction.
2. Add a simple observable variable that records it, with a known initial value. Preserve the original computation and order.
3. Create an input that reaches the event and an input that does not. Verify the observations on the original method.
4. Perform one extraction and rerun those tests.
5. Retain the instrumentation during the local refactoring sequence if it helps compare alternatives.
6. Once extracted behavior has durable tests, remove or refactor temporary sensing tests and variables. Do not discard the only protection for still-changing behavior.

Reconstructed from the DOM-building example:

```text
before: if node satisfies the original predicate:
            add node
            nodeAdded = true

after:  if isBasicChild(node):
            add node
            nodeAdded = true
```

A positive and negative case demonstrate that the extracted predicate remains connected to the original path. They do not exhaust all branches inside the predicate. Add targeted cases for rules being changed, operator precedence, and boundary values. Reset observations between runs; a stale `true` would conceal a missing call.

## Extract What You Know

Feathers suggests starting with about two or three lines, at most five, that can be named confidently. This is a conservative heuristic for manual extraction with weak coverage, not a universal method-length limit.

Compute the **coupling count**: the number of values passed into and out of the extracted method. A maximum calculation taking two values and returning one has count three. Instance-variable accesses are excluded from this count because they do not cross the new method interface, but their side effects still matter.

Prefer small fragments with low coupling counts, especially zero-count commands whose code can be moved intact within the same object. For every parameter and result, inspect its declaration and preserve its type, passing mode, conversion, and use. Consider a sensing variable when a nonzero count leaves uncertainty. Add focused tests for the extracted behavior promptly.

Common failures include overlooking a local value, accidentally using an instance field with the same name, creating an unintended override, swapping arguments, returning the wrong variable, and narrowing a numeric type.

## Gleaning Dependencies

When less critical presentation code is tangled with critical logic, first test the critical behavior. Then extract the surrounding dependencies with those tests checking that the critical behavior survived.

In the entry-addition example, protect the conditions under which entries enter a collection before extracting display updates. This does not establish that the display still works. Identify the remaining check explicitly, such as a focused integration or visual check, and do it. The distinction is relative to the actual project; never assume that UI or logging behavior is inherently unimportant.

## Break Out Method Object

Move the method's work into an object dedicated to that operation. Original parameters initialize the new object; one execution method performs the work. Local values can become fields belonging to this operation, allowing smaller methods and useful observations without adding unrelated transient state to the original class.

Create a fresh operation object for each original invocation unless a separately tested lifecycle change is intended. Preserve initialization, aliasing, output assignment, and exceptions. See the [catalog procedure](ch25-dependency-breaking-techniques.md#break-out-method-object) before editing.

## Choose the resulting structure

| Strategy | Example shape | Prefer when |
|---|---|---|
| Skeletonize Methods | `if (orderNeedsRecalculation(order)) recalculateOrder(order)` | You need to inspect or restructure control flow |
| Find Sequences | `recalculateOrder(order)` contains its own guard | A higher-level sequence explains the operation better |

Both shapes can be useful at different stages. Preserve short-circuit evaluation and the timing of predicate calls during extraction.

Extract to the current class first. An awkward name such as `recalculateOrder` can expose a responsibility without mixing method extraction with cross-class movement. Once tests support the boundary, move the method if that improves ownership. Extract small pieces and be prepared to undo and redo a grouping when it reveals a better structure.

## Anti-patterns and takeaways

- Avoid large extractions based only on blank lines; locals may connect distant chunks.
- Avoid treating a refactoring tool as validation for unrelated manual edits.
- Avoid interpreting coupling count as a purity or safety score: zero-count methods may mutate global state.
- Avoid promoting locals to long-lived shared fields merely to expose them; an operation object may better preserve their lifecycle.
- Avoid keeping temporary sensing APIs indefinitely when behavioral tests can replace them.

## Connects to

- [Chapter 13](ch13-characterization-tests.md): characterization and targeted connection tests.
- [Chapter 20](ch20-large-classes.md): decide where extracted responsibilities belong.
- [Chapter 23](ch23-not-breaking-anything.md): discipline during mechanical edits.
- [Extract Method appendix](appendix-extract-method.md): data-flow and type checks.
