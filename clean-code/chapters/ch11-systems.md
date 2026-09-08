# Chapter 11: Systems

Source: printed pp. 153–170; PDF pp. 184–201. Dr. Kevin Dean Wampler.

## Core Idea

Keep domain behavior separate from object construction and infrastructure concerns. A system can evolve incrementally when its important concerns remain independently understandable and testable.

## Frameworks Introduced

### Separate Constructing a System from Using It

Use this when business logic constructs its concrete services, looks them up globally, or chooses configuration details while doing normal work.

1. Identify construction, wiring, and configuration decisions.
2. Move them to startup or dedicated construction modules.
3. Pass the resulting dependencies to the application.
4. Make the application operate on their contracts without knowing the startup mechanism.

The chapter calls this **Separation of Main**. Its dependency arrows point from startup toward the application: the application should not depend on `main` to resolve its needs.

Why it works: tests can supply appropriate collaborators without running heavyweight construction. Failure mode: moving creation into a global locator changes syntax while leaving runtime code responsible for finding its dependencies.

### Factories for “When,” Separate from “How”

Use a factory when application behavior decides when an object is needed but should not choose its concrete construction details. The order-processing example lets the application request a line item through an abstract factory while startup supplies the factory implementation.

The factory has a specific purpose: preserve runtime control over creation timing without coupling the consumer to implementation construction. It is not a required layer around every constructor.

### Dependency Injection and Inversion of Control

**Dependency Injection** supplies dependencies through constructors or setters rather than asking the object to resolve them. It applies **Inversion of Control** to dependency management.

Use constructor or setter injection according to the contract and project conventions. A container is one mechanism; direct assembly in startup is also covered by the source's separation. The chapter describes a service lookup as only partial inversion because the consumer still initiates resolution.

### Cross-Cutting Concerns

Use this concept when persistence, transactions, security, or other policies appear across many otherwise independent domain objects.

1. Identify the repeating concern and the places it intersects domain behavior.
2. Specify where and under what conditions it must apply.
3. Encapsulate its implementation in an appropriate mechanism.
4. Keep domain behavior independently testable.
5. Verify that the integration applies the concern at the intended boundaries.

The chapter explores Java proxies, pure Java AOP frameworks, and AspectJ. These are examples of separating concerns, not a mandate to introduce aspect-oriented programming into every system.

| Mechanism in the source | Useful property | Cost to assess |
|---|---|---|
| Java dynamic proxy | Intercepts interface operations | Boilerplate and limited scope of interception |
| Framework-based AOP | Supplies much of the interception machinery | Framework behavior and configuration become part of the design |
| AspectJ | Richer aspect mechanisms | Additional concepts, tooling, and adoption effort |
| Plain domain objects | Domain behavior can be exercised independently | Integration still needs separate verification |

The technologies and framework comparisons describe the book's era. Check the actual project and current primary documentation before choosing or changing such tooling.

### Test Drive the System Architecture

Start with the simplest architecture that supports present needs while preserving separation of concerns. Add infrastructure as demonstrated requirements appear. The source distinguishes Big Design Up Front from useful early design: understand scope, goals, and general structure while retaining the ability to change course.

### Optimize Decision Making; Use Standards Wisely

Place decisions with people who have the relevant knowledge. Defer choices while doing so can improve information without undermining necessary progress. Adopt a standard for demonstrated benefits such as interoperability or expertise, not merely because it is a standard.

### Domain-Specific Languages

Use an API or small language that expresses operations in domain terms when it reduces translation between business meaning and implementation. A DSL can exist within an ordinary programming language; it need not introduce a parser or external syntax.

## Worked Example

The source compares a runtime getter that lazily constructs `MyServiceImpl` with construction separated into startup.

Reconstructed illustration:

```java
Service service = new ConfiguredService(configuration);
Application application = new Application(service);
application.run();
```

The application receives a usable `Service`; its ordinary logic does not choose `ConfiguredService`. A test supplies a suitable implementation to the same constructor.

For an operation that must create line items later, inject a `LineItemFactory` instead of eagerly creating all items. The application controls *when* creation happens while the factory controls *how*.

Validate these separately:

- Domain tests establish behavior with supplied collaborators.
- Startup tests establish that actual configuration creates and connects the intended implementations.
- Integration tests establish that persistence, transactions, or interception occur at the required boundaries.

The source's EJB2 counterexample shows why this matters: container inheritance, lifecycle requirements, and deployment machinery invade business objects, making isolated tests and reuse difficult.

## Key Concepts

- **Separation of Main**: startup assembles objects and then hands them to the application.
- **Dependency Injection**: externally supplied collaborators.
- **Inversion of Control**: a responsibility is assigned to a dedicated mechanism instead of the consumer.
- **POJO**: Plain Old Java Object, used here for domain behavior independent of invasive infrastructure.
- **Cross-cutting concern**: a policy intersecting multiple domain units.
- **Aspect**: a modular description of behavior applied at selected points.
- **DSL**: a vocabulary and structure for expressing a particular domain.
- **BDUF**: attempting to design the complete system before implementation feedback.

## Mental Models

- Separate the construction crane from the working hotel.
- Treat configuration and wiring as a system responsibility with a clear home.
- Preserve options through modularity while implementing today's requirements.

## Anti-patterns

- Scattered lazy construction hides global setup decisions in ordinary methods.
- A service locator preserves hidden resolution dependencies.
- Infrastructure dictates domain inheritance and lifecycle unnecessarily.
- A framework is selected for prestige or standard status without a demonstrated need.
- Passing domain unit tests is treated as proof that infrastructure configuration works.

## Key Takeaways

1. Separate construction, runtime behavior, and cross-cutting policies.
2. Inject dependencies or factories through explicit contracts.
3. Evolve architecture with feedback while keeping concerns separate.
4. Verify both isolated behavior and actual assembly.

## Connects To

- [Chapter 8](ch08-boundaries.md): owned contracts and adapters.
- [Chapter 10](ch10-classes.md): dependency inversion and responsibilities.
- [Chapter 13](ch13-concurrency.md): separating scheduling from application work.
