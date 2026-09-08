# Chapter 4: Building Tests

Source: printed pp. 85-100; PDF pp. 107-122. The source demonstrates JavaScript with Mocha and Chai; use the existing project's test tools when applying the ideas.

## Core idea

Self-testing code makes small refactorings practical by detecting regressions while the responsible edit is still small and fresh. Test the behavior most likely to fail; a large count of weak tests is not the goal.

## Build a useful feedback loop

1. Identify the behavior and boundary that the forthcoming changes affect.
2. Create a representative fixture and assert a meaningful result. Favor the existing suite when it already supplies this protection.
3. Demonstrate that a new test can fail for the intended defect, for example by temporarily introducing a relevant fault, then restoring the code.
4. Run focused tests frequently enough to localize a failure to a small change. Run broader checks at meaningful integration boundaries and according to project requirements.
5. Investigate failures before further restructuring. Recover the latest known-good state without discarding unrelated work.

**Why it works:** the combination of frequent execution and small edits reduces the search space for a regression. A passing test that never exercises the changed path does not provide that benefit.

The book suggests checking the affected area at least every few minutes and all tests at least daily. Those are workflow heuristics from the source, not a substitute for the current repository's required checks.

## Risk-driven selection

Prioritize calculations, branches, shared updates, and boundary conditions. Fowler does not routinely test trivial field accessors. A setter that updates derived state or another object is different: it carries real behavioral risk.

Use coverage to find untested areas, then assess their significance. Coverage does not establish assertion quality or prove that all relevant behavior is protected. Add a focused regression test when a bug exposes a missing case.

## Fixture discipline

Use **setup-exercise-verify** (also called **given-when-then** or **arrange-act-assert**): establish known inputs, perform the operation, and inspect the outcome. Teardown matters when resources or shared state survive a test.

Create fresh mutable fixtures per test, commonly through `beforeEach`. A shared immutable fixture can be appropriate; a mutable shared object can make tests depend on order. Consider setup cost based on measurement before accepting that risk.

Prefer a focused verification story. One assertion per test can improve failure diagnosis, but the chapter allows closely related outcomes in a single test. Treat this as a clarity decision rather than a numerical rule.

## Worked example: production planning

The source models a province's demand and producers. It calculates shortfall and profit, and a producer's production update affects province totals. This reconstruction retains the significant outcomes without reproducing the application.

| Source scenario | Expected shortfall | Expected profit | What it exercises |
|---|---:|---:|---|
| Standard province fixture | 5 | 230 | Representative calculations |
| First producer's production changed to 20 | -6 | 292 | Propagation of an update |
| No producers, demand 30 | 30 | 0 | Empty collection |
| Standard fixture, demand 0 | -25 | 0 | Numeric boundary |
| Standard fixture, demand -1 | -26 | -10 | Existing behavior that prompts a domain question |

Illustrative test shape, assuming the source model and fixture are available:

```javascript
it("updates the province when production changes", () => {
  const province = new Province(sampleProvinceData());
  province.producers[0].production = 20;
  expect(province.shortfall).equal(-6);
  expect(province.profit).equal(292);
});
```

The negative-demand case prompts a separate question: should the domain permit it, reject it, or clamp it? Recording existing results for a refactoring baseline does not decide the intended product rule. Change that rule as explicit behavior work.

The chapter initially accepts one computed result as a regression baseline while noting it could be checked manually. For application, distinguish “this preserves existing behavior” from “this is independently established as correct.” That distinction prevents existing defects from becoming accidental specifications.

## Boundary probes

- For a collection, consider empty and representative multi-element cases.
- For a number, consider zero, negatives when meaningful, and values around branch thresholds.
- For UI-derived strings, consider blank input and the actual conversion behavior.
- For malformed structures, determine whether input comes from a validated internal source or an external boundary. That determines whether validation is part of the contract.
- Distinguish a failed assertion from an unexpected exception during setup or execution; both need interpretation.

The source discusses discarding a malformed-input test outside the supported behavior. Do not generalize that to ignoring all error behavior. Errors that callers observe or rely on belong in the behavior boundary.

## Mental models, anti-patterns, and takeaways

Think of tests as a rapid bug detector, not a proof system. Think of each fixture as an isolated experiment. Focus effort on mistakes the contemplated transformation could cause.

Avoid console output that needs manual comparison, shared mutable fixtures, copying the implementation into expected-value calculations, and postponing all testing until exhaustive coverage is possible. Preserve real behavioral assertions when internal organization changes.

The chapter introduces and recommends **Test-Driven Development** as a short failing-test, implementation, refactoring cycle. It does not provide a complete TDD methodology or require changing the user's chosen development process.

See [Ch 1](ch01-first-example.md) for statement regression tests, [Ch 2](ch02-principles.md) for The Two Hats and legacy constraints, and [Ch 9](ch09-organizing-data.md) for eliminating redundant state safely.
