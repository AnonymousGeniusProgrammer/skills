# Chapter 11: I Need to Make a Change. What Methods Should I Test?

Source: printed pp. 151-171; PDF pp. 174-194.

## Core idea

Reason forward from each change point to the observations it can affect. An **effect sketch** records values and results connected by possible influence, helping select tests that protect consequences rather than merely matching edited methods.

## Build an effect sketch

1. Mark each method or value that will change.
2. For a returned value, find callers that use it.
3. For modified state, find the methods and objects that later read it.
4. Inspect superclasses, subclasses, and other clients with access to that state.
5. Follow mutable objects passed through parameters or returned by collaborators.
6. Include static/global state and external effects discovered along those paths.
7. Continue until you reach useful observation points or a justified boundary beyond which this effect cannot propagate.

Use a node for a relevant variable, object state, or method result; an arrow means a change can influence the next node. This is not merely a call graph. A caller may ignore a return value, while a mutation can influence later code without a direct call relationship.

## Three common propagation routes

| Route | What to inspect | Easily missed case |
|---|---|---|
| Return values used by callers | Call sites and derived calculations | A caller stores the result for later use |
| Mutation through passed objects | Aliases, shared collections, returned subobjects | A constructor retains a list reference |
| Static/global data changed and later read | Readers beyond the local call chain | A helper silently updates the current display |

Language-specific mechanisms can add routes. Inspect actual code and dispatch rules rather than treating this as an exhaustive compiler model.

## Worked example: directory index

Reconstruction of `InMemoryDirectory`: `generateIndex` builds an element containing names of the directory's other elements and adds it to the collection. The proposed change maintains the index during each `addElement` call.

An initial sketch is:

```text
generateIndex ----> elements ----> getElementCount
addElement -------/        \----> getElement(name)
generateIndex ----> index.text --> getElement("index").getText()
```

Both mutation points can affect the count and lookup results. Count alone is insufficient: it can stay correct while the text is wrong. Inspecting the returned index element extends the observation to its contents.

Before changing the lifecycle, capture the currently supported sequence: create directory, add elements, generate index, then query. Add new tests for interleaved additions only as desired new behavior. The source's old implementation behaves poorly if index generation is repeated; characterize that only if it is relevant to the change or supported usage, and keep any intended fix explicit.

Now inspect `Element.addText`. If it also writes to a global display, the sketch must acquire a second observable branch. A familiar-looking data class can carry an external effect that its signature does not reveal.

## Aliasing and immutability

The source's `CppClass` retains a supplied declarations list. Other holders of that list, its mutable elements, or returned elements could affect later results. To conclude otherwise, inspect construction and access paths: who retains references, who mutates them, and when?

An immutable Java string narrows effects differently from a mutable list. A final reference is not deep immutability. Likewise, a C++ `const` method does not by itself prove total absence of effects: `mutable` state and other reachable objects need consideration.

Treat access control and language guarantees as reasoning tools, with their actual scope. Private state often narrows where to look; package/protected/public access broadens the client set. Document a verified contextual invariant when it materially reduces future reasoning.

## Simplify effect structure

After tests exist, remove duplication that makes equivalent behavior flow through independent paths. In the source, making `getInterface` call `getDeclaration` allows tests of interface generation to exercise declaration retrieval too. This narrows the effect graph and can simplify later test selection.

That does not make every query fully covered automatically. An observation can exercise another method without asserting all of its relevant behavior. Use the graph to select tests, then verify the assertions discriminate the changes you care about.

## Mental models and trade-offs

- Debugging often reasons backward from a failure; change planning reasons forward from a modification.
- Simpler effect sketches mean less hidden knowledge is needed to predict consequences.
- Encapsulation serves understanding. A small exposure that enables explanatory tests may improve practical understanding now and make stronger encapsulation feasible later.
- "This object should be passive" is a hypothesis to verify, not a boundary to assume.

## Apply it

For each change point, show its path to at least one chosen observation and list any outgoing effects requiring separate coverage. Where a path is uncertain, use targeted exploration or a reversible perturbation to check whether the proposed test detects it.

## Connects to

- [Ch 12](ch12-many-changes-one-area.md): choose interception points for several changes.
- [Ch 13](ch13-characterization-tests.md): choose cases and assertions at those points.
- [Ch 20](ch20-large-classes.md): use effect structure to find responsibilities.
