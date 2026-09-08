# Chapter 9: Making Implicit Concepts Explicit

Source: supplied PDF, pages 123-146. Examples are compact reconstructions; pseudocode is illustrative.

## Core Idea

Look for domain meaning that conversation or code already relies on but the model does not represent. Give useful concepts an explicit name, responsibility, and implementation, then refine them through scenarios rather than expecting the first formulation to be final.

## Finding Missing Concepts

| Signal | Investigation | Useful outcome |
|---|---|---|
| Experts repeatedly use an absent term | Ask what decisions or information it brings together | A concept that replaces hand waving with precise discussion |
| Procedures are hard to explain or gain exceptions rapidly | Walk through the awkward behavior with an expert | A distinction that separates previously entangled responsibilities |
| Expert statements appear contradictory | Examine their assumptions, purposes, and contexts | A deeper reconciliation or explicitly distinct models |
| Available expertise is incomplete or hard to access | Read relevant domain literature and compare it with local practice | Better candidate models and more precise questions |
| The first model helps but still feels awkward | Try another responsibility assignment or abstraction | Accumulated learning rather than attachment to the first idea |

These are leads, not an automatic noun-to-class conversion. Test whether the proposed concept improves explanation, behavior, and design. Contradictions need not all be reconciled into one model; Chapter 14 provides boundaries for cases where different meanings should coexist.

### Worked Example: Itinerary Hidden in a Report

A routing engine writes rows for voyage legs, and a report derives the sequence and timing of loading and unloading. Operations asks for the cargo's itinerary. Initially the developer treats the itinerary as just report data already available in the tables.

Recognizing Itinerary as a domain concept changes several responsibilities:

1. Represent the ordered legs, with relevant voyage, location, and timing meaning.
2. Let the Routing Service return an Itinerary instead of writing booking tables itself.
3. Let the booking application persist the result with the rest of the booking.
4. Let the report and operations application use the same domain behavior instead of assembling equivalent facts independently.
5. Use the new term in discussions of the integration and routing result.

The data existed before the concept. The improvement comes from unifying behavior and interfaces around its meaning, reducing duplication and decoupling routing from the database representation.

### Worked Example: Accrual Versus Payment

An Interest Calculator combines earned interest, payments, and amounts due. More payment timing cases make it increasingly awkward. An expert explains that earnings and payments are distinct postings under accrual accounting.

Model Accrual explicitly. Accrual schedules describe how income is earned; individual accruals can be retained, aggregated, and posted. Interest and fees now share a useful conceptual basis, while payments remain separate.

The batch process can ask an Asset for accruals through a date and post the returned results to the appropriate ledgers. Ledger knowledge moves out of incidental batch-script logic into the relevant domain representation.

Reading accounting literature could reveal the same important separation even without a highly engaged expert. It might lead to a less complete initial model, which is still useful. Literature supplies starting points, not proof that every local business follows the same rules.

## Less Obvious Categories of Concepts

### Explicit Constraints

Start by naming a constraint in the object that naturally owns it. A Bucket that limits contents to capacity can factor the rule into `constrainedToCapacity`, making the operation's purpose visible while preserving its behavior.

Promote the constraint to a separate concept when:

- Evaluation requires information unrelated to the host object's basic meaning.
- Related rules recur across unrelated objects and cause duplication or artificial inheritance.
- The rule is prominent in domain discussion but hidden inside procedural implementation.

Why it works: a named constraint gets a clear place to evolve while the host remains focused. A method is enough for some rules; an object or related set of objects is justified when the concept has more substantial meaning or dependencies.

### Processes as Domain Objects

Ask whether the process itself matters to experts or is just a program mechanism. Routing has business meaning and can be expressed as a Service. Alternative domain approaches may be represented as Strategies.

Keep internal algorithms encapsulated unless their selection or identity is part of the domain. Turning every procedure into a named domain object increases ceremony without necessarily adding knowledge.

## Specification

A Specification is a predicate-like Value Object that states criteria another object must satisfy. Use it when a business rule is important but does not fit comfortably into the candidate object's responsibility.

1. State the criteria independently of the candidate and of the operation that will use the answer.
2. Provide a satisfaction test in the language of the model.
3. Supply the parameters or policy information the Specification needs without burdening the candidate with unrelated dependencies.
4. Keep the rule in the domain layer.
5. Reuse the meaning across validation, selection, and construction where appropriate, while allowing different implementation mechanisms.

Why it works: a rule that would overwhelm Invoice or leak into a collection application becomes an explicit domain concept. The pattern captures useful predicate behavior without requiring an entire general-purpose logic programming system.

| Use | Question | Implementation concern |
|---|---|---|
| Validation | Does this candidate meet the criteria? | A direct predicate can be sufficient |
| Selection | Which existing candidates meet them? | Query efficiency and preserving the same rule meaning |
| Building to order | Can a new or reconfigured result meet them? | Construction/search algorithm and validation of its output |

A Specification describes the acceptable result. It is neither the result itself nor necessarily an algorithm for finding it.

## Worked Example and Code: Delinquent Invoices

The meaning of delinquency may depend on an invoice's due date, a customer's grace period, and an evaluation date. Adding all policy sources and workflows to Invoice would obscure its basic purpose as a request for payment.

Reconstructed predicate:

```text
DelinquentInvoiceSpecification(evaluationDate).isSatisfiedBy(invoice):
    deadline = invoice.dueDate + invoice.customer.gracePeriod
    return evaluationDate is after deadline
```

The chapter's example supplies an evaluation date to a short-lived Specification. The strictly-after boundary is part of that example; decide and test the actual boundary when adopting a policy in a project.

For an account warning, evaluate its invoices and detect whether any satisfy the Specification. For a report over a large population, loading all invoices into memory would be a poor implementation despite preserving the abstract predicate.

### Query implementation choices

| Choice | Benefit | Cost |
|---|---|---|
| Evaluate an in-memory collection | Direct reuse of the predicate | Unsuitable when the population is large or remote |
| Have a mapping/query facility translate model criteria | Efficient selection while retaining domain expression | Requires suitable infrastructure |
| Put SQL generation directly in the Specification | Makes the example's rule-to-query relation obvious | Leaks schema/mapping details into domain code |
| Let the Specification select a specialized repository query | Keeps SQL inside persistence access | Criteria and mechanics require careful division of responsibility |
| Query a broad candidate set, then apply the predicate | More generic query mechanism and clear domain rule | Extra data retrieval and in-memory filtering |

Evans presents the SQL-in-Specification version and then identifies its mapping leak; it is not the final universal recommendation. The choice depends on the platform and population sizes. Preserve consistent satisfaction semantics across implementations, especially date boundaries and any policy parameters.

A repository can offer specialized queries without becoming the owner of the business rule. Conversely, a Specification should not become a vehicle for exposing arbitrary table details to clients. A stored procedure can also implement selection while the domain-level Specification carries the permitted criteria.

## Worked Example: Specify a Packer Before Optimizing It

The chapter uses a simplified warehouse-packing problem with containers, drums, capacity, required container features, and incompatible contents. These are example model requirements, not an operational chemical-storage standard.

Begin with validation:

1. State each drum's container requirements as a Specification.
2. Define whether a proposed packed configuration satisfies all relevant requirements and capacity constraints.
3. Give the packing Service a contract: produce a complete compliant arrangement, or report that it found no complete answer.
4. Use the criteria to validate results independently of the packing algorithm.

This separates the domain definition of an acceptable packing from the technical search for one. An optimized solver can change without making other application components understand its algorithm.

### Use a limited working prototype

A simple first-fit implementation can give the rest of the application meaningful behavior while specialists develop a better solver. It can also elicit expert feedback on the criteria and interface.

Keep its limitations explicit:

- A greedy failure means the algorithm found no answer; it does not prove no valid arrangement exists.
- Placement order can consume a specialized container on an ordinary item and block a later item.
- The source prototype can modify containers before throwing; rollback is left to a higher level.
- The feature-check snippet illustrates only part of the model. In applying it, validate the prospective final contents and every affected requirement, including existing occupants' incompatibility constraints.

That last distinction prevents the convenience predicate for one incoming item from being mistaken for a complete final-configuration check. A prototype's actual guarantees must match the interface used by its clients.

The prototype is useful because the domain model and contract make a limited implementation possible. Keep that interface current as the real solver develops so integration problems surface early. A working prototype can unblock collaboration without claiming to satisfy requirements it has not implemented.

## Key Concepts

- **Implicit concept**: domain meaning relied on by existing work but absent from the explicit model.
- **Explicit constraint**: a named rule represented in a method, object, or relationship rather than hidden in mechanics.
- **Specification**: criteria expressed as a predicate-like Value Object for candidate satisfaction.
- **Validation**: testing whether a present candidate meets criteria.
- **Selection**: finding existing candidates that meet criteria.
- **Building to order**: creating or configuring a result to meet criteria.
- **Accrual**: recognition of earned income separately from the receipt of payment in the chapter's accounting example.

## Anti-patterns and Key Takeaways

1. Follow absent vocabulary with investigation, not automatic class generation.
2. Keep rules in the domain while finding the object or method that actually fits them.
3. Distinguish criteria from the algorithms that evaluate, query, or generate against them.
4. Compare predicate and query semantics; a shared name does not ensure equivalent rules.
5. Use prototypes with clear contracts and visible incompleteness to obtain real feedback.
6. Expect repeated attempts. Keep the learning when a candidate model is replaced.

## Connects To

- [Chapter 2](ch02-communication-and-language.md): language provides evidence of missing concepts.
- [Chapter 6](ch06-life-cycle-of-domain-objects.md): repositories and factories collaborate with specifications.
- [Chapter 10](ch10-supple-design.md): composite specifications and expressive interfaces.
- [Chapter 11](ch11-applying-analysis-patterns.md): borrow prior modeling knowledge without copying it blindly.
