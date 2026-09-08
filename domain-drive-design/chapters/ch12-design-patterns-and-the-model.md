# Chapter 12: Relating Design Patterns to the Model

Source: supplied PDF, pages 191-200. The chapter develops Strategy and Composite as examples, not an exhaustive list of design patterns suitable for domain modeling.

## Core Idea

A technical design pattern becomes a domain pattern when its structure and behavior express an actual domain concept. Evaluate both levels: whether the implementation works and whether the model says something meaningful about the business.

## Two-Level Fit Test

Use this test before introducing a familiar software pattern into a domain layer:

1. State the domain distinction or behavior that needs expression.
2. Describe the pattern's conceptual claim without technical class names.
3. Ask whether experts recognize that claim in concrete scenarios.
4. Check the implementation consequences: dependencies, clients, state, object count, and operational behavior.
5. Introduce the pattern only when it resolves a present modeling or implementation need.

A pattern can still be useful solely as an implementation technique. The error is claiming that every useful technical arrangement is part of the domain model.

## Strategy, Also Called Policy

### When to use it

A meaningful process has legitimate alternatives, and the varying rule is tangled with the stable process. Repeated conditionals may be a symptom, but the domain motivation is to make the policy itself explicit.

### How to apply it

1. Separate what the process always does from the criterion or behavior that varies.
2. Name the varying concept in domain terms.
3. Define an interface expressing the contribution each policy makes.
4. Implement the alternatives behind that interface.
5. Let clients deliberately choose the appropriate policy, then test both the policy and the stable process.

The technical benefit is substitution of algorithms. The modeling benefit is independent expression of a rule and the behavior it governs. Both matter; implementing interchangeable classes without a meaningful policy concept provides only the first.

### Worked example: Route-finding policies

A Routing Service constructs an Itinerary satisfying a Route Specification. It can favor speed or price. When the service receives a flag for fastest versus cheapest, computations throughout the routing algorithm branch on that flag. More criteria would spread more conditional logic.

Extract a Leg Magnitude Policy that assigns a numeric magnitude to a Leg. Routing can then minimize the total magnitude without knowing whether it represents time, cost, or another agreed preference.

```text
interface LegMagnitudePolicy:
    magnitude(leg) -> number

fastest.magnitude(leg)  -> travelDuration(leg)
cheapest.magnitude(leg) -> transportCost(leg)

itinerary = routingService.find(routeSpecification, chosenPolicy)
```

This is reconstructed pseudocode. The important contract is the meaning of the magnitude and its use in comparing routes. Before inventing a weighted policy, establish meaningful units, normalization, and tradeoffs with the business. A time-plus-money expression has no inherent business meaning merely because it returns a number.

The model now states an explicit rule: choose an admissible Itinerary with minimal total Leg magnitude under the selected policy. It distinguishes admissibility, expressed by the Specification, from preference, expressed by the Policy.

This statement also reveals assumptions worth testing. Are costs additive per Leg? Can a local Leg score represent all relevant preferences? Do whole-route constraints or connections change the calculation? The source illustrates a useful model, not proof that every routing problem can be represented by independent additive scores.

The book warns that evaluating Legs directly during itinerary search may be too inefficient for production. Chapter 14 preserves the local interface while using a different external routing implementation. An expressive domain interface need not dictate a naive internal algorithm.

### Tradeoffs

Clients need enough knowledge to select a Strategy. Putting selection somewhere else does not eliminate that responsibility. Strategies may increase object count; stateless implementations may be shared when appropriate. The usual technical considerations still apply even when the pattern has domain meaning.

## Composite

### When to use it

The domain contains a true part-whole hierarchy, and the parts can be understood as the same conceptual type as the whole. The fact that an object graph is recursive is not sufficient.

### How to apply it

1. Find a common abstraction that is meaningful for both leaves and containers.
2. Define useful questions or operations at every level.
3. Let leaves answer from their own values.
4. Let composites answer from their contents using domain-appropriate aggregation.
5. Make clients operate through the common abstraction where distinctions are irrelevant.
6. Preserve constraints about valid assembly even when the class diagram becomes more uniform.

The decisive feature is behavioral symmetry. A tree of classes without meaningful common behavior captures little of Composite's value.

### Worked example: Routes made of routes

The first shipment model treats a Route as a sequence of Legs. Experts later distinguish five logical segments, planned at different times by different people. Door movements also differ from scheduled rail and ship transportation. Adding separate collections and types for each level makes traversal and operational-plan generation cumbersome.

Composite fits because every level can be understood as moving a container from one place to another. A whole Route contains subroutes; subroutes may contain smaller routes; individual Legs form the leaves. Clients generating a plan can treat these uniformly.

```text
Route:
    origin()
    destination()
    operationalSteps()

Leg.operationalSteps():
    return stepsForThisLeg

CompositeRoute.operationalSteps():
    return concatenate(child.operationalSteps() in route order)
```

This reconstructed interface illustrates useful symmetry. It is not copied source code. A real implementation must enforce end-to-end continuity and any required segment ordering; Composite's uniform type does not itself prove a valid itinerary.

The simplified class diagram communicates less about which particular segments belong where. The model can communicate that assembly information through instance diagrams, other views, construction behavior, and code. A model is more than one static diagram.

Arbitrary nesting and route splicing become possible, but those possibilities alone do not justify implementing unused features. The team was well served by the simpler sequence before distinct subroutes became a real requirement.

## Contrast: Flyweight Is an Implementation Choice

The chapter's repeated electrical-outlet values can share instances to reduce storage. That sharing concerns representation, not a business concept. Equal Value Objects can use Flyweight without changing their meaning.

Entities cannot be merged merely because their current attributes match: continuity and identity distinguish them. The point is not that no Entity-related implementation can ever share supporting data; it is that the chapter's interchangeable-value sharing does not establish Entity sameness.

| Question | Strategy as domain pattern | Composite as domain pattern | Flyweight in this example |
|---|---|---|---|
| What does it express? | A policy governing a process | Parts and wholes of one conceptual kind | Shared representation of equal values |
| What must experts recognize? | Legitimate alternative rules | Meaningful uniform behavior across levels | No new domain concept is required |
| Main failure | Naming technical branching as a policy without meaning | Treating any recursive association as part-whole sameness | Accidentally conflating distinct Entities |

## Mental Models and Anti-Patterns

- **Pattern as a claim about reality for this purpose:** verify the abstraction's domain meaning rather than selecting it from a catalog first.
- **Uniform structure requires uniform questions:** Composite works when clients can ask meaningful questions at every level, not merely recurse.
- **Interface versus algorithm:** a domain-facing Routing Service can remain stable while its implementation changes substantially.
- **Premature pattern enrichment:** do not implement every extension a pattern makes possible.
- **Pattern-name language:** call a business policy by its actual meaning when possible; forcing experts to speak in technical pattern names weakens the shared language.

## Key Takeaways

1. Technical and domain motivations can coexist in the same pattern application.
2. Strategy separates a meaningful policy from a stable process.
3. Composite requires conceptual and behavioral sameness across levels.
4. Useful implementation patterns need not become domain concepts.

Related: [Value Objects](ch05-a-model-expressed-in-software.md), [Specifications](ch09-making-implicit-concepts-explicit.md), [analysis patterns](ch11-applying-analysis-patterns.md), [model translation](ch14-maintaining-model-integrity.md).
