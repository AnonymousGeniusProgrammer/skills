# Chapter 12: Emergence

Source: printed pp. 171–176; PDF pp. 202–207. Jeff Langr.

## Core Idea

Use Kent Beck's ordered rules of simple design to refine working software. Tests, removal of duplication, clear intent, and restraint in mechanisms work together; their order resolves conflicts.

## Frameworks Introduced

### Four Rules of Simple Design

The book gives these priorities:

1. Runs all the tests.
2. Contains no duplication.
3. Expresses the programmer's intent.
4. Minimizes the number of classes and methods.

Use the rules when deciding whether a refactoring improves the design. The last rule is subordinate: fewer methods are not an improvement if they obscure intent or duplicate knowledge. Conversely, very small methods and SRP can be taken too far; unnecessary mechanisms should be removed.

### Refactoring as the Design Feedback Loop

1. Establish the required behavior with tests.
2. Make a small implementation change.
3. Inspect the resulting design for duplication, obscured intent, and excess mechanisms.
4. Refine the relevant structure.
5. Re-run the behavior checks.

Testability supplies feedback about coupling: difficult setup or uncontrollable dependencies can indicate a need for clearer boundaries. Tests improve design through the decisions they encourage; their mere presence does not prove that the design is good.

### Remove Duplication of Knowledge

Use this when two pieces of code implement the same underlying idea, even if their text differs.

- A collection's `isEmpty()` can derive its answer from `size()` instead of maintaining an independent emptiness flag.
- Image transformations can share the operation that replaces the old image and disposes of its resources.
- Similar algorithms with a genuine variation point can share a skeleton through **Template Method**.

First establish that the repeated logic represents one reason to change. Accidental resemblance is insufficient; merging unrelated concepts can create selector arguments and artificial coupling.

### Expressive Design

Choose names that reveal responsibilities, standard pattern vocabulary where it accurately describes the design, and tests that illustrate the intended behavior. Spend time refining the explanation after the code first works.

### Minimal Classes and Methods

Use this as the balancing rule when a design grows more files and forwarding calls without clearer concepts. The source specifically criticizes mandatory interfaces for every class and dogmatic separation of all data from all behavior.

## Worked Example: Vacation Policy

The source shows two vacation-accrual workflows with the same overall sequence but different rules for a region's minimum allowance.

Reconstructed Template Method:

```java
abstract class VacationPolicy {
    final void accrueVacation() {
        calculateBaseHours();
        applyRegionalMinimum();
        updatePayroll();
    }

    abstract void applyRegionalMinimum();
    abstract void calculateBaseHours();
    abstract void updatePayroll();
}
```

This is a structural illustration. In a concrete design, the genuinely shared operations have shared implementations; they need not all be abstract as in this self-contained outline.

Reason through the transformation:

1. Verify the existing outcomes for both regional policies, including below and above the minimum.
2. Identify the shared sequence and the actual varying calculation.
3. Extract the stable sequence and leave the minimum rule as the variation point.
4. Run the same behavioral checks for each policy.
5. Ask whether the abstraction reduced duplicated policy without making the original workflows harder to understand.

Template Method introduces inheritance and a fixed sequence. If the two policies evolve independently or their workflows are only superficially similar, a shared helper or composition may be more appropriate. That is application judgment rather than another named prescription from the chapter.

## Decision Table

| Proposed change | Apply the ordered rules |
|---|---|
| Delete tests to make the system smaller | Reject: size is the lowest priority |
| Extract a shared invariant into one implementation | Favor when both uses truly share the invariant |
| Add a helper whose name exposes a meaningful step | Favor when it improves intent despite adding a method |
| Introduce interfaces for all classes | Remove the requirement unless an actual boundary needs it |
| Merge unrelated branches into a configurable utility | Reassess whether the apparent duplication represented one idea |
| Keep an extra abstraction with no remaining use | Remove it when behavior and intent remain clear |

## Key Concepts

- **Simple design**: the four ordered rules used together.
- **Emergence**: structure discovered through implementation, testing, and refinement.
- **Implementation duplication**: repeated logic or state encoding the same fact.
- **Template Method**: a stable algorithm skeleton with selected overridable steps.
- **Expressiveness**: making the intended operation apparent to the reader.
- **Minimality**: eliminating unnecessary mechanisms after preserving the higher priorities.

## Mental Models

- Use the priority order to resolve competing “clean code” recommendations.
- Treat tests as support for revising a design, not a reason to freeze it.
- Let recurring real behavior reveal reusable concepts.

## Anti-patterns

- Counting methods without considering what they communicate.
- Creating a framework before actual variation is understood.
- Keeping parallel state that can disagree, such as an independent count and empty flag.
- Ending development at the first working draft.
- Repeating an algorithm because the instances differ cosmetically.

## Key Takeaways

1. Apply the four rules in their stated order.
2. Eliminate repeated knowledge, not merely repeated characters.
3. Use clear names and tests to express design intent.
4. Remove abstractions that do not earn their complexity.

## Connects To

- [Chapter 9](ch09-unit-tests.md): readable tests supporting change.
- [Chapter 10](ch10-classes.md): responsibility and cohesion.
- [Chapter 14](ch14-successive-refinement.md): a longer example of emergent structure.
