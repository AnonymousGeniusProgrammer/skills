# Chapter 8: Boundaries

Source: printed pp. 113–120; PDF pp. 144–151. James Grenning.

## Core Idea

Keep outside code and uncertain interfaces behind a small boundary shaped by the application's needs. Use tests to make assumptions about that boundary executable.

## Frameworks Introduced

### Shape the Interface for the Consumer

Use this when a provider's broad interface gives clients capabilities or representation knowledge they do not need.

1. List the operations the application actually uses.
2. Identify unwanted capabilities and vendor-specific types leaking through callers.
3. Put a narrow application-facing interface around the unstable or overly broad boundary.
4. Keep the provider's details in a small implementation area.

The chapter's sensor-map example notes that a map exposes operations such as clearing its contents, even if recipients should only look up sensors. Generics constrain types but do not restrict all operations. An owned `Sensors` interface can limit what clients do and isolate the map representation.

This does not require a wrapper around every standard-library object. Read the boundary's maintenance cost and the capability leak before adding another abstraction.

### Learning Tests

Use this while learning a library whose behavior your application will depend on.

1. Form one concrete question about an operation or configuration.
2. Exercise that behavior in a small isolated test.
3. Record the observed result as an assertion.
4. Repeat until the required usage is understood.
5. Retain useful tests as regression checks when the dependency changes.

Why it works: the experiment both teaches the API and preserves the assumption that would otherwise live in memory. Failure mode: tests of irrelevant library capabilities cost maintenance without protecting actual use.

### Use the Interface You Wish You Had

Use this when another component is not ready but the application's needs are clear enough to express.

1. Define the operation the consumer needs in its own terms.
2. Supply a fake implementation for local development and tests.
3. Develop the consumer against that contract.
4. Implement an adapter once the actual external interface is available.
5. Test the real adapter against the provider's actual behavior.

The provisional interface is a design hypothesis. A fake confirms the consumer's assumptions; it does not prove that the future provider will fulfill them.

## Worked Example

The chapter describes a communications team waiting for a transmitter API. They knew they needed to emit a data stream at a specified frequency. They defined an application-facing transmitter contract, implemented a fake, and later bridged the real API with a `TransmitterAdapter`.

Reconstructed contract:

```java
interface Transmitter {
    void transmit(Frequency frequency, DataStream data);
}
```

The value types above are illustrative additions to make parameter meaning clear; they are not a copied source listing.

| Part | Responsibility | Verification |
|---|---|---|
| Communications controller | Decide what to transmit and when | Tests using a fake transmitter |
| Application-facing interface | Express the operation the controller needs | Contract review and consumer tests |
| Transmitter adapter | Translate the operation to the real API | Tests of units, ordering, failures, and lifecycle |
| External transmitter | Perform the external operation | Provider documentation and actual integration behavior |

A useful adapter test catches a unit mismatch that a fake cannot: if the controller supplies one frequency representation and the provider expects another, verify the translation explicitly. This extends the source example's boundary-testing reasoning without claiming a particular transmitter protocol.

## Learning Example: Logging

The chapter explores log4j through small tests until the required console configuration is understood, then encapsulates that knowledge. The reusable technique is the learning experiment. The source's logging version and configuration are historical examples, not instructions to install that version in a current project.

## Key Concepts

- **Boundary**: an interface separating owned code from external or uncertain behavior.
- **Adapter**: translates the interface needed by one side to the interface provided by the other.
- **Wrapper**: narrows or contains the use of an external object or API.
- **Learning test**: an executable experiment that records an API behavior.
- **Outbound test**: a test of an external interface in the way production code actually uses it.
- **Fake**: a substitute with enough behavior to support isolated development or testing.
- **Seam**: a place where an alternative implementation can be supplied.

## Mental Models

- Treat the boundary as the place where outside change becomes an internal contract decision.
- Think of learning tests as executable notes about the specific behavior you depend on.
- Let the consumer describe its need before adopting the provider's vocabulary wholesale.

## Anti-patterns

- **Unrestricted provider interface everywhere**: clients become coupled to unnecessary capabilities and types.
- **Fake as proof of integration**: consumer tests pass while the real adapter's assumptions remain unverified.
- **Premature exact imitation**: the application adopts an unfinished provider API and spreads its later changes widely.
- **Universal wrapping**: abstractions are added without a real boundary problem.
- **Upgrade paralysis**: lack of boundary tests makes dependency updates difficult to assess.

## Key Takeaways

1. Expose the operations consumers need.
2. Keep provider-specific knowledge concentrated.
3. Preserve useful learning experiments as tests.
4. Verify real integration separately from fake-based consumer behavior.

## Connects To

- [Chapter 6](ch06-objects-and-data-structures.md): controlling representation exposure.
- [Chapter 7](ch07-error-handling.md): translating errors at a boundary.
- [Chapter 11](ch11-systems.md): dependency injection and separating construction from use.
