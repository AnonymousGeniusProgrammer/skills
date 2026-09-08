---
name: working-with-legacy-code
description: Apply Michael C. Feathers's Working Effectively with Legacy Code when changing code with weak test coverage, introducing characterization tests, choosing seams, breaking dependencies, or refactoring large methods and classes. Also use for chapter and technique lookup from the book. Focus on preserving existing behavior while making a concrete change; this is not a general mandate to rewrite old code or modernize dependencies.
---

# Working Effectively with Legacy Code

Michael C. Feathers · first printing September 2004, copyright 2005 · 25 chapters plus appendix · generated 2026-09-08.

## Apply the book to the requested change

Use Feathers's **Legacy Code Change Algorithm**:

1. **Identify change points.** Locate the decisions and state the request requires changing. Distinguish intended new behavior from behavior to preserve.
2. **Find test points.** Trace the effects to observable results, state, or interactions. Choose observations that would detect the relevant mistakes.
3. **Break dependencies.** Make the chosen unit constructible and executable in a harness through the smallest practical behavior-preserving seam.
4. **Write tests.** Characterize relevant existing behavior. Record what the code actually does, including surprising behavior that must remain stable for this change.
5. **Make changes and refactor.** Add tests for the desired change, implement it, and improve structure within the protection obtained.

Inspect repository code and existing test/build conventions before selecting a technique. Read the relevant chapter or catalog recipe before applying details not covered here. Use the smallest useful group of references; do not load the whole book skill for every task.

When reporting implementation work, make the behavioral claim reviewable: what changed, which behavior was preserved, what the tests observe, which seam was introduced, and any remaining integration gap. For a chapter question, use the source locator in its reference file. With no task or topic, provide a brief orientation and the index rather than initiating repository changes.

## Core judgments

**Legacy code means code without tests in Feathers's working definition.** Age, language, or untidy formatting alone does not determine the problem. Tests supply fast feedback about behavior during change.

**Cover and Modify.** Tests act as a software vise, holding selected behavior steady while an aspect changes. A passing test supports only its exercised paths and asserted observations. Keep a fast local editing loop and use broader checks for interactions that substitutes exclude. The book's timing examples express the value of feedback; they are not universal performance budgets.

**The Legacy Code Dilemma.** Tests are needed before changing code, but some changes may be needed to install tests. Keep those first changes mechanical: preserve signatures, move intact bodies, inspect the diff, and check production wiring. An extra parameter or temporary hook can be a reasonable foothold. A broad redesign before coverage expands uncertainty.

**Sensing and separation are different needs.** Sensing observes an effect; separation removes a dependency that obstructs execution. A fake may do both. A no-op collaborator separates, but cannot prove a required notification occurred. Never override the algorithm the test is meant to protect.

**A seam includes an enabling point.** A seam permits a behavior substitution without editing the use site for each substitution. The enabling point selects the alternative: an object supplied at construction, the test's subclass choice, or build/link configuration. Check that production still selects its real dependency. Object, link, and preprocessing seams have different scopes.

**Choose tests by effects.** Trace returns, mutated arguments and aliases, object state, global state, and external interactions. An effect sketch is not just a call graph. An interception point may observe several changes; a pinch point concentrates relevant effects for a particular change set. Select nearby observations when feasible and broader ones when they provide an economical first cover. Do not assume one count or return value detects every outgoing effect.

**Characterization precedes correction.** On an unchanged baseline, choose a relevant input, observe output, and encode the observed result. A surprising result can be useful preservation evidence without being desirable behavior. Keep an intended bug fix explicit. Investigate test failures; do not automatically replace expected results. Helper tests need connection checks for call position, arguments, conversions, and result use.

**Sprout and wrap under time pressure.** First try the actual construction/invocation probe. If existing coverage is too costly for this change, Sprout Method/Class develops new behavior separately and inserts it into the old flow. Wrap Method/Class puts new behavior around an existing operation. Verify the connecting path, order, and exception behavior; tests of the new unit leave the old body and some integration unprotected.

**Expose responsibilities incrementally.** Use method groups, hidden methods, changeable decisions, and feature sketches to find class boundaries. For a monster method, extract small understood fragments, sense intermediate effects, or break out a method object. Extract into the current class before combining that move with cross-class relocation. For duplication, extract differences until the common operation becomes shareable. Let names and tested change scenarios guide the next step.

**Respect language and lifecycle details.** Compiler success does not prove unchanged dispatch or field binding. C++ base-constructor calls do not dispatch to a derived test override. A post-construction setter cannot prevent earlier side effects. Lazy creation changes timing; globals need isolation; replacement needs ownership and alias checks. Read the specific recipe rather than treating all substitution techniques as interchangeable.

## Route by the immediate obstacle

| Need | Read |
|---|---|
| Identify change consequences and preservation tests | [11: effects](chapters/ch11-what-methods-to-test.md), [12: test points](chapters/ch12-many-changes-one-area.md), [13: characterization](chapters/ch13-characterization-tests.md) |
| Construct a class or invoke a method | [9: construction](chapters/ch09-class-into-test-harness.md), [10: invocation](chapters/ch10-method-into-test-harness.md), then the [catalog](chapters/ch25-dependency-breaking-techniques.md) |
| Add behavior with little time | [6: sprout/wrap](chapters/ch06-not-much-time.md), [8: adding features](chapters/ch08-adding-features.md) |
| Untangle libraries or procedural code | [14: libraries](chapters/ch14-library-dependencies.md), [15: API-heavy code](chapters/ch15-all-api-calls.md), [19: procedural code](chapters/ch19-procedural-code.md) |
| Understand or restructure a difficult area | [16: understanding](chapters/ch16-understanding-code.md), [20: classes](chapters/ch20-large-classes.md), [21: duplication](chapters/ch21-duplication.md), [22: methods](chapters/ch22-monster-methods.md) |

## Chapter index

Titles follow the book; punctuation is normalized. Each file contains printed and PDF page references.

| # | Title | Focus |
|---|---|---|
| [01](chapters/ch01-changing-software.md) | Changing Software | Intended change and preservation |
| [02](chapters/ch02-working-with-feedback.md) | Working with Feedback | Change algorithm, software vise |
| [03](chapters/ch03-sensing-and-separation.md) | Sensing and Separation | Fakes and observations |
| [04](chapters/ch04-seam-model.md) | The Seam Model | Seams and enabling points |
| [05](chapters/ch05-tools.md) | Tools | Harnesses, refactoring tools |
| [06](chapters/ch06-not-much-time.md) | I Don't Have Much Time and I Have to Change It | Sprout and wrap |
| [07](chapters/ch07-slow-changes.md) | It Takes Forever to Make a Change | Understanding, lag, dependencies |
| [08](chapters/ch08-adding-features.md) | How Do I Add a Feature? | TDD, Programming by Difference |
| [09](chapters/ch09-class-into-test-harness.md) | I Can't Get This Class into a Test Harness | Construction obstacles |
| [10](chapters/ch10-method-into-test-harness.md) | I Can't Run This Method in a Test Harness | Parameters, access, hidden effects |
| [11](chapters/ch11-what-methods-to-test.md) | I Need to Make a Change. What Methods Should I Test? | Effect sketches |
| [12](chapters/ch12-many-changes-one-area.md) | I Need to Make Many Changes in One Area. Do I Have to Break Dependencies for All the Classes Involved? | Interception and pinch points |
| [13](chapters/ch13-characterization-tests.md) | I Need to Make a Change, but I Don't Know What Tests to Write | Characterization and targeted testing |
| [14](chapters/ch14-library-dependencies.md) | Dependencies on Libraries Are Killing Me | Owned boundaries |
| [15](chapters/ch15-all-api-calls.md) | My Application Is All API Calls | Skin and Wrap, responsibility extraction |
| [16](chapters/ch16-understanding-code.md) | I Don't Understand the Code Well Enough to Change It | Sketches, markup, scratch refactoring |
| [17](chapters/ch17-system-structure.md) | My Application Has No Structure | Story, Naked CRC, scrutiny |
| [18](chapters/ch18-test-code-organization.md) | My Test Code Is in the Way | Naming and separation |
| [19](chapters/ch19-procedural-code.md) | My Project Is Not Object Oriented. How Do I Make Safe Changes? | C functions and seams |
| [20](chapters/ch20-large-classes.md) | This Class Is Too Big and I Don't Want It to Get Any Bigger | Responsibility heuristics |
| [21](chapters/ch21-duplication.md) | I'm Changing the Same Code All Over the Place | Duplication and orthogonality |
| [22](chapters/ch22-monster-methods.md) | I Need to Change a Monster Method and I Can't Write Tests for It | Small extractions, sensing variables |
| [23](chapters/ch23-not-breaking-anything.md) | How Do I Know That I'm Not Breaking Anything? | Deliberate editing and compiler limits |
| [24](chapters/ch24-overwhelmed.md) | We Feel Overwhelmed. It Isn't Going to Get Any Better | Local progress and team learning |
| [25](chapters/ch25-dependency-breaking-techniques.md) | Dependency-Breaking Techniques | All 24 named recipes |
| [Appendix](chapters/appendix-extract-method.md) | Refactoring | Extract Method |

## Topic index

- **API boundaries and adapters** → [14](chapters/ch14-library-dependencies.md), [15](chapters/ch15-all-api-calls.md), [Adapt Parameter](chapters/ch25a-parameters-and-extraction.md#adapt-parameter).
- **Build time and compilation firewalls** → [7](chapters/ch07-slow-changes.md).
- **Characterization tests** → [13](chapters/ch13-characterization-tests.md).
- **Constructors and globals** → [9](chapters/ch09-class-into-test-harness.md), [catalog](chapters/ch25-dependency-breaking-techniques.md).
- **Dependency-breaking technique by name** → [complete catalog](chapters/ch25-dependency-breaking-techniques.md#complete-catalog).
- **Duplication and orthogonality** → [21](chapters/ch21-duplication.md).
- **Effect sketches, interception points, pinch points** → [11](chapters/ch11-what-methods-to-test.md), [12](chapters/ch12-many-changes-one-area.md).
- **Extract Method and coupling count** → [appendix](chapters/appendix-extract-method.md), [22](chapters/ch22-monster-methods.md).
- **Fakes, mocks, and Null Object** → [3](chapters/ch03-sensing-and-separation.md), [9](chapters/ch09-class-into-test-harness.md).
- **Feature sketches and Single Responsibility Principle** → [20](chapters/ch20-large-classes.md).
- **Inheritance, Liskov Substitution, normalized hierarchy** → [8](chapters/ch08-adding-features.md), [Extract Interface](chapters/ch25b-interfaces-and-supplied-objects.md#extract-interface).
- **Naked CRC and system story** → [17](chapters/ch17-system-structure.md).
- **Preserve Signatures and Lean on the Compiler** → [23](chapters/ch23-not-breaking-anything.md).
- **Private methods and command/query separation** → [10](chapters/ch10-method-into-test-harness.md).
- **Procedural code and function pointers** → [19](chapters/ch19-procedural-code.md), [recipe](chapters/ch25c-relocation-and-substitution.md#replace-function-with-function-pointer).
- **Scratch refactoring and listing markup** → [16](chapters/ch16-understanding-code.md).
- **Seams and enabling points** → [4](chapters/ch04-seam-model.md).
- **Sensing variables, gleaning dependencies, method objects** → [22](chapters/ch22-monster-methods.md).
- **Sprout, Wrap, Decorator** → [6](chapters/ch06-not-much-time.md).
- **TDD and Programming by Difference** → [8](chapters/ch08-adding-features.md).
- **Test organization** → [18](chapters/ch18-test-code-organization.md).

## Supporting files

- [cheatsheet.md](cheatsheet.md): decisions, defaults, and trade-offs.
- [patterns.md](patterns.md): compact technique inventory and routing.
- [glossary.md](glossary.md): key terms in alphabetical order.

## Source and scope

Synthesized from the user-provided *Working Effectively with Legacy Code* PDF, filename `[PROGRAMMING][Working Effectively. with Legacy Code].pdf`, ISBN 0-13-117705-2. This copy has 458 PDF pages; for numbered book pages, **PDF page = printed page + 23**. Locators are one-based and refer to this copy.

The text layer was extracted with pypdf after structure-aware extraction failed because a native compiler was unavailable. Representative seam and pinch-point diagrams were visually checked; the skill does not reproduce or comprehensively interpret every diagram. It contains original synthesis and compact reconstructed examples, not the full book text. Source listings have occasional inconsistencies; examples here illustrate the technique and are not ready-made project code.

Historical tools and APIs are described in context. Verify current tool behavior and language rules in the actual project before implementation. Additional lifecycle and integration checks are application guidance, not verbatim rules from Feathers. Apply these methods within the user's requested scope and existing project conventions.
