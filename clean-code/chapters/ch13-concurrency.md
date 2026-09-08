# Chapter 13: Concurrency

Source: printed pp. 177–192; PDF pp. 208–223. Brett L. Schuchert.

## Core Idea

Separate concurrency control from the work being performed, minimize shared mutable state, and test schedules and lifecycle behavior deliberately. Correct individual methods do not necessarily form a correct concurrent protocol.

## Frameworks Introduced

### Concurrency as Separation of What from When

Use concurrency for a concrete requirement such as overlapping wait time, handling multiple requests, or processing independent data. It adds scheduling, state, and testing costs, and does not automatically improve performance.

First identify what must execute independently and what shared constraints remain. A container or framework does not remove the need to understand which application objects or resources are shared.

### Concurrency Defense Principles

1. **Single Responsibility Principle**: give scheduling and synchronization a separate home from domain behavior.
2. **Limit the Scope of Data**: restrict access to shared state so a protection policy can be applied consistently.
3. **Use Copies of Data**: consider independent or read-only copies when that avoids sharing; measure relevant copying costs.
4. **Threads Should Be as Independent as Possible**: partition work and state so each worker can operate without coordinating every step.

Why it works: fewer shared invariants mean fewer possible interfering actions. Failure mode: a copied container still shares its mutable elements, or apparently local operations use shared services underneath.

### Know the Execution Model

| Model | Coordination problem | Failure to investigate |
|---|---|---|
| Producer-Consumer | Producers add work to a bounded queue; consumers remove it | Full/empty waits, backpressure, completion signaling |
| Readers-Writers | Many readers share a resource that writers update | Starvation, stale information, read/update exclusion |
| Dining Philosophers | Workers require several contested resources | Circular acquisition, deadlock, livelock |

Use these models to identify the actual resource protocol before selecting locks or queues.

### Protect the Whole Invariant

When correctness depends on a sequence of calls, individual synchronized methods may be insufficient. Choose among the source's approaches:

- **Client-Based Locking**: every client holds the appropriate lock across the complete sequence.
- **Server-Based Locking**: the shared object exposes one operation that performs the sequence under its protection.
- **Adapted Server**: an intermediary supplies the atomic operation when the original server cannot change.

Prefer an interface that makes the correct protocol easy to use consistently. Keep the protected region small enough to avoid unrelated work under the lock, but large enough to cover the complete invariant.

### Plan Shutdown Early

Model how blocked workers are released, how pending work is handled, and how resource ownership ends. A producer that stops without waking its consumer can leave the entire application waiting forever.

The source emphasizes shutdown as a separate difficult behavior. Verify normal completion, idle shutdown, shutdown with pending work, and worker failure. The precise policy—drain, cancel, or reject—belongs to the application contract.

### Test Threaded Code Deliberately

1. Verify the underlying nonthreaded behavior first.
2. Make execution configurable: single worker, multiple workers, varying collaborator speeds, and repeated runs.
3. Exercise resource limits and shutdown.
4. Run relevant configurations and target environments.
5. Vary schedules with controlled coordination or test-only instrumentation.
6. Investigate intermittent failures instead of dismissing them after a passing rerun.

The source calls schedule perturbation **jiggling**. Sleeps and yields can expose defects; they do not establish a correctness guarantee or substitute for synchronization. The book contains historical instrumentation and API examples whose exact spelling and platform behavior need verification before implementation.

## Worked Example: Lost Update

The chapter shares one counter, initially 42, between two calls to `++lastIdUsed`.

| Step | Worker A | Worker B | Shared counter |
|---|---|---|---|
| 1 | Reads 42 | | 42 |
| 2 | | Reads 42 | 42 |
| 3 | Computes and stores 43 | | 43 |
| 4 | | Computes and stores 43 | 43 |

Two calls can return the same value and lose one update. The one-line expression does not imply a single indivisible operation.

An illustrative Java counter using the source's atomic-library idea:

```java
final class IdSequence {
    private final AtomicInteger value = new AtomicInteger(42);

    int next() {
        return value.incrementAndGet();
    }
}
```

This solves only the single-counter operation, subject to the required range/overflow contract. It does not make a larger operation involving the ID, a registry, and a persistence write atomic. Choose the protected unit from the domain invariant.

## Worked Example: Check Then Act

`hasNext()` followed by `next()` can race even when both methods are individually synchronized. Another worker may consume the last element between them. An atomic `takeNext` operation, with a defined empty/closed result, lets the owner guard the whole action. Keep that distinction when applying command-query separation from chapter 3.

## Key Concepts

- **Bound resource**: a resource with a fixed capacity or quantity.
- **Mutual exclusion**: only one participant accesses a protected resource at a time.
- **Starvation**: a participant is denied progress for an excessive or indefinite period.
- **Deadlock**: participants wait on one another's held resources so none can proceed.
- **Livelock**: participants keep reacting or retrying without useful progress.
- **Critical section**: code whose concurrent execution must be controlled to preserve correctness.
- **Jiggling**: varying execution timing or ordering to expose concurrency defects.
- **Atomic operation**: an operation that other participants cannot observe or interfere with partway through its defined effect.

## Mental Models

- Treat a concurrent API as a protocol over shared invariants.
- Separate work logic from the schedule that invokes it.
- Treat a rare failure as evidence of a possible untested interleaving.

## Anti-patterns

- Making a large method synchronized without analyzing the invariant and contention.
- Assuming a thread-safe collection makes a sequence of calls atomic.
- Tuning thread counts before identifying the bottleneck.
- Using production sleeps as a fix for a race.
- Testing only successful work and omitting lifecycle transitions.
- Treating repeated passing runs as proof of correctness.

## Key Takeaways

1. Minimize and encapsulate shared mutable state.
2. Guard complete invariants rather than arbitrary method boundaries.
3. Keep scheduling independently testable.
4. Specify and verify shutdown.
5. Use testing to expose failures while retaining an explicit correctness argument.

## Connects To

- [Appendix A](appendix-a-concurrency-ii.md): interleavings, executors, CAS, throughput, and deadlock conditions.
- [Chapter 3](ch03-functions.md): query/command separation and temporal coupling.
- [Chapter 11](ch11-systems.md): construction and concern boundaries.
