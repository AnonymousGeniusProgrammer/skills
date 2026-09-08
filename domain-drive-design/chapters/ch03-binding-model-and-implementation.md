# Chapter 3: Binding Model and Implementation

Source: supplied PDF, pages 25-35. Examples and comparisons are synthesized from the chapter.

## Core Idea

Choose a model that both explains the domain and provides a practical basis for implementation. Keep modeling and programming in one feedback loop so discoveries in either activity improve the other.

## Frameworks and Application

### Model-Driven Design

Use when a domain model is becoming disconnected from working software, or when code implements successive features without an organizing domain concept.

1. Select a consequential part of the domain behavior and trace its concepts to implementation responsibilities.
2. Ask whether the mapping is direct enough that the model explains the code and the code expresses the model.
3. If implementation is impractical, explore a different domain abstraction. If implementation is efficient but misrepresents the domain, revise it and the model together.
4. Exercise candidate designs with working behavior and expert scenarios. Bring transaction, persistence, performance, and tool constraints into the discussion early.
5. Propagate accepted conceptual changes into the shared language, requirements discussions, diagrams, and implementation.

Why it works: analysis and programming reveal different kinds of evidence. A shared model retains the learning from both and makes implemented behavior easier to explain and challenge.

The goal is demanding in both directions. A supposedly correct analysis model is insufficient if it cannot support useful software; an easy-to-code arrangement is insufficient if it loses the central business meaning. There are multiple valid abstractions of a domain, so implementation difficulty can be a reason to seek a better model rather than abandon modeling.

Purely technical elements need not masquerade as domain concepts. Multiple subsystems may use different models, but within one bounded context the same domain model should connect analysis, design, and implementation.

### Hands-On Modelers

Use when architects or analysts supply diagrams while programmers independently decide how the software actually works.

- Have technical model contributors participate in implementation details, including small examples or collaborative coding.
- Give programmers responsibility for expressing and refining the model; ensure access to domain experts and model discussions.
- Use the Ubiquitous Language to communicate implementation feedback to people with other specialties.
- Retain specialist roles where they help, while keeping modeling and implementation connected.

Why it works: subtle responsibility assignments, interface meanings, and technology constraints rarely survive a diagram-only handoff. A modeler who stops working with the code loses timely feedback; developers who see themselves as mere implementers can unknowingly erode the model through local changes.

This is not a demand that every participant perform identical work. It is a requirement for a live exchange across responsibilities, especially between technical people making model decisions and those changing the code.

## Worked Example: From PCB File Scripts to Nets and Buses

PCB engineers assign layout rules to thousands of nets. Many nets belong to a bus and should share rules. The external layout tool understands nets but has no bus concept.

### Initial mechanism

Scripts sort net names, find a naming prefix, and append rules to matching lines in a file. For a single operation this can be practical. As many operations accumulate, business meaning is inferred repeatedly from string manipulation. A different file format requires rewriting much of the solution even though the domain operation has not changed.

### Model and responsibility changes

| Element | Responsibility |
|---|---|
| Net | Expose its own rules together with rules applying through its bus |
| Bus | Represent the grouping and accept rules applying to its members |
| Layout Rule | Represent a constraint and its parameter |
| Net-list import | Translate an external representation into model objects |
| Inferred Bus Factory | Infer initial bus membership from the source naming convention |
| Repository | Locate a net or bus by its domain name |
| Rule export | Expand the model's assigned rules into the external tool's required form |

Naming conventions move to an input interpretation responsibility. They no longer define what a bus means throughout the model. File parsing and output remain necessary but stop carrying the central rule-assignment logic.

Reconstructed pseudocode:

```text
Net.assignedRules():
    return union(this.directRules, this.bus.assignedRules())

assignBusRule(busName, rule):
    bus = busRepository.findByName(busName)
    bus.assignRule(rule)
```

The source uses an `AbstractNet` superclass for shared rule assignment. The important modeling decision is that a net's effective rules include its bus's rules; that does not require copying the book's historical Java collection APIs into a modern project.

### Test the concept, separately from its discovery mechanism

Create two nets and explicitly add them to a bus. Assign a minimum-width rule to the bus. Verify that both nets report that rule. This test should not depend on the names matching a prefix: a bus is not conceptually defined by the importer’s naming trick.

Test name-based inference separately, and test import/export at their interfaces. The resulting tests can distinguish a defect in domain behavior from a defect in interpreting the external file format. A new interactive UI can use the same model operation.

### Trade-off

The script remains a credible solution for one simple, stable file operation. The model earns its cost when multiple operations share the same domain meaning, when richer constraints appear, or when new interfaces must reuse that behavior. Do not justify a large model merely because the implementation language supports objects.

## Worked Example: User Expectations and the Implemented Model

The chapter describes a historical browser where a favorite appears to be a named website but is stored as a file. A colon in its name triggers a filename error that is meaningless under the user's apparent model.

Two coherent resolutions are possible: make the file-based concept part of the user's understanding, or change storage so a favorite can follow its own intended naming rules. Keeping the discrepancy and patching individual symptoms preserves the contradiction.

Apply the underlying test to your own UI: does a user action encounter a surprising rule from a different implementation model? Align the behavior and the user's domain expectations. This does not require exposing technical internals in every interface; the source example specifically illustrates a mismatch between the presented and implemented concepts.

## Key Concepts

- **Model-Driven Design**: a design whose domain responsibilities and terminology directly express a useful domain model.
- **Analysis model**: a model developed to understand a domain, potentially without regard to implementation feasibility.
- **Hands-On Modelers**: technical model contributors who remain involved with implementation, and developers who participate in modeling.
- **Modeling paradigm**: conceptual constructs a tool or language can express directly, such as objects, logical rules, or mathematical functions.
- **Conceptual responsibility**: the domain role an implemented element plays, beyond its technical mechanics.

## Mental Models and Anti-patterns

- Treat a model/code mismatch as a design problem to investigate in both directions.
- Treat programming as a design activity: responsibility assignments and names can change the model.
- Avoid **the authoritative wall diagram** when it cannot explain transaction boundaries or working behavior.
- Avoid **parallel analysis and implementation models** joined by a complicated translation that nobody maintains.
- Avoid **objects as decorated records** when the important domain behavior remains implicit elsewhere.
- Avoid **hands-off architecture** that receives feasibility feedback only after implementation has diverged.

The chapter's assessments of procedural languages and tools reflect its period. Preserve the criterion of expressing the model clearly; evaluate contemporary language capabilities in the project rather than treating those historical assessments as present-day compatibility rules.

## Key Takeaways

1. Require domain fidelity and implementation practicality from the same model.
2. Test core behavior without incidental parsing, storage, or UI mechanisms.
3. Keep expert knowledge connected to programmers making detailed decisions.
4. Use small implementations to reveal the consequences of model choices early.
5. Align externally visible behavior with the model users are being taught.

## Connects To

- [Chapter 2](ch02-communication-and-language.md): propagate conceptual change through the language.
- [Chapter 4](ch04-isolating-the-domain.md): isolate the implementation that expresses the model.
- [Chapter 6](ch06-life-cycle-of-domain-objects.md): make lifecycle and consistency boundaries practical.
- [Chapter 14](ch14-maintaining-model-integrity.md): make the single-model requirement local to a context.
