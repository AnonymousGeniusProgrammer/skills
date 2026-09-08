---
name: domain-drive-design
description: "Knowledge base from Domain-Driven Design: Tackling Complexity in the Heart of Software by Eric Evans. Use when applying Evans's domain modeling, Ubiquitous Language, Entities and Value Objects, Aggregates, Bounded Contexts, integration patterns, strategic distillation, or refactoring toward deeper insight; also use for studying or referencing the book."
---

# Domain-Driven Design

**Author:** Eric Evans | **Source:** 324 PDF pages | **Chapters:** 17 plus Conclusion and pattern-language appendix | **Generated:** 2026-09-08 | **Depth:** study, technical extraction

## Use This Skill

Use the core guidance below for orientation. Before giving detailed advice or explaining an example, read the relevant chapter through the indexes. Load only the chapters needed for the question.

- With a topic or pattern, use the Topic Index.
- With a chapter number such as `ch06`, use the Chapter Index.
- For a quick design choice, read [cheatsheet.md](cheatsheet.md).
- For terminology, read [glossary.md](glossary.md); for pattern conditions, read [patterns.md](patterns.md).
- For a book overview, show the chapter progression and suggest a reading path matched to the user's problem.

When applying the book to a project, inspect actual scenarios, domain terms, relevant code, invariants, and team boundaries. Distinguish observed facts, assumptions, Evans's guidance, and your adaptation. Explain the problem a pattern resolves, its costs, and how the proposed behavior can be checked. Treat generated models as hypotheses to test with implementation and domain expertise.

## Core Frameworks and Judgment

### Learn a model that can drive working software

**Knowledge Crunching:** combine domain experts' experience with developers' modeling and implementation experiments. Use real scenarios, seek important rules, and revisit abstractions as understanding improves. A requirements transcript or exhaustive noun list is not yet a useful model.

**Ubiquitous Language:** use the model's terms and relationships in conversations, diagrams, tests, and code within its context. Awkward explanations and mismatched words are design evidence. Change the language and the implementation together. A glossary helps but does not replace shared use.

**Model-Driven Design:** keep the model and implementation mutually informative. Favor concepts that both explain the domain and support a practical design. Implementation limitations should feed back into modeling. **Hands-On Modelers** stay involved with code; responsibility for useful models cannot be isolated from implementation experience.

Read [1][c1], [2][c2], and [3][c3] for the learning loop and its examples.

### Give tactical elements responsibilities with domain meaning

**Layered Architecture:** isolate domain behavior from presentation, application orchestration, and infrastructure. The application coordinates a task; the domain expresses its rules. The book's conventional layers do not mandate a particular modern framework or package layout. **Smart UI** remains a pragmatic alternative for sufficiently simple applications; elaborate modeling must earn its cost. See [4][c4].

**Entities** need continuity and a deliberate definition of identity. **Value Objects** express descriptive meaning through their attributes; prefer immutability and replace values as wholes. Attribute equality does not merge distinct Entities. **Services** represent meaningful domain operations that fit neither an Entity nor a Value Object naturally; make their intent explicit and avoid turning all behavior into services. **Modules** organize cohesive domain concepts rather than merely technical categories. See [5][c5].

**Aggregates** define boundaries for invariants, access, and life-cycle consistency. Identify the rules that must hold at commit, select a root, and control changes through it. External objects retain references to roots; the source permits temporary use of an internal reference within one operation. Avoid expanding an Aggregate to every associated object. Decide which relationships can tolerate delayed reconciliation from domain evidence, not a universal preference for eventual consistency.

**Factories** create complete valid products or fail clearly; distinguish new creation from reconstitution of an existing identity. **Repositories** offer model-oriented access to existing objects, normally roots needing direct retrieval. They are not automatically one-per-table, and their individual operations should not silently determine the caller's transaction boundary. Understand query costs despite encapsulation. See [6][c6] and the integrated Cargo example in [7][c7].

### Refactor for deeper insight and a supple design

Look for missing concepts in repeated special cases, expert vocabulary, awkward constraints, and implicit processes. A **Specification** makes a criterion explicit and can support validation, selection, or construction without equating their mechanisms. A failed heuristic search does not prove no satisfying object exists. See [9][c9].

Use **Intention-Revealing Interfaces**, **Side-Effect-Free Functions**, and **Assertions** so clients can understand behavior and anticipate effects. Follow **Conceptual Contours**, reduce unnecessary dependencies with **Standalone Classes**, and use **Closure of Operations** where results naturally remain in the same conceptual type. These simplify important domain work; they are not reasons to add abstraction everywhere. See [10][c10].

Borrow **analysis patterns** for their concepts, alternatives, and implementation experience. Retain borrowed names only while their meaning stays recognizable. Apply **Strategy/Policy** or **Composite** as domain patterns when they express real domain distinctions, not merely useful technical structure. See [11][c11] and [12][c12].

A **breakthrough** can remove a mistaken organizing assumption. Evaluate it through formerly troublesome scenarios, expert understanding, and transition cost. Continue focused exploration and small refactorings; do not schedule breakthroughs or demand perfect cost predictions. Respect release timing and avoid technically elegant models that experts cannot use. See [8][c8] and [13][c13].

### Protect model integrity and choose sustainable integration

Define a **Bounded Context** wherever one model is to remain consistent. It is a scope of meaning and development practice, not a synonym for Module, deployment, or microservice. Build a **Context Map** of the actual models, contacts, translation, sharing, and team relationships before drawing a desired future.

Use **Continuous Integration** within each context to integrate both concepts and implementation. Choose relationships based on required integration, control, and real cooperation:

- **Shared Kernel:** jointly steward a limited shared model and its implementation.
- **Customer/Supplier Development Teams:** negotiate downstream needs in upstream planning, supported by shared acceptance tests.
- **Conformist:** follow a usable upstream model when influence is unavailable and integration is worthwhile.
- **Anticorruption Layer:** protect a distinct model by translating semantics and operations, not only transport formats.
- **Separate Ways:** avoid integration whose benefit does not justify its cost.
- **Open Host Service:** provide a coherent protocol for many consumers.
- **Published Language:** use a documented interchange model while retaining freedom to evolve internal models.

Test boundary translations and consider migration and deployment. Different meanings may require asymmetric or partial translations. Read [14][c14] before recommending a context relationship or transformation.

### Concentrate effort and organize the whole

**Core Domain:** identify what makes this application distinctive and valuable. Put skilled, committed developers and domain learning there. Necessary infrastructure, generic behavior, and widely used objects are not automatically core.

Use a **Domain Vision Statement** to explain value and a **Highlighted Core** to make actual core elements easy to find. Factor **Generic Subdomains** and **Cohesive Mechanisms** into supporting roles. A **Segregated Core** makes important concepts explicit in the code; an **Abstract Core** expresses fundamental interactions through meaningful abstractions. Avoid speculative reuse and abstraction based solely on dependency counts. See [15][c15].

Adopt **large-scale structure** only when it clarifies a model that Modules and distillation cannot adequately explain. Follow **Evolving Order**: refine the structure from experience. **System Metaphor** offers a useful analogy when one fits; **Responsibility Layers** reflect conceptual dependencies; **Knowledge Level** supports constrained user configuration; a **Pluggable Component Framework** requires mature shared abstractions and restricts future change. See [16][c16].

Combine context, distillation, and structure rather than treating them as alternatives. Begin with assessment, keep strategic decisions connected to application experience, communicate them, and preserve feedback. Judge the result by useful behavior and continued learning, including successors' ability to replace the original model with a better one. See [17][c17] and [Conclusion][conclusion].

## Chapter Index

| Chapter | Title | Focus |
|---|---|---|
| [01][c1] | Crunching Knowledge | Modeling through discovery and feedback |
| [02][c2] | Communication and the Use of Language | Ubiquitous Language, diagrams, documents |
| [03][c3] | Binding Model and Implementation | Model-Driven Design, Hands-On Modelers |
| [04][c4] | Isolating the Domain | Layered Architecture, Smart UI |
| [05][c5] | A Model Expressed in Software | Entities, values, services, associations, Modules |
| [06][c6] | The Life Cycle of a Domain Object | Aggregates, Factories, Repositories |
| [07][c7] | Using the Language: An Extended Example | Cargo design and integration iterations |
| [08][c8] | Breakthrough | Syndicated loans and a changed organizing assumption |
| [09][c9] | Making Implicit Concepts Explicit | Constraints, processes, Specifications |
| [10][c10] | Supple Design | Interfaces, assertions, functions, closure, Shares Math |
| [11][c11] | Applying Analysis Patterns | Accounts, accruals, Posting Rules |
| [12][c12] | Relating Design Patterns to the Model | Strategy, Composite, Flyweight distinction |
| [13][c13] | Refactoring Toward Deeper Insight | Exploration, prior art, timing |
| [14][c14] | Maintaining Model Integrity | Contexts, integration choices, transformations |
| [15][c15] | Distillation | Core Domain and supporting models |
| [16][c16] | Large-Scale Structure | Evolving Order, layers, knowledge, components |
| [17][c17] | Bringing the Strategy Together | Assessment, strategic decisions, feedback |
| [Conclusion][conclusion] | Epilogues and Looking Forward | Handoffs, outcomes, lasting learning |
| [Appendix][appendix] | The Use of Patterns in This Book | Pattern language and reading method |

## Topic Index

- **Abstract Core / Core Domain / Cohesive Mechanisms:** [15][c15]
- **Accounts / Accruals / Posting Rules:** [11][c11]
- **Aggregates / Factories / Repositories:** [6][c6]; application [7][c7]
- **Analysis patterns:** [11][c11]
- **Anticorruption Layer:** [14][c14]; Cargo example [7][c7]
- **Assertions / Closure / Conceptual Contours:** [10][c10]
- **Associations / Entities / identity:** [5][c5]
- **Bounded Context / Context Map / Continuous Integration:** [14][c14]
- **Breakthrough:** [8][c8]; continuing process [13][c13]
- **Cargo / Delivery History / Itinerary:** [7][c7], [9][c9]
- **Composite / Strategy / Policy / Flyweight:** [12][c12]
- **Conformist / Customer-Supplier / Shared Kernel / Separate Ways:** [14][c14]
- **Declarative style / Specification composition / subsumption:** [10][c10]
- **Diagrams / documents / explanatory models:** [2][c2]
- **Domain Vision Statement / Generic Subdomains / Highlighted Core:** [15][c15]
- **Evolving Order / Knowledge Level / System Metaphor:** [16][c16]
- **Hands-On Modelers / Model-Driven Design:** [3][c3]
- **Implicit concepts / constraints / processes / Specification:** [9][c9]
- **Intention-Revealing Interfaces / Side-Effect-Free Functions / Standalone Classes:** [10][c10]
- **Knowledge Crunching:** [1][c1]
- **Layered Architecture / Smart UI:** [4][c4]
- **Modules / Services / Value Objects:** [5][c5]
- **Open Host Service / Published Language:** [14][c14]
- **Pluggable Component Framework / Responsibility Layers:** [16][c16]
- **Refactoring / exploration teams:** [13][c13]; strategic targets [15][c15]
- **Segregated Core:** [15][c15]
- **Share Pie / syndicated loans:** [8][c8], [10][c10]
- **Strategic assessment / architecture teams:** [17][c17]
- **Ubiquitous Language:** [2][c2]; preservation [14][c14]

## Source Scope and Limits

Synthesized from the supplied `Domain Driven Design.pdf`, identified as Eric Evans's *Domain-Driven Design: Tackling Complexity in the Heart of Software*. This 324-page copy starts at Chapter 1 and lacks front matter. Page references in the guides are physical PDF pages, not printed edition pages. The original 17-chapter structure was verified against headings and PDF bookmarks; conclusion boundaries were checked against page text.

Technical extraction retained text and tables, but many diagrams became image placeholders and some code or formula formatting was damaged. Selected source figures were visually inspected; most source images were not read individually. Guides use labeled reconstructions and pseudocode where needed. They are study aids, not a verbatim transcription or complete image archive.

The scope is Evans's book. Later DDD developments such as CQRS, event sourcing, and microservice conventions are not supplied as original book rules. Historical products and project outcomes are not current recommendations. Project-specific technology, concurrency, operational behavior, and regulations require their own evidence when implementing these ideas.

[c1]: chapters/ch01-crunching-knowledge.md
[c2]: chapters/ch02-communication-and-language.md
[c3]: chapters/ch03-binding-model-and-implementation.md
[c4]: chapters/ch04-isolating-the-domain.md
[c5]: chapters/ch05-a-model-expressed-in-software.md
[c6]: chapters/ch06-life-cycle-of-domain-objects.md
[c7]: chapters/ch07-using-the-language.md
[c8]: chapters/ch08-breakthrough.md
[c9]: chapters/ch09-making-implicit-concepts-explicit.md
[c10]: chapters/ch10-supple-design.md
[c11]: chapters/ch11-applying-analysis-patterns.md
[c12]: chapters/ch12-design-patterns-and-the-model.md
[c13]: chapters/ch13-refactoring-toward-deeper-insight.md
[c14]: chapters/ch14-maintaining-model-integrity.md
[c15]: chapters/ch15-distillation.md
[c16]: chapters/ch16-large-scale-structure.md
[c17]: chapters/ch17-bringing-strategy-together.md
[conclusion]: chapters/conclusion.md
[appendix]: chapters/appendix-pattern-language.md
