# Chapter 4: The Seam Model

Source: printed pp. 29-44; PDF pp. 52-67.

## Core idea

A **seam** is a place where behavior can be varied without editing that place. Every seam has an **enabling point**, the place where the alternative behavior is selected. Identify both before claiming that a dependency is substitutable.

## Find and choose a seam

Look beyond source text to the steps that turn it into an executing program: preprocessing, compilation/linking, loading, and object construction. An otherwise fixed call may be replaceable at one of those stages.

| Seam | Selection mechanism / enabling point | Useful situation | Main cost |
|---|---|---|---|
| Object seam | Supplied object, factory choice, or construction of a test subclass | A specific OO collaboration or method call | Requires suitable dispatch and controllable construction |
| Link seam | Link inputs, library selection, or relevant class-loading configuration | Pervasive calls into a replaceable library | Test behavior can be hidden in build configuration |
| Preprocessing seam | Macro definition or conditional build configuration | C/C++ code with few other accessible boundaries | Text substitution can obscure meaning and create build variants |

Prefer object seams in OO code when practical because the variation is explicit in the program. Link and preprocessing seams remain useful where dependencies are widespread and a local object seam would require too much unprotected editing.

## Worked example: introduce an object seam

Reconstruction of the chapter's C++ error-reporting example. `Init()` calls a global routine that communicates with a difficult subsystem. Add an overridable member with the same signature that delegates to the original global function:

```cpp
class AsyncReceiver {
protected:
    virtual void PostReceiveError(unsigned type, unsigned error) {
        ::PostReceiveError(type, error);
    }
public:
    bool Init();
};
```

The existing unqualified call within `Init()` now reaches the member, whose production implementation forwards to the global function. A test subclass overrides the member to record the arguments or suppress the interaction.

- Seam: the call to `PostReceiveError` inside `Init()`.
- Enabling point: construction of `AsyncReceiver` versus the test subclass.
- Preservation argument: the production delegate passes the same arguments to the same global routine in the same position.
- Required check: confirm name resolution and signature equivalence, then establish a test that reaches the hook.

The snippet is schematic: the global declaration and the other dependencies of `Init()` still belong to the surrounding program. Removing one dependency does not imply that the whole method is isolated.

## A virtual call needs control of the receiver

If a method creates `new FormulaCell(...)` locally and immediately calls `cell.Recalculate()`, the receiver is fixed within the same method. The call has no usable external enabling point merely because `Recalculate` is polymorphic.

Pass a `Cell`, obtain it through an overridable factory, or otherwise expose a selection point; then tests can supply a receiver. Distinguish discovering an existing seam from making a small structural edit that opens one.

In the book's Java example, making a private static helper a protected instance method permits subclass replacement while leaving its caller unchanged. That reasoning depends on actual visibility and dispatch semantics. Check the target language rather than assuming every method call is overridable.

## Link and preprocessing examples

For a graphics library that mostly receives drawing commands, build a test target against replacement drawing functions. Empty functions can provide separation. Recording each command and its arguments supplies sensing. Functions that return information need deliberate test responses; a generic success code may bypass the branch being tested.

For C's `db_update`, a test-controlled preprocessor definition can redirect calls to a recording implementation. The enabling point is the test macro configuration, not the call site. Keep selection visibly confined to the intended test build and verify that the production build still uses the real implementation.

The book's Java classpath example illustrates link-like substitution using classes with matching names. It is a historical mechanism example, not a promise about every present build tool or module configuration.

## Anti-patterns

- Editing the body under test to replace every dangerous call independently: this expands the untested change and gives production and tests different algorithm bodies.
- Naming a seam without identifying how a test selects its behavior: this confuses possible polymorphism with usable substitution.
- Letting test link inputs leak into production: the program can compile and run while silently using the fake.
- Choosing a complicated recording scheme before knowing the observations needed: begin with the smallest sufficient sensor.

## Apply it

Produce a seam description with four fields: call/boundary, enabling point, production implementation, test implementation. Validate the production route and demonstrate that the test route actually executes. This is stronger evidence than the presence of an interface alone.

## Connects to

- [Ch 3](ch03-sensing-and-separation.md): decide what a replacement must do.
- [Ch 9](ch09-class-into-test-harness.md): expose construction choices.
- [Ch 25](ch25-dependency-breaking-techniques.md): exact named transformations.
