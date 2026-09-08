# Chapter 9: Unit Tests

Source: printed pp. 121–134; PDF pp. 152–165. Robert C. Martin.

## Core Idea

Tests preserve the ability to change production code only while they remain readable, reliable, and inexpensive to use. Design test code with the same care as production code, emphasizing clear behavioral examples.

## Frameworks Introduced

### The Three Laws of TDD

When practicing the book's TDD method:

1. Begin production work with a failing unit test.
2. Write only enough of that test to demonstrate failure; a compilation failure counts.
3. Write only enough production behavior to make the current failing test pass.

These rules keep tests and implementation close together in a short feedback cycle. After green, refine structure while preserving behavior. The source presents TDD as a core discipline; describing it does not establish that every unrelated edit in an existing project needs a new test.

Why it works: a narrow failing example clarifies the next behavior and makes regressions visible. Failure mode: tests that repeat implementation details can pass without checking the intended outcome.

### Build-Operate-Check

Use this when a test's setup machinery obscures what behavior it demonstrates.

1. **Build** the state relevant to the scenario.
2. **Operate** on the system through the behavior under test.
3. **Check** the observable result.

Remove irrelevant conversion, request-building, casting, and navigation detail from the scenario into well-named helpers. Keep enough data visible to understand why the expected result follows.

### Domain-Specific Testing Language

Use this when repeated low-level setup or assertions make tests harder to read than the domain rule.

1. Identify repeated domain operations in actual tests.
2. Name small helpers for those concepts.
3. Keep scenario-specific facts at the call site.
4. Refine the helpers as the tests evolve.

The testing language emerges through use. Building a general testing framework before the scenarios exist can produce more mechanism than clarity.

### Single Concept per Test

Prefer one coherent behavioral conclusion in a test. Minimize assertions when that improves clarity, but do not split a single concept into cumbersome scaffolding just to enforce exactly one assertion.

The chapter discusses one-assert-per-test, then explicitly accepts multiple assertions when splitting creates excessive duplication or mechanism. Its stronger recommendation is one concept per test.

### F.I.R.S.T.

| Property | Apply it by | Typical failure |
|---|---|---|
| **Fast** | Keep feedback short enough for frequent runs | Slow tests are avoided during refactoring |
| **Independent** | Make each test establish its own preconditions | One failure triggers an order-dependent cascade |
| **Repeatable** | Control relevant environment, data, time, and dependencies | A result changes with machine, network, or earlier activity |
| **Self-Validating** | Produce an automatic pass/fail outcome | Humans must inspect logs to decide success |
| **Timely** | Write tests before the production behavior in TDD | The design hardens before testability is considered |

The spelling in this book is **Timely**, not an alternative expansion such as “Thorough.” A full integration test may require infrastructure; make that dependency explicit rather than pretending it is an isolated unit test.

## Worked Example: Month Arithmetic

The chapter examines a miscellaneous `addMonths` test containing several separate calendar scenarios. Reconstruct the behavioral rules as individually named cases:

| Starting date | Operation | Expected date | Concept |
|---|---|---|---|
| 31 May 2004 | Add one month | 30 June 2004 | Clamp a day absent from the target month |
| 31 May 2004 | Add two months directly | 31 July 2004 | Preserve the day when the target month supports it |
| 30 June 2004 | Add one month | 30 July 2004 | Preserve an ordinary supported day |
| 28 February | Add one month | 28 March | A last day does not automatically become the target month's last day |

The chapter uses the last case to expose a missing example in the original test suite. “Add two months directly” is different from two successive one-month calls when an intermediate result is clamped; preserve the contract under test.

Rewritten illustrative test:

```java
@Test
void addingOneMonthClampsToTheLastValidDay() {
    DateValue start = date(2004, 5, 31);

    DateValue result = start.addMonths(1);

    assertEquals(date(2004, 6, 30), result);
}
```

`DateValue` and `date` are illustrative helpers, not standard APIs or the book's exact listing.

## Worked Example: A Test Language's Trade-off

For an environmental controller, the source replaces multiple Boolean checks with a compact string encoding heater, blower, cooler, and alarm states. Uppercase means on and lowercase means off. It knowingly accepts a mental mapping cost to make a set of expected states easier to scan.

Apply the judgment, not the encoding mechanically: compare whether a compact domain representation makes related scenarios easier to verify or makes failures cryptic for the team. A named expected-state object can serve the same purpose in a project that does not share the string convention.

## The Dual Standard

Test code may favor readability over memory or CPU optimization because it runs under different constraints. It still needs clean names, structure, and maintainability. This exception does not excuse a slow suite that undermines frequent feedback.

## Key Concepts

- **TDD**: short cycles of a failing test, sufficient implementation, and refinement.
- **Build-Operate-Check**: visible separation of setup, action, and verification.
- **Test DSL**: helpers expressing scenarios in the application's vocabulary.
- **Single concept**: one understandable behavioral proposition.
- **Dual standard**: different efficiency constraints, equal care for clarity.
- **F.I.R.S.T.**: Fast, Independent, Repeatable, Self-Validating, Timely.
- **Tests enable the “-ilities”**: reliable tests support maintainability, flexibility, and reuse by reducing fear of change.

## Mental Models

- Treat each test as documentation by example that should explain its own failure.
- Treat repeated hard setup as feedback about dependency structure.
- Regard the test suite as maintained software rather than disposable scaffolding.

## Anti-patterns

- **Quick-and-dirty tests**: maintenance cost eventually motivates deleting the protection they provide.
- **Mystery setup**: helpers hide the facts that explain the result.
- **Miscellaneous tests**: unrelated scenarios share a test and obscure intent.
- **One-assert dogmatism**: a simple test is replaced by inheritance or duplicated setup solely to count assertions.
- **Manual result interpretation**: printing output is mistaken for an automatically verified test.

## Key Takeaways

1. Make the domain behavior more prominent than setup machinery.
2. Test one concept with enough assertions to establish it.
3. Keep tests fast, independent, repeatable, self-validating, and timely.
4. Refactor tests alongside production code.

## Connects To

- [Chapter 8](ch08-boundaries.md): learning and adapter tests.
- [Chapter 12](ch12-emergence.md): tests as the first rule of simple design.
- [Chapter 16](ch16-refactoring-serialdate.md): boundary testing of a real date library.
