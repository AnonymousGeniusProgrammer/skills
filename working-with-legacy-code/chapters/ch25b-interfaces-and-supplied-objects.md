# Chapter 25 recipes: Interfaces and supplied objects

Source: printed pp. 356–387; PDF pp. 379–410. Return to the [catalog and selection guide](ch25-dependency-breaking-techniques.md).

## Extract Implementer

Source: pp. 356–361 / PDF 379–384.

**Use when:** The existing class's name is the right abstraction name, and its implementation can move to a separately named concrete class.

1. Copy the concrete implementation into a newly named class, retaining signatures and initialization.
2. Convert the original type into an interface: retain the needed public method declarations; remove implementation and instance state.
3. Make the new concrete class implement that interface. In C++, handle virtual destruction correctly, including a definition if using a pure virtual destructor.
4. Remove unnecessary implementation imports from the interface.
5. Update production construction sites to instantiate the implementer. Clients that only use the abstraction keep their type.
6. Compile relevant targets and test both the implementer and the client seam.

**Worked example:** `ModelNode` becomes the abstraction; `ProductionModelNode` contains the former implementation.

**Trade-off:** Construction sites still need a concrete type. Existing parents, subclasses, protected methods, or concrete-type assumptions can make the change extensive. Feathers demonstrates a hierarchy migration but recommends considering the more direct Extract Interface with another name instead. Do not restructure a whole hierarchy merely to obtain a preferred name.

## Extract Interface

Source: pp. 362–368 / PDF 385–391.

**Use when:** You own a collaborator type and need a substitutable view for particular clients.

1. Create an empty interface and make the original class implement it.
2. Change the relevant client's reference to that interface.
3. Compile to identify required operations; copy their declarations with exact signatures into the interface.
4. Implement those operations in a fake with the necessary observations or controlled results.
5. Pass it to the client and test the original client logic. Verify the production type still supplies the contract.

**Worked example:** `PaydayTransaction` needs a `TransactionRecorder` that can save a transaction. The new interface need not contain every method of `TransactionLog` merely because those methods exist.

**Trade-off:** In C++, turning a formerly nonvirtual method virtual can activate a same-signature method in a derived class and change existing calls. Inspect the hierarchy first. A distinctly named virtual delegator to the original operation can preserve existing binding more predictably. Apply the actual language's interface and override rules; do not generalize this C++ hazard to every language. Follow established naming conventions and prefer a name expressing the client's need.

## Introduce Instance Delegator

Source: pp. 369–371 / PDF 392–394.

**Use when:** A static service method needs an object seam for tests.

1. Add an overridable instance method to the service class with the same arguments and result under a suitable new name.
2. Make it forward directly to the existing static method.
3. Supply a service object to the relevant client, using parameterization or another seam.
4. Replace that client's static call with the instance call; use a test implementation to sense or separate.

**Worked example:** `updateBalance` delegates to the static account-balance operation; a client accepts a banking-services object.

**Trade-off:** A mixed static/instance utility class looks transitional. Once callers migrate under tests, the static bodies may move into ordinary instance methods. Creating a fresh real service inside the client would defeat substitution. Unlike Extract and Override Call, the forwarding hook lives on the collaborator class, not on the client.

## Introduce Static Setter

Source: pp. 372–376 / PDF 395–399.

**Use when:** A singleton/global service must be substituted and local parameter passing or a getter seam is not yet practical.

1. Enable construction of a test implementation with the minimum needed access, often a protected constructor and test subclass.
2. Add an explicitly named testing setter for the shared instance or service provider.
3. Define ownership and lifecycle behavior for replacing the old instance.
4. Set a controlled instance for each test and restore known state through reliable cleanup.
5. Verify the normal production access path and the test's isolation from other users of the global.

**Worked example:** Replace the singleton router that supplies a dispatcher. For a global factory that creates fresh objects, substitute a provider object rather than unintentionally changing it into a singleton product.

**Trade-off:** The seam changes process-wide state. Cleanup cannot by itself make concurrent tests safe; isolate or serialize affected tests according to the harness. Do not delete an object still referenced elsewhere, restore a freed pointer, or blindly copy the book's raw-pointer deletion example. This is a temporary opening toward explicit dependencies, not a recommendation to spread global setters.

## Link Substitution

Source: pp. 377–378 / PDF 400–401.

**Use when:** Free functions or external libraries obstruct a harness and the build can select alternate definitions.

1. Identify the exact functions or classes to replace.
2. Provide alternative definitions with compatible signatures and linkage.
3. Configure the dedicated test build to select the alternatives instead of production definitions.
4. Record calls, arguments, order, and configured results as needed. Reset recording state between tests.
5. Check both test and production resolution.

**Worked example:** A fake account-deposit function appends calls to a test-owned log. Data-sink libraries, such as drawing APIs, are often useful boundaries because tests can examine emitted operations.

**Trade-off:** Build configuration becomes the enabling point. A missing return behavior or wrong ABI can invalidate the substitute. The book also describes Java classpath substitution; verify the actual project's loading rules before relying on it. Use Definition Completion for the particular case of supplying missing definitions behind real declarations, accepting its duplication cost.

## Parameterize Constructor

Source: pp. 379–382 / PDF 402–405.

**Use when:** A constructor internally creates a collaborator that tests need to supply.

1. Add a constructor variant that accepts that collaborator.
2. Replace the corresponding creation expression in that variant with the supplied reference, preserving remaining initialization.
3. Keep the original signature and have it create the normal collaborator and delegate to the new constructor where supported.
4. Test with a fake and verify the normal construction path retains the original configuration and behavior.

**Worked example:** `MailChecker` accepts a receiver for tests; its original constructor supplies a real receiver and keeps the checking period unchanged.

**Trade-off:** A new public parameter exposes another type to clients. An interface can limit that dependency. Preserve allocation/ownership and creation timing, especially when the old creation occurred amid other constructor work. Default arguments may introduce header dependencies in C++; do not copy historical language restrictions on constructor delegation as current facts.

## Parameterize Method

Source: pp. 383–384 / PDF 406–407.

**Use when:** One operation constructs a collaborator internally and tests need a controlled object for that invocation.

1. Add a variant accepting the object; preserve the remaining signature and body.
2. Replace internal construction with the supplied value.
3. Keep the original entry point as a forwarder that creates the normal object.
4. Verify result, cleanup, and exceptional paths in both routes.

**Worked example:** A test-case runner accepts a result collector; the original `run()` creates its usual collector and delegates.

**Trade-off:** Do not change per-invocation creation into long-lived state accidentally. Moving creation to a forwarding argument may change its order relative to cleanup or other work in the original body. Check this explicitly. Document ownership transfer where relevant; a method that deletes its prior collector must not receive an alias to that same object and then use it after deletion.

## Primitivize Parameter

Source: pp. 385–387 / PDF 408–410.

**Use when:** Domain objects have prohibitive construction/build dependencies, but a bounded computation can operate on a simple representation.

1. Test-drive a free function over the required primitive representation.
2. Add a small bridge on the original class that builds that representation and delegates.
3. Check the bridge and integration as far as the available harness permits; state any remaining coverage gap.
4. Plan the next local step toward testing the original class or a cohesive sprouted class.

**Worked example:** A sequence-gap calculation uses vectors of event durations, so it can be tested without constructing persistence-bound sequence and event objects.

**Trade-off:** This can expose representation, copy data, add an untested translation, and postpone proper decoupling. It is Feathers's reluctant fallback, not a general preference for primitive APIs. Test conversion of order, units, and absence explicitly when possible; do not mistake the calculation's coverage for coverage of the bridge.
