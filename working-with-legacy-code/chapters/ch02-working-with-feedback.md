# Chapter 2: Working with Feedback

Source: printed pp. 9-20; PDF pp. 32-43.

## Core idea

Use **Cover and Modify**: establish tests that detect unintended change, then edit with frequent feedback. Feathers contrasts this with **Edit and Pray**, where careful editing is followed by broad, delayed checking.

## The Legacy Code Change Algorithm

The book's sequence is:

1. Identify change points.
2. Find test points.
3. Break dependencies.
4. Write tests.
5. Make changes and refactor.

**Identify change points.** Name the functions, methods, state, or decisions that must move. If the location is uncertain, investigate the code before rearranging it.

**Find test points.** Choose places where tests can observe effects from those changes. The test point may be a caller or an output boundary, rather than the edited method itself. Use effect reasoning when the reach is unclear.

**Break dependencies.** Remove the obstacles to constructing and exercising the chosen unit. Choose the smallest mechanical transformation that preserves production behavior. This step exists because the **Legacy Code Dilemma** is real: protection is needed before changing code, yet changing code is often necessary to install protection.

**Write tests.** Capture existing behavior in the affected area. These tests supply evidence about what the system does today; desired new behavior belongs in separate tests.

**Make changes and refactor.** Add the new behavior with short feedback cycles, then improve the structure within the protection obtained. The result should deliver the requested change and enlarge the area that can be changed confidently next time.

## The software vise

Use tests as a **software vise**: they hold selected behavior steady while one aspect changes. This is testing to detect change, which differs from demonstrating conformity to a complete specification. Passing tests support a claim only about the observations and cases they cover.

Keep a runnable, local set of tests close to the change. A small edit followed immediately by a failure sharply narrows the cause. Several unrelated changes followed by a next-day failure impose a larger debugging problem.

## Unit-test judgment

Feathers emphasizes two qualities: fast execution and useful error localization. His unit-test boundary excludes database access, network communication, filesystem access, and special environment setup. Tests involving those dependencies still have value; separate them so they do not consume the inner editing loop.

The chapter calls a 0.1-second unit test slow. Read that as the author's feedback-scale example, not a universal contemporary performance budget: 30,000 tests at 0.1 seconds take about 50 minutes. The applicable decision is whether the suite is quick enough to run after small changes. Use a focused subset during editing and relevant broader tests at appropriate checkpoints.

Higher-level tests can provide initial cover for several classes and make finer tests easier to introduce. They also verify interactions excluded by fakes. Their limitations are slower execution, less precise failure localization, and more work to reach a specific internal branch.

## Worked example

Reconstruction of the invoice responder example: `InvoiceUpdateResponder` needs a database connection and a servlet. The desired changes are in its response text and in `Invoice.getValue()`.

1. Try to instantiate each class in a test. A plain invoice is easy; the responder's collaborators obstruct setup.
2. Determine what the responder actually needs from the servlet: a collection of invoice IDs.
3. Apply **Primitivize Parameter** to pass the IDs, retaining the existing production source of those values.
4. Apply **Extract Interface** to the database dependency so tests can supply a controlled collaborator.
5. Verify the wiring edits conservatively, then add tests around response generation and invoice values.
6. Make the intended behavior change and run the focused tests after each small transformation.

The tests do not prove the real database adapter works. They make the responder's logic observable without requiring the database. Preserve or add appropriate adapter/integration checks where those interactions matter.

## Trade-offs and anti-patterns

- **Temporary design scars:** an extra parameter or overridable hook may be worthwhile if it enables protection with less risk. Clean it up when surrounding tests make that safe.
- **Testing the entire application for every edit:** useful system tests become a bottleneck when they are the only available feedback.
- **Insisting on perfect design before tests:** this expands the most weakly protected part of the change.
- **Covering everything before delivering anything:** work from the requested change and grow protected islands incrementally.

## Apply it

Record the change point, test point, blocking dependency, chosen transformation, observed baseline, and resulting test command. Continue when the target behavior is observable and the focused tests run reliably; report remaining gaps with their practical effect.

## Connects to

- [Ch 9](ch09-class-into-test-harness.md) and [Ch 10](ch10-method-into-test-harness.md): construction and execution obstacles.
- [Ch 12](ch12-many-changes-one-area.md): economical coverage across several change points.
- [Ch 13](ch13-characterization-tests.md): choose useful preservation tests.
