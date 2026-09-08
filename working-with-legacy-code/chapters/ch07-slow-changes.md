# Chapter 7: It Takes Forever to Make a Change

Source: printed pp. 77-85; PDF pp. 100-108.

## Core idea

Separate time spent understanding the change from **lag time**, the delay between editing and receiving useful feedback. Dependency boundaries can reduce lag even when the whole-system build remains expensive.

## Diagnose the delay

| Observation | Investigate | Productive next step |
|---|---|---|
| Finding the change consumes most of the work | Missing concepts, tangled responsibilities, poor names | Use understanding and structure techniques in Ch 16-17 |
| Editing is easy but every check takes minutes | Compile, link, startup, and external-resource dependencies | Put the relevant class or cluster in a smaller harness |
| A dependency implementation change rebuilds many clients | Concrete types and declarations crossing boundaries | Extract a stable interface and redirect consumers |
| A single class is too entangled to isolate economically | A larger boundary with fewer outward dependencies | Look for a useful interception or pinch point |

The book suggests sub-ten-second local compile-and-test cycles, and often below five seconds. Treat those as Feathers' aspirations for a responsive editing loop, not a current guarantee for every toolchain. Measure the project's actual wait and optimize the portion developers repeatedly experience.

## Break build dependencies

1. Try constructing the target class in a small harness; record each dependency that blocks it.
2. Remove runtime obstacles such as database access using explicit substitutions.
3. Inspect the concrete declarations still needed to compile and link the target.
4. Extract interfaces at the boundary to more volatile implementations.
5. Ensure types returned through those interfaces do not restore the same concrete dependency.
6. If useful, package the protected cluster and its interfaces separately from the implementation dependencies.
7. Check what depends on the cluster as well as what the cluster depends on.

This applies the **Dependency Inversion Principle** to the feedback loop. Clients relying on a sufficiently stable abstract boundary need not change merely because an implementation changes. The physical build layout must honor that boundary for the recompilation benefit to appear.

## Worked example

Reconstruction of the opportunity-processing example:

```text
OpportunityProcessing -> DatabaseGateway <- DatabaseImplementation
```

Initially, `AddOpportunityFormHandler` takes `ConsultantSchedulerDB`, and database operations create concrete `OpportunityItem` objects. Merely faking a connection can leave implementation types crossing the boundary.

Use **Extract Implementer** so the original database type name becomes an interface and the old body moves to an implementation class. Apply the same reasoning to the item type if it still ties the handler to database implementation details. Tests of the handler now use substitutes through the gateway types.

Put the processing logic and its tests on one side, gateway abstractions on the boundary, and database implementations on the other. An implementation-only change should no longer require recompiling the processing clients when the build system's dependency rules permit that. An interface change still can.

The same technique can protect callers of the handler: expose the public processing contract rather than forcing every client to depend on its implementation.

## Costs and failure modes

- More interfaces, files, and packages can make a clean rebuild slightly longer while making ordinary incremental builds much shorter. Compare the workflows actually used.
- An interface that exposes concrete dependency types can leave the compilation coupling intact.
- Moving files without changing dependency direction does not create a boundary.
- Batching many edits to amortize slow builds increases the debugging cost of a failure. Reducing lag enables smaller, more informative steps.

## Apply it

Record the old local feedback time, the dependency boundary changed, and the new focused build/test command and elapsed time. Keep broader checks for the integrations that the smaller harness deliberately excludes.

## Connects to

- [Ch 12](ch12-many-changes-one-area.md): select a larger testable cluster.
- [Ch 14](ch14-library-dependencies.md): isolate third-party abstractions.
- [Ch 25](ch25-dependency-breaking-techniques.md): Extract Interface versus Extract Implementer.
