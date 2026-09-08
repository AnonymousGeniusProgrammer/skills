# Chapter 13: I Need to Make a Change, but I Don't Know What Tests to Write

Source: printed pp. 185-195; PDF pp. 208-218.

## Core idea

**Characterization tests** record what the existing code actually does so that unintended changes become visible. They describe an observed baseline; they do not declare that every captured behavior is desirable or correct.

## Establish a baseline

1. Exercise the code in a controlled harness.
2. Make an assertion that is expected to fail because its expected value is a deliberate probe.
3. Inspect the failure to learn the actual result.
4. Confirm the setup and observed behavior, then replace the probe with that stable expected result.
5. Repeat for other cases relevant to understanding and changing the code.

A baseline comes from the pre-change program. Once established, a failing characterization test is a reason to inspect the production change, not to refresh expected values automatically. When a behavior change is intended, revise its specific expectation deliberately and preserve neighboring cases.

## Worked example: page generator

Reconstruction of the source's introductory example:

```java
PageGenerator generator = new PageGenerator();
assertEquals("probe-value", generator.generate());
```

The observed result for a freshly created generator is an empty string. After inspecting that result, replace the probe with `assertEquals("", generator.generate())`. The test now preserves one concrete fact about the default state.

For the next case, associate a row, run again, inspect the resulting markup, and store that result as the expectation. Keep the row association in the test setup: changing setup as well as the assertion would characterize a different case.

The value of this procedure appears on later runs, when a structural edit changes the result unexpectedly. The first failure is a learning instrument; subsequent failures test the preservation claim.

## Decide what to characterize

Use two passes:

1. **Understand the area.** Start with simple representative behavior. Explore tangled logic, likely failure cases, extreme inputs, and apparent invariants. Organize tests so a reader can understand the main behavior before its peculiar cases.
2. **Target the change.** Inspect the exact statements, branches, state transfers, and conversions that will move or change. Add cases that would reveal realistic mistakes in those operations.

This is not blind black-box sampling. Read the code to choose better questions. If the suite cannot give sufficient confidence for the planned edit, shrink or change the edit rather than treating a large number of weak tests as protection.

The chapter's **Method Use Rule** encourages tests for an untested legacy method before depending on it. Apply that principle to the methods whose behavior the current work relies on, preserving the user's requested scope rather than launching a whole-codebase testing campaign.

## Existence and connection

When extracting or moving behavior, establish both:

- **Existence:** the moved logic still computes or performs the relevant behavior.
- **Connection:** the old entry point still reaches it with the right values and uses the result/effects correctly.

A helper's isolated test cannot establish the second claim. A caller test that can pass without executing the helper may also be inadequate.

In the source's fuel-value example, moving a computation changes an internal calculation into a parameter/result boundary. Choose input that reaches the changed branch and discriminates the arithmetic or type conversion. An integral-valued result can conceal truncation; a fractional-valued case exposes loss of precision where the language permits a narrowing conversion. Check actual language rules rather than assuming all narrowing is implicit.

Also inspect evaluation changes. Moving an expression into an argument can evaluate it on paths where it was previously conditional. That is a practical application of the chapter's connection and conversion checks.

## Confirm the intended path

Ask whether the test could pass without running the relevant branch. If uncertain, use a debugger, coverage evidence, or a temporary **sensing variable** to establish path execution. Then check that an incorrect result would fail the assertion. Reaching a conversion is insufficient when the chosen values make correct and incorrect conversions indistinguishable.

## Unexpected behavior and bugs

Mark suspicious baseline behavior clearly and determine whether users or other code depend on it. Keep preservation and defect correction separate. A known defect can remain documented by a characterization test while a planned fix gains a desired-behavior test and an explicit compatibility decision.

Do not silently fix unrelated behavior during extraction/refactoring, and do not treat the baseline as an instruction to preserve a defect forever. The next action depends on the task and the consequences of changing it.

## Test quality and completion

The useful stopping condition is a set of cases that explains the relevant current behavior and detects plausible errors in the planned change. No fixed test count or coverage percentage establishes this.

Practical application beyond the source's simple examples: control nondeterministic inputs such as clocks or random values at a seam. If output normalization is necessary, limit it to genuinely irrelevant variation; broad normalization can hide the change the test should catch.

## Connects to

- [Ch 11](ch11-what-methods-to-test.md) and [Ch 12](ch12-many-changes-one-area.md): where to observe.
- [Ch 22](ch22-monster-methods.md): sensing variables and manual extraction.
- [Ch 23](ch23-not-breaking-anything.md): careful changes before full protection.
