# Chapter 10: Supple Design

Source: supplied PDF, pages 147-180. Paint and Share Pie diagrams on PDF pages 158 and 178 were visually checked. Examples and tables are synthesized.

## Core Idea

Make domain elements understandable, predictable, and easy to combine so developers can extend them without tracing every implementation detail. Suppleness comes from meaningful boundaries and clear behavior, not from adding layers of abstraction for hypothetical flexibility.

## Six Patterns for Supple Design

### Intention-Revealing Interfaces

Use when callers must inspect implementation to discover what an operation promises.

- Name types, operations, and parameters by domain purpose and effect.
- State rules and relationships at the interface; encapsulate the mechanism enforcing them.
- Sketch a behavioral test from the client's perspective to discover the interface you want, then make the implementation support it.
- Check the actual effects as well as the name. A persuasive name cannot repair contradictory behavior.

Why it works: the client developer can work with a domain abstraction rather than carry both the client's problem and the callee's algorithm in mind.

### Side-Effect-Free Functions

Use when calculations and mutations are entangled or intermediate results cannot safely be reused.

1. Separate a calculation/query from the command that changes observable state.
2. Make the command small and explicit about the change it performs.
3. Look for a Value Object that naturally owns the calculation and can return a new value.
4. Make recombination safe by preserving existing values during calculations.

The source advocates strict command/query separation: commands change state without returning domain information; functions return results without observable state changes. This is the book's design guidance, not a claim that every existing API must immediately be rewritten.

For the same relevant inputs and state, a calculation should have predictable results. No observable mutation alone does not guarantee determinism if a calculation secretly depends on changing time or external information; make relevant inputs and assumptions clear in the actual design.

### Assertions

Use when a command's name leaves uncertainty about affected state, delegated effects, or valid inputs.

- State **preconditions** under which the guarantee applies.
- State **postconditions** describing resulting state and effects.
- State **invariants** of the object or Aggregate at its appropriate consistency boundary.
- Check those claims through language facilities, tests, or concise documentation appropriate to the project.

Assertions describe states rather than execution steps. They allow callers to reason about a result without tracing all delegations. Check whether the guarantees fit the concepts users and developers are being invited to assume; logically consistent but conceptually surprising behavior remains difficult to use.

### Conceptual Contours

Use when a large object contains unrelated responsibilities or a collection of tiny objects makes clients reconstruct the missing whole.

1. Follow complete domain operations and values rather than a fixed preferred class or method size.
2. Observe which responsibilities change together over successive requirements.
3. Look for stable distinctions explaining those changes.
4. Refactor boundaries to align with those distinctions and replay representative scenarios.

Why it works: a meaningful partition can accommodate unanticipated changes because it corresponds to the domain's underlying organization. Localized changes are evidence of fit, not proof of eternal stability. Broad disruption is an opportunity to reconsider the model.

### Standalone Classes

Use when an intricate computation can be understood independently of the connected objects currently hosting it.

Remove dependencies that are not fundamental to the concept. A computational Value Object is often a useful candidate. Keep necessary dependencies explicit rather than replacing every domain type with primitives to make the dependency graph appear smaller.

Implicit concepts still count. Three integers encoding pigment are not conceptually simpler than an explicit Pigment Color. The objective is fewer ideas that must be held together, not merely fewer declared class references.

### Closure of Operations

Use when combining values can return another value of the same conceptual type.

Examples include adding two Share Pies to obtain a Share Pie, or mixing two Pigment Colors to obtain a Pigment Color. Closure can be over an abstract type even when the concrete implementations differ.

Treat the receiver as an input when its state participates. An operation returning its own type while accepting an additional money amount is useful but not fully closed under that type. Near-closure with familiar primitives can still reduce conceptual load.

Value Objects are the usual opportunity because calculations can freely create equivalent or derived values. Entity lifecycle and identity make arbitrary new-instance creation a different matter.

## Worked Example: From Ambiguous Paint Mutation to a Recipe Model

1. **Reveal intent.** A method named `paint(Paint)` becomes `mixIn(Paint)` and abbreviated color/volume accessors gain meaningful names.
2. **Expose a missing value.** Pigment Color distinguishes subtractive pigment mixing from a display-color model. Put the relevant mixing computation with that value and return a new color.
3. **State effects.** The existing receiver gains the argument's volume, while the argument's volume remains unchanged.
4. **Challenge the apparent contradiction.** Physical paint mixing suggests the source paint should be depleted. But the application must later report the unmixed inputs needed to make the recipe; consuming them would lose required information.
5. **Change the model.** Distinguish Stock Paint from Mixed Paint. A mixture records its constituents and derives volume and color from them.

The checked diagram makes the resulting responsibilities explicit:

| Operation | Meaning |
|---|---|
| `MixedPaint.mixIn(StockPaint)` | Add a constituent to the mixture |
| `MixedPaint.getVolume()` | Sum constituent volumes |
| `MixedPaint.getColor()` | Combine constituent pigment colors through value calculations |
| `MixedPaint.stockConstituents()` | Expose the recipe's constituent information |

Now one simple command adds a constituent; the other operations can be queries. The model explains why the original stock descriptions remain available. The improvement comes from identifying the application's actual concept, not enforcing a physically intuitive invariant indiscriminately.

## Worked Example: Accrual Boundaries Accommodate New Rules

Separating accrual schedules from payments allows different earning rules to vary independently of how payments are handled. A later early/late-payment requirement applies similarly to interest and fees, so one Payment concept can support it.

The earlier refactoring did not predict this feature. It aligned the design with a domain distinction that also matters to the new feature. Use this example to distinguish coherent flexibility from building a universal mechanism intended to anticipate every request.

## Composite Specifications

Specifications can be combined with `and`, `or`, and `not`; each operation returns another Specification. This makes rule composition closed under Specifications and lets client code express criteria declaratively.

Reconstructed interface:

```text
Specification.isSatisfiedBy(candidate) -> boolean
Specification.and(other) -> Specification
Specification.or(other) -> Specification
Specification.not() -> Specification
```

A composite object tree is one implementation. A compact encoded expression interpreted at runtime is another when fine-grained object overhead is expensive. Choose according to the platform, clarity, and required operations. Implement only the operators actually needed; an `and`-only design can be adequate.

### Subsumption: Preserve the Book's Direction

Evans uses `stronger.subsumes(weaker)` to mean:

```text
for every candidate x:
    stronger.isSatisfiedBy(x) implies weaker.isSatisfiedBy(x)
```

Thus the stronger Specification accepts a subset of the candidates accepted by the weaker one. State this direction explicitly because the word can be used differently elsewhere.

Use it when comparing requirements themselves, such as detecting whether a revised rule is at least as restrictive as the old one. Checking today's stored objects cannot establish this relation: no present object may exercise the changed condition.

For a minimum-threshold predicate `value >= threshold`, a higher threshold subsumes a lower one under this definition. With conjunctions of identical leaf criteria, containing every old leaf and adding others establishes implication. Parameterized leaves can support their own implication rules.

These are restricted proof techniques. General combinations with `or` and `not` require more sophisticated reasoning. A leaf-set check that fails need not prove that implication is false if the supported representation is incomplete. Prefer a deliberately restricted capability over silently pretending to support general logical proof.

## Worked Example and Code: Shares Math

A Loan method initially computes a principal-payment distribution and mutates the Loan's shares in the same loop. Split it into calculation and application first. Then observe that the meaningful calculations concern groups of shares forming a whole, not just individual Share objects.

Introduce Share Pie. An intermediate model ties it to a Loan as a locally identified Entity. Treating it instead as a Value Object permits immutable arithmetic over distributions, with no dependence on one Loan's history.

Reconstructed pseudocode:

```text
distribution = loan.shares.prorated(paymentAmount)
loan.replaceShares(loan.shares.minus(distribution))

defaultDrawdown = facility.shares.prorated(drawdownAmount)
loan.replaceShares(loan.shares.plus(defaultDrawdown))

agreed = facility.shares.prorated(loan.amount)
deviation = loan.shares.minus(agreed)
```

`plus` and `minus` are closed under Share Pie. `prorated` takes an amount and returns a Share Pie, so it is deliberately only partly closed. The Loan owns the state change; the value operations can be used freely for analysis and intermediate calculations.

Useful assertions describe the arithmetic meaning:

- The total is the sum of the parts.
- Addition and subtraction operate on the corresponding owner's shares.
- Proration divides an amount according to a given share basis.
- Calculation does not alter any existing Share Pie.

The illustrative source code uses floating-point amounts and omits substantial financial detail. This is a model and responsibility example; the source does not specify a complete money representation, rounding allocation algorithm, zero-basis policy, or transaction implementation. Resolve such requirements from the actual project when applying the model.

Why it works: arithmetic supplies a familiar compositional structure. The refactoring makes Loan methods read like definitions of business transactions, while the complex calculations are concentrated in a small, testable value model.

## Declarative Design and Its Limits

Executable declarations can tie stated properties directly to behavior. A domain-specific language can also align programs closely with domain vocabulary. Evaluate both by how well the model can evolve:

| Opportunity | Failure mode to examine |
|---|---|
| Remove repetitive persistence or mapping work | A restrictive declaration cannot express an important concept |
| Generate consistent implementation from a declaration | Regeneration destroys or conflicts with handwritten changes |
| Express rules declaratively | Procedural control features reintroduce order-dependent effects |
| Provide a domain-specific language | Maintaining grammar, libraries, and clients makes ordinary model refactoring too costly |

A declarative *style* can be achieved through clear, composable domain interfaces without building a new language or general inference engine. Prefer a narrowly useful mechanism that preserves modeling freedom.

## Choosing Where to Apply the Effort

Concentrate on a consequential, intricate subdomain. Carve out a coherent calculation or rule structure and make that area predictable and expressive. The remaining design becomes smaller and can use the extracted model at a higher level.

Draw on an established formalism when it truly fits: arithmetic, accounting, or predicates can supply well-understood composition rules. Keep the implementation consistent with those rules so familiar names do not create false expectations.

## Key Concepts

- **Supple design**: a design whose concepts are clear, predictable, and readily recombined or changed.
- **Intention-Revealing Interface**: public names and contracts communicating purpose and effect.
- **Side-Effect-Free Function**: an operation returning a result without observable state mutation.
- **Assertion**: a precondition, postcondition, or invariant characterizing behavior and state.
- **Conceptual Contour**: a domain distinction along which responsibilities can be divided coherently.
- **Standalone Class**: a class understandable with minimal dependence on other domain concepts.
- **Closure of Operations**: combination that stays within the same conceptual type.
- **Subsumption**: here, the implication from a stricter Specification to a weaker one.

## Anti-patterns and Key Takeaways

1. Avoid confusing flexibility with indirection. Make one important area simple to use and change.
2. Avoid treating informative names as complete behavioral guarantees.
3. Avoid hidden mutations in reusable calculations.
4. Avoid imposing uniform class size or stripping meaningful types down to primitives.
5. Avoid implementing a whole logical calculus when a few specialized predicates suffice.
6. Keep assertions consistent with the actual domain concept, especially when physical intuition and application purpose differ.

## Connects To

- [Chapter 5](ch05-a-model-expressed-in-software.md): value semantics and conceptual dependencies.
- [Chapter 8](ch08-breakthrough.md): the Share Pie shift in domain understanding.
- [Chapter 9](ch09-making-implicit-concepts-explicit.md): explicit constraints and Specifications.
- [Chapter 15](ch15-distillation.md): select high-value areas and extract coherent mechanisms.
