# Techniques and patterns

See linked chapters for examples and qualifications.

## Diagnose complexity through a change

**When to use**: A change is unexpectedly difficult.
**How**: Identify amplified edits, required knowledge, and undiscoverable obligations. Trace them to dependencies or obscurity; compare an alternative on the same task.
**Trade-offs**: Some dependencies are necessary. Prefer making them simple and explicit when they cannot be removed. [Ch 2](chapters/ch02-nature-of-complexity.md)

## Design a deep interface

**When to use**: Creating or reviewing a module boundary.
**How**: Describe the caller's capability, enumerate formal and informal obligations, and identify decisions hidden by the implementation. Simplify common usage with complete operations and useful defaults.
**Trade-offs**: More internal responsibility helps only when it belongs to the abstraction. Essential guarantees stay visible. [Ch 4](chapters/ch04-deep-modules.md), [Ch 8](chapters/ch08-pull-complexity-downwards.md)

## Localize shared knowledge

**When to use**: Modules share format or representation knowledge.
**How**: Identify an owner; combine tightly related pieces or create an abstraction that hides the decision. Check whether changing that decision now stays local.
**Trade-offs**: A facade that exposes the same details only relocates leakage. Runtime stages need separate modules only when knowledge boundaries support them. [Ch 5](chapters/ch05-information-hiding.md)

## Generalize current operations

**When to use**: An API mirrors individual features.
**How**: Replace specialized operations with a few reusable primitives at a useful level, such as text ranges. Keep current usage straightforward and policy with its caller.
**Trade-offs**: Excessive generalization demands glue or unused machinery. Fewer methods with more flags may be worse. [Ch 6](chapters/ch06-general-purpose-modules.md)

## Remove empty forwarding layers

**When to use**: Adjacent methods mostly forward calls.
**How**: State each layer's abstraction. Expose the true owner, redistribute responsibilities, or merge inseparable layers. Evaluate decorator alternatives similarly.
**Trade-offs**: Dispatch and distinct implementations can justify repeated signatures through useful behavior. [Ch 7](chapters/ch07-layers-and-abstractions.md)

## Replace pass-through state deliberately

**When to use**: Values traverse methods that do not use them.
**How**: Use a natural shared owner or a per-instance context supplied during construction to objects that need it.
**Trade-offs**: Context can hide dependencies or become a shared-state grab bag. Favor coherent, preferably immutable state; preserve independent instances. [Ch 7](chapters/ch07-layers-and-abstractions.md)

## Choose defaults and adaptive behavior

**When to use**: Callers must configure implementation mechanics.
**How**: Ask who can choose best. Compute a value internally when the module has enough information; otherwise provide a meaningful caller control and a useful default where possible.
**Trade-offs**: Hiding a setting users need can make the abstraction unusable. [Ch 5](chapters/ch05-information-hiding.md), [Ch 8](chapters/ch08-pull-complexity-downwards.md)

## Extract an independently understandable subtask

**When to use**: Considering splitting a long method or repeated block.
**How**: Create a child whose contract lets readers understand the parent without reading the child, and vice versa. Alternatively restructure repeated work to occur once.
**Trade-offs**: Conjoined helpers and complex parameter lists can cost more than the extraction saves. Length alone is insufficient. [Ch 9](chapters/ch09-together-or-apart.md)

## Separate mechanism, action, and grouping policy

**When to use**: A reusable mechanism contains use-specific knowledge.
**How**: In undo/redo, let history manage ordering and traversal, action implementations manage their own reversal, and higher-level code set group boundaries.
**Trade-offs**: Keep text-specific actions with text knowledge. Separation is relative to the mechanism, not a demand for an isolated class per tiny concern. [Ch 9](chapters/ch09-together-or-apart.md)

## Broaden semantics to remove a special case

**When to use**: A special case could be an ordinary outcome.
**How**: Define the desired postcondition, such as ensuring absence. Use normal representations such as an empty range when ordinary operations handle them correctly.
**Trade-offs**: Preserve distinctions required by callers. A broader contract is different from discarding failures under the old one. [Ch 10](chapters/ch10-define-errors-out-of-existence.md)

## Consolidate recovery and request handling

**When to use**: Recovery is duplicated or misplaced.
**How**: Recover locally where possible; otherwise aggregate common responses at an appropriate boundary. A request-abort boundary cleans up one request and continues processing. Rare failures may reuse a broader existing recovery mechanism.
**Trade-offs**: Distinguish request failure from fatal failure. Error promotion can be expensive; masking must preserve the promised behavior and necessary failure information. [Ch 10](chapters/ch10-define-errors-out-of-existence.md)

## Design it twice with comments

**When to use**: Choosing a meaningful interface or implementation design.
**How**: Sketch different alternatives, write their contracts, and trace representative use. Compare exposed knowledge, generality, and implementation cost before choosing or combining.
**Trade-offs**: Sketches usually suffice. Short comments indicate simplicity only when complete and clear. [Ch 11](chapters/ch11-design-it-twice.md), [Ch 15](chapters/ch15-comments-first.md)

## Document precision, intuition, and shared decisions

**When to use**: Readers need facts missing from declarations or purpose hidden in implementation.
**How**: Specify units, boundaries, ownership, and invariants; explain block-level intent and rationale. Give each cross-module decision one discoverable home and local references.
**Trade-offs**: Keep contracts separate from mechanism. Avoid duplicating information readers can already find at its authoritative location. [Ch 13](chapters/ch13-comments-and-abstractions.md), [Ch 16](chapters/ch16-modifying-existing-code.md)

## Use naming and reader feedback as design tests

**When to use**: Names are vague, generic containers conceal meaning, or a reviewer guesses incorrectly.
**How**: Identify the missing conceptual distinction. Choose precise, consistent names or a named representation. Document non-obvious callback or lifecycle conditions.
**Trade-offs**: Follow established meanings and match detail to scope; long names and forced uniformity can also confuse. [Ch 14](chapters/ch14-choosing-names.md), [Ch 17](chapters/ch17-consistency.md), [Ch 18](chapters/ch18-obvious-code.md)

## Refactor with behavioral feedback

**When to use**: A new requirement strains the current abstraction, or a bug needs correction.
**How**: Compare a design incorporating the requirement from the outset with feasible alternatives. Use existing tests to check behavior; for a bug, first demonstrate the failure with a regression test. Update relevant contracts and rationale.
**Trade-offs**: Respect compatibility and time constraints. Tests aid refactoring without determining design quality by themselves. [Ch 16](chapters/ch16-modifying-existing-code.md), [Ch 19](chapters/ch19-software-trends.md)

## Measure and simplify the common path

**When to use**: A measured performance problem remains after naturally efficient design.
**How**: Record a baseline, locate the costly work, seek a structural fix, then sketch the minimal common operation if local redesign is necessary. Reorganize special cases and measure again.
**Trade-offs**: Preserve all required behavior and other common operations. Retain added complexity only for a demonstrated benefit. [Ch 20](chapters/ch20-performance.md)
