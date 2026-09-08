# Chapter 15: JUnit Internals

Source: printed pp. 251–266; PDF pp. 282–297. Robert C. Martin. Case study: JUnit's `ComparisonCompactor`.

## Core Idea

Even well-structured code can become clearer through careful naming, boundary analysis, and separation of computation from assembly. Refactoring is exploratory: later evidence can justify reversing an earlier extraction.

## Frameworks Applied

### Start from Executable Examples

Use this when a compact algorithm has subtle edge cases. Read its tests before rearranging it, then inspect the algorithm's invariants.

The source's compactor highlights differences between expected and actual strings while retaining a chosen amount of surrounding context. The test suite covers nulls, equality, context lengths, and overlapping prefix/suffix matches. The author reports complete execution coverage for the original suite. Coverage provides useful confidence, not proof that every possible contract has been tested.

### Name Values for Their Actual Meaning

1. Remove redundant scope encodings when the project does not need them.
2. Resolve local/field name collisions with semantic distinctions.
3. Distinguish an index from a length and an input string from a compacted string.
4. Name the public operation for the formatted comparison it returns, not merely one internal step.

The chapter moves from ambiguous `expected` locals to compacted-result names and from suffix “index” arithmetic to an actual suffix length.

### Make Temporal Coupling Structural

The suffix calculation must respect the prefix already found, so the two do not overlap.

1. Identify the prerequisite value and why it is needed.
2. Express the dependency in a contract or orchestration that makes invalid order difficult.
3. Evaluate whether a parameter clarifies the relationship or is only an unexplained ordering trick.
4. Keep the requirement apparent in the final flow.

The author tries passing the prefix index, then decides a combined prefix-and-suffix analysis operation explains the dependency more clearly. The durable lesson is to reveal the reason for the order, not a blanket preference for fields or parameters.

### Encapsulate Boundary Conditions

Use this when repeated `+1` and `-1` terms reveal a representation mismatch.

1. State whether each value is an index, count, or exclusive bound.
2. Choose a consistent representation.
3. Centralize conversion where it is meaningful, such as accessing a character from the end.
4. Recheck zero-length, complete-match, and overlap cases.
5. Remove branches made unnecessary by the clearer representation only after verifying their role.

Why it works: boundary arithmetic becomes local and explainable. Failure mode: renaming an index “length” without changing its arithmetic makes the code more misleading.

### Separate Analysis from Synthesis

First determine common prefix/suffix lengths. Then compose the formatted result from context and the differing middle. Present these operations in a readable dependency order.

## Worked Example

The source includes expected `abc` and actual `adc`, with one character of context. The conceptual result shows `a[b]c` versus `a[d]c`.

Reason through it:

1. The shared prefix has length one: `a`.
2. The shared suffix has length one: `c`.
3. The differing slices are `b` and `d`.
4. The configured context allows the one shared character on either side.
5. Format those components into the comparison message.

Now inspect expected `abc` and actual `abbc`. Prefix matching can extend through `ab`; suffix matching must stop before it overlaps that prefix. One side then contains an empty differing region and the other contains `b`. This is why suffix computation depends on prefix analysis.

Illustrative pseudocode:

```text
if either input is null or both inputs are equal:
    format the original pair
else:
    find a common prefix
    find a common suffix that does not overlap the prefix
    compact each differing middle with bounded context
    format the compacted pair
```

The pseudocode captures the contract and dependency without reproducing the full source implementation.

## Review Table

| Change | Reason | Regression to check |
|---|---|---|
| Rename fields and locals distinctly | Prevent input/output ambiguity | Both formatted sides still use the correct input |
| Name the compaction condition | Make the branch's purpose visible | Null and equal-string handling |
| Convert suffix index to length | Remove repeated offset adjustments | Empty suffix and fully shared suffix |
| Combine dependent analysis | Enforce the no-overlap prerequisite | Prefix extending near or to a string boundary |
| Remove redundant conditionals | Simplify unconditional assembly | Zero context and no common prefix/suffix |
| Inline an unhelpful earlier extraction | Improve the final narrative | Same outcomes across the existing suite |

## The Reversals Matter

The final implementation reverses some earlier decisions, including extraction choices and predicate direction. These are not mistakes to hide in a study guide. They demonstrate that a refactoring is evaluated by the resulting code, and an experiment can be undone when a later arrangement reads better.

The source also notices that an old suffix branch was effectively nonfunctional because its previous representation could never make the condition false. The improvement exposed that fact; it should not be described as a newly proven user-visible defect without corresponding evidence.

## Key Concepts

- **Comparison compaction**: displaying a differing substring with bounded shared context.
- **Prefix/suffix overlap**: double-counting shared characters from opposite directions.
- **Boundary normalization**: using a consistent interpretation of indexes and lengths.
- **Temporal coupling**: suffix analysis depends on prefix analysis.
- **Analysis/synthesis split**: compute descriptive facts, then assemble output.
- **Refactoring experiment**: a reversible structural change assessed through behavior and clarity.

## Mental Models

- Treat names as hypotheses about what values mean; verify them against arithmetic.
- Express the prerequisite that makes an operation correct.
- Judge the final reading path rather than preserving each intermediate extraction.

## Anti-patterns

- Treating famous library code as beyond review.
- Equating coverage with exhaustive correctness.
- Replacing clear locals with mutable fields only to reduce arguments.
- Adding unexplained parameters to force order without expressing the dependency.
- Retaining a helper after its conceptual purpose disappears.

## Key Takeaways

1. Read tests and invariants before changing a compact algorithm.
2. Normalize bounds and name lengths accurately.
3. Make sequencing dependencies visible.
4. Accept reversals when they improve the final design.

## Connects To

- [Chapter 3](ch03-functions.md): extraction and the Stepdown Rule.
- [Chapter 17](ch17-smells-and-heuristics.md): G11, G28–G34, and naming heuristics.
- [Appendix C](appendix-c-cross-references.md): finding the source's heuristic annotations.
