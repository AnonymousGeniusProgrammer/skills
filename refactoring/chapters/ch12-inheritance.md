# Chapter 12: Dealing with Inheritance

Source: printed pp. 349-404; PDF pp. 371-426. Entry locators use printed pages; add 22 for PDF pages.

## Core idea

Use inheritance for coherent shared behavior and variation, and move to delegation when the subtype contract, coupling, or axis of variation no longer fits. Fowler does not advocate a blanket ban on inheritance.

## Catalog mechanics

### Pull Up Method — p. 350

**Use when:** subclasses contain the same behavior that belongs in a common parent.

**How:** inspect differences and missing test cases; normalize signatures and equivalent bodies first; ensure dependencies are available from the parent; copy the method there; run static checks; remove subclass copies one at a time and test.

**Watch:** similarly written methods may carry different meaning. Pull up prerequisite fields or behavior as appropriate, and preserve dynamic dispatch for calls made by the moved method.

### Pull Up Field — p. 353

**Use when:** subclass fields have the same meaning and use.

**How:** inspect all uses; align names; declare common storage with suitable subclass access; remove duplicate declarations; test.

**Watch:** names alone do not establish equivalence. In dynamic languages, fields may appear through assignment, making this largely a constructor-body move. Preserve initialization and per-instance ownership.

### Pull Up Constructor Body — p. 355

**Use when:** constructors repeat initialization that can legitimately run in the superclass.

**How:** establish the superclass constructor and calls to it; slide common statements to a legal shared position; move them into the superclass and pass required arguments; test. Common work that cannot move early can be extracted and pulled up as a method.

**Watch:** constructors have language-specific sequencing constraints. Do not call code that depends on uninitialized subclass state. If the move becomes tangled, a factory may offer a better creation boundary.

### Push Down Method — p. 359

**Use when:** behavior applies only to some subclasses, and callers can use their narrower interfaces.

**How:** place the method in the subclasses that need it; remove it from the parent; test and remove any unnecessary copies.

**Watch:** if callers legitimately expect the operation on the parent interface, simply deleting it breaks that contract. Appropriate polymorphic default behavior or another interface arrangement may be needed.

### Push Down Field — p. 361

**Use when:** only a subset of subclasses needs a field.

**How:** declare and initialize it in the appropriate subclasses; remove parent storage and unnecessary copies; test.

**Watch:** check inherited methods, constructors, serialization, and consumers before narrowing its availability. A specialized field does not belong in every instance merely because a parent can store it.

### Replace Type Code with Subclasses — p. 362

**Use when:** a type code drives repeated behavioral variation or type-specific data deserves structural expression.

**How:** encapsulate the type code; introduce one subtype with a type getter; centralize selection, usually behind a factory; test; add remaining subtypes incrementally; remove redundant type storage; push down behavior and replace matching conditionals where useful.

**Watch:** simple classification often needs only a code. Directly subclassing the host is unsuitable when that type changes during the object's lifetime or another independent dimension already uses inheritance. In those cases, wrap the type in its own object and apply the subtype hierarchy to that collaborator.

### Remove Subclass — p. 369

**Use when:** a subclass distinguishes a category but no longer supplies useful structural or behavioral variation.

**How:** encapsulate construction in factories; centralize type tests in the parent; represent the distinction in a field; migrate tests and callers to that representation; remove the subclass; verify.

**Watch:** replacing a subclass with a field requires accounting for runtime type checks, serialization, construction, and any overridden behavior. It is more specific than merging any parent/child pair.

### Extract Superclass — p. 375

**Use when:** existing classes reveal genuine shared data and behavior.

**How:** introduce an empty parent and establish the inheritance relationships; adjust constructors; pull up common initialization, methods, and fields one at a time; extract further common fragments where appropriate; consider a common client interface.

**Watch:** an apparent real-world taxonomy is a clue, not proof of substitutability. Extract Class provides a composition alternative. Existing useful interfaces and inheritance constraints determine which is simpler.

### Collapse Hierarchy — p. 380

**Use when:** a parent and child no longer differ enough to justify separate classes.

**How:** choose the surviving class and meaningful name; move all required data and behavior into it using pull-up/push-down moves; update references; remove the empty class; test.

**Watch:** preserve the behavior of other subclasses and construction paths. A parent can be a live interface even when it contains little implementation.

### Replace Subclass with Delegate — p. 381

**Use when:** variation must change dynamically, several independent dimensions must vary, or subclass coupling has become costly.

**How:** hide construction behind factories as needed; create a delegate containing variant data and a host reference when required; attach it during variant construction; move one overridden method into it while preserving forwarding; move externally needed dispatch to the host; test; repeat; migrate construction to the host and remove the old subclass.

**Watch:** preserve base behavior when no delegate exists. A hierarchy of delegates can supply common defaults for several variants. Replacing `super` calls with host calls can recurse if the host immediately delegates back; pass the undecorated base result or provide an explicit base operation. Verify method visibility and subtype-dependent clients.

### Replace Superclass with Delegate — p. 399

**Use when:** inheritance reuses implementation but exposes an inappropriate interface, confuses related identities, or couples classes too tightly.

**How:** create a field referring to a former-superclass instance; add forwarders for the supported inherited features; test coherent groups, including getter/setter pairs; once access is routed through the delegate, remove the inheritance link.

**Watch:** clients that rely on subtype assignability must be migrated. Forward only a coherent supported interface as the design evolves. Separate the basic relationship change from later sharing or identity changes.

## Worked example: a physical scroll is not a catalog item

The source has `Scroll` inherit `CatalogItem`. The catalog item has a title and tags; the physical scroll has a last-cleaned date. Several physical copies can refer to the same catalog entry.

1. Add a contained catalog item and forward the existing title/tag operations to it.
2. Remove inheritance after callers retain equivalent supported access.
3. Recognize that composition alone still creates one catalog item per scroll.
4. Separate scroll ID from catalog-item ID before sharing. The original inherited ID represented the physical scroll; treating it as the catalog ID would merge the wrong entities.
5. Use Change Value to Reference: obtain the existing catalog item by catalog ID from the repository, while keeping the physical scroll's own ID and cleaning state.
6. Remove constructor inputs now obtained through the catalog object.

Compact reconstruction of the resulting identity boundary:

```javascript
class Scroll {
  constructor(id, lastCleaned, catalogId, catalog) {
    this.id = id;
    this.lastCleaned = lastCleaned;
    this.catalogItem = catalog.get(catalogId);
  }
  get title() { return this.catalogItem.title; }
  hasTag(tag) { return this.catalogItem.hasTag(tag); }
}
```

The snippet abbreviates encapsulation and omits cleaning calculations. Verify two scrolls can retain distinct physical IDs and cleaning dates while resolving the same catalog object. Changing shared catalog metadata should affect both only when that is the intended reference semantics.

## Second example: premium booking as a collaborator

The source's premium booking changes talkback availability, adds a fee to ordinary pricing, and supplies dinner behavior. Inheritance is initially reasonable. Delegation becomes useful when a booking must switch premium status without replacing its identity, or when another variation needs the inheritance dimension.

Factories preserve the caller-facing creation choices. The new delegate receives premium extras and access to necessary host data. Move simple overrides first; treat the price method's `super` call separately so the premium fee is applied exactly once. The refactoring supplies a place for dynamic changes; exposing a new “become premium” operation is additional capability work.

## Decision table and takeaways

| Evidence | Starting move |
|---|---|
| Equivalent behavior repeated in siblings | Normalize, then Pull Up Method |
| Parent member meaningful only to certain variants | Push Down Method/Field after checking callers |
| Category has no remaining behavior worth separating | Remove Subclass |
| Parent and child no longer meaningfully differ | Collapse Hierarchy |
| Host's category must change while identity remains | Delegate to a replaceable type/strategy object |
| Reuse relationship fails the supertype contract | Replace Superclass with Delegate |

Check substitutability, initialization order, dispatch, and identity before changing hierarchy shape. Prefer the simplest relationship that fits today's behavior and change it when evidence warrants. See [Ch 7](ch07-encapsulation.md) for composition boundaries, [Ch 9](ch09-organizing-data.md) for identity, [Ch 10](ch10-conditional-logic.md) for polymorphic behavior, and [Ch 11](ch11-refactoring-apis.md) for factories and migration.
