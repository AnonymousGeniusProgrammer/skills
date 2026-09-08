# Chapter 5: Tools

Source: printed pp. 45-54; PDF pp. 68-77. Tool names and API examples describe the book's publication era.

## Core idea

Choose tools that shorten the path from an edit to trustworthy feedback. Automated refactoring is valuable when its semantic checks match the transformation being performed; a menu item named "refactor" is not itself a preservation guarantee.

## Evaluate an automated transformation

Before relying on an unfamiliar transformation in untested code, try a small representative case. For Extract Method, check collisions with existing methods in both the class and its bases. An accidental override can change behavior while leaving the program compilable.

Inspect whether an operation changes evaluation count, order, dispatch, visibility, or binding. Existing tests remain useful even with a good tool, especially when automated transformations are interleaved with manual editing. If a tool does not check the relevant preconditions, use the conservative manual procedure for that case.

## Worked example: a misleading inline operation

Reconstruction of the chapter's side-effect example:

```java
int value = getValue();
int total = 0;
for (int n = 0; n < 10; n++) {
    total += value;
}
```

Suppose `getValue()` increments a field before returning a constant. Inlining the call into `total += getValue()` changes the increment count from one to ten. The computed total can stay identical while the object state changes.

The relevant check therefore observes the side effect as well as the total. This is a useful miniature experiment when evaluating a tool's treatment of repeated evaluation. Once a limitation is known, narrow reliance on that operation rather than assuming all automated edits share the same safety level.

## Testing harness decisions

| Need | Book's tool family | Enduring criterion |
|---|---|---|
| Frequent tests in the implementation language | xUnit | Simple test authoring, isolation, focused suites, clear assertions |
| Controlled collaborator behavior | Fakes or mock objects | Exercise the real unit with deliberate inputs and observations |
| Shared, tabular input/output examples | FIT | Executable examples that people specifying behavior can understand |
| Collaborative pages and suites of FIT tests | FitNesse | Maintainable shared examples and runnable groupings |

A unit-test harness can execute integration tests too. The harness name does not establish the test's isolation or speed. GUI-based testing can check user journeys, but a volatile UI is often an expensive and distant observation point for detailed logic.

## Fixture isolation

The book's JUnit example creates an independent test-case instance per test method and prepares data in `setUp`. One pay test adds an overtime card; another checks normal pay. Fresh setup prevents the added card from affecting the second test.

Translate the principle into the framework actually used by the project: each test should receive the state it requires, and cleanup should restore shared resources it changed. A fresh fixture object alone cannot isolate static fields, singletons, databases, or files. Inspect the project's installed framework conventions before using current API syntax.

The CppUnitLite discussion shows another useful trade-off: minimize repetitive registration work so adding a test stays cheap, while accommodating the language/compiler available. The book's macros and old C++ library types are historical examples, not installation or migration instructions.

## Anti-patterns and takeaways

- A passing result assertion can miss newly repeated side effects; inspect the semantics of the edit.
- An expensive testing product cannot compensate for an inaccessible boundary in the application.
- Tool-driven edits remain bounded by what the tool actually checks.
- Make it easy to add and run the next relevant test with the existing project toolchain.

## Connects to

- [Ch 2](ch02-working-with-feedback.md): fast feedback and distinct test layers.
- [Ch 22](ch22-monster-methods.md): different strategies with and without trustworthy extraction support.
- [Appendix](appendix-extract-method.md): manual Extract Method mechanics.
