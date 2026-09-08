# Chapter 25 recipes: Parameters and extraction

Source: printed pp. 326–355; PDF pp. 349–378. Return to the [catalog and selection guide](ch25-dependency-breaking-techniques.md).

## Adapt Parameter

Source: pp. 326–329 / PDF 349–352.

**Use when:** A method parameter is difficult to create or fake, and Extract Interface cannot provide a useful seam, especially for a type owned by a library.

1. Define a small interface expressing the caller's need, keeping required changes to the method trivial.
2. Implement a production adapter around the original parameter and a fake for tests.
3. Write a test that passes the fake. Update the method to use the new interface, and adapt the production call site.
4. Verify both the method and the adapter's translation of the original contract.

**Worked example:** Feathers replaces a servlet request parameter with `ParameterSource`. The caller needs the first value for a name; the production adapter maps a missing or empty array to absence and otherwise returns its first element. Tests supply a small fake source.

**Trade-off:** The signature changes, and translation adds behavior that can be wrong. Preserve absent/empty handling, ordering, and exceptions. Do not casually eliminate null handling while making the seam; that is another semantic decision. Prefer Extract Interface if you own the type and only need a smaller view of existing operations.

## Break Out Method Object

Source: pp. 330–336 / PDF 353–359.

**Use when:** A long method needs tests but its containing class is hard to instantiate, or many locals frustrate smaller extractions.

1. Create a class for one invocation's work. Give its constructor the original method's parameters, preserving exact types and passing semantics. Include the original receiver only if its members are needed.
2. Store those inputs in fields with matching types and initialize them explicitly.
3. Add an execution method and copy the original body into it intact.
4. Inspect remaining references to the original class. Use a narrow receiver interface or necessary access methods; compiler errors help locate uses but do not prove the data flow is unchanged.
5. Make the original method create the operation object and delegate once, preserving its result and exceptions.
6. Test the operation object. Refactor locals into operation fields and smaller methods only as coverage allows.

**Worked example:** `GDIBrush.draw` becomes a `Renderer` operation. The renderer needs point drawing, so a `PointRenderer` interface expresses that collaboration. A fake can record points without instantiating the real graphics brush.

**Trade-off:** The first move may expose formerly private behavior and create an awkward back-reference. A narrow interface limits that dependency. Preserve references to mutable arguments, initialization order, and the original per-call lifetime; caching one operation object can change state sharing and reentrancy. If the method needs no instance context, Expose Static Method may be smaller.

## Definition Completion

Source: pp. 337–338 / PDF 360–361.

**Use when:** An extreme C/C++ dependency problem requires alternative method definitions for an existing declaration.

1. Confirm the methods are declared in a header and defined separately, rather than inline in that header.
2. Include the real declaration in a dedicated test source file.
3. Exclude the production implementation from that test executable.
4. Supply matching test definitions, including construction/destruction and any other required methods. Compile/link to find missing definitions.
5. Add recording behavior where observations matter and verify which implementation each executable uses.

**Trade-off:** Duplicate definitions impose maintenance and debugger costs. Never link competing definitions into the same executable. Tests exercise clients of the replaced class, not the class's real methods. Feathers reserves this for the worst coupling and recommends replacing it with a clearer seam once tests permit.

## Encapsulate Global References

Source: pp. 339–344 / PDF 362–367.

**Use when:** Related global data or free-function calls prevent independent testing across several methods or classes.

1. Identify the related globals and choose a name for their responsibility.
2. Move data into an object with equivalent initialization, initially retaining field names and access to keep the move mechanical.
3. Replace the former global declarations with a global instance of that object. Redirect uses with compiler assistance and targeted search.
4. For free functions, instead introduce a small interface and a production implementation whose methods forward directly to the original functions.
5. Supply the new object through constructor/method parameterization, a getter, or a static setter where needed for tests.
6. With coverage, move related behavior onto the object and improve encapsulation.

**Worked example:** Active and suspended frame buffers move together into `Frame`; an `OptionSource` wrapper forwards option lookups and updates. The forwarding method must retain the original return value, arguments, and side effects.

**Trade-off:** Grouping globals does not itself remove global access. Preserve shared identity and initialization; replacing one shared buffer with several independent copies changes behavior. Initially moving data or simple forwarding calls is less invasive than also relocating substantial logic.

## Expose Static Method

Source: pp. 345–347 / PDF 368–370.

**Use when:** You need to test a method without constructing its class, and its work does not require that instance's data or methods.

1. Confirm the dependency claim by inspecting the entire body and called helpers.
2. Add a distinctly named static method with the same parameters and result contract.
3. Move the body intact and retain the original instance method as a forwarding entry point when needed.
4. Test the static method and check the original connection. Use the narrowest visibility the harness needs.

**Worked example:** Packet validation moves to `validatePacket`, allowing direct tests while `validate` delegates.

**Trade-off:** Static does not mean pure: globals, static initialization, and side effects can remain. Do not convert instance data to shared static state to force this technique to fit. Inherited dispatch and overrides may require retaining the original method. Preserve predicate operators exactly; the book's successive example listings should not be treated as a textual refactoring script.

## Extract and Override Call

Source: pp. 348–349 / PDF 371–372.

**Use when:** One localized external or static call blocks execution or needs to be sensed.

1. Find the called method's declaration and preserve its signature in a new overridable method on the current class.
2. Move the external call into that method, forwarding its arguments and result unchanged.
3. Replace the original call with a call to the new method.
4. Override the new method in a test subclass to record inputs or supply controlled results.

**Worked example:** A local `formStyles` forwards to `StyleMaster.formStyles`; a test override returns a controlled collection.

**Trade-off:** This adds a hook to the current class and leaves the real outbound integration outside that test. Check call count, ordering, exception flow, and argument evaluation. With many calls on the same collaborator, a single getter or adapter may be clearer than many hooks.

## Extract and Override Factory Method

Source: pp. 350–351 / PDF 373–374.

**Use when:** Constructor-created collaborators prevent testing, and the language permits the required dispatch during construction.

1. Identify the complete creation sequence, including dependent objects and configuration reads.
2. Extract it into an overridable factory method that returns the collaborator.
3. Have the original constructor call that factory.
4. Override it in a test subclass, then verify construction uses the substitute.

**Worked example:** A workflow engine's creation of readers, persistence objects, and a transaction manager moves into `makeTransactionManager`.

**Trade-off:** A C++ base constructor does not dispatch a virtual call to the derived test override. In languages that allow such dispatch, the override may run before derived fields are initialized; it must not rely on them. Prefer constructor parameterization when it fits. Superseding after construction cannot undo a harmful constructor call.

## Extract and Override Getter

Source: pp. 352–355 / PDF 375–378.

**Use when:** Several methods use one awkward collaborator, and its creation can be deferred until after construction without changing required behavior.

1. Move all creation work into an overridable getter.
2. Initialize the stored reference as absent in every constructor.
3. Make the getter construct and store the object on first use.
4. Redirect every relevant access through the getter.
5. Override it in tests to supply a fake; verify production creation and cleanup separately.

**Worked example:** A workflow engine lazily gets its transaction manager so a test subclass can provide one without constructing the real manager.

**Trade-off:** Delaying creation changes when configuration is read, resources are acquired, and exceptions occur. Use only when that timing is acceptable, or choose a different seam. Direct field accesses can bypass initialization. Preserve ownership and deletion rules, and address concurrent first access if the production object is shared. Calling the getter from a C++ base constructor does not solve the dispatch limitation.
