# Chapter 1: Crunching Knowledge

Source: supplied PDF, pages 2-11. Examples below are compact reconstructions, not quotations.

## Core Idea

Develop a useful model by cycling between expert conversation, explicit concepts, and working software. The product of modeling is knowledge that changes how the software solves a business problem, including behavior and rules as well as the things being tracked.

## Frameworks and Application

### Knowledge Crunching

Use when requirements describe screens, files, or reports but leave the domain reasoning unclear.

1. Pick a concrete business situation that matters to the application. Ask experts to walk through what happens, what they decide, and why.
2. Sketch candidate concepts and relationships. Clarify synonyms, inconsistent meanings, and distinctions that experts make implicitly.
3. Exercise the candidate model against the scenario. Look for a step that cannot be expressed without hand waving, an unexplained exception, or a rule hidden in procedural instructions.
4. Try another model, including removing concepts. Discuss the alternatives in the same vocabulary that would appear in code.
5. Implement enough behavior to make the interpretation observable. Feed the result back into the discussion and revise both model and code.

Why it works: an expert's initial request is often constrained by familiar tools. A concrete model and prototype expose possibilities the initial feature description could not express. Developers contribute abstraction and implementation feedback; experts contribute the distinctions that make the abstraction useful.

Failure mode: a sequence of interviews followed by an implementation handoff sends knowledge in one direction. An iterative feature factory can have a similar weakness if it never consolidates what it learns into reusable concepts.

### Ingredients of Effective Modeling

Keep all five ingredients active; none is a substitute for the others.

| Ingredient | Concrete practice | Evidence it is missing |
|---|---|---|
| Binding the model and implementation | Implement an early slice of domain behavior and evolve it with the model | An attractive analysis model has no recognizable expression in code |
| Cultivating a language based on the model | Use the same concept names and relationships in conversations and implementation | Every explanation requires translation |
| Developing a knowledge-rich model | Give domain rules and activities explicit expression | Classes store fields while unexplained conditionals perform the business work |
| Distilling the model | Keep concepts that explain the application; remove concepts that do not | The model grows into an inventory of everything experts know |
| Brainstorming and experimenting | Compare alternatives with scenarios and small implementations | The first diagram becomes an unquestionable specification |

### Continuous Learning and Deep Models

Treat learning about the particular business as engineering work. Record acquired understanding in a usable model, language, and implementation so it can survive staff changes. A stable group with accumulated domain knowledge can make better distinctions than a succession of isolated implementers.

A deep model may change the organizing perspective rather than simply add detail. In the shipping example, movement of containers is initially central. Further inquiry reveals that transfers of legal and practical responsibility better explain the company's business decisions. Itineraries remain useful, but their place in the model changes.

Use this distinction when an implementation keeps acquiring awkward exceptions. Investigate the perspective organizing the concepts before adding another conditional or abstraction layer. A deeper model is judged by its usefulness and explanatory power, not its sophistication alone.

## Key Concepts

- **Domain model**: a deliberately selected abstraction of the domain that supports the application's purposes.
- **Knowledge crunching**: collaborative experimentation that turns fragmented expertise into useful concepts and relationships.
- **Knowledge-rich design**: a design that explicitly expresses relevant behavior, rules, and decisions.
- **Distillation**: separating what matters to this application from knowledge that can remain outside its model.
- **Deep model**: an abstraction that captures a more consequential domain understanding than the initial surface description.
- **Policy**: an explicit business rule or decision strategy that can be discussed as a concept.

## Worked Example: Learning Through PCB Probe Simulation

Start with a request for reports about electronic circuit boards. Instead of encoding file transformations, investigate what the reports help designers reason about.

1. **Discover the initial vocabulary.** A net connects pins; a pin belongs to a component instance. Clarify that a tool-specific name for an instance is not necessarily a separate domain concept.
2. **Choose a narrow question.** Trace signal propagation to find paths with excessive hops. The experts supply a simplification of component behavior; the software need not simulate the electronics inside a chip.
3. **Locate behavior.** A net contributes a hop. A component's type determines permitted signal paths between its pins. This distinguishes reusable type knowledge from individual instances.
4. **Remove an attractive distraction.** Topology is meaningful in PCB design but unnecessary for this simulation. Leave it out until a relevant requirement calls for it.
5. **Close the loop.** A small Java prototype, driven by tests and dummy data, computes actual path lengths without first building a UI or persistence layer. Experts can now evaluate the interpretation rather than only a diagram.
6. **Revise priorities.** The simulation is later dropped as the team learns where the application's value lies. Its early contribution remains: domain knowledge, shared language, and a functioning learning process.

The lesson is not to begin every project with a simulation. Choose a prototype that tests an uncertain, consequential interpretation of your own domain. Neither a discarded feature nor a removed concept makes the learning investment worthless.

## Worked Example and Code: Making Overbooking Explicit

An initial booking method attaches cargo to a voyage. A new rule permits bookings up to 110% of capacity. Putting the multiplication directly into the method produces the correct local calculation but obscures the business concept.

Recognize overbooking as a policy, give it a name, and move the decision behind that name. Reconstructed Java-style excerpt:

```java
public int makeBooking(Cargo cargo, Voyage voyage) {
    if (!overbookingPolicy.isAllowed(cargo, voyage)) return -1;
    int confirmation = orderConfirmationSequence.next();
    voyage.addCargo(cargo, confirmation);
    return confirmation;
}
```

The policy compares the prospective booked quantity with `voyage.capacity() * 1.1`. The caller now exposes the business decision and the sequence of booking actions separately.

Two reasons justify the object: experts can recognize and challenge the rule, and programmers can locate its implementation. Multiple interchangeable policies are not a prerequisite; expressing an important concept is itself a reason. The 10% allowance and the failure sentinel are details of this example, not recommended defaults for other software.

Use the extra abstraction selectively. A trivial detail in a supporting area may not deserve its own type; a central business decision often does. Evaluate whether the new name carries domain knowledge and improves discussion.

## Mental Models and Anti-patterns

- Treat the model as a hypothesis tested by scenarios and implementation, not a transcription of the first expert interview.
- Treat removing an irrelevant concept as progress when it clarifies the application.
- Avoid **noun harvesting**: business rules and activities can be as important as entities.
- Avoid **expert-only analysis** and **developer-only abstraction**: either breaks part of the learning loop.
- Avoid **technical success as sufficient evidence**: working code can embody a shallow or wrong interpretation.
- Avoid **premature completeness**: the model need not describe the entire profession or physical world.

## Key Takeaways

1. Ask for decisions and concrete scenarios behind requested features.
2. Make an uncertain interpretation executable early enough to learn from it.
3. Give consequential rules names that experts can use.
4. Update vocabulary, model, and code together as understanding changes.
5. Reassess the model's organizing perspective when exceptions accumulate.

## Connects To

- [Chapter 2](ch02-communication-and-language.md): cultivate and test the shared language.
- [Chapter 3](ch03-binding-model-and-implementation.md): maintain the implementation link.
- [Chapter 9](ch09-making-implicit-concepts-explicit.md): discover policies, constraints, and specifications.
- [Chapter 15](ch15-distillation.md): focus modeling effort on the core domain.
