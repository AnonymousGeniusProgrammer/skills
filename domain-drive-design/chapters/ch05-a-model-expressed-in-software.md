# Chapter 5: A Model Expressed in Software

Source: supplied PDF, pages 44-69. Worked examples and decision tables are synthesized from the chapter.

## Core Idea

Choose model elements by their domain meaning: continuity calls for an Entity, descriptive equivalence for a Value Object, and an important operation without a natural object owner may call for a Service. Associations and Modules must express domain understanding as deliberately as individual objects do.

## Frameworks and Application

### Associations

Every traversable model association needs a corresponding implementation mechanism. A pointer, an in-memory collection, or a lookup can implement the same modeled relationship if its observable semantics agree.

Simplify relationships with three moves:

1. **Impose a traversal direction.** Support the directions required by meaning and actual operations. A conceptual relationship does not automatically require pointers in both directions.
2. **Add a qualifier.** Discover a domain condition that reduces multiplicity, such as one investment for a particular stock within an account.
3. **Eliminate nonessential associations.** Retain only relationships needed by the application or intrinsic to the model concept.

Why it works: constrained associations both communicate more knowledge and reduce the web that must be maintained. Preserve genuine bidirectionality when the domain or required behavior calls for it; its deliberate presence then has meaning.

### Entities (Reference Objects)

Use when users care that something is the same individual through changes or across representations.

1. State what sameness means in the domain, including what must remain distinct despite identical attributes.
2. Define an identification operation that works across the relevant lifecycle, persistence, and distribution boundaries.
3. If an identifier is assigned, preserve it across representation changes and ensure uniqueness within the required scope. Determine whether that scope is local, system-wide, or shared externally.
4. Keep the Entity focused on identity, continuity, and behavior intrinsic to it. Move descriptive groups or other responsibilities to meaningful collaborators.
5. Distinguish identification from search and matching. A name, address, or phone number may help a person find the right Entity without defining its identity.

Why it works: memory identity and descriptive equality are unreliable substitutes for business identity. Reloading, transmitting, or updating an Entity must not accidentally make it a different business object.

An ID column alone does not establish a meaningful Entity. Conversely, an Entity can have multiple implementations or representations that express the same domain identity. External identifiers also need a plan for missing, reused, or inconsistent values; do not assume an identifier is unique merely because it is familiar.

### Value Objects

Use when the model cares about what an object describes rather than which individual instance it is.

- Form a conceptual whole, such as an address rather than unrelated address fields scattered through a Person.
- Give the value behavior appropriate to its meaning. A Value Object can be complex and can reference Entities.
- Use value-based equality appropriate to that whole; avoid inventing domain identity.
- Prefer immutability and full replacement when the described value changes.
- Decide copying versus sharing as an implementation issue once the domain permits interchangeability.

Why it works: removing identity and mutation obligations simplifies lifecycle reasoning and permits optimizations without changing the domain meaning.

| Implementation choice | When it helps | Constraint or cost |
|---|---|---|
| Copy an immutable value | Locality or communication cost favors independent copies | More objects or storage |
| Share an immutable value | Many equivalent values and low communication overhead | Remote references can make each interaction expensive |
| Mutable value implementation | Frequent change and costly creation/replacement justify it | The mutable instance must not be shared; preserve value semantics |

Evans explicitly allows the last case as a performance-motivated exception. Immutability is the default; it is not a claim that every object called a value in an implementation must be physically immutable under all circumstances.

Reconsider bidirectional links between Value Objects. A required back-reference to the *same individual instance* suggests identity may matter after all. An invariant about an equivalent value is a different claim and needs a clear purpose.

### Services

Use a domain Service when all three characteristics hold:

1. The operation is an important domain concept with no natural home in an Entity or Value Object.
2. Its interface is expressed through elements of the domain model.
3. It is stateless with respect to the service instance's own history.

Name its activity in the Ubiquitous Language. Keep the responsibility explicit and bounded. First look for an appropriate responsibility on an existing model element; do not strip objects of behavior merely because procedural coordination is easy.

**Stateless does not mean side-effect-free.** A service may operate on or change Entities and other accessible information. The point is that selecting another service instance does not change behavior because of that instance's private business history.

| Kind of service | Example responsibility | Reason for placement |
|---|---|---|
| Domain | Coordinate a funds transfer using Accounts and Ledgers | Carries a domain operation and business rules |
| Application | Initiate a transfer task and arrange notification of its result | Coordinates the application interaction |
| Infrastructure | Deliver a notification by a technical transport | Provides a generic mechanism |

A service is not automatically remote, independently deployed, or accessed through an elaborate framework. Interface granularity is also a trade-off: a coherent domain operation can prevent detailed business coordination from leaking into application code, at the cost of some client control.

### Modules (Packages)

Use Modules to organize concepts so people can reason within one area or about relationships among areas without loading every detail.

1. Group concepts with rich, coherent relationships and shared domain meaning.
2. Name each group with vocabulary that can enter model discussions.
3. Check conceptual coupling as well as reference counts: can the area be explained with little knowledge of neighboring internals?
4. If concepts cannot be separated coherently, revise the model or look for an overlooked organizing concept.
5. Refactor Module boundaries and names as domain understanding evolves. Early package decisions are hypotheses too.

Prefer conceptual clarity when a narrowly mechanical reduction of dependencies would make the model harder to understand. Technical coupling still matters, but a lower number alone does not prove a better partition.

Keep technical partitioning rules to those that provide actual value. Splitting one conceptual object across many framework-mandated packages can consume the mental capacity needed to understand the domain. Separate generated implementation code when people do not need to read it, while keeping the model's maintained expression easy to find.

## Worked Example: Identity Is a Modeling Choice

Consider three pairs of objects:

| Situation | Domain question | Model consequence |
|---|---|---|
| Two equal deposits on the same day | Did two transactions occur? | They are distinct Entities despite matching amount, account, and date |
| Two equal money amounts | Does the particular instance matter? | Interchangeable Value Objects can describe the amounts |
| A transaction in a check register and on a bank statement | Are these two records of the same event? | Match domain identity despite different recording dates or representations |

Now consider seats in a stadium. Assigned seating must distinguish a particular seat; a seat number is meaningful within the stadium's identification scope. For general admission, tracking those physical seat numbers may falsely impose a constraint the ticket does not have. The same physical things therefore need not receive the same model classification in different applications.

Apply the test before choosing a type or table: identify the operation that would be wrong if equal-looking instances were exchanged. If there is no such operation, investigate a value representation. If there is, define the continuity and identity the operation requires.

## Worked Example: An Address in Three Domains

- A mail-order application may need an address only to direct a parcel and validate payment information. Equivalent addresses are interchangeable descriptions.
- A postal-routing application may track an individual delivery location through reorganized postal zones. Continuity can make Address an Entity.
- A utility needs to recognize that two callers are requesting service for the same destination. It can model that destination as an Entity called Address, or make Dwelling the Entity and use Address as its descriptive value.

The final alternative matters: the choice is not determined by the noun alone. Compare candidate models by what they make explicit and how they support the business decisions.

## Worked Example and Code: Qualifying an Investment Association

An account initially exposes a collection of investments. If the actual domain rule is one investment per stock within an account, a stock symbol can qualify the association.

Reconstructed interface sketch:

```java
Investment getInvestment(String stockSymbol);
```

An in-memory implementation may use a map; a persistence-backed implementation may perform a suitable query. Both must preserve the modeled cardinality and the operation's meaning. If the application tracks separate lots, the proposed qualifier is insufficient. Do not force a one-result API merely because a map is convenient.

The book's inline SQL and simplified query helper illustrate alternative traversal mechanisms. They are not a prescribed persistence design; use the project's actual data access conventions when applying the modeling idea.

## Mixed Modeling Paradigms

An object model is not the only possible domain model. Rules, mathematical computations, or workflows may be expressed more directly in another paradigm. Adding another tool, however, creates a need to keep the combined model coherent.

- Try the dominant paradigm's available modeling options before introducing another runtime.
- Choose concepts that the selected paradigm can express naturally.
- Use the same domain names across tools and discuss the combined behavior in one language within the context.
- Use notation that conveys the model rather than forcing everything into UML.
- Verify that the additional tool earns its integration and learning cost.

A rules engine that becomes an unrelated program beside a passive object store loses the intended model/code connection. The chapter's technology-maturity judgments are historical; the reusable criteria are expressiveness, available skill, known operational limits, and integration cost.

## Key Concepts

- **Entity**: an object defined by continuity and identity rather than descriptive attributes.
- **Value Object**: a conceptually whole description whose instances are interchangeable according to domain value.
- **Service**: an explicit standalone operation with a model-based interface and no behavior dependent on its own instance history.
- **Module**: a named, cohesive part of the model and its implementation.
- **Qualifier**: a domain restriction that narrows an association's multiplicity.
- **Conceptual cohesion**: the extent to which a group of elements forms an understandable domain idea.
- **Identity scope**: the boundary within which identification must uniquely distinguish relevant Entities.

## Anti-patterns and Key Takeaways

1. Avoid giving every object an artificial domain identity. Decide what sameness means first.
2. Avoid mutating a shared value. Replace it or use an explicitly unshared implementation.
3. Avoid a service layer that drains behavior from Entities and Values. Use Services for operations that actually belong there.
4. Avoid maintaining every possible association. Keep directional, qualified relationships that earn their cost.
5. Avoid packages that describe only framework tiers while hiding the domain's conceptual organization.
6. Avoid treating a rules engine or object language as a substitute for a coherent model.

## Connects To

- [Chapter 6](ch06-life-cycle-of-domain-objects.md): aggregate identity, lifecycle, construction, and retrieval.
- [Chapter 7](ch07-using-the-language.md): the building blocks in a cargo application.
- [Chapter 10](ch10-supple-design.md): value operations, clear interfaces, and conceptual boundaries.
- [Chapter 14](ch14-maintaining-model-integrity.md): distinguish Modules within a model from Bounded Contexts between models.
