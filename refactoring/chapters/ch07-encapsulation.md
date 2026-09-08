# Chapter 7: Encapsulation

Source: printed pp. 161-196; PDF pp. 183-218. Entry locators use printed pages; add 22 for PDF pages.

## Core idea

Protect the choices that callers should not need to know: data representation, membership updates, navigation, and algorithm details. Reassess indirection when its cost exceeds the coupling it removes.

## Catalog mechanics

### Encapsulate Record — p. 162

**Use when:** a mutable record's representation is spread through clients, or stored and calculated values should be interchangeable behind an interface.

**How:** encapsulate the record variable; introduce a wrapper class with a temporary raw-record accessor; provide an object-returning access path; migrate clients to methods or getters incrementally; remove raw access after migration. For nested structures, consider further record and collection encapsulation.

**Watch:** prioritize writers in complex records. Copying or providing a read-only view can help readers, but check nested aliases and objects retained by constructors. A wrapper does little if clients can still mutate the same underlying data through another reference. An effectively immutable phase result may need no such wrapper.

### Encapsulate Collection — p. 170

**Use when:** clients can change collection membership without the owner seeing it.

**How:** encapsulate the reference; add owner operations such as add/remove; migrate membership changes; remove an unnecessary whole-collection setter, or make it copy incoming membership; return a protected view or a copy from the getter; test.

**Watch:** copying the collection protects membership, not necessarily the mutable elements. A read-only view and a snapshot also differ when the owner later changes the collection. Choose what callers require. Exposing useful read operations is reasonable; a wrapper for every collection query is unnecessary.

### Replace Primitive with Object — p. 174

**Use when:** a primitive acquires domain behavior, formatting, comparison, or rules that repeat across callers.

**How:** encapsulate the primitive; create a value class that initially wraps the same data; change the setter/construction path to store the wrapper; retain the old external primitive view while clients migrate; test; clarify accessors and move related behavior.

**Watch:** avoid silently tightening validation during the representation change. Decide whether equality and identity should be value-based or reference-based as a separate, explicit step.

### Replace Temp with Query — p. 178

**Use when:** a temporary calculation can become a named reusable query and removing the temporary simplifies extraction.

**How:** verify that the value is fully determined before use and is stable wherever queried; make the binding read-only where possible; extract the calculation into a side-effect-free function; test; inline the temporary.

**Watch:** recomputation is not equivalent to a historical snapshot if dependencies mutate. Separating queries from updates may be necessary first. Outside a shared class context, a query with many parameters may be less helpful than a local variable.

### Extract Class — p. 182

**Use when:** a subset of data and behavior has a coherent responsibility separate from the rest.

**How:** choose the split; create the component and a reference to it; move fields incrementally; move methods, usually lower-level dependencies before their callers; test each move; review both interfaces and names; decide whether clients should see the component.

**Watch:** the source uses “parent” and “child” for the containing and extracted objects here. This is composition, not an instruction to introduce inheritance. Exposing a mutable component changes what clients can retain and modify.

### Inline Class — p. 186

**Use when:** a class no longer carries a useful responsibility, or merging two classes will enable a better subsequent split.

**How:** add delegating methods on the receiving class; move clients to those methods; transfer the original class's functions and data incrementally; delete the original after it is empty and unreferenced.

**Watch:** preserve the source object's observable identity and lifecycle where they matter. “Small class” alone is not proof it adds no useful abstraction.

### Hide Delegate — p. 189

**Use when:** clients know a navigation relationship that the serving object could hide.

**How:** introduce forwarding operations on the serving object for the needed delegate behavior; migrate clients to them; remove exposed delegate access if no clients still need it.

**Watch:** hide a meaningful dependency. Forwarding every operation indiscriminately can create a Middle Man. The right boundary can change over time.

### Remove Middle Man — p. 192

**Use when:** maintaining a forwarding interface costs more than insulating clients from a collaborator.

**How:** expose a suitable getter for the delegate; move callers through it one at a time; remove unused forwarding methods.

**Watch:** clients now depend on the collaborator's interface and the relationship used to reach it. Keep useful forwarding methods when their boundary still earns its place. This is not a mandate to dismantle all encapsulation.

### Substitute Algorithm — p. 195

**Use when:** another algorithm or existing library operation expresses the same behavior more clearly.

**How:** isolate the old algorithm behind a complete function; capture its relevant behavior in tests; prepare the alternative; run static checks and compare outcomes; use the original as a differential reference while investigating discrepancies.

**Watch:** decompose an entangled algorithm before substitution. Match ordering, duplicates, equality, errors, side effects, and supported input cases, rather than comparing only one ordinary output. A desired functional change belongs after the behavior-preserving substitution.

## Worked example: extract a telephone number

The source's `Person` contains a name, office area code, office number, and telephone formatting. Reconstruct the sequence:

1. Introduce a `TelephoneNumber` instance owned by each person.
2. Move area code behind that instance while the person's old accessor delegates. Check existing callers.
3. Move the number in the same way.
4. Move formatting, then rename fields in the component: “office” is context supplied by the person, not an intrinsic part of a telephone number.
5. Decide whether the component stays hidden or becomes a value passed to callers.

Illustrative final shape (abbreviated, not a complete class implementation):

```javascript
class TelephoneNumber {
  constructor(areaCode, number) {
    this.areaCode = areaCode;
    this.number = number;
  }
  toString() {
    return `(${this.areaCode}) ${this.number}`;
  }
}
```

The important invariant is that moving storage does not change the person's existing formatted number or update semantics. New formatting, normalization, or validation rules require a separate decision. If exposing the component makes aliasing troublesome, Ch 9's Change Reference to Value can be a subsequent move.

## Boundary choice table

| Problem | Useful protection | What that alone does not protect |
|---|---|---|
| Reassigning a record variable | Access functions | Mutations through a returned record |
| Adding/removing collection members | Owner operations plus protected reads | Mutation of contained objects |
| Storing a caller-provided mutable object | Copy or ownership agreement | All nested aliases unless addressed |
| Navigation coupled to a changing relationship | Hide Delegate | The cost of an ever-expanding forwarding API |
| Opaque calculation mixed with surrounding work | Extract, then Substitute Algorithm | Equivalence outside the tested or supported domain |

## Takeaways and connections

Encapsulation is about knowledge and control, not the mere presence of getters. Choose the depth of copying and exposure consciously. Extraction and inlining, like hiding and exposing delegates, are reversible responses to different pressures. See [Ch 3](ch03-code-smells.md), [Ch 6](ch06-first-refactorings.md), [Ch 9](ch09-organizing-data.md), and [Ch 12](ch12-inheritance.md).
