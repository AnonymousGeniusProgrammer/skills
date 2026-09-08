# Chapter 4: Isolating the Domain

Source: supplied PDF, pages 36-43. Tables and examples are synthesized from the chapter.

## Core Idea

Keep the implementation of domain concepts and business rules cohesive enough to understand and change independently of presentation, application coordination, and technical infrastructure. This isolation makes an expressive model possible.

## Frameworks and Application

### Layered Architecture

Use when one feature mixes domain decisions with widgets, persistence, network operations, and task management.

1. Trace a real user operation through the program. Classify the responsibilities it performs.
2. Identify the concepts, business state, and rules that belong to the domain model.
3. Move task orchestration, presentation, and generic technical mechanisms to appropriate collaborators.
4. Connect layers through interfaces and appropriate interaction patterns so each can retain a coherent design.
5. Check whether the domain behavior can now be discussed and exercised without simultaneously reasoning about a UI or database workflow.

| Layer in Evans's presentation | Responsibility | State and decisions |
|---|---|---|
| User Interface / Presentation | Show information and interpret an external actor's commands | Presentation concerns; the actor may be another system |
| Application | Coordinate meaningful application tasks and delegate to domain objects | Task progress, not business rules or business situation state |
| Domain / Model | Express business concepts, business information, and rules | Business state and its valid changes |
| Infrastructure | Provide generic technical capabilities | Storage, message transport, and support mechanisms |

Why it works: these concerns change for different reasons and at different rates. A domain design can become more precise when a rule change does not require tracing event handlers and database scripts to discover all of its meaning.

The book describes conventional layering, with dependencies toward lower layers and infrastructure services supporting application and domain work. It does not prescribe a modern dependency-inversion or hexagonal architecture package layout. Apply its isolation criterion within the project's architecture, and identify any modern adaptation explicitly.

### Relating the Layers

Separation still requires collaboration. Upper layers can invoke lower-layer interfaces. If lower-level objects need to notify higher-level collaborators, use an indirect mechanism such as callbacks or Observer instead of introducing knowledge of the higher-level concrete design.

MVC and related presentation patterns are examples of ways to connect these responsibilities. Select a mechanism that lets domain objects express the model without being responsible for their own screens or application tasks. Layers are conceptual responsibility divisions, not a requirement to deploy four separate services.

Infrastructure may also appear as a framework that requires inheritance or certain signatures. Evaluate its effect on how freely the domain can be expressed, not only whether its classes are placed in an infrastructure folder.

### Selective Use of Architectural Frameworks

Use framework features where they solve actual technical problems. Ask which constraints each adopted feature imposes on domain classes and which supporting mechanisms it removes from them.

If framework ceremony or assumptions dominate business objects, narrow its use or place it behind a suitable interface. The chapter's historical example is the overuse of J2EE entity beans; preserve the lesson about selective adoption rather than repeating its technology advice as current guidance.

## Worked Example: A Funds Transfer

An operator selects two accounts, enters an amount, and requests a transfer. Several kinds of responsibility appear in one interaction:

1. The presentation accepts and interprets the request.
2. The application coordinates the transfer task, using the available domain objects and technical services.
3. The domain enforces the business relationship between the corresponding debit and credit.
4. Infrastructure provides persistence and transaction mechanisms needed by the implementation.

The application should not define the matched-debit-and-credit rule as incidental orchestration. If another entry point requests the same business operation, the domain meaning must remain the same.

Synthesized responsibility sketch, not production banking code:

```text
Presentation: interpret a transfer request
Application: arrange the work needed to carry it out
Domain: perform a valid transfer with corresponding debit and credit
Infrastructure: supply technical storage and transaction facilities
```

The source intentionally omits security and simplifies the domain. It is an illustration of responsibility placement, not a complete funds-transfer design. In real project work, retain existing security, transaction, and failure-handling requirements rather than deriving their absence from this example.

### Review the result

- A change from a screen to a machine request should not redefine the business rule.
- A persistence decision should not require a domain object to manage presentation state.
- A test of the domain rule should expose its business meaning directly.
- Application task progress may be stateful even though the application layer does not own business situation state.

## Smart UI: A Contextual Alternative

The chapter calls Smart UI an anti-pattern in the context of domain-driven design while recognizing situations where it is a legitimate approach. It is intended for simple, mainly data-entry/display applications with few business rules and limited modeling needs.

| Consideration | Smart UI | Isolated model-driven domain |
|---|---|---|
| Initial delivery of simple functions | Can be quick with suitable tools | Layering and modeling create overhead |
| Team skills | Lower modeling demands | Requires appropriate design skill and commitment |
| Reuse of business behavior | Rules tend to be repeated in UI operations | Shared domain concepts can express reusable behavior |
| Growth | More separate simple applications | Can support richer interconnected behavior |
| Move to a different design approach | No easy incremental path is promised | Model evolves through explicit refactoring |

Use this decision deliberately. A general-purpose language does not make a Smart UI architecture automatically ready for domain-driven design later. Conversely, a simple CRUD application does not justify a complex modeling infrastructure merely to follow a pattern.

The choice is not limited to two architecture names. The chapter also acknowledges intermediate approaches such as Transaction Script. For DDD, the decisive condition is an isolated, cohesive domain implementation capable of expressing its model.

## Key Concepts

- **Domain layer**: implemented business concepts, information, and rules corresponding to the domain model.
- **Application layer**: task coordination that delegates business reasoning to domain collaborators.
- **Infrastructure service**: a generic technical capability offered through an interface.
- **Layered Architecture**: partitioning responsibilities into cohesive layers with controlled dependencies.
- **Smart UI**: placing business logic in separate UI operations with shared data storage, suitable for some simple applications.
- **Separation of concerns**: enabling different kinds of design reasoning without entangling their details.

## Mental Models and Anti-patterns

- Treat the domain layer as the place where business meaning can be seen together.
- Treat a layer's responsibility as more important than its directory name.
- Avoid **domain logic diffused across UI and storage**: changing a rule becomes a search through unrelated technologies.
- Avoid **a thick application layer** whose scripts own rules that should be explicit in the domain.
- Avoid **framework-driven modeling** where the framework determines business abstractions by convenience.
- Avoid **uncommitted DDD**: adopting its infrastructure while leaving business logic in UI actions incurs cost without its main benefit.

## Key Takeaways

1. Separate business decisions from application coordination and technical mechanisms.
2. Evaluate isolation by whether domain behavior can be understood and exercised independently.
3. Apply frameworks selectively where they serve an expressive implementation.
4. Match the modeling investment to actual domain complexity and team capability.
5. Use Chapter 14 for interference from other models and Chapter 15 for complexity within the domain itself.

## Connects To

- [Chapter 3](ch03-binding-model-and-implementation.md): isolation supports a direct model/code relationship.
- [Chapter 5](ch05-a-model-expressed-in-software.md): assign domain responsibilities to appropriate building blocks.
- [Chapter 14](ch14-maintaining-model-integrity.md): protect the model from incompatible external models.
- [Chapter 15](ch15-distillation.md): keep the central domain understandable amid supporting detail.
