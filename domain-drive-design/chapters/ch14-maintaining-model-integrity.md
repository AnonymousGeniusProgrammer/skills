# Chapter 14: Maintaining Model Integrity

Source: supplied PDF, pages 205-246. This is the book's main reference for Bounded Contexts, Context Maps, integration relationships, and transformations between them.

## Core Idea

Keep each model internally consistent within an explicit scope, then deliberately manage its relationships with other models. Enterprise-wide unification is often too costly or unsuitable. Accidental mixing is dangerous even when classes and data formats look compatible.

## Bounded Context

Define where one model's terms, concepts, and rules apply and will be kept unified. State the relevant team responsibilities, application usage, code, and schema boundaries. A boundary must reflect how development actually works.

1. Identify the model and the people who change or consume it.
2. Identify artifacts driven by that model, including persistence when applicable.
3. Separate foreign or divergent models and name each context.
4. Make ownership, consistency expectations, and points of contact explicit.
5. Revisit the boundary when team coordination or model needs change.

**A context is not a Module.** Modules also organize concepts within a single model. Separate namespaces can conceal accidental fragmentation as easily as they can mark intentional separation. Nor does the pattern prescribe one process, database, team, or microservice per context. Deployment and organizational choices affect boundaries, but are not equivalent to them.

### Worked example: Two meanings of Charge

One team reuses another team's Charge for customer invoicing, adding fields and treating a posted amount as an amount due. The original bill-payment code assumes a deductible percentage that the new records lack. Code reuse has mixed vendor-payment and customer-billing meanings.

Splitting Customer Charge and Supplier Charge fixes the immediate defect. Preventing recurrence also requires an explicit boundary and either deliberate shared-model integration or deliberate separation.

Watch for **duplicate concepts** (two inconsistent representations of the same idea within an intended unified model) and **false cognates** (the same term hiding different meanings). Language confusion often precedes failing interfaces or data corruption.

## Continuous Integration

Maintain both conceptual and implementation consistency inside a Bounded Context:

- Exercise the Ubiquitous Language continually so people reconcile evolving interpretations.
- Use a reproducible merge/build process and automated tests.
- Keep unintegrated changes short enough that incompatible work cannot accumulate substantially.

A build server alone does not integrate concepts. Conversely, a shared conversation without running integrated software cannot demonstrate consistency. Evans discusses daily or more frequent integration as common practice; choose a cadence that actually prevents divergence. Distinct contexts need explicit boundary tests, but need not coordinate every internal model change at the same pace.

## Context Map

Map the current terrain before proposing its future shape.

1. Name every relevant context, including implicit models in procedural and legacy systems.
2. Show contacts, data or functionality exchanged, translation responsibilities, and deliberate sharing.
3. Describe actual team cooperation and control, not only technical arrows.
4. Mark contradictions and unclear relationships explicitly; repair outright contradictions before attempting wholesale reorganization.
5. Make the map understandable and available to the team. Update it when reality changes.

A compact diagram or textual table is enough if everyone can identify the context of code and conversations. An external system may itself contain contradictory models; a box around it does not prove internal unity.

Useful map columns are context, purpose, responsible team, relevant artifacts, neighbor, information exchanged, translation owner, and relationship. Use a pattern name only when its conditions fit; document variation instead of forcing a label.

## Relationship Patterns

| Pattern | Use when | Apply it | Main cost or constraint |
|---|---|---|---|
| Shared Kernel | Teams need a limited common model and can cooperate closely | Explicitly share a small model/code/schema subset; consult before changes; integrate and run both teams' tests regularly | Shared change and deployment coordination; neither team owns unilateral freedom |
| Customer/Supplier Development Teams | A downstream team depends on a willing upstream supplier | Include downstream needs in upstream planning; negotiate commitments; jointly define acceptance tests run upstream | Requires actual incentives and budget, not helpful promises alone |
| Conformist | Upstream will not accommodate downstream, but its model is usable and integration valuable | Follow upstream semantics in the overlap; extend additively where possible | Deeper dependence and reduced local modeling freedom |
| Anticorruption Layer | Integration is needed but an independent local model must be protected | Expose local concepts and translate foreign meanings and operations behind them | Translation can become substantial software with its own maintenance cost |
| Separate Ways | Integration adds too little value | Develop independently; offer convenient joint access if useful | Later integration may require difficult translation or model reconciliation |
| Open Host Service | Many consumers need a coherent set of capabilities | Offer an available, documented service protocol; generalize common needs and isolate idiosyncratic ones | Protocol design, stability, and evolution obligations |
| Published Language | Multiple parties need an understandable interchange model | Use a documented shared language; translate internal models to and from it as needed | Stable interchange semantics must coexist with evolving internal models |

### Shared Kernel versus Conformist

Both can have equivalent concepts in the overlap. Shared Kernel means joint stewardship and consultation. Conformist means following a model whose owner does not collaborate on the downstream team's terms. The organizational relationship is part of the pattern.

A Shared Kernel may contain core or generic concepts, but is defined by sharing, not strategic importance. It reduces duplication rather than promising to eliminate it. The source's weekly kernel integration example is less frequent than integration within each team; it is not a universal release rule.

### Customer/Supplier in practice

Yield Analysis consumes Booking data but does not automatically change Booking policies; human specialists make resulting business decisions. The analysis team uses different models and tools. A customer/supplier arrangement lets downstream requirements compete openly in upstream planning, backed by tests that detect broken expectations.

Changing those acceptance tests changes the agreement and calls for communication. Without upstream motivation, the downstream team must choose based on what it can actually obtain: separate, conform, or translate.

## Anticorruption Layer: Meaning Before Transport

An ACL translates conceptual objects and actions. A network client, serializer, or protocol bridge alone is not an ACL.

A common decomposition is:

```text
local model -> local Service interface -> Adapter -> foreign Facade -> external system
                                          |
                                      Translator
```

- **Local interface:** one or more coherent Services, occasionally an Entity, expressed in the local model.
- **Facade:** simplifies the foreign interface while retaining foreign semantics. It belongs conceptually to the foreign context; it does not invent an intermediate domain model.
- **Adapter:** knows how to make equivalent requests through the foreign protocol or facade.
- **Translator:** converts meanings and representations; separating it from request handling makes both testable.
- **Transport:** placed pragmatically according to access and deployment constraints.

Omit a facade when the foreign interface is already simple. Translate both directions only where needed; some translations are inherently asymmetric. Relationship-specific tracing or auditing can belong here. If extensive translation overwhelms the new system, reconsider the boundary or consciously conform rather than gradually contaminating the local model.

### Worked example: Routing across two models

Booking uses Route Specification and Itinerary. Efficient route search uses a Transport Network model with nodes and a matrix suitable for network algorithms. The local Routing Service keeps its meaningful contract while translating the request and result.

```text
route(spec):
    constraints = translator.locationsFrom(spec)
    nodeIds = networkTraversal.findPath(constraints)
    itinerary = translator.itineraryFrom(nodeIds)
    return itinerary
```

This reconstruction replaces damaged source formatting. Boundary tests should check the contract that the returned Itinerary satisfies the supplied Specification, alongside explicit failure behavior for unsatisfied requests in the actual implementation.

An origin, optional customs-clearance location, and destination become an ordered location list. The reverse is ambiguous: an arbitrary list can contain many intermediate locations without identifying any as customs clearance. No reverse conversion is needed for this request.

Returned node identifiers are resolved and paired into departure/arrival operations to build Legs. Both teams maintain the translator and its tests. Do not assume matching location codes or arbitrary node pairing unless verified for the actual systems. Only translate the information required by the collaboration, not every element of both models.

## Open Host Service and Published Language

An Open Host Service reduces repeated custom integrations by exposing common capabilities through one coherent protocol. A single consumer's unusual need should not continually complicate that shared interface; a dedicated translator can serve the exception.

Published Language means sufficiently documented and available to its intended community for compatible independent interpretation. It does not mean public internet access, open-source licensing, or necessarily XML. The chapter's XML and Chemical Markup Language examples illustrate the value of a shared domain vocabulary and supporting tooling; they are historical examples, not current technology recommendations.

Do not equate the published interchange model with the host's internal model. The former needs stability; the latter must remain free to evolve. An existing published language can be sufficient without adding a third language merely for symmetry.

## Choosing a Context Strategy

| Larger unified context favors | Smaller distinct contexts favor |
|---|---|
| Smooth interaction across user tasks | Independent team action |
| One coherent explanation instead of models plus mappings | Manageable communication and integration scope |
| Avoiding difficult or impossible translation | Specialized user language and computational models |
| Strong shared language | Less need for one highly versatile abstraction |

Make these choices using functional integration needs, model differences, team capabilities and incentives, and deployment feasibility. Numerical team sizes in the chapter are illustrations, not boundary-sizing formulas. Preserve useful specialized terminology; do not protect accidental jargon merely because it already exists.

Before splitting a context solely because its model is hard to grasp, consider [distillation](ch15-distillation.md) and [large-scale structure](ch16-large-scale-structure.md). A large coherent model with compelling integration needs may need better organization rather than semantic separation.

### Unifying an elephant

Different partial models can be valid for different purposes. Minimal integration may need only a translation of location. Rich integration may require a new model that incorporates useful observations and discards misleading implications. Combining names or class sets is not the same as unifying meaning. A deeper shared model is an outcome of learning, not an assumption on which to base a merger schedule.

## Transformations

### Separate models to Shared Kernel

1. Verify that each starting context is internally coherent.
2. Establish sharing, naming, integration, and test procedures before adding shared code.
3. Select a small duplicated, preferably non-core subdomain to learn the process.
4. Have developers from both teams reconcile terms and design the shared model and tests.
5. Implement and integrate both consumers; revisit concepts when implementation reveals problems.
6. Remove obsolete translations and repeat in later increments.

Choose one existing model, reconcile selected pieces carefully, or develop a deeper one. Defer specialized language conflicts unless a useful unifying model exists. An alternative is to transfer responsibility for a subdomain to one favored context and have the other use its services, avoiding ongoing shared ownership.

### Shared Kernel to one continuously integrated context

Harmonize integration practices, circulate people to build shared understanding, clarify each core, then progressively merge core concepts. Increase integration frequency as sharing expands. The transition can require temporary translators and significant coordination. Move through the awkward core-merger phase deliberately without exceeding the team's capacity. Preserve specialized user needs when replacing both models with one.

### Phasing out legacy

Choose a testing strategy, then repeatedly select a small function to move, identify ACL changes, implement, and deploy under the project's release process. After the new function works in the field, remove unneeded translation and legacy pieces when practical. The ACL can grow temporarily before shrinking. Development increments and production release sizes need not be identical.

### Ad hoc integration to Published Language

Evaluate existing standards first. If none fits, distill the host's core into a useful interchange vocabulary, document it for collaborators, implement their translations, and switch over coherently. Keep the language stable while reserving freedom to evolve the host model behind translation.

## Anti-Patterns and Takeaways

- Map ambitions as if they were current reality, and teams will act on false boundaries.
- Share code because names match, and incompatible assumptions can leak into production.
- Rely on goodwill without negotiated supplier commitments, and downstream plans become speculative.
- Build an ACL without evaluating its cost, and protection itself can consume the project.
- Treat every system as internally consistent, and translators may encode contradictions unknowingly.

Make boundaries explicit, integrate meaning as well as code, test the contacts, and choose relationships the teams can actually sustain.

Related: [shared language](ch02-communication-and-language.md), [tactical Modules](ch05-a-model-expressed-in-software.md), [Cargo integration](ch07-using-the-language.md), [strategy coordination](ch17-bringing-strategy-together.md).
