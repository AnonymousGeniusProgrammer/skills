# Appendix A: Concurrency II

Source: printed pp. 317–348; PDF pp. 348–379. Brett L. Schuchert. Read with [chapter 13](ch13-concurrency.md).

## Core Idea

Reason about the operation's full shared-state protocol, then select scheduling and synchronization mechanisms. Use concrete interleavings, resource acquisition graphs, and throughput assumptions to explain why a proposed design works.

## Frameworks and Techniques

### Separate Request Processing from Scheduling

The client/server example separates accepting a request, processing it, and choosing when it runs. A scheduling interface can invoke work directly or submit it to an executor without changing the request's business logic.

1. Keep request processing independently testable.
2. Define the scheduling contract and ownership of accepted work.
3. Supply a scheduler appropriate to the deployment.
4. Verify worker failure, resource release, and shutdown as well as successful throughput.

An executor centralizes scheduling machinery; its queueing, pool size, and lifecycle still require a deliberate contract. The source's executor examples illustrate this separation, not a universal pool configuration.

### Count Interleavings Under Explicit Assumptions

For `T` threads, each performing `N` ordered steps, the source's simplified model counts order-preserving interleavings as:

```text
number of interleavings = (N × T)! / (N!)^T
```

The formula was checked against the rendered page 322. It assumes fixed straight-line sequences and preserves the internal order of each thread. It is not a complete model of every compiler transformation, processor memory effect, loop, or branch.

For two threads with two steps each, the six orderings are `1122`, `1212`, `1221`, `2112`, `2121`, and `2211`. For eight steps each, the count is 12,870. The source uses this to show why a short expression can participate in many schedules; the number itself is not a test-coverage target.

### Compare-and-Swap and Atomic Values

**Compare-and-Swap (CAS)** changes a value only if it still matches the expected prior value. A failed comparison tells a caller that interference occurred, so an appropriate operation may retry.

Use an atomic library operation for a single-value invariant when its contract fits. Use a lock, transaction, or another coordination mechanism for a larger invariant that spans values or operations. An atomic field does not automatically make its containing object atomic.

The source makes strong performance claims for nonblocking updates. Treat them as historical context and measure the actual workload; retries, contention, and the required invariant affect the choice.

### Server-Based Locking

Prefer a shared object's operation to own the complete protected action when feasible. A separate `containsKey` and `put` sequence can race despite individually safe methods; a suitable `putIfAbsent` operation encapsulates the contract.

An adapter can supply that contract for an unmodifiable component, provided all relevant access obeys the same protection policy. A wrapper cannot protect writes that bypass it.

## Worked Example: Last Item Race

The appendix shares an integer iterator between two workers:

1. A checks that an item remains.
2. B checks the same condition.
3. B takes the last item.
4. A attempts to take an item based on its now-stale check.

The defect occurs at a lifecycle boundary, so most ordinary iterations succeed. A synchronized check and synchronized next operation do not protect the interval between them.

One source solution combines them into `getNextOrNull()`. That is an intentional absence contract even though chapter 7 generally discourages null. In another language or project, use its explicit optional/completion result while preserving the atomic operation.

Rewritten pseudocode:

```text
takeNext:
    acquire the iterator's lock
    if exhausted:
        return completion
    value = current element
    advance
    return item(value)
```

Process the returned item outside the iterator's lock unless that processing is part of the shared invariant. Test zero items, one item with several workers, exact-once consumption, and completion.

## Throughput Model

The source assumes one second of I/O wait and half a second of CPU work per page, independent pages, and one CPU's processing capacity.

| Configuration | Idealized result | Assumption |
|---|---|---|
| Sequential | 1.5 seconds per page, about 0.67 pages/second | Waiting and processing do not overlap |
| Three concurrent workers | About 2 pages/second in steady state | One worker can process while others wait |

The second figure excludes ramp-up, shutdown, coordination, resource limits, and variable service times. It is a reason to investigate overlap, not a promise of a threefold speedup.

A useful application sequence is to measure wait and compute time, identify the shared bottleneck, test a bounded range of concurrency levels, and inspect both throughput and resource behavior. Adding workers beyond the limiting resource can make contention worse.

Some appendix listings contain inconsistencies or apparent errors—for example, the wrapper on printed p. 329 has a presence condition inconsistent with `putIfAbsent`, and the page-reading example's locking deserves scrutiny. Reconstruct the intended invariant and verify actual code rather than copying these listings as production-ready implementations.

## Deadlock Conditions and Interventions

The source names four required conditions:

| Condition | Meaning | Possible intervention | Cost or limitation |
|---|---|---|---|
| **Mutual exclusion** | A resource cannot serve competing users simultaneously | Remove exclusive sharing where the resource model permits | Many resources intrinsically require exclusion |
| **Lock & wait** | A participant holds resources while waiting for others | Release acquired resources when the full set cannot be obtained | Retries can cause starvation or livelock |
| **No preemption** | Resources cannot be taken back from their holder | Use a safe cancellation/release protocol where supported | Partially completed work may need recovery |
| **Circular wait** | A cycle of participants waits for one another's resources | Apply a consistent global acquisition order | Order may increase hold times or be hard to know in advance |

Break a condition throughout the relevant protocol to rule out that deadlock model. Adding more resources merely postpones exhaustion unless a bound proves all participants can proceed.

### Worked Deadlock Example

Two operations need both a database connection and a messaging connection. One acquires database then messaging; the other acquires messaging then database. If each holds the resource the other needs and waits, they form a cycle.

1. Draw the held/requested resource relation.
2. Choose a global order, such as database before messaging, if it is feasible for all paths.
3. Ensure every participating operation follows that order.
4. Release resources on success, failure, and cancellation.
5. Exercise constrained pools and overlapping operations.

The important fact is opposite acquisition order, not the create/update labels in the printed narrative, which are inconsistent across its description.

## Testing Technique and Limits

The appendix's demonstration test repeatedly runs known-broken code and succeeds when it exposes a lost increment. Distinguish this from a regression test, which should assert the correct behavior after the fix.

Use varied configurations, representative load, and recorded failure conditions. The source discusses Monte Carlo testing and historical ConTest instrumentation. Passing repeated runs can mean either correct code or a test that failed to expose the problem; maintain an explicit invariant argument.

## Key Concepts

- **Executor**: a mechanism that schedules submitted work.
- **Future**: a handle for a result that may become available later.
- **CAS**: conditional atomic replacement based on an expected old value.
- **Server-based locking**: synchronization owned by the shared operation.
- **Order-preserving interleaving**: a mixed schedule retaining each participant's internal step order.
- **Steady-state throughput**: completed work per time after start-up effects.
- **Monte Carlo testing**: repeated tests with varied inputs or configurations to expose failures.

## Key Takeaways

1. Explain the complete invariant before selecting a primitive.
2. Put compound operations behind a coherent concurrency contract.
3. State assumptions behind interleaving and throughput calculations.
4. Analyze all four deadlock conditions and the costs of breaking one.
5. Verify source listings before adapting them.

## Connects To

- [Chapter 13](ch13-concurrency.md): execution models and shutdown.
- [Chapter 8](ch08-boundaries.md): adapters and real integration tests.
- [Chapter 17](ch17-smells-and-heuristics.md): precision, boundaries, and hidden ordering.
