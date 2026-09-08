# Chapter 25 recipes: Relocation and substitution

Source: printed pp. 388–413; PDF pp. 411–436. Return to the [catalog and selection guide](ch25-dependency-breaking-techniques.md).

## Pull Up Feature

Source: pp. 388–391 / PDF 411–414.

**Use when:** A cluster of methods is independent of the bad dependencies that prevent constructing its current class.

1. Identify the cluster and verify that its transitive dependencies exclude the problematic environment.
2. Create an abstract superclass and move the cluster there, preserving signatures.
3. Move the fields and helper methods it requires, using compiler feedback and reference inspection.
4. Preserve initialization and inherited binding; leave environment-specific behavior in the original production subclass.
5. Create a concrete test subclass with the minimal setup access and test the moved feature.

**Worked example:** Scheduling calculations move into `SchedulingServices`, leaving database-related schedule updates in `Scheduler`.

**Trade-off:** The new inheritance boundary can be less coherent than delegation to a validator or calculator. It is a test foothold. Do not move hidden external dependencies upward with the feature or ignore changed field resolution. Test production construction as well as the convenient test subclass.

## Push Down Dependency

Source: pp. 392–395 / PDF 415–418.

**Use when:** A few environment-specific dependencies stop the main class from building in the harness, and overriding alone still pulls their types into that build.

1. Identify the troublesome methods, fields, and imports.
2. Create a production subclass named for the relevant environment.
3. Move that environment-specific implementation into it; leave abstract hooks in the original class and make it abstract.
4. Update production construction sites to use the new subclass and preserve initialization and dispatch.
5. Supply a test subclass that implements the hooks with controlled behavior. Test the remaining logic.

**Worked example:** Windows dialogs move out of an off-market trade validator into a Windows-specific subclass. A test subclass suppresses or models those interactions while exercising validation.

**Trade-off:** The moved UI code may contain real decisions, including retry and validity changes. A do-nothing override does not test those decisions. Identify that gap and later separate policy from the external calls under tests. Do not declare the entire validator covered merely because the base logic runs.

## Replace Function with Function Pointer

Source: pp. 396–398 / PDF 419–421.

**Use when:** Procedural code needs runtime substitution and a link seam would require awkward build changes.

1. Locate the function declaration and preserve its exact type.
2. Rename the production function and introduce a pointer using the original callable name, or group callbacks in an operations structure when appropriate.
3. Define and initialize the pointer to the production function before any call can occur.
4. Update the production definition's name and compile relevant callers.
5. Substitute a compatible recording function in tests and restore state reliably.

**Worked example:** Calls to `db_store` go through a pointer initialized to `db_store_production`; tests select an in-memory recorder.

**Trade-off:** This introduces indirect runtime calls and mutable selection state. Check signatures, calling conventions, initialization order, and thread/test isolation. In C, declare shared pointers appropriately in headers and define them once in a source file; do not multiply definitions across translation units. The book's suggestion to consider C++ migration is optional context, not required task scope.

## Replace Global Reference with Getter

Source: pp. 399–400 / PDF 422–423.

**Use when:** A client accesses one global object in several places and needs a local substitution point.

1. Add an overridable getter on that client that returns the same global reference.
2. Replace relevant direct accesses with the getter.
3. Override it in a test subclass to return a controlled collaborator; extract a collaborator interface if necessary.
4. Verify the client still uses the getter on every exercised path and the normal getter preserves global lookup semantics.

**Worked example:** `RegisterSale` gets inventory through its own getter before looking up an item by barcode.

**Trade-off:** This replaces the client's view of a global without replacing the process-wide instance. Other clients may still access the real global. Unlike Extract and Override Getter, it need not introduce lazy creation or caching. Preserve repeated lookup behavior if the global can vary, and inspect any constructor access before relying on a test override.

## Subclass and Override Method

Source: pp. 401–403 / PDF 424–426.

**Use when:** Existing method boundaries isolate a dependency or an observation point from the behavior being tested.

1. Find the smallest useful set of methods to override, preferably existing methods.
2. Make them overridable and accessible as required by the language, after checking inheritance collisions.
3. Create a test subclass whose overrides provide separation, controlled return values, or recording.
4. Instantiate it in the harness and verify the target production methods still execute.

**Worked example:** A forwarding test substitutes message construction when it only needs to test routing. The replacement message must still provide the properties routing actually uses.

**Trade-off:** Overridden bodies are outside that test's coverage. An empty message cannot establish correct addressing or content. In C++, private virtual methods can be overridden, but changing a nonvirtual method to virtual may alter existing dispatch. Constructor calls need separate analysis. The paper-view mental model makes the boundary explicit: the test sees the original methods through the portions replaced by the test subclass.

## Supersede Instance Variable

Source: pp. 404–407 / PDF 427–430.

**Use when:** Construction can run acceptably, but later operations need a different stored collaborator and constructor parameterization is currently awkward.

1. Identify the stored reference and all aliases to its object.
2. Add an explicitly named `supersede...` method accepting the replacement.
3. Release the former owned resource correctly and update every relevant reference without leaving dangling aliases.
4. Construct normally in the test, supersede before the target operation, and assert through the replacement.
5. Verify subsequent cleanup respects ownership of both old and new objects.

**Worked example:** A blending pen builds and configures a parameter during construction; the test replaces it with a sensing parameter before later pen operations.

**Trade-off:** This cannot prevent hardware access, network traffic, or any other work already performed during construction. The C++ pager example specifically demonstrates why a derived override does not intercept the base constructor's call. If that work is unacceptable, choose a seam before it executes. Mutable replacement also makes object history harder to understand; keep the purpose explicit and minimize production use.

## Template Redefinition

Source: pp. 408–411 / PDF 431–434.

**Use when:** Compile-time type substitution fits the design, particularly when problematic dependencies are already part of templated code.

1. Identify the collaborator type to vary.
2. Parameterize the owning class by that type and make the implementation available to instantiation as required by the language/build.
3. Give the template a separate name.
4. Keep the original production name as an alias for the template instantiated with the original type.
5. Instantiate with a compatible fake in tests; compile and check the production specialization too.

**Worked example:** `AsyncReceptionPortImpl<SocketType>` uses a supplied socket type. The original port name aliases the real socket specialization; tests instantiate a fake socket version.

**Trade-off:** Moving C++ implementations into headers can increase recompilation and dependencies. A fake specialization does not prove every real-type interaction. Preserve the algorithm exactly while parameterizing; do not fix suspicious predicates during the seam change. Check existing forward declarations and type-identity assumptions before relying on the alias to preserve source compatibility.

## Text Redefinition

Source: pp. 412–413 / PDF 435–436.

**Use when:** The language supports replacing method definitions after loading, or a deliberate preprocessing seam can supply the replacement.

1. Identify precisely the method to replace.
2. Load its defining module in a controlled test context.
3. Supply the alternate method definition before executing the tests.
4. Verify replacement scope and ensure other tests cannot unknowingly inherit the altered definition, using isolation or supported restoration.

**Worked example:** The Ruby example reopens `Account` and replaces deposit reporting, leaving deposit's balance update in place.

**Trade-off:** The replacement persists in that interpreter unless explicitly restored; it is not automatically local to a test or instance. The source also points to C/C++ preprocessing seams, whose selection happens during preprocessing rather than runtime reopening. Keep the production definition independently checked and do not redefine the behavior the test claims to protect.
