# Chapter 7: Different Layer, Different Abstraction

Source: first edition (2018), PDF pages 56-64. Reference notes.

## Core idea

A layer should add an abstraction that justifies the interfaces and dependencies it introduces. Repeating the same interface through adjacent layers often signals confused responsibility.

## Pass-through methods

A **pass-through method** mostly forwards its arguments to another method with a similar signature. It adds something to learn and maintain while hiding little. A change to the lower signature can force a corresponding change above it.

When a class contains many such methods:

1. State the distinct responsibility of each layer.
2. If a feature belongs entirely below, consider letting callers use that lower abstraction directly.
3. If responsibilities overlap, redistribute functionality so each layer owns a coherent capability.
4. If the classes cannot be separated cleanly, consider merging them.

Use this as a design investigation. A dispatcher legitimately adds routing behavior despite forwarding arguments. Several implementations of one interface can also reduce cognitive load; those implementations usually occupy the same layer and provide different behavior.

## Decorators: compare alternatives

Decorators add behavior around an existing object while preserving much of its API. Their cost is boilerplate and potential proliferation of shallow layers.

| Relationship of added behavior | Alternative to examine |
|---|---|
| Natural to the base capability and useful to most callers | Put it in the base abstraction with an ordinary default. |
| Specific to one use case | Put it with that use case. |
| Closely related to an existing decorator | Combine the extensions into a deeper decorator. |
| Can operate independently | Use a separate component without wrapping the entire base API. |

Ousterhout is skeptical of overusing decorators, but allows that they sometimes fit. Similar signatures alone are insufficient to reject one.

## Interface versus implementation

The public abstraction need not resemble internal storage. An editor can store lines internally while exposing insertion and deletion of arbitrary text ranges. The conversion between those views is useful functionality: callers stop splitting and joining lines themselves.

Think of layers as progressively different ways to understand a task: files over cached blocks over device operations, or reliable byte streams over best-effort packets.

## Pass-through variables and context objects

A **pass-through variable** travels through methods that do not use it, creating signature changes and awareness at every level. The chapter considers:

- An existing shared object that naturally owns the value.
- A global variable, with the drawback that independent instances interfere.
- A **context object** containing state shared across one system instance. Store its reference in major objects and supply it through constructors, rather than forwarding individual values through ordinary calls.

The context approach permits multiple instances and makes test configuration easier. It retains many global-state risks: dependencies may be obscure, the object can become an unrelated collection of fields, and mutation can create thread-safety problems. Prefer immutable context values where feasible. The book presents context as a compromise, not a universal dependency-injection architecture.

## Anti-patterns

- Layers whose only explanation is that one calls the other.
- A wrapper per minor feature without enough abstraction benefit.
- Exposing internal representations as the caller's only working model.
- Replacing pass-through arguments with uncontrolled shared state.

## Key takeaways

1. Trace one operation and identify what each layer adds.
2. Distinguish useful dispatch or implementation variation from empty forwarding.
3. Include both signature burden and hidden-state risks when moving context.

## Connects to

- [Ch 4](ch04-deep-modules.md): cost and benefit of an interface.
- [Ch 8](ch08-pull-complexity-downwards.md): hiding representation work.
- [Ch 19](ch19-software-trends.md): shared interfaces and inheritance.
