# Chapter 10: Classes

Source: printed pp. 135–152; PDF pp. 166–183. With Jeff Langr.

## Core Idea

Measure a class by the responsibilities it owns and the changes it must absorb. Group related state and behavior into cohesive units so a maintainer can work on one concern without understanding unrelated machinery.

## Frameworks Introduced

### The Single Responsibility Principle

The book defines SRP as one reason for a class or module to change. Use it when a class appears small but serves unrelated purposes, or when a broad name such as `Manager` hides its role.

1. List actual responsibilities rather than counting methods.
2. Identify the changes that would independently affect them.
3. Group methods and state by those reasons to change.
4. Give each group a concise name and coherent interface.
5. Separate groups when the current change or demonstrated coupling justifies it.

The source's short `SuperDashboard` example still has two responsibilities: GUI management and version information. Even five methods can represent too many independent concerns.

The chapter proposes describing a class in about 25 words without “if,” “and,” “or,” or “but” as a diagnostic exercise. Use it to expose mixed purposes, not as a grammar rule for valid class documentation.

### Cohesion

Use this when fields are used by separate subsets of methods.

1. Map methods to the state they read and update.
2. Identify groups that hang together as a meaningful whole.
3. Check whether temporary variables became fields solely to support extracted helpers.
4. Extract a coherent object when a real responsibility and lifetime emerge.

Why it works: the reader can understand state and its governing behavior together. Failure mode: promoting locals to shared state reduces parameter count but introduces hidden sequencing, reentrancy, or concurrency problems. The source itself observes that accumulated helper-specific fields can reveal another class waiting to be separated.

Maximal cohesion, where every method touches every field, is not always desirable or possible. The goal is logical relatedness, not a numerical score.

### Organizing for Change: Open-Closed Principle

Use this when adding one feature repeatedly requires editing unrelated behavior inside a large class. Seek a boundary where new behavior can extend the design while stable code remains unchanged.

The chapter refactors SQL generation from one class with operations for several statement types into separate statement responsibilities. Shared pieces such as a where clause or column list receive an appropriate home.

The book explicitly says to leave a logically complete class alone if the anticipated change is not needed. Actual system change should prompt the redesign; speculative future extensibility is insufficient.

### Isolating from Change: Dependency Inversion Principle

Use a stable abstraction when concrete details make a client volatile or hard to test. The chapter's portfolio should depend on a stock-price interface rather than directly on a changing external exchange API.

1. Identify the operation the client needs.
2. Define its contract independently of a specific provider.
3. Supply the provider through the interface.
4. Use a deterministic implementation in isolated tests.
5. Verify the actual provider at its own boundary.

An interface is useful because it isolates a concrete variation. Creating one for every class regardless of need adds mechanisms without establishing this benefit.

## Worked Example: Prime Printing

The chapter takes a long prime-printing function and separates three reasons to change:

| Responsibility | Resulting role | Example change it absorbs |
|---|---|---|
| Invocation and execution environment | `PrimePrinter` | Change how the program is started |
| Page, row, and column formatting | `RowColumnPagePrinter` | Change output layout |
| Prime generation algorithm | `PrimeGenerator` | Change how primes are computed |

The source grows from roughly one page to several because it gains meaningful names, declarations, and whitespace. The algorithm is preserved through many small tested transformations; this is not a replacement written from scratch.

Apply the sequence:

1. Capture exact existing output for representative inputs.
2. Name the phases and extract one coherent operation.
3. Move formatting state with formatting behavior.
4. Separate algorithm state from presentation state.
5. Re-run the output checks after each step.

The successful result is independent change to layout and generation with preserved output, not a predetermined number of classes.

## Worked Example: Volatile Prices

Illustrative reconstruction of the portfolio seam:

```java
interface PriceSource {
    Money priceOf(Symbol symbol);
}

class Portfolio {
    private final PriceSource prices;

    Portfolio(PriceSource prices) {
        this.prices = prices;
    }
}
```

The omitted valuation method uses the supplied source. Test the valuation with fixed prices, then test the exchange adapter separately. The interface names and types above are illustrative; the book uses a `StockExchange` interface with `currentPrice`.

## Encapsulation and Organization

Keep state and utilities private when possible. The source permits relaxing visibility for tests as a last resort after seeking a design that preserves encapsulation. It does not justify exposing every internal field for convenience.

Its Java organization starts with constants and fields, followed by public operations with nearby private helpers. Apply the readability goal through the language and project conventions.

## Key Concepts

- **Responsibility**: a reason for the class to change.
- **Cohesion**: the degree to which state and behavior form a logical unit.
- **Coupling**: dependence on other units' choices or details.
- **SRP**: one reason to change.
- **OCP**: support extension while keeping stable behavior closed to modification.
- **DIP**: depend on abstractions rather than concrete implementation details.
- **God class**: a class accumulating many independent responsibilities.

## Mental Models

- Organize a toolbox into labeled compartments that match actual tasks.
- Let different change causes reveal useful boundaries.
- Review the number of concepts a maintainer must understand, not merely the number of files opened.

## Anti-patterns

- **Method-count sizing**: a short class can still mix responsibilities.
- **Parameter reduction through shared mutable fields**: dependencies become implicit.
- **Universal interfaces**: abstractions have no variation or boundary to protect.
- **Speculative partitioning**: a stable class is split for a feature that may never arrive.
- **Tests coupled to a live volatile provider**: behavior changes because the outside world changes.

## Key Takeaways

1. Identify independent reasons to change.
2. Keep related state and behavior together.
3. Use current change pressure to justify separation.
4. Isolate external volatility behind a client-relevant contract.
5. Preserve behavior through small, verified transformations.

## Connects To

- [Chapter 3](ch03-functions.md): extraction and abstraction levels.
- [Chapter 11](ch11-systems.md): construction and injected dependencies.
- [Chapter 12](ch12-emergence.md): restraint against excess classes and methods.
