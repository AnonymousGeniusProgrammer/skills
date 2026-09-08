# Chapter 19: My Project Is Not Object Oriented. How Do I Make Safe Changes?

Source: printed pp. 231–244; PDF pp. 254–267.

## Core idea

Procedural code can use the same change algorithm: find observable behavior, separate inconvenient dependencies, and establish tests. Functions, linking, preprocessing, and function pointers supply seams even without inheritance.

## Choose a seam that the language supports

| Situation | Starting point | What to verify |
|---|---|---|
| Function transforms a supplied structure | Call it in a test and inspect the structure | Initialize every global it reads; restore shared state |
| Function calls an external service through a free function | Link Substitution | Production and test executables resolve the intended definitions |
| Link units drag in unrelated code | Smaller compilation units or a preprocessor seam | Test compilation still exercises the intended production body |
| Different instances or tests need different collaborators | Replace Function with Function Pointer | Production pointers, signatures, state, and ownership remain correct |
| C++ is already a feasible project direction | Encapsulate Global References, then parameterize collaborators | Migration scope, linkage, and behavior are independently checked |

Begin with the least disruptive option. Moving a whole C project to C++ is an architectural decision, not a prerequisite for testing a function.

## Establish a procedural test harness

1. Pick the function or a larger interception point that contains the change.
2. Identify its inputs, mutated arguments, return value, global reads/writes, and outbound calls.
3. Make a minimal test executable that links the function's implementation.
4. For an external call that prevents execution, supply a test implementation with the same contract through a link seam. Record relevant arguments instead of silently discarding behavior that the test needs to observe.
5. Initialize and reset the recording state for each test. Assert both local results and required interactions.
6. Verify that the production executable retains its real implementation. Passing test linkage alone cannot establish this.

Feathers's `set_writetime` example shows that an apparently inaccessible low-level routine may only need a small structure and a controlled global clock value. Its result is observable through the structure. Start with that direct test before designing a new abstraction.

In the `scan_packets` example, a notification function is replaced at link time. A do-nothing replacement permits execution but provides only separation. A recording replacement also senses whether notification occurred and with what arguments. Respect the language's definition and linkage rules; two competing definitions in one executable are not a dependable selection mechanism.

## Add new behavior in testable functions

When a legacy procedure mixes calculation with communication, use Sprout Method's procedural equivalent: a small new function for the behavior, with a short call from the old routine.

Reconstructed from the command-sending example:

```c
/* The caller owns the returned buffer; errors follow the project's contract. */
char *command = form_command(name, options);
if (command != NULL) {
    send_bytes(command);
    free(command);
}
```

Test command formation directly, including boundary cases and formatting. Then test the connection that supplies the name/options and sends the result. Preserve allocation failure behavior and ownership; extraction must not introduce a leak or double free. A pure formatting test does not establish that a command was sent.

## Introduce explicit variability when necessary

A structure of function pointers can express a collaborator's operations, such as database retrieval and update. Tests install recording functions; production installs real ones. This gives more control than a single global link replacement, but requires deliberate initialization and a way to carry instance state. Do not scatter uninitialized callbacks or add a callback parameter to every function without a concrete test need.

Where C++ adoption is justified, Feathers incrementally wraps global operations in a `ResultNotifier` object. The wrapper initially forwards to the original global operation. A `Scanner` can then accept a notifier and tests substitute one. Preserve the original default construction path, using Parameterize Constructor, while adding the explicit collaborator path. The migration earns value through local tests, not through syntax conversion alone.

## Mental models and anti-patterns

- Treat a procedural program's globals as shared object state: isolation problems become easier to name even when the implementation remains procedural.
- A seam that replaces an entire algorithm proves little about that algorithm. Substitute its troublesome boundary instead.
- Avoid tests that pass only in a particular order because recording variables or global configuration leaked from earlier tests.
- Avoid a test-only copy of the production function; it can drift independently from what ships.
- Avoid allowing a language migration to absorb the original change request. Prefer incremental, test-supported boundaries.

## Connects to

- [Chapter 3](ch03-sensing-and-separation.md): distinguish recording behavior from simply suppressing a call.
- [Chapter 4](ch04-seam-model.md): link and preprocessing seams.
- [Chapter 6](ch06-not-much-time.md): sprouting independently testable behavior.
- [Chapter 25](ch25-dependency-breaking-techniques.md): Link Substitution, Replace Function with Function Pointer, Encapsulate Global References.
