# Chapter 6: Objects and Data Structures

Source: printed pp. 93–102; PDF pp. 124–133. Robert C. Martin.

## Core Idea

Choose deliberately between objects that hide representation behind behavior and data structures that expose information for procedures to use. Neither representation is universally superior; they support different kinds of change.

## Frameworks Introduced

### Data Abstraction

Use this when clients depend on an object's internal fields despite a layer of getters and setters.

1. Identify the operations clients actually need.
2. Separate those needs from the current representation.
3. Expose operations in terms of the concept, including its invariants.
4. Keep representation decisions within the implementation.

The chapter compares Cartesian fields with an interface that can read Cartesian or polar coordinates and set coordinates together. It also compares exposing tank capacity and gallons with exposing the proportion of fuel remaining. The latter expresses the client's needed abstraction without advertising the storage scheme.

Why it works: callers depend on a stable meaning rather than the current layout. Failure mode: mechanically adding getters and setters exposes the same representation through more syntax.

### Data/Object Anti-Symmetry

Use this when deciding where behavior belongs and which changes to make inexpensive.

| Expected change | Procedural operations over data | Polymorphic objects |
|---|---|---|
| Add an operation for existing types | Add a procedure; data types can stay unchanged | Each relevant implementation must support the operation |
| Add a new type supporting existing operations | Existing procedures may need another case | Add an implementation; existing callers can stay unchanged |

The table describes the classical structures in the chapter, not every language feature. Ask which dimension is likely to vary and keep that judgment specific to the part of the system under discussion.

### The Law of Demeter

Use this when a method navigates through a graph of behavioral objects to obtain data for work it performs itself.

The chapter's formulation permits calls on the containing class, objects created within the method, arguments supplied to it, and objects held by the containing instance. Traversing objects returned by those calls can expose knowledge of strangers' internals.

1. Identify whether each navigated value is an object hiding behavior or a deliberately exposed data structure.
2. State the ultimate operation that motivated the traversal.
3. Ask the natural owner to perform that operation.
4. Keep representation knowledge inside the owner.

Counting dots is insufficient. Splitting a chain into temporary variables does not remove the dependency. Navigation through plain data is different, and fluent APIs do not automatically represent exposed internals.

### DTOs and Active Record

Use **Data Transfer Objects** for passing structured data across boundaries. Accessors required by a framework do not by themselves turn a DTO into a behavioral object.

The chapter treats **Active Record** as a data structure with navigation/persistence operations such as save and find. Keep unrelated business policy out of such records when it would create a hybrid of public data and object behavior. Put policy in objects operating on that data, or make a deliberate behavioral model that actually owns its invariants.

## Worked Example

The chapter's scratch-directory example begins by traversing context → options → directory → absolute path, then constructing a filename and opening an output stream.

The useful question is why the caller needed the path. Its real goal was to create a scratch file. A reconstructed interface is:

```java
OutputStream output = context.createScratchFile(classFileName);
```

Now the context can own directory selection, path construction, and stream setup. The caller still needs a clear ownership contract for closing the returned resource; shorter syntax does not settle resource lifetime.

A second worked decision uses the chapter's shapes:

- With `Circle`, `Rectangle`, and `Square` as plain data, a new perimeter calculation can be added alongside area without changing those data definitions.
- With a `Shape.area()` interface, a new triangle implementation can support existing area clients without adding a triangle branch to each caller.
- Select based on actual variation. “All procedural code must become objects” contradicts the chapter's explicit conclusion.

## Key Concepts

- **Object**: hides representation and exposes meaningful behavior.
- **Data structure**: exposes information for external procedures to use.
- **Data abstraction**: a contract expressed in conceptual terms instead of storage terms.
- **Hybrid**: exposes internal data while also carrying substantial behavioral responsibilities.
- **Train wreck**: chained navigation that can reveal a deep object graph to the caller.
- **DTO**: a representation intended for transferring data.
- **Active Record**: a record-like data structure with persistence/navigation methods.

## Mental Models

- Think in two change dimensions: new types versus new operations.
- Ask for the intended job, rather than the internal pieces needed to perform it elsewhere.
- Judge encapsulation by what callers must know, not by field visibility alone.

## Anti-patterns

- **Getter-based pseudo-encapsulation**: every field remains externally readable and writable without invariant protection.
- **Hybrids**: clients gain both dependency on internal structure and dependency on behavior.
- **Dot-count policing**: syntax is mistaken for a design diagnosis.
- **Delegation explosion**: adding dozens of pass-through getters avoids long chains while retaining the same representation coupling.
- **Object-oriented absolutism**: data structures are rejected even when new operations dominate the expected changes.

## Key Takeaways

1. Decide what variation to make easy.
2. Expose either purposeful behavior or deliberate data clearly.
3. Hide representation through abstractions rather than automatic accessors.
4. Diagnose navigation by the knowledge it exposes.

## Connects To

- [Chapter 3](ch03-functions.md): repeated switches and polymorphic dispatch.
- [Chapter 8](ch08-boundaries.md): controlling third-party representations.
- [Chapter 10](ch10-classes.md): responsibilities and change isolation.
