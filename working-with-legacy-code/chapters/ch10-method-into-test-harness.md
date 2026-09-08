# Chapter 10: I Can't Run This Method in a Test Harness

Source: printed pp. 137-150; PDF pp. 160-173.

## Core idea

After construction works, distinguish method access, difficult parameters, unwanted effects, and missing observations. Choose a seam that exposes or separates the needed behavior while preserving the algorithm being tested.

## Hidden methods

First try the public operation that calls the private method. It exercises the helper in its actual context and avoids inventing a broader contract than its callers require.

If that route is impractical, consider whether the private behavior belongs on a separate class where it can be public safely. A desire to test several inaccessible responsibilities is useful design evidence. Making a method public solely for direct testing is not automatically safe: callers could invoke it in states that violate the original class's protocol.

For a smaller initial step, the chapter changes a C++ helper from private to protected and exposes it through a test subclass. A public forwarding method or a suitable `using` declaration gives the test access while preserving the actual implementation. This is a deliberate encapsulation trade-off that can enable later extraction.

Feathers prefers explicit structural seams over permanently testing through private-state reflection, because hidden access can conceal the design pressure that should lead to separated responsibilities. Treat this as his design judgment; use the project's context when choosing the smallest viable step.

## Difficult library parameters

The source's historical C# example receives framework file objects that have inaccessible constructors and cannot be subclassed. Extract Interface directly on a library class is unavailable when its source is outside the project's control.

Use **Adapt Parameter**:

1. Determine the operations the method actually uses, such as filename, content length, and input stream.
2. Define a project-owned protocol for those operations.
3. Write a production wrapper that delegates to the framework object.
4. Change the method to consume that protocol.
5. Use simple test objects implementing the protocol, and validate the production conversion path.

A collection boundary may require its own adaptation. Preserve enumeration order, ownership, exceptions, and stream lifetime as applicable. The example illustrates a response to an inaccessible API shape, not a claim that current framework versions have exactly the same restrictions.

## Worked example: sense a GUI side effect

Reconstruction of the account-detail window example. An event handler reads a command, opens a detail window, obtains a symbol, formats text, and sends it to another component. Running it under test would display windows and wait for interaction.

1. Extract `performCommand(command)` from the framework event handler, passing the plain command value.
2. Extract the interaction with the detail window into commands/queries, named by what this caller needs.
3. Extract the final display update into a method.
4. In a test subclass, provide a known account symbol and record the final display text.
5. Execute the real `performCommand` and assert its output.

Schematic test hooks:

```java
String getAccountSymbol() { return "SYM"; }
void setDescription(String description) { }
void setDisplayText(String text) { recordedText = text; }
```

The source observes `"SYM: basic account"` after the `"project activity"` command. The hooks remove the UI interactions while leaving the formatting/control logic real. A test that overrides `performCommand` itself would remove the very logic it needs to examine.

The initial extractions may have coarse names or lifecycle coupling. For example, a setter that also creates and shows a window is not a clean final abstraction. After tests exist, move the symbol source and display responsibilities into separate collaborators and address those protocols.

## Command/Query Separation

Use a **command** for an action that changes state and a **query** for obtaining a value without changing it. This makes reasoning about repeated calls easier: reading a value should not unexpectedly open another window or advance a calculation.

During the first mechanical extraction, existing code may not yet respect this distinction. Preserve the existing behavior, make it observable, and separate responsibilities in subsequent protected steps. Renaming a side-effecting operation as a query does not make it one.

## Other routes

- **Expose Static Method** can expose logic with little instance dependence without constructing the containing class. Keep the existing entry point delegating to it.
- **Break Out Method Object** can move a long method to an object that is easier to instantiate and decompose.
- **Subclass and Override Method** is useful when the unwanted dependency is already isolated in a suitable overridable operation.

## Completion and failure modes

Demonstrate both a controllable stimulus and an observation of the real logic's effect. Check that no remaining constructor, field initializer, or unwrapped call still performs the unwanted action. Distinguish tests of the extracted logic from tests of framework wiring and real adapters.

## Connects to

- [Ch 3](ch03-sensing-and-separation.md): define the observation requirement.
- [Ch 14](ch14-library-dependencies.md) and [Ch 15](ch15-all-api-calls.md): own a boundary around libraries/frameworks.
- [Ch 20](ch20-large-classes.md): extract the responsibilities exposed by test hooks.
