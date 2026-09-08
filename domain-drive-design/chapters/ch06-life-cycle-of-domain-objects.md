# Chapter 6: The Life Cycle of a Domain Object

Source: supplied PDF, pages 70-95. The purchase-order figures on PDF page 75 were visually checked. Examples are synthesized from the source.

## Core Idea

Use Aggregates to define consistency and ownership, Factories to encapsulate construction, and Repositories to provide intentional access to existing objects. These patterns protect domain meaning from the mechanics of lifecycle management.

## Frameworks and Application

### Aggregates

An Aggregate is a cluster of associated objects treated as a unit for data changes, with a boundary and one Entity designated as its root.

1. State the invariants that must hold when a change commits. Identify which objects participate in each rule.
2. Investigate change frequency, contention, ownership, and deletion semantics. Related objects need not all share the same consistency requirement.
3. Choose the boundary that keeps strict invariants together without indiscriminately locking loosely related objects.
4. Give the root responsibility for controlling access and ensuring the whole Aggregate's invariants.
5. Define how rules between Aggregates become consistent within the time the domain permits.

The source's implementation rules are significant:

- The root has global identity within the relevant system. Internal Entities need only local identity within the Aggregate.
- External objects retain references to the root. The root may lend an internal Entity reference transiently for a single operation; external clients must not retain it.
- Copies of internal Value Objects can leave the boundary without preserving an association to that Aggregate.
- Direct queries obtain roots; internal objects are reached by traversal through their root.
- Members may reference other Aggregate roots.
- Deleting an Aggregate removes its owned contents together.
- A committed change to any member must satisfy the invariants of the whole Aggregate.

Why it works: a clear boundary makes the scope of valid change and reference control explicit. It replaces both insufficient per-object checking and overcautious locking of an entire interconnected graph.

Rules spanning Aggregates are not assumed continuously up to date in the source. Event processing, batches, or other mechanisms can resolve them within a specified time. Determine that tolerance from domain evidence. The chapter does not mandate microservices, an event broker, or a universal implementation recipe for cross-aggregate coordination.

### Factories

Use when constructing an object or Aggregate is complex, exposes its internal structure, or couples clients to concrete product classes.

Each creation operation should produce a complete, consistent result atomically from the client's perspective, or fail clearly. For a new Entity Aggregate, optional details may be added later if they are not required by an invariant. An immutable Value Object leaves construction fully described in its final form.

| Construction situation | Appropriate location | Reason |
|---|---|---|
| Add a member inside an existing Aggregate | Factory Method on the root, when natural | Preserves access control and Aggregate invariants |
| One object naturally supplies the facts and rules for another | Factory Method on that closely related object | Communicates the spawning relationship and avoids pulling its internals outward |
| Complex construction has no natural host | Dedicated Factory | Encapsulates assembly without burdening an unrelated domain object |
| Simple concrete object, no useful abstraction or complex assembly | Constructor | Directness can be clearer than an unnecessary Factory |

A Factory is tightly coupled to its product. Choose arguments that already have a close conceptual relationship with the product, or are suitably basic abstractions. A parameter from which the Factory extracts many internal details creates more coupling than one simply incorporated into the result.

The Factory is responsible for valid creation but can delegate invariant logic to the product. Creation-only rules may belong in the Factory when carrying them through the object's active life would add clutter. Avoid moving ordinary ongoing domain rules out of their appropriate object merely to satisfy a construction pattern.

### Reconstitution Is Not New Domain Creation

Rebuilding an Entity from stored data creates a programming-language instance, not a new domain identity.

- Preserve the identity supplied by the stored representation; do not assign a new tracking identifier.
- Deal explicitly with stored invariant violations. Rejecting an invalid request to create a new object is different from discovering an already existing inconsistent object.
- Define an appropriate repair or failure strategy rather than silently ignoring invalid persisted state.

The chapter identifies the need for such a strategy; it does not prescribe automatic repair or authorize changing existing business data. In project work, follow the project's data-handling requirements.

### Repositories

Use for Aggregate roots that need direct access and are not conveniently reached by traversal. Present a conceptual collection of objects of a type with meaningful selection, addition, and removal operations.

1. Decide which objects actually need direct retrieval; avoid a repository for every table or class.
2. Express query criteria and results in terms of the model, encapsulating storage, queries, and reconstitution.
3. Return fully instantiated domain objects or appropriate collections. Counts and meaningful summary calculations also fit the concept.
4. Start with useful specific queries. Introduce Specification-based querying when the requirements and infrastructure justify its cost.
5. Keep transaction control with the caller that knows the unit of work; repository insert/remove operations do not ordinarily commit independently.
6. Characterize important query costs so developers can use the abstraction responsibly.

A repository's type can be an abstract type or interface, not necessarily one concrete class. Persistence mechanisms can change behind it, and an in-memory implementation can support tests. That freedom does not excuse ignoring whether a query loads the entire database.

The source discusses uncommon direct access to stored Values, such as saved alternative itineraries or enumerated values. Use the normal root-oriented rule while checking whether an apparent exception reflects an unrecognized Entity or a genuine stored-value use case. Avoid expanding access to mutable Aggregate internals.

## Worked Example: Purchase-Order Integrity Under Concurrent Changes

A purchase order has a $1,000 approval limit. Initially it contains three guitars at $100 and two trombones at $200: total $700.

Two users edit separate line items from that initial state:

- George adds two guitars and sees a total of $900.
- Amanda adds one trombone and also sees a total of $900.

If locking and validation cover only the changed line item, both updates may commit. The stored order then totals $1,100. Each local view looked valid; the combined result violates the order-wide invariant.

### First correction: align consistency with the rule

Treat the order and its line items as the unit whose total must be valid when a change commits. A transaction mechanism protecting that unit prevents the two apparently independent changes from silently violating the shared limit.

This is a modeling boundary, not a command to keep database locks open throughout a user's interaction. The chapter uses locking to illustrate the issue. A concrete concurrency mechanism must satisfy the same invariant within the application's actual interaction and transaction design.

### Second correction: avoid locking the whole catalog

Suppose a line item reads its price directly from a shared Part. Locking Parts along with orders would make different orders contend and can introduce deadlocks. Domain inquiry reveals that an order's price is not necessarily today's catalog price: an archived fulfilled order must retain the price relevant to that order.

Copy the applicable price into the line item. Keep Part separately managed. The order/line-item invariant can now be enforced within the order Aggregate; changes in catalog prices can be reviewed or applied through a separate domain process where required.

| Relationship | Consistency treatment | Domain reason |
|---|---|---|
| Order and its line-item totals | Strict at commit | Approval limit applies to the combined order |
| Order and owned line-item lifecycle | Managed together | The items belong to that order |
| Catalog Part and prices on existing orders | Governed by order status and business policy | Historical and current prices need not be identical |

The lesson is to tighten true invariants and loosen relationships that domain evidence allows to change independently. Simply choosing the smallest possible Aggregate or copying every external value would miss that reasoning.

## Worked Example: Local Identity and Independent Lifecycle

A repair application tracks a car by VIN and identifies its tires to record rotations. If a tire has meaning only within that car's maintenance history, it can be an internal Entity with local identity. Clients find the car and access the tires through it.

An engine block may have a separately tracked serial number and a meaningful lifecycle independent of the car. In an application requiring that tracking, it may be an Aggregate root of its own. The same physical containment does not determine the same software ownership boundary.

Ask what happens after removal, replacement, and disposal. If the system still needs to find and manage the removed component independently, reconsider the proposed ownership and identity scope.

## Factory and Repository Collaboration

Keep their purposes separate even if a repository internally delegates reconstitution to a Factory.

```text
Create:
    factory constructs a valid new Aggregate
    client adds its root to the repository

Retrieve:
    repository locates stored state
    reconstitution preserves the existing domain identity
    client receives the existing conceptual Aggregate
```

Avoid a transparent `find-or-create` operation when the difference between new and existing is meaningful. For a Value Object, straightforward creation often makes the shortcut unnecessary. For an Entity, concealing new identity creation can mislead the caller.

## Relational Storage Decisions

| Database role | Design approach from the chapter |
|---|---|
| Primarily an object store for this model | Prefer transparent mappings; accept selective model or schema compromises that keep the relationship understandable |
| Legacy or external system's database | Recognize a second model and choose a context relationship deliberately |
| Shared database serving other applications | Treat its independent constraints explicitly rather than letting an accidental divergent model emerge |

A simple mapping is not necessarily one object per table. An Aggregate may span or compose storage structures. If storage is intended exclusively for the object model, outside writes can bypass its invariants and restrict future refactoring. Address actual sharing requirements as architecture decisions rather than assuming all databases have that exclusive role.

## Key Concepts

- **Aggregate**: objects grouped as a unit for changes, with a root and a consistency boundary.
- **Aggregate root**: the Entity controlling external access and responsibility for the Aggregate's invariants.
- **Invariant**: a rule that must hold at the relevant consistency boundary, especially when changes commit.
- **Factory**: an abstraction that encapsulates creation or reconstitution complexity.
- **Repository**: model-oriented access to a conceptual collection of existing objects.
- **Reconstitution**: restoring a programming representation of an existing conceptual object.
- **Local identity**: distinction meaningful within an Aggregate rather than throughout the whole system.

## Anti-patterns and Key Takeaways

1. Avoid validating only the modified row when a rule spans multiple members.
2. Avoid merging every related object into one boundary; investigate contention and domain timing.
3. Avoid retaining mutable internal references outside the root's control.
4. Avoid forcing clients to assemble complex internals or decode query results.
5. Avoid assigning fresh identity during retrieval or silently committing inside each repository operation.
6. Avoid loading all objects to compute a summary that the persistence mechanism can perform appropriately.
7. Preserve framework compatibility while keeping lifecycle meaning explicit; pattern-shaped wrappers alone do not improve the design.

## Connects To

- [Chapter 5](ch05-a-model-expressed-in-software.md): identity, value semantics, and constrained associations.
- [Chapter 7](ch07-using-the-language.md): lifecycle boundaries in the shipping model.
- [Chapter 9](ch09-making-implicit-concepts-explicit.md): Specifications as criteria for validation and retrieval.
- [Chapter 14](ch14-maintaining-model-integrity.md): relationships with external or legacy models.
