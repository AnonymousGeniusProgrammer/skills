# Chapter 2: Principles in Refactoring

Source: printed pp. 45-70; PDF pp. 67-92.

## Core idea

Refactoring is a particular form of restructuring: compose small changes that preserve observable behavior while improving understandability and the cost of future modification. Its justification is development effectiveness, not aesthetic purity.

## The Two Hats

Use **The Two Hats** to distinguish changing functionality from restructuring existing functionality.

1. State the current behavior and the desired new behavior separately.
2. While refactoring, retain existing expectations. Change a test's access path if an internal interface moves; investigate a changed result instead of automatically accepting it.
3. Switch to feature or bug-fix work explicitly when changing what the program does. Introduce the appropriate new or changed expectations there.
4. Switch back to refactoring to clarify the resulting implementation.

Switches may happen several times within minutes. This is a distinction in intent and feedback, not a requirement for separate branches or commits. Fowler discusses separate commits as a team tradeoff, not a universal rule.

**Observable behavior** depends on the system boundary. Internal call stacks and interfaces can change while the program's behavior remains stable. A published interface used by independent clients has different constraints from a private helper whose callers are all editable. Treat outputs, state changes, errors, ordering, identity, and relevant resource guarantees according to the actual contract. That checklist is an application of the book's definition, not a verbatim list from it.

## Choose the occasion

| Occasion | Use it when | Practical action |
|---|---|---|
| Preparatory refactoring | The present structure makes an imminent feature or fix awkward | Make the existing capability easier to extend, then add the change |
| Comprehension refactoring | Reading reveals intent that the code conceals | Rename, extract, and reorganize to store that understanding |
| Litter-Pickup Refactoring | A nearby obstacle can be cheaply improved | Clean it within task scope; defer a large distraction |
| Planned refactoring | Accumulated problems need concentrated attention | Explain the cost of the obstacle and keep intermediate states working |
| Long-term refactoring | A library or architectural boundary must migrate gradually | Establish a shared direction and move callers incrementally |
| Refactoring in a Code Review | Author and reviewer can explore changes together | Make suggestions concrete within the authorized review mode |

**The Rule of Three**, attributed to Don Roberts, suggests extracting a recurring solution at its third occurrence. It is a duplication heuristic, not a rule to wait for three dangerous copies or to abstract every repeated expression.

**When to stop or defer:** leave an opaque but stable component alone if it can be used through its interface without understanding or modifying its internals. Defer an expensive cleanup that does not help the current small feature enough. Compare rewrite and refactor costs through evidence; the book offers no reliable formula for choosing a rewrite.

## Design Stamina Hypothesis and Yagni

The **Design Stamina Hypothesis** connects sustained delivery speed with internal design quality. Fowler calls it a hypothesis grounded in experience, not a measured universal crossover curve.

Use **Yagni** to design well for understood needs and add costly flexibility when actual variation requires it. Refactoring makes this credible by allowing structure to evolve. Ask:

1. Does the mechanism help today's callers, or only an imagined future caller?
2. Does it add present complexity?
3. How difficult would it be to introduce later?
4. Is there a concrete irreversible decision that makes earlier preparation valuable?

Small, well-named functions can improve present design without buying speculative features. Yagni does not remove architectural thinking. The relevant tradeoff is the cost of present machinery versus the difficulty of later change.

## Constraints that change the approach

### Published interfaces and ownership

If all clients can move together, update the declaration and callers as one coherent change. If clients are independent, keep the old entrypoint delegating to the new one and migrate the clients that are under control. Retire compatibility only when its obligations end; some wrappers persist indefinitely.

Fowler favors team ownership because tiny ownership boundaries turn ordinary edits into published-interface migrations. This is process guidance, not permission to edit another team's repository.

### Integration

Long-lived branches make semantic integration harder. A text merge can succeed while a newly added call targets a function renamed on another branch. Fowler's **Continuous Integration** means integration into the shared mainline at least daily, not merely pulling mainline into a private branch or running a CI server. Adapt implementation to the project's established workflow.

### Tests and legacy code

Self-testing code supplies rapid feedback for small steps. With weak coverage, focus on the relevant area and establish tests around useful seams. Fowler points to Michael Feathers for detailed legacy techniques; this chapter is not a replacement for that method.

Reliable, language-aware automated refactorings can offer a narrower safe path without a full suite. Their guarantees depend on the operation and language. Text replacement is not equivalent to semantic refactoring; reflection and dynamic dispatch require care. Reduce step size when confidence falls.

### Database changes

Evolve schema, access code, and existing data together using versioned migrations. **Parallel change**, also called **expand-contract**, spreads a migration over compatible states: introduce the new representation, synchronize as needed, migrate readers and writers, then remove the old representation after compatibility and rollback needs permit it. Schema migration has data and deployment concerns beyond an in-memory rename.

## Worked example: move a library behind an abstraction

This reconstructs the chapter's **Branch By Abstraction** example at a conceptual level.

1. Identify what callers actually require from the old library.
2. Introduce an abstraction implemented by the old library. Existing results stay the baseline.
3. Move callers behind that abstraction in small increments.
4. Add an implementation backed by the replacement library; verify that it satisfies the required behavior.
5. Switch use at the boundary and retire old paths when no longer needed.

The technique avoids keeping the whole application broken while migrating. Its cost is temporary coexistence and an abstraction that must preserve the relevant semantics. An API wrapper with matching method names is insufficient if error behavior, ordering, or lifecycle differs.

## Performance judgment

Refactoring and optimization both can preserve functionality, but their goals differ. Refactoring improves modification cost; optimization improves resource use. In ordinary systems, favor clear, tunable code and then measure actual hot spots. Optimize one measured issue, rerun behavior checks and measurement, and keep improvements that meet the relevant objective.

The source's date-range anecdote shows why: profiling revealed repeated creation of equivalent empty ranges, and an existing named factory made a shared result easy to introduce. Its reported speedup is historical evidence for measurement, not a forecast for another system. Hard real-time systems need explicit budgets; this is an exception to casual deferral of performance work.

## Anti-patterns

- Calling a days-long broken rewrite a refactoring obscures the loss of feedback.
- Selling cleanup as moral virtue hides the concrete cost or benefit.
- Treating every ugly module as urgent neglects frequency of change and opportunity cost.
- Adding extension hooks without clients burdens both current use and future refactoring.
- Treating tests as proof or automated tools as infallible exceeds their guarantees.

## Takeaways and connections

Keep working states, make the next needed change easier, and preserve the relevant boundary contract. See [Ch 3](ch03-code-smells.md) for diagnosis, [Ch 4](ch04-building-tests.md) for feedback, [Ch 5](ch05-catalog.md) for mechanics, and [Ch 11](ch11-refactoring-apis.md) for interface migration.
