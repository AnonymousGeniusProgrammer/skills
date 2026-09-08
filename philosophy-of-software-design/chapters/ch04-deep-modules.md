# Chapter 4: Modules Should Be Deep

Source: first edition (2018), PDF pages 31-39. Reference notes.

## Core idea

A module earns its place by hiding useful implementation complexity behind an interface that is substantially easier to understand. Depth measures that relationship; it is not a synonym for a large class.

## Frameworks and concepts

- **Module**: a unit with an interface and an implementation, including a function, class, subsystem, or service.
- **Interface**: everything a caller must know to use a module correctly. Include signatures and types, but also behavior, sequencing, ownership, exceptions, and other necessary constraints.
- **Abstraction**: a simplified view that omits details unimportant to its users. Omitting a detail users actually need creates a **false abstraction**.
- **Deep module**: substantial functionality behind a relatively simple interface.
- **Shallow module**: little complexity hidden relative to the cost of learning and using its interface.
- **Classitis**: multiplying small classes on the assumption that more decomposition necessarily improves design.

## How to evaluate depth

1. Describe the useful capability in terms of the caller's task.
2. List everything the caller must know, including informal obligations.
3. Identify the implementation decisions the caller can safely ignore.
4. Compare the knowledge hidden with the interface burden introduced.
5. If the interface mostly describes the implementation, reconsider the boundary, move responsibility, or combine related functionality.

The book's rectangle illustration uses the top edge for interface cost and area for functionality. Treat it as a conceptual cost-benefit comparison, not a literal ratio based on lines of code or a numeric quality score.

## Common cases and defaults

Design the interface so ordinary usage requires little knowledge. Put uncommon controls behind separately discoverable operations. A rich interface can have low effective complexity when most callers only need a small, coherent part.

In the Unix I/O example, a small family of operations hides storage layout, caching, devices, scheduling, and permissions. Sequential access is ordinary; seeking is an extra facility. In the Java stream example, requiring callers to assemble a buffering wrapper exposes a choice the author believes should have a useful default.

The comparison concerns design, not a current API recommendation. Similarly, the garbage-collector example emphasizes removal of explicit memory-reclamation obligations; runtime configuration and performance effects are outside that simplified illustration.

## False abstraction check

A filesystem may hide block allocation, but a database may need its durability and flushing guarantees. Expose the guarantee necessary for correct use while hiding its mechanism. A short interface that leaves such requirements implicit is not deep in the useful sense.

## Anti-patterns and limits

- Extracting a trivial wrapper whose name and signature demand as much thought as its body.
- Splitting every method above an arbitrary line count.
- Counting classes as evidence of modularity while callers must coordinate all of them.
- Enlarging a class with unrelated responsibilities simply to make it look deep.

Shallow modules are sometimes unavoidable. Use the red flag to examine net benefit, not as a categorical prohibition on small functions, containers, or wrappers.

## Key takeaways

1. Count informal caller obligations as interface complexity.
2. Hide decisions that can remain irrelevant to users.
3. Make common usage simple and uncommon controls separable.
4. Choose boundaries by complexity reduction rather than size limits.

## Connects to

- [Ch 5](ch05-information-hiding.md): what to hide.
- [Ch 7](ch07-layers-and-abstractions.md): layers that add a useful abstraction.
- [Ch 9](ch09-together-or-apart.md): splitting and joining decisions.
