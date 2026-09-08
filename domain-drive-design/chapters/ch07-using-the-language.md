# Chapter 7: Using the Language: An Extended Example

Source: supplied PDF, pages 96-114. This guide follows the chapter's successive designs; alternatives are not a single final implementation.

## Core Idea

Apply the building blocks together and replay real application scenarios after each decision. Identity, access paths, construction, and consistency boundaries should reinforce the domain model while leaving room for implementation changes.

## Model and Initial Responsibilities

The example supports cargo tracking, advance booking, and eventual invoicing. Application coordinators arrange booking, incident logging, and tracking queries; domain objects provide the business answers.

| Concept | Classification in this example | Reason |
|---|---|---|
| Customer | Entity, separate root | Continuity across company systems matters; reuse an established customer identifier |
| Cargo | Entity, root | Identical crates remain distinct shipments with tracking IDs |
| Carrier Movement | Entity, separate root | One particular scheduled trip matters |
| Handling Event | Entity, separate root | A particular occurrence matters independently of equivalent descriptive facts |
| Location | Entity, separate root | Places with the same name are not interchangeable |
| Delivery History | Entity within Cargo | Its continuity is derived from the Cargo whose actual history it describes |
| Delivery Specification | Value Object within Cargo | Interchangeable delivery goals can apply to different cargoes |
| Customer Role | Value qualifying an association | Describes the role in one shipment, without independent history |

The Handling Event identification scheme in the example combines Cargo ID, completion time, and type. Treat this as a domain assumption verified for that example, not a universally collision-free event ID scheme.

Delivery Specification states a goal; Delivery History records what actually happened. Two cargoes may share an equal specification but cannot share the same history merely because both histories are initially empty.

## Framework Application: Refine Relationships Before Adding Machinery

1. **Qualify customer relationships by role.** For one Cargo, each role has one Customer. This states more than an unrestricted many-to-many association.
2. **Remove reverse links without a requirement.** Customer need not retain every Cargo it has ever shipped. A query can find relevant cargoes when needed.
3. **Choose traversal according to the business focus.** Handling Event refers to Carrier Movement; a reverse collection is not justified just because it can be drawn.
4. **Separate shared roots.** Customer, Location, and Carrier Movement are used by many cargoes and have independent identity.
5. **Give direct access only where required.** Initially the design needs repositories for Customer, Location, Carrier Movement, and Cargo. Being a root makes Handling Event eligible for a repository; it does not make a repository immediately necessary.

Why it works: the model retains meaningful associations without forcing an enormous navigable object graph. The resulting access rules explain which objects are obtained independently and which are understood through their owner.

## Worked Example: Change a Destination and Repeat a Booking

### Change the destination

Replace the Cargo's Delivery Specification with a new value describing the revised goal. This avoids mutating a potentially shared specification and keeps the goal concept separate from the Cargo's identity and actual history.

### Use an old Cargo as a prototype

A returning customer wants a similar booking. Copying the graph indiscriminately would duplicate identities or historical facts. Make the lifecycle distinctions explicit:

| Part of the prototype | Treatment in the new Cargo |
|---|---|
| Tracking ID | Generate a new ID |
| Delivery History | Create a new empty history in the initial design |
| Customer-role map | Copy the collection and role assignments |
| Referenced Customers | Retain references to the existing Customer Entities |
| Delivery Specification | The chapter's proposed Factory returns an unset specification to be supplied for the new booking |

The source gives alternative creation interfaces: a method on the prototype or a standalone Factory, with ID generation either passed in or encapsulated. Choose the location according to construction responsibility and coupling.

Reconstructed initial-design sketch:

```text
newCargoFrom(prototype):
    create Cargo with a new tracking identity
    initialize its own empty Delivery History
    copy customer-role assignments, keeping the Customer references
    leave the delivery goal for the new booking to specify
```

The new Aggregate is valid under that design's creation rules while optional booking details remain to be completed. In another domain, required invariants may make an unset goal invalid; apply the project's actual lifecycle rules rather than copying this assumption.

## Worked Example: Remove Contention From Incident Logging

### Initial design

Delivery History stores a collection of Handling Events. Adding an event therefore also updates the Cargo's history collection. Even though Handling Event is a separate root, its insertion involves the Cargo Aggregate and can contend with a concurrent Cargo update.

### New evidence

Incident logging is an operational task that should complete quickly. Events are entered frequently, while histories may be queried much less often. The stored back-reference creates coordination that the domain behavior need not require.

### Revised design

Add a Handling Event Repository and let Delivery History query events by Cargo. A targeted query can return the latest relevant event for a status calculation. The history no longer needs persistent state and can itself be derived when requested, while retaining its conceptual continuity through the Cargo.

The Cargo Factory no longer needs to construct a stored empty history. Event insertion can complete within its own Aggregate. Clients still understand Cargo, History, and Events through the same model.

| Evidence | Favors |
|---|---|
| Frequent independent event insertion and occasional status queries | Repository-backed derivation |
| Frequent traversal of the complete history with cheap direct object links | Explicit collection may remain preferable |
| A possible future query nobody needs yet | Insufficient reason by itself to pay for the redesign |

Why it works: distinctions among Entities, Values, and Aggregates identify which changes affect domain meaning and which are implementation choices. The model's conceptual association can survive a change from stored collection to query.

This example does not imply that every derived object is a Value Object. Delivery History's identity remains tied to its Cargo even when its in-memory instance is recreated.

## Worked Example: Integrate Sales Allocation Without Importing Its Model

A separate sales system allocates booking capacity across categories. Directly passing its category strings throughout the booking application would make that system's model shape the local one.

1. **Name the local responsibility.** Introduce Allocation Checker, not merely a class named after the external system.
2. **Find a local concept for the criteria.** Enterprise Segment represents dimensions by which this business groups cargo for a purpose. It is a Value Object.
3. **Translate at the boundary.** Allocation Checker maps local segments to the sales system's categories and exposes only the relevant capabilities. This is an Anticorruption Layer.
4. **Use the concept consistently.** The Cargo Repository can answer a summary query for booked quantity in a segment without returning every Cargo.
5. **Keep the decision in the domain.** Move allocation acceptance logic and segment derivation out of the Booking Application. The application coordinates; Allocation Checker owns the domain responsibility.

Why not let Cargo derive its own segment? Segmentation depends on a purpose and a changing strategy. The same Cargo could be grouped differently for allocation and tax accounting. Owning the input data does not make Cargo the owner of every classification rule applied to that data.

A policy object could later represent segmentation explicitly if requirements justify it. The chapter does not add that complexity preemptively.

### Performance trade-off

Relatively stable segmentation information could be cached near Allocation Checker to reduce remote calls. That creates synchronization work and a stale-data concern. Keep this implementation choice behind the local interface and make it only for a demonstrated performance need.

The example illustrates translation and responsibility placement, not a complete concurrency-safe reservation protocol for capacity shared across systems. Retain actual integration and consistency requirements when implementing it.

## Modules as Domain Language

Grouping classes into packages called Entities, Values, or Services tells readers about a pattern catalog, not shipping. Prefer coherent domain groupings such as customer relationships, shipping operations, and billing, with names that support the team's conversations.

Evaluate whether the package story explains the business and whether related responsibilities can be understood together. Revise the groupings as the model evolves rather than preserving this example's partition as a template.

## Key Concepts

- **Delivery Specification**: desired delivery conditions, independent of a particular realized history.
- **Delivery History**: the actual handling history associated with one Cargo.
- **Handling Event**: an identified occurrence in handling a Cargo.
- **Carrier Movement**: a particular carrier trip between locations.
- **Enterprise Segment**: a purpose-specific grouping of business activity by selected dimensions.
- **Allocation Checker**: a local domain responsibility that also shields the model from an external allocation representation.

## Anti-patterns and Key Takeaways

1. Avoid blind deep copying: create new local identity while preserving references to independent existing Entities.
2. Avoid automatic repository-per-root generation: add direct access when an actual use case requires it.
3. Avoid forcing stored collections where derivation better fits the access and contention pattern.
4. Avoid moving allocation rules into application orchestration.
5. Avoid making a data-owning object responsible for every strategy that uses its data.
6. Avoid organizing domain Modules by the names of tactical patterns.
7. Replay scenarios after a model or implementation change to check that the building blocks still work together.

## Connects To

- [Chapter 5](ch05-a-model-expressed-in-software.md): classifications and constrained relationships.
- [Chapter 6](ch06-life-cycle-of-domain-objects.md): ownership, construction, and direct access.
- [Chapter 9](ch09-making-implicit-concepts-explicit.md): specifications and emerging domain concepts.
- [Chapter 14](ch14-maintaining-model-integrity.md): Anticorruption Layer and context relationships.
