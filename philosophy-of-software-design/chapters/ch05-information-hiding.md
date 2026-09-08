# Chapter 5: Information Hiding (and Leakage)

Source: first edition (2018), PDF pages 40-49. Reference notes.

## Core idea

Organize modules around design decisions they can own privately. A decision reflected in several implementations creates a dependency even if their public APIs never mention it.

## Frameworks and mental models

**Information hiding** confines knowledge such as a file format, data representation, protocol mechanism, or algorithm within a module. Ask which design changes could then occur without affecting callers.

**Information leakage** occurs when a design decision is reflected in multiple modules. It can be explicit in an interface or implicit in several implementations. Access modifiers alone do not solve it: getters and setters may reveal the same representation that private fields nominally hide.

**Temporal decomposition** organizes modules by execution order. It becomes a problem when stages share knowledge that should have one owner. Reading and writing a file happen at different times but both depend on its format; that format can belong to one abstraction used in both phases.

## How to repair leakage

1. Name the duplicated knowledge precisely: framing rules, serialization layout, representation, or another design decision.
2. Identify every place that relies on it, including implementations behind apparently clean interfaces.
3. Combine tightly related modules or introduce an owner that can actually hide the shared knowledge.
4. Design callers' operations around the needed capability rather than exposing that owner's data structures.
5. Check whether changes to the decision now stay local. A new facade that re-exposes the details has only relocated the leakage.

## Source examples

**HTTP receiving and parsing**: a reader needs framing information, including the body length, to know when the request is complete. A separate parser that reinterprets the same structure duplicates protocol knowledge. A combined receive-and-parse operation can give callers a complete request with one invocation.

**Parameter access**: returning the internal parameter map exposes representation, mutability concerns, and extra lookup work. Named accessors can hide storage, decoding, and conversion.

Illustrative API shape, adapted from the chapter; declarations omit error contracts:

```java
String getParameter(String name);
int getIntParameter(String name);
```

For actual use, specify missing-parameter and conversion behavior. The chapter's accessors report those failures; [Ch 10](ch10-define-errors-out-of-existence.md) discusses organizing their handling.

**Defaults**: infer an HTTP response version from its request and supply ordinary header values automatically. Callers should not reconstruct information the library already has.

## Partial hiding and overexposure

Some knowledge is needed by only a minority of callers. Put those controls behind separate operations so common usage does not require learning them. **Overexposure** is the red flag when ordinary use forces callers to understand rarely needed features.

Apply hiding inside classes as well: private methods can contain sub-decisions, and limiting the places that access a field reduces internal dependencies.

## Anti-patterns and limits

- Separate read/modify/write classes purely because the actions occur in that order.
- A representation getter mistaken for a useful abstraction.
- A new shared class whose interface exposes all the supposedly hidden knowledge.
- Hiding a setting users must control to meet their requirements.

Prefer automatic adaptation where it can make the right decision. Expose necessary configuration or guarantees when correct use genuinely depends on them. Information hiding aims to reduce what callers need to know, not conceal information they still need.

## Key takeaways

1. Decompose by knowledge, then express execution order within that organization.
2. Check for leakage through implementations as well as interfaces.
3. Let an abstraction perform complete, useful operations for callers.
4. Separate uncommon controls from common usage.

## Connects to

- [Ch 6](ch06-general-purpose-modules.md): interfaces independent of one particular caller.
- [Ch 8](ch08-pull-complexity-downwards.md): absorbing shared caller work.
- [Ch 9](ch09-together-or-apart.md): deciding where shared knowledge belongs.
