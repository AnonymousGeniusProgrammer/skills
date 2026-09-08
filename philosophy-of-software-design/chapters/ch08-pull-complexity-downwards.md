# Chapter 8: Pull Complexity Downwards

Source: first edition (2018), PDF pages 65-68. Reference notes.

## Core idea

When complexity belongs to a module's capability, handle it inside that module so its many callers do not each solve the problem. A simple interface often matters more than the simplest possible implementation.

## Decision test

Pull responsibility downward when all three conditions are supported:

1. The responsibility is closely related to the module's existing capability.
2. Absorbing it removes meaningful work from callers, often from several places.
3. The module's interface becomes simpler or easier to use.

Then compare total system complexity. A harder implementation is acceptable when it contains necessary complexity behind a coherent contract. Moving unrelated policy into a low layer fails this test.

## Configuration decision

Before exposing a parameter, ask whether users or higher layers can determine a better value than the implementation can.

| Situation | Preferred direction |
|---|---|
| The implementation can infer a good value from its own observations | Compute or adapt internally. |
| Most callers need ordinary behavior, a few need control | Supply a useful default with an explicit override. |
| The caller has real domain knowledge unavailable below | Expose the meaningful choice and explain its effect. |
| Neither developer nor caller knows how to choose | Investigate the mechanism; a knob merely transfers the unresolved problem. |

The source example derives a network retry interval from observed successful response times rather than making each administrator choose a fixed value. The chapter does not specify a production retry algorithm or a universal timeout multiplier.

## Mental models and source distinctions

- **One owner, many users**: compare the cost of solving the problem once with the cost of every caller understanding it.
- **Complete capability**: a text module that accepts arbitrary ranges can own line splitting and joining even if lines remain its internal representation.
- **Knowledge belongs where it is needed**: a backspace command is UI policy. Moving it into the text model contributes little caller simplification and leaks UI knowledge downward.

## Anti-patterns and limits

- Throwing an exception simply because choosing a sensible local response is difficult.
- Adding configuration so users must resolve an implementation question they are less equipped to answer.
- Moving the whole application into one module under the banner of pulling complexity down.

This principle complements, rather than overrides, the separation of general mechanism from special-purpose policy. Expose information and failures that callers actually require.

## Key takeaways

1. Optimize the combined experience of implementers and callers.
2. Absorb mechanism that belongs to the abstraction.
3. Let callers choose only when their knowledge or requirements justify the choice.

## Connects to

- [Ch 5](ch05-information-hiding.md): defaults and partial hiding.
- [Ch 9](ch09-together-or-apart.md): boundaries and specialization.
- [Ch 10](ch10-define-errors-out-of-existence.md): exception masking and its limits.
