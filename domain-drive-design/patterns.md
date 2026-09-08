# Pattern and Technique Catalog

Details: [chapter index](SKILL.md#chapter-index).

## Knowledge Crunching; Ubiquitous Language (Ch 1-2)
**When to use:** Discovering meaning.
**How:** Combine scenarios, shared language, and experiments.
**Trade-offs:** Requires sustained dialogue.

## Model-Driven Design; Hands-On Modelers (Ch 3)
**When to use:** Models should drive software.
**How:** Evolve model and implementation together.
**Trade-offs:** Demands modeling and coding skill.

## Layered Architecture; Smart UI (Ch 4)
**When to use:** Choosing architectural separation.
**How:** Isolate rich behavior; use Smart UI for simple needs.
**Trade-offs:** Separation costs effort; UI coupling limits growth.

## Entities; Value Objects (Ch 5)
**When to use:** Defining sameness.
**How:** Model continuity explicitly; otherwise favor descriptive immutable values.
**Trade-offs:** Identity adds life-cycle complexity.

## Services (Ch 5)
**When to use:** Operations lack a natural object home.
**How:** Define stateless domain operations.
**Trade-offs:** Overuse impoverishes objects.

## Associations; Modules (Ch 5)
**When to use:** Dependencies obscure concepts.
**How:** Limit traversal; group cohesive domain responsibilities.
**Trade-offs:** Alternative retrieval may be needed.

## Aggregates (Ch 6)
**When to use:** Coordinating invariants and life cycles.
**How:** Define a consistency boundary and root-controlled access.
**Trade-offs:** Large boundaries increase contention.

## Factories; Reconstitution (Ch 6)
**When to use:** Construction is complex.
**How:** Create valid wholes; preserve restored identity.
**Trade-offs:** Keep behavior separate.

## Repositories (Ch 6)
**When to use:** Direct retrieval is needed.
**How:** Offer model-oriented root access; encapsulate storage.
**Trade-offs:** Query cost and transaction scope.

## Breakthrough; Exploration Teams (Ch 8, 13)
**When to use:** Current concepts obstruct scenarios.
**How:** Explore with experts, implement, test.
**Trade-offs:** Insight is uncertain; transitions cost.

## Making Implicit Concepts Explicit (Ch 9)
**When to use:** Rules hide in awkward behavior.
**How:** Examine language, constraints, processes, and contradictions.
**Trade-offs:** Reject unhelpful candidate abstractions.

## Specification (Ch 9)
**When to use:** A criterion needs explicit reuse.
**How:** Separate criteria from task mechanisms.
**Trade-offs:** Execution efficiency needs design.

## Intention-Revealing Interfaces; Assertions (Ch 10)
**When to use:** Clients cannot predict behavior.
**How:** Express purpose and behavioral contracts.
**Trade-offs:** Maintain meaningful contracts.

## Side-Effect-Free Functions; Closure of Operations (Ch 10)
**When to use:** Combinations are hard to reason about.
**How:** Compute without mutation; return natural same-type values.
**Trade-offs:** Not every operation closes.

## Conceptual Contours; Standalone Classes (Ch 10)
**When to use:** Change and comprehension spread too far.
**How:** Separate distinct concepts and unnecessary dependencies.
**Trade-offs:** Mechanical fragmentation loses cohesion.

## Composite Specifications; Subsumption (Ch 10)
**When to use:** Combining or comparing criteria.
**How:** Compose predicates; prove stronger-implies-weaker relationships.
**Trade-offs:** General implication can exceed simple algorithms.

## Shares Math; Declarative Style (Ch 10)
**When to use:** A formalism fits domain operations.
**How:** Use value arithmetic and expressive combinations.
**Trade-offs:** Establish actual arithmetic assumptions; avoid speculative frameworks.

## Analysis Patterns; Account/Entry (Ch 11)
**When to use:** Established models fit a business construction.
**How:** Adapt concepts; preserve names' meanings.
**Trade-offs:** No turnkey solution.

## Posting Rules (Ch 11)
**When to use:** Account dependencies become tangled.
**How:** Express derived postings and choose eager, account-based, or rule-based firing.
**Trade-offs:** Initiation and progress need clear ownership.

## Strategy/Policy; Composite (Ch 12)
**When to use:** Domain variation or true part-whole sameness exists.
**How:** Encapsulate policies; expose uniform composite behavior.
**Trade-offs:** Patterns require conceptual fit.

## Flyweight (Ch 5, 12)
**When to use:** Repeated equivalent values justify sharing.
**How:** Share representation without changing meaning.
**Trade-offs:** Does not collapse distinct Entity identities.

## Bounded Context; Context Map (Ch 14)
**When to use:** Multiple models coexist.
**How:** Map real scopes, contacts, and responsibilities.
**Trade-offs:** Boundaries require deliberate maintenance.

## Continuous Integration (Ch 14)
**When to use:** Several developers evolve one model.
**How:** Frequently reconcile language, code, builds, and tests.
**Trade-offs:** Coordination effort grows.

## Shared Kernel; Customer/Supplier Development Teams (Ch 14)
**When to use:** Teams can cooperate.
**How:** Steward shared concepts, or negotiate downstream needs and tests.
**Trade-offs:** Requires real commitments.

## Conformist; Separate Ways (Ch 14)
**When to use:** Cooperation or integration value is limited.
**How:** Follow usable upstream semantics, or remove unnecessary integration.
**Trade-offs:** Independence or modeling freedom is constrained.

## Anticorruption Layer; Facade; Adapter; Translator (Ch 14)
**When to use:** Foreign semantics threaten local meaning.
**How:** Expose local services; adapt and translate.
**Trade-offs:** Significant maintenance may result.

## Open Host Service; Published Language (Ch 14)
**When to use:** Many parties exchange capabilities or information.
**How:** Publish coherent service and interchange contracts.
**Trade-offs:** Stability obligations restrict change.

## Context Transformations; Legacy Phase-Out (Ch 14)
**When to use:** Current boundaries no longer fit.
**How:** Establish tests, migrate small responsibilities, remove obsolete translations.
**Trade-offs:** Temporary complexity and deployment coordination.

## Core Domain; Generic Subdomains (Ch 15)
**When to use:** Effort is dispersed.
**How:** Prioritize distinctive value; separate generic support.
**Trade-offs:** Classification evolves.

## Domain Vision Statement; Highlighted Core (Ch 15)
**When to use:** Direction or navigation is unclear.
**How:** State value; mark core or write a distillation document.
**Trade-offs:** Keep documentation current.

## Cohesive Mechanisms; Segregated Core (Ch 15)
**When to use:** Computation or support obscures the core.
**How:** Encapsulate computation; separate core responsibilities.
**Trade-offs:** Refactoring and additional interfaces.

## Abstract Core (Ch 15)
**When to use:** Fundamental abstractions can express major interactions.
**How:** Separate those abstractions from specialized implementations.
**Trade-offs:** Requires deep insight, not import counting.

## Evolving Order; System Metaphor (Ch 16)
**When to use:** The whole is hard to explain.
**How:** Evolve minimal rules; adopt a useful analogy if available.
**Trade-offs:** Revisit misleading constraints.

## Responsibility Layers (Ch 16)
**When to use:** Natural domain strata exist.
**How:** Organize broad responsibilities with downward conceptual dependencies.
**Trade-offs:** Local designs accept global constraints.

## Knowledge Level (Ch 16)
**When to use:** Users configure model behavior and relationships.
**How:** Separate configuration using ordinary objects.
**Trade-offs:** Indirection and migration complexity.

## Pluggable Component Framework (Ch 16)
**When to use:** Mature shared models justify interoperability.
**How:** Substitute implementations through Abstract Core contracts.
**Trade-offs:** Shared contracts inhibit core evolution.

## Strategic Assessment and Feedback (Ch 17)
**When to use:** Coordinating design across a project.
**How:** Assess contexts, language, core, technology, and skills; learn from practice.
**Trade-offs:** Requires communication and restraint.
