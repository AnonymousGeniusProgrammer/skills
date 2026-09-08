# Chapter 25: Dependency-Breaking Techniques

Source: printed pp. 325–413; PDF pp. 348–436. The 24 techniques are divided into three reference files so only the relevant recipes need to be loaded.

## Core idea

Introduce the smallest practical seam that permits tests. These techniques aim to preserve behavior, but their initial application may happen before tests exist. They require deliberate editing and verification; none is a guarantee of equivalence or necessarily the final design.

Read [Chapter 23](ch23-not-breaking-anything.md) before an uncovered transformation. Preserve signatures and method bodies where possible, inspect construction and dispatch, and establish characterization tests as soon as the seam works. Then make the requested behavior change with tests.

## Selection guide

| Obstacle | Start with | Escalate when |
|---|---|---|
| You own an awkward collaborator's type | Extract Interface | Adapt Parameter if the interface cannot be changed or remains too broad |
| A collaborator is constructed internally | Parameterize Constructor or Parameterize Method | A factory/getter seam may fit better when parameterization is intrusive |
| One external call blocks testing | Existing Subclass and Override Method seam, or Extract and Override Call | Several calls on the same object may justify a getter or wrapper |
| A global object blocks isolation | Replace Global Reference with Getter | Static Setter affects all users; encapsulation can support gradual parameter passing |
| A method needs no original instance state | Expose Static Method | Use a method object if it needs substantial local or instance context |
| Only some features have bad dependencies | Pull Up Feature or Push Down Dependency | Prefer tested delegation as the structure becomes clearer |
| Procedural/link dependency | Link Substitution or function pointers | Definition Completion is a costly temporary option for extreme coupling |
| The language supports type substitution or reopening definitions | Template Redefinition or Text Redefinition | Check build cost and the scope of replacement |

Choose by the actual failure in the harness, the observations needed, and the language's dispatch rules. See [Chapter 9](ch09-class-into-test-harness.md) for construction obstacles and [Chapter 10](ch10-method-into-test-harness.md) for invocation obstacles.

## Complete catalog

Each locator below is a printed start page; its PDF page is 23 higher.

| Technique | Page | Detailed recipe |
|---|---:|---|
| Adapt Parameter | 326 | [Parameters and extraction](ch25a-parameters-and-extraction.md#adapt-parameter) |
| Break Out Method Object | 330 | [Parameters and extraction](ch25a-parameters-and-extraction.md#break-out-method-object) |
| Definition Completion | 337 | [Parameters and extraction](ch25a-parameters-and-extraction.md#definition-completion) |
| Encapsulate Global References | 339 | [Parameters and extraction](ch25a-parameters-and-extraction.md#encapsulate-global-references) |
| Expose Static Method | 345 | [Parameters and extraction](ch25a-parameters-and-extraction.md#expose-static-method) |
| Extract and Override Call | 348 | [Parameters and extraction](ch25a-parameters-and-extraction.md#extract-and-override-call) |
| Extract and Override Factory Method | 350 | [Parameters and extraction](ch25a-parameters-and-extraction.md#extract-and-override-factory-method) |
| Extract and Override Getter | 352 | [Parameters and extraction](ch25a-parameters-and-extraction.md#extract-and-override-getter) |
| Extract Implementer | 356 | [Interfaces and supplied objects](ch25b-interfaces-and-supplied-objects.md#extract-implementer) |
| Extract Interface | 362 | [Interfaces and supplied objects](ch25b-interfaces-and-supplied-objects.md#extract-interface) |
| Introduce Instance Delegator | 369 | [Interfaces and supplied objects](ch25b-interfaces-and-supplied-objects.md#introduce-instance-delegator) |
| Introduce Static Setter | 372 | [Interfaces and supplied objects](ch25b-interfaces-and-supplied-objects.md#introduce-static-setter) |
| Link Substitution | 377 | [Interfaces and supplied objects](ch25b-interfaces-and-supplied-objects.md#link-substitution) |
| Parameterize Constructor | 379 | [Interfaces and supplied objects](ch25b-interfaces-and-supplied-objects.md#parameterize-constructor) |
| Parameterize Method | 383 | [Interfaces and supplied objects](ch25b-interfaces-and-supplied-objects.md#parameterize-method) |
| Primitivize Parameter | 385 | [Interfaces and supplied objects](ch25b-interfaces-and-supplied-objects.md#primitivize-parameter) |
| Pull Up Feature | 388 | [Relocation and substitution](ch25c-relocation-and-substitution.md#pull-up-feature) |
| Push Down Dependency | 392 | [Relocation and substitution](ch25c-relocation-and-substitution.md#push-down-dependency) |
| Replace Function with Function Pointer | 396 | [Relocation and substitution](ch25c-relocation-and-substitution.md#replace-function-with-function-pointer) |
| Replace Global Reference with Getter | 399 | [Relocation and substitution](ch25c-relocation-and-substitution.md#replace-global-reference-with-getter) |
| Subclass and Override Method | 401 | [Relocation and substitution](ch25c-relocation-and-substitution.md#subclass-and-override-method) |
| Supersede Instance Variable | 404 | [Relocation and substitution](ch25c-relocation-and-substitution.md#supersede-instance-variable) |
| Template Redefinition | 408 | [Relocation and substitution](ch25c-relocation-and-substitution.md#template-redefinition) |
| Text Redefinition | 412 | [Relocation and substitution](ch25c-relocation-and-substitution.md#text-redefinition) |

## Break Out Method Object

For the method-object route from the monster-method chapter, read the [complete procedure and rendering example](ch25a-parameters-and-extraction.md#break-out-method-object).

## Evidence after a seam change

Verify that the test calls the production logic intended for coverage, the substitute supplies the required inputs or observations, and the normal entry point still selects the production dependency. A fake that bypasses the target logic is not evidence about that logic. Check shared-state cleanup, reference ownership, and construction timing as applicable.

The recipes synthesize the book's procedures. Additional lifecycle, concurrency, and integration checks are practical application guidance; historical sample listings are not reproduced as deployable code.
