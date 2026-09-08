# Chapter 14: Successive Refinement

Source: printed pp. 193–250; PDF pp. 224–281. Robert C. Martin. Case study: `Args`, a command-line argument parser.

## Core Idea

When new variants make a working implementation increasingly tangled, stop extending the tangle and discover a better partition through small tested transformations. Intermediate structures can be temporary steps toward a clearer final design.

## Frameworks Introduced

### Successive Refinement

Use this when a routine began simply but each new case now requires coordinated changes in several places.

1. Identify the behavior that already works and the checks that protect it.
2. Describe the repeated variation causing the growing complexity.
3. Introduce the smallest structural seam for that variation.
4. Migrate one case while keeping existing behavior working.
5. Run unit and relevant acceptance checks.
6. Migrate the remaining cases and remove obsolete paths.
7. Reassess names, ownership, and dependencies after the migration.

Why it works: each small step limits the possible cause of a regression. Failure mode: replacing the entire parser at once makes it difficult to tell whether changed results are intentional improvements or lost behavior.

### Identify the Real Axis of Variation

The initial parser handles Boolean options reasonably well. Adding string and integer options leads to parallel maps, parsing branches, conversions, getters, and error state. Each argument type affects several locations.

The emerging **ArgumentMarshaler** concept owns the behavior that varies with argument type. The parser retains overall schema interpretation and traversal, while each marshaler knows how to consume and represent its value.

Inspect the concrete repeated work before introducing a hierarchy. “Several types with corresponding behavior” is the evidence; visual similarity alone is not enough.

### Temporary Structure Can Enable a Safe Migration

The source initially moves functionality into a common marshaler class before pushing it into type-specific derivatives. It changes one map and one operation at a time, then consolidates storage and replaces repeated type handling.

Do not promote intermediate compromises into permanent recommendations. Temporary casts, type checks, or duplicate paths can be acceptable during a bounded migration if they are removed or justified before the final state is delivered.

### Give Error Information a Home

Move error identifiers, offending arguments, parameters, and formatting responsibility out of parsing orchestration into `ArgsException`.

The chapter recognizes a trade-off: default error-message formatting inside the exception is convenient, but consumers wanting different messages must supply their own presentation. Separate parsing from presentation further when the actual application requires localization or another error interface.

## Reference Tables

### Final Parser Contract Illustrated in the Book

| Schema element | Meaning |
|---|---|
| `l` | Boolean option named `l` |
| `p#` | Integer option named `p` |
| `d*` | String option named `d` |

The example schema is `l,p#,d*`; options are queried after successful construction. The punctuation above is parser syntax, not general command-line convention.

### Responsibility Map

| Responsibility | Final home | Why |
|---|---|---|
| Interpret schema and traverse supplied arguments | `Args` | Coordinates parsing |
| Consume and convert one argument type | An `ArgumentMarshaler` implementation | Type-specific behavior changes together |
| Track failure details | `ArgsException` | Error state belongs with its meaning |
| Supply default diagnostic text | `ArgsException` in the source | Convenient, with a presentation trade-off |
| Define command-line use for the application | Caller | The parser should not embed application behavior |

## Worked Example

Reconstructed usage of the source's interface:

```java
Args options = new Args("l,p#,d*", new String[] {
    "-l", "-p", "8080", "-d", "reports"
});
boolean logging = options.getBoolean('l');
int port = options.getInteger('p');
String directory = options.getString('d');
```

This compact usage example is reconstructed; it is not the parser implementation.

Trace the migration that makes those typed operations maintainable:

1. Add a marshaler type without changing any parser behavior.
2. Store a Boolean marshaler where the Boolean value had been stored and route the existing getter through it.
3. Move setting behavior into that object and verify the existing Boolean scenarios.
4. Repeat for strings and integers, initially retaining the old orchestration if needed.
5. Consolidate the type-specific maps into the shared marshaler lookup.
6. Move type conversion and argument consumption behind the marshaler operation.
7. Move parsing failure details into `ArgsException` and remove redundant parser state.
8. Put the resulting responsibilities into their own modules and reread the top-level flow.

The source's final marshaler API accepts an argument iterator. A Boolean option records its presence without consuming a value; string and numeric marshalers consume a value and report missing or invalid input. That contract is more important than reproducing every temporary class shown along the way.

## Behavioral Verification

| Scenario | What must be preserved or explicitly changed |
|---|---|
| Empty schema and empty arguments | Valid empty result |
| Unknown option | Identifies the offending option |
| Nonletter or malformed schema entry | Reports a schema problem |
| Boolean option present/absent | Correct value and presence semantics |
| String option without a value | Missing-value failure |
| Integer option with invalid text | Invalid-integer failure with useful context |
| Multiple options | Correct iterator advancement and option ownership |
| Existing accepted invocation | Same externally visible interpretation |

The narrative includes a reminder about acceptance tests: unit checks alone did not stand in for every FitNesse scenario. Keep the relevant outer behavioral checks in the refinement loop.

## Extensibility Has a Cost

The final design makes a new argument type localized, not edit-free. The book identifies a new marshaler, a typed accessor, schema-selection handling, and potentially an error code/message as extension points. Avoid claiming that adding a type requires no modification anywhere.

Similarly, this case study does not establish a need to write a custom parser in a current application. Its reusable contribution is the refactoring method and responsibility discovery.

## Key Concepts

- **Successive refinement**: improving a working draft through small verified changes.
- **ArgumentMarshaler**: type-specific argument consumption and value handling.
- **Schema**: the description of supported option names and types.
- **Incrementalism**: controlling change size so behavior remains continuously assessable.
- **Separation of concerns**: placing parsing, type conversion, and diagnostics in coherent homes.
- **Acceptance test**: an outer check of behavior through the application's expected usage.

## Mental Models

- Look for one concept distributed across several conditionals and containers.
- Treat an intermediate design as a bridge with an explicit destination.
- Regard a newly failing test as information about the last small transformation.

## Anti-patterns

- Continuing to add types after each addition visibly multiplies conditional logic.
- A large rewrite labeled as refactoring without preserving behavior.
- Keeping transitional duplication after the new route is in place.
- Leaving error bookkeeping scattered through the parser.
- Claiming a design is open to extension when several hidden change sites remain.

## Key Takeaways

1. Stop feature accumulation when the existing structure stops scaling.
2. Discover the repeated variation and migrate one case at a time.
3. Maintain both local and relevant outer behavior checks.
4. Finish the migration by deleting obsolete paths.
5. Describe the final extension cost honestly.

## Connects To

- [Chapter 3](ch03-functions.md): one level of abstraction and repeated selection.
- [Chapter 7](ch07-error-handling.md): error ownership.
- [Chapter 12](ch12-emergence.md): design emerging through tested refinement.
