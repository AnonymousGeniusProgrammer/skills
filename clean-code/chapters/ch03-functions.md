# Chapter 3: Functions

Source: printed pp. 31–52; PDF pp. 62–83. Robert C. Martin.

## Core Idea

A function should express one coherent operation through steps at the next level of abstraction. Its name, arguments, effects, and placement should let a reader follow the program without repeatedly opening implementation details.

## Frameworks Introduced

### Do One Thing; One Level of Abstraction per Function

Use these together when a function mixes policy, navigation, string manipulation, resource handling, and formatting.

1. State the operation in a brief sentence beginning with “To …”.
2. Identify the conceptual steps that accomplish it.
3. Compare their abstraction levels: a business operation should not sit beside a delimiter append or internal path traversal.
4. Extract a group when its name introduces a meaningful concept below the caller's abstraction.
5. Read the caller again. It should express one operation through its immediate substeps.

“One thing” does not mean “one statement.” The chapter explicitly allows a function to check a page, include supporting content, and render HTML when these are the immediate steps of its named purpose. An extraction that merely rephrases an `if` without introducing a new abstraction does not improve this property.

Why it works: the reader can understand the operation before deciding which details to inspect. Failure mode: tiny forwarding methods can increase navigation without adding meaning.

### The Stepdown Rule

Use this when arranging related functions. Present the high-level operation first, followed by the functions that explain its steps, descending one level at a time. Read the result as a series of “To do X, do A, B, and C” paragraphs.

Follow the language's declaration requirements and project organization. The goal is a clear dependency narrative, not a mandatory physical ordering in every language.

### Command Query Separation

Use this when a call looks like a question but mutates state, or when callers cannot tell whether a returned Boolean describes prior state or an attempted update.

- A query answers a question about state.
- A command changes state.
- Separate them where that produces an unambiguous contract.
- When an operation must combine observation and change atomically, preserve that atomic contract and name it explicitly; separate calls can create a race. This is a cross-chapter application of the concurrency guidance.

The chapter's password-validation example hides session initialization inside a method whose name promises only a check. A caller can accidentally erase session state by asking a question. Separate validation from session creation or make the complete operation explicit.

### Keep Argument Meaning Visible

Use this when a signature forces readers to memorize order, direction, or selector values.

1. Determine which values are genuinely independent inputs.
2. Give repeated groups a domain name when they represent a real concept, such as a center point.
3. Split selector-driven operations into explicitly named entry points where their purposes differ.
4. Return transformed values instead of hiding output in an apparently ordinary input parameter.
5. Move behavior to a natural owning object when that improves the design.

Martin's preference is zero arguments, then one, then two, with three requiring caution and more than three strong justification. It is a design pressure, not a reason to move unrelated inputs into global or shared mutable state. Cartesian coordinates, an expected/actual comparison, and floating-point tolerance show that multiple arguments can be meaningful.

### Concentrate Repeated Type Selection

Use this when multiple operations repeat the same switch over a type code. Put type-specific behavior behind a common interface and centralize construction in a factory. Callers then dispatch through the interface rather than each repeating type knowledge.

This targets repeated variation. A single clear decision over a stable set of data may be suitable as a switch; chapter 6 explains the opposing change costs of procedural and object-oriented representations.

### Separate Normal Processing from Error Handling

Use this when status-code checks obscure the main operation. In the book's Java setting, exceptions allow a higher-level handler to surround a clearly named normal operation. Extract the normal operation and recovery responsibilities separately.

The chapter proposes a function devoted to a `try`/`catch` boundary. Apply the separation of concerns while preserving the actual language's error model and recovery requirements. Its examples do not establish that catching and logging every exception is adequate recovery.

## Key Concepts

- **Niladic / monadic / dyadic / triadic**: functions with zero / one / two / three arguments.
- **Flag argument**: an input that selects distinct behaviors within one function.
- **Output argument**: a parameter used to return a result by mutation.
- **Side effect**: an observable change beyond the operation that the interface leads readers to expect.
- **Temporal coupling**: a dependency on operations occurring in a particular order.
- **Argument object**: a named concept that groups related inputs.
- **DRY**: eliminate duplicated knowledge or implementation that must evolve together.
- **Abstraction level**: the degree of policy versus implementation detail expressed by an operation.

## Reference Table

| Signal | Useful next move | What to preserve |
|---|---|---|
| Sections named “initialization,” “processing,” and “cleanup” inside one long body | Extract meaningful operations and keep orchestration clear | Resource lifetime and ordering |
| A branch contains low-level details under a high-level name | Give those details a lower-level operation | Conditions and effects |
| A Boolean selects two workflows | Introduce explicit operation names | Public compatibility where required |
| Several values always travel together | Model their shared concept | Units, validation, and natural relationships |
| Repeated switches on the same type | Consider factory plus polymorphic behavior | Existing cases and failure semantics |
| A “check” changes state | Separate or explicitly name the full operation | Required atomicity |
| Four copies of almost the same algorithm | Identify the common decision or operation | Legitimate differences among cases |

## Worked Example

The chapter refines a FitNesse HTML routine that combines page lookup, inherited setup/teardown discovery, text assembly, and rendering. Its final structure lets the reader first see that test support is included before HTML is returned.

A compact reconstruction using new illustrative names:

```java
String renderTestPage(Page page) {
    if (page.isTest()) {
        includeTestSupport(page);
    }
    return page.toHtml();
}

void includeTestSupport(Page page) {
    includeSetup(page);
    includeBody(page);
    includeTeardown(page);
}
```

This fragment illustrates abstraction levels; the supporting types and inclusion methods are omitted. It is not a replacement implementation of FitNesse.

The transformation proceeds by first protecting the original rendering behavior, then extracting the repeated inclusion logic, naming the phases, and finally arranging the functions as a top-down explanation. At the high level, the reader should not need to know how an inherited page is found or which string delimiter represents inclusion.

Test the transformation against ordinary pages, test pages, missing optional setup/teardown, and suite-specific behavior. Preserve inclusion order: the new layout must not quietly change the generated output.

## Mental Models

- Think of functions as the verbs of a small language for the application.
- Use the “To …” paragraph to distinguish coherent decomposition from a list of unrelated activities.
- Treat a signature as a call-site reading exercise: can its purpose be inferred without hovering over each parameter?

## Anti-patterns

- **Shrinking by hiding inputs**: shared mutable fields can reduce argument counts while making dependencies harder to see.
- **Misleading predicates**: query-shaped names conceal updates or initialization.
- **Output disguised as input**: a caller must inspect the implementation to learn which argument is modified.
- **Repeated selection**: every new subtype requires changes across many independent operations.
- **Error-code dependency magnet**: a shared error enumeration couples unrelated callers to all possible failures.
- **Extraction by line count alone**: the result gains methods but loses a coherent narrative.

## Thresholds and Limits

Martin advocates very small functions, says they should rarely reach twenty lines, and describes a preference for one or two indentation levels. He explicitly says the small-function rule is based on his experience rather than research proof. Use these as prompts to inspect structure. Chapter 12 gives a counterweight against excessive numbers of tiny classes and methods.

Multiple returns, `break`, and `continue` can improve small functions; the chapter does not require a single exit in all cases.

## Key Takeaways

1. Keep each function's steps at one level below its purpose.
2. Make inputs, outputs, side effects, and sequencing apparent.
3. Remove repeated knowledge through a meaningful abstraction.
4. Refine a tested draft through small changes.
5. Judge the whole reading experience, including navigation between functions.

## Connects To

- [Chapter 6](ch06-objects-and-data-structures.md): when polymorphism or data-oriented procedures fit likely changes.
- [Chapter 7](ch07-error-handling.md): error boundaries and caller-oriented exception design.
- [Chapter 12](ch12-emergence.md): limiting unnecessary mechanisms.
- [Chapter 13](ch13-concurrency.md): preserving atomicity across operations.
