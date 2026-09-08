# Chapter 17: Smells and Heuristics

Source: printed pp. 285–316; PDF pp. 316–347. Robert C. Martin.

## Core Idea

Use the catalog as a vocabulary for diagnosing concrete maintenance problems. A smell starts an investigation; its presence alone does not prove a defect or prescribe a rewrite.

## How to Apply the Catalog

1. Identify the observed behavior, comprehension cost, or change dependency.
2. Select the relevant heuristic and inspect the code that establishes it.
3. State the practical consequence and the smallest useful improvement.
4. Check competing guidance and project constraints.
5. Verify behavior and explain any intended contract change.

For review findings, prefer “This helper updates the session although its name promises only validation; callers can erase state during a check” over “Violates N7.” The ID is a reference, not the argument.

The tables preserve all **66 source identifiers and names**. Their actions are synthesized rather than copied definitions.

## Comments — C1–C5

| ID | Source name | Decision-ready interpretation |
|---|---|---|
| C1 | Inappropriate Information | Keep implementation rationale in source; place change history and issue records in their appropriate systems. |
| C2 | Obsolete Comment | Update or remove claims that no longer describe the implementation. |
| C3 | Redundant Comment | Remove repetition when code already states the same fact clearly. |
| C4 | Poorly Written Comment | Make a useful comment accurate, concise, and understandable at its location. |
| C5 | Commented-Out Code | Use version history for obsolete implementations; remove unexplained inactive source. |

Read [chapter 4](ch04-comments.md) for useful comments that should remain.

## Environment — E1–E2

| ID | Source name | Decision-ready interpretation |
|---|---|---|
| E1 | Build Requires More Than One Step | Offer a predictable build entry point that orchestrates the necessary internal steps. |
| E2 | Tests Require More Than One Step | Make the relevant test suite straightforward to invoke and interpret. |

These concern usable entry points. A build may internally contain many stages; the smell is an undocumented ritual the operator must reconstruct.

## Functions — F1–F4

| ID | Source name | Decision-ready interpretation |
|---|---|---|
| F1 | Too Many Arguments | Inspect independence, grouping, and ownership of inputs; reduce interpretation burden without hiding dependencies. |
| F2 | Output Arguments | Prefer explicit return values or an owning object's clearly named mutation. |
| F3 | Flag Arguments | Split distinct selected operations when doing so clarifies the call. |
| F4 | Dead Function | Remove genuinely unused behavior after checking its full relevant caller/contract surface. |

Read [chapter 3](ch03-functions.md) for argument trade-offs and abstraction levels.

## General — G1–G36

| ID | Source name | Decision-ready interpretation |
|---|---|---|
| G1 | Multiple Languages in One Source File | Reduce confusing embedded-language boundaries where the framework allows; keep intentional templates coherent. |
| G2 | Obvious Behavior Is Unimplemented | Compare reasonable expectations with the established contract before declaring a defect or expanding behavior. |
| G3 | Incorrect Behavior at the Boundaries | Verify equality, empty inputs, limits, transitions, and error cases explicitly. |
| G4 | Overridden Safeties | Investigate warnings and failing checks instead of suppressing their evidence for convenience. |
| G5 | Duplication | Give repeated knowledge, selection, or algorithm structure one appropriate implementation. |
| G6 | Code at Wrong Level of Abstraction | Separate general concepts from representation-specific details in the units that own them. |
| G7 | Base Classes Depending on Their Derivatives | Keep general contracts independent of concrete implementations unless the deliberately closed design requires coupling. |
| G8 | Too Much Information | Keep interfaces small and useful; expose what consumers need without publishing incidental internals. |
| G9 | Dead Code | Remove unreachable or unused paths when their lack of required behavior is established. |
| G10 | Vertical Separation | Keep related definitions and uses close enough to read together. |
| G11 | Inconsistency | Use the same conventions for semantically equivalent operations and values. |
| G12 | Clutter | Remove artifacts that add no behavior, guarantee, or useful explanation. |
| G13 | Artificial Coupling | Relocate reusable concepts that are unnecessarily nested in or attached to unrelated specific modules. |
| G14 | Feature Envy | Consider moving behavior to the concept whose data and responsibilities it primarily uses. |
| G15 | Selector Arguments | Recognize non-Boolean selectors too; a mode code can hide several operations behind one name. |
| G16 | Obscured Intent | Replace dense expressions, obscure encodings, and unexplained values with explicit concepts. |
| G17 | Misplaced Responsibility | Put behavior where a reader expects to find its policy and ownership. |
| G18 | Inappropriate Static | Use instance behavior when it belongs to an object or must vary polymorphically; retain legitimate stateless operations. |
| G19 | Use Explanatory Variables | Name meaningful intermediate results to expose an algorithm's reasoning. |
| G20 | Function Names Should Say What They Do | State the action, units, and mutation/value-return distinction at the call site. |
| G21 | Understand the Algorithm | Explain why the implementation works, including boundaries; passing examples alone are insufficient understanding. |
| G22 | Make Logical Dependencies Physical | Obtain assumptions such as limits or origins through the owner’s explicit contract. |
| G23 | Prefer Polymorphism to If/Else or Switch/Case | Consider centralized construction and dispatch for repeated type selection; weigh the data/object change trade-off. |
| G24 | Follow Standard Conventions | Use consistent project conventions for names, organization, and formatting. |
| G25 | Replace Magic Numbers with Named Constants | Name values whose meaning is hidden; retain transparent literals in clear formulas. |
| G26 | Be Precise | Make assumptions about uniqueness, units, ranges, absence, rounding, and concurrent updates explicit. |
| G27 | Structure over Convention | Prefer types and interfaces that enforce a useful invariant over a rule everyone must remember manually. |
| G28 | Encapsulate Conditionals | Give a domain condition a meaningful name when it clarifies the decision. |
| G29 | Avoid Negative Conditionals | Prefer an affirmative predicate when it reduces mental negation and fits the flow. |
| G30 | Functions Should Do One Thing | Express one coherent operation through immediate conceptual steps. |
| G31 | Hidden Temporal Couplings | Expose required ordering through data flow, contract, or an operation that owns the sequence. |
| G32 | Don't Be Arbitrary | Make organization choices that have an intelligible reason beyond temporary convenience. |
| G33 | Encapsulate Boundary Conditions | Give limits a consistent representation and centralize meaningful boundary arithmetic. |
| G34 | Functions Should Descend Only One Level of Abstraction | Keep sibling steps at the same level and details in the next layer. |
| G35 | Keep Configurable Data at High Levels | Define configurable policy near system configuration and pass it down to users. |
| G36 | Avoid Transitive Navigation | Let immediate collaborators provide the needed operation instead of exposing an internal graph. |

### Important Qualifications

- **G2**: “obvious” is not a substitute for requirements or compatibility analysis. The SerialDate study itself leaves some expectations unresolved.
- **G7**: the source allows deliberately fixed hierarchies, such as a tightly coupled finite-state-machine implementation.
- **G18**: the source uses `Math.max` as a valid static operation. Prefer meaningful ownership, not objects created solely to invoke a stateless calculation.
- **G23**: Martin's **One Switch** rule concentrates a particular type selection in a factory, with behavior dispatched polymorphically elsewhere. [Chapter 6](ch06-objects-and-data-structures.md) explicitly gives circumstances where procedural operations over data fit better.
- **G25**: the source explicitly permits some transparent literals. A constant named `TWO` adds no information to a clear formula.
- **G26**: choose a representation and contract appropriate to exact monetary amounts or other precision-sensitive values; do not extrapolate a single source example into all numerical computing.
- **G36**: distinguish behavioral objects from intentionally exposed data and fluent operations; punctuation alone is not evidence.

## Java — J1–J3

| ID | Source name | Decision-ready interpretation |
|---|---|---|
| J1 | Avoid Long Import Lists by Using Wildcards | Martin favors wildcard imports for brevity. Treat this as a source preference; follow the actual project's import policy. |
| J2 | Don't Inherit Constants | A constants-only relationship is not behavioral inheritance; give constants an appropriate named home. |
| J3 | Constants versus Enums | Use a domain enum for a closed set of meaningful alternatives when the language and API support it. |

Using wildcard imports does not remove dependencies on the concrete classes the implementation actually uses. Preserve this distinction from the source's broader coupling rhetoric.

## Names — N1–N7

| ID | Source name | Decision-ready interpretation |
|---|---|---|
| N1 | Choose Descriptive Names | Revisit names as meaning and responsibilities become clearer. |
| N2 | Choose Names at the Appropriate Level of Abstraction | Name the contract's concept rather than one implementation technique. |
| N3 | Use Standard Nomenclature Where Possible | Use established pattern, language, and domain terms accurately. |
| N4 | Unambiguous Names | Distinguish roles so a reader does not need to infer which meaning applies. |
| N5 | Use Long Names for Long Scopes | Wider scope needs more explicit context than a short local loop. |
| N6 | Avoid Encodings | Prefer semantic meaning over redundant type, scope, or application prefixes. |
| N7 | Names Should Describe Side-Effects | Reveal initialization, caching, mutation, and other effects that a caller must understand. |

Read [chapter 2](ch02-meaningful-names.md) for vocabulary and scope decisions.

## Tests — T1–T9

| ID | Source name | Decision-ready interpretation |
|---|---|---|
| T1 | Insufficient Tests | Look for unexplored behavior and unverified calculations rather than stopping at an intuitive test count. |
| T2 | Use a Coverage Tool! | Use execution gaps to guide investigation; coverage does not establish correct assertions. |
| T3 | Don't Skip Trivial Tests | The source values cheap examples with useful documentary value; select tests that actually verify behavior. |
| T4 | An Ignored Test Is a Question about an Ambiguity | Track and resolve the unanswered contract question rather than normalizing ignored failures. |
| T5 | Test Boundary Conditions | Exercise the edges where intuitive algorithms often fail. |
| T6 | Exhaustively Test Near Bugs | Expand around a discovered defect to find related errors in the same reasoning. |
| T7 | Patterns of Failure Are Revealing | Group failing examples by input shape to infer the mistaken assumption. |
| T8 | Test Coverage Patterns Can Be Revealing | Compare executed and unexecuted branches with failing-case patterns. |
| T9 | Tests Should Be Fast | Keep feedback frequent enough to support normal development and refactoring. |

## Worked Example

A date method intended to return the following weekday returns the original date when the weekday matches.

1. **G3 / T5** identify equality as the boundary to reproduce.
2. **G21** asks for the arithmetic explanation: zero offset must become seven for strictly following behavior.
3. **T6 / T7** extend tests around the defect and across all weekday offsets.
4. **G19 / G33** suggest naming and centralizing the offset calculation.
5. **G20 / N4** verify the method name distinguishes “following” from “on or after.”

This reconstructs the reasoning in [chapter 16](ch16-refactoring-serialdate.md). A useful review names the incorrect returned date and the expected contract, then proposes the minimal fix; it does not simply enumerate the five IDs.

## Mental Models

- Treat a smell as a question about cost or correctness.
- Prefer rules whose application exposes a real dependency or invariant.
- Use the four ordered rules in [chapter 12](ch12-emergence.md) when prescriptions conflict.

## Key Takeaways

1. Support each finding with observed code and a practical consequence.
2. Preserve the original identifiers for precise lookup.
3. Use exceptions and counterweights from the surrounding chapters.
4. Separate historical preferences from operative project requirements.

## Connects To

- [Appendix C](appendix-c-cross-references.md): corrected lookup guidance for this PDF's cross-reference irregularities.
- [Cheatsheet](../cheatsheet.md): a shorter decision aid.
- [Patterns](../patterns.md): reusable techniques organized by situation.
