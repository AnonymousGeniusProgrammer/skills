---
name: clean-code
description: "Apply Clean Code by Robert C. Martin and contributors during code-quality reviews and behavior-preserving refactoring: naming, functions, comments, responsibilities, errors, boundaries, tests, and concurrency. Also use when studying the book or looking up a chapter, worked example, or smell/heuristic identifier."
---

# Clean Code: A Handbook of Agile Software Craftsmanship

**Source**: Robert C. Martin and contributors; first printing July 2008, copyright 2009. **Coverage**: 17 chapters and 3 appendices from a 462-page PDF. **Generated**: 2026-09-08. **Language**: English.

## Apply the Skill

Use the book as a specific school of design judgment. Preserve the user's requested scope, required behavior, public contracts, and project conventions. Distinguish source guidance from a proposed adaptation to another language or architecture.

- **Code review**: inspect the relevant code and callers, load the matching chapter, and tie each finding to an observable defect or concrete maintenance cost. Explain the consequence and the smallest justified improvement; a heuristic ID alone is not a finding.
- **Refactoring**: establish the behavior and relevant checks, identify the change pressure, and make small transformations. Report intentional behavior or compatibility changes separately. Verify meaningful outcomes using the project's workflow.
- **Study or explanation**: read the requested chapter or topic reference before making detailed claims. Use its worked example, trade-offs, and source page range.
- **Heuristic lookup**: read [chapter 17](chapters/ch17-smells-and-heuristics.md) for C/E/F/G/J/N/T identifiers. Use [Appendix C](chapters/appendix-c-cross-references.md) for example locations.
- **Quick decision**: read [cheatsheet.md](cheatsheet.md). For a reusable technique, use [patterns.md](patterns.md); for a definition, use [glossary.md](glossary.md).

Load only the references relevant to the current question. The chapter index and topic index below are routing aids, not instructions to read the entire skill.

## Core Frameworks and Mental Models

### Improve a Tested Draft

Use the **Boy Scout Rule** for a nearby, useful improvement when touching code. Refine incrementally: name the problem, protect the relevant behavior, make one coherent change, and verify it. The Args and JUnit studies demonstrate that a polished design emerges through many small experiments, including reversals.

Use **Simple Design** to resolve competing recommendations, in the book's order:

1. Runs all the tests.
2. Contains no duplication.
3. Expresses intent.
4. Minimizes classes and methods.

Small functions and classes should reveal meaningful concepts. The final rule counterbalances excessive fragmentation and unnecessary interfaces. Tests support changing the design; their presence does not prove every behavior is correct.

### Make Meaning and Effects Visible

Choose **intention-revealing names** after establishing what the entity actually means. Use one word for one concept without forcing different semantics under the same word. Let scope provide context and make names more explicit as scope grows.

Apply **Do One Thing** together with **One Level of Abstraction per Function**: a function's immediate steps should explain its purpose at the next level down. Extract a concept when its name adds meaning; merely restating a line behind another call can increase navigation.

Use the **Stepdown Rule** and **Newspaper Metaphor** to present the main operation before its details where the language and project permit. Keep related concepts close and use the team formatter.

Make mutation, initialization, units, and ordering apparent. **Command Query Separation** discourages a question-shaped operation with hidden effects. Preserve a combined atomic operation when separating the calls would introduce a race. Reduce argument interpretation through meaningful ownership and grouping, not by hiding independent inputs in shared mutable fields.

### Choose Boundaries by Their Meaning and Change Cost

Use **Data/Object Anti-Symmetry** when selecting a representation: procedures over explicit data make new operations easier; polymorphic objects make new types easier. Select for the actual variation. An object should hide representation behind meaningful behavior; a DTO may deliberately expose data.

Apply the **Law of Demeter** by asking what job a client needs rather than navigating an object's internals. Inspect the dependency, not the number of dots. Plain data and fluent operations need their own interpretation.

Use **SRP** to identify one reason for a class to change, and **cohesion** to group related state and behavior. Use **OCP** and **DIP** to isolate demonstrated variation and volatile concrete details. A need for current change is stronger evidence than a speculative future subtype.

Separate construction from use through **Separation of Main**, **Dependency Injection**, and factories when runtime creation timing matters. Keep domain behavior independently testable and verify actual assembly separately. Proxies and aspects in the source illustrate separation of cross-cutting concerns; they do not mandate a framework.

### Make Failures and Tests Part of the Contract

Design error boundaries around what callers can do. Translate external failures into useful categories with cause and context. Use the **Special Case Pattern** or an explicit absence result for valid domain alternatives; preserve the distinction between no data and failure to obtain data.

Use **learning tests** to record the external behaviors the application depends on. Use adapters to contain outside details and fakes for isolated consumer development. Test the actual adapter as well.

Keep tests readable through **Build-Operate-Check** and small domain-specific helpers. Prefer one concept per test over exactly one assertion. **F.I.R.S.T.** means Fast, Independent, Repeatable, Self-Validating, and Timely. When practicing the book's TDD method, use its three laws in chapter 9; retain the actual project's development workflow for other work.

Keep comments that explain intent, constraints, consequences, or non-obvious rationale. Replace comments that merely decode an expression with better code where possible. Verify comments against the implementation rather than deleting useful explanations indiscriminately.

### Treat Concurrency as a Protocol

Separate scheduling from domain work, minimize shared state, and identify the complete invariant. Individually synchronized methods do not make a sequence of calls atomic. Prefer a coherent operation owned by the shared component when that removes repeated client locking.

Identify the producer-consumer, readers-writers, or resource-acquisition model. Verify resource exhaustion, errors, and shutdown. Investigate intermittent failures; a passing rerun does not disprove the defect. Use the appendix's interleaving and throughput models only with their stated assumptions.

## Chapter Index

Page ranges are **printed book pages**. Add 31 for the corresponding one-based PDF page in this source.

| Reference | Title | Main uses | Pages |
|---|---|---|---|
| [ch01](chapters/ch01-clean-code.md) | Clean Code | Boy Scout Rule, authorship, maintenance cost | 1–16 |
| [ch02](chapters/ch02-meaningful-names.md) | Meaningful Names | Vocabulary, intent, context, searchability | 17–30 |
| [ch03](chapters/ch03-functions.md) | Functions | One thing, abstraction levels, arguments, effects | 31–52 |
| [ch04](chapters/ch04-comments.md) | Comments | Rationale, contracts, drift, redundant prose | 53–74 |
| [ch05](chapters/ch05-formatting.md) | Formatting | Newspaper metaphor, proximity, team conventions | 75–92 |
| [ch06](chapters/ch06-objects-and-data-structures.md) | Objects and Data Structures | Data/object trade-off, Demeter, DTOs | 93–102 |
| [ch07](chapters/ch07-error-handling.md) | Error Handling | Caller-oriented errors, special cases, null | 103–112 |
| [ch08](chapters/ch08-boundaries.md) | Boundaries | Adapters, learning tests, provisional interfaces | 113–120 |
| [ch09](chapters/ch09-unit-tests.md) | Unit Tests | TDD, Build-Operate-Check, F.I.R.S.T. | 121–134 |
| [ch10](chapters/ch10-classes.md) | Classes | SRP, cohesion, OCP, DIP | 135–152 |
| [ch11](chapters/ch11-systems.md) | Systems | Construction/use, DI, cross-cutting concerns | 153–170 |
| [ch12](chapters/ch12-emergence.md) | Emergence | Ordered Simple Design, duplication, restraint | 171–176 |
| [ch13](chapters/ch13-concurrency.md) | Concurrency | Shared state, atomicity, execution models, shutdown | 177–192 |
| [ch14](chapters/ch14-successive-refinement.md) | Successive Refinement | Args, marshalers, incremental migration | 193–250 |
| [ch15](chapters/ch15-junit-internals.md) | JUnit Internals | String compaction, bounds, temporal coupling | 251–266 |
| [ch16](chapters/ch16-refactoring-serialdate.md) | Refactoring SerialDate | Legacy testing, domain types, compatibility | 267–284 |
| [ch17](chapters/ch17-smells-and-heuristics.md) | Smells and Heuristics | All 66 C/E/F/G/J/N/T identifiers | 285–316 |
| [Appendix A](chapters/appendix-a-concurrency-ii.md) | Concurrency II | Interleavings, CAS, executors, throughput, deadlock | 317–348 |
| [Appendix B](chapters/appendix-b-serialdate.md) | org.jfree.date.SerialDate | Before/after listing index and study routes | 349–408 |
| [Appendix C](chapters/appendix-c-cross-references.md) | Cross References of Heuristics | Verified lookup guidance | 409–410 |

## Topic Index

- **Abstraction / arguments / atomic query-command** → ch03, ch06, ch13.
- **Adapters / API boundaries / absent values** → ch07, ch08.
- **Boy Scout Rule / behavior preservation** → ch01, ch14, ch15.
- **Classes / cohesion / coupling** → ch10, ch17.
- **Comments / contracts** → ch04, ch07.
- **Concurrency / CAS / critical sections** → ch13, Appendix A.
- **Construction / cross-cutting concerns** → ch11.
- **Dates / DayDate / domain enums** → ch16, Appendix B.
- **Deadlock / dining philosophers** → ch13, Appendix A.
- **Demeter / DTO / data versus objects** → ch06.
- **DI / DIP / factories** → ch10, ch11.
- **Duplication / DRY / emergent design** → ch12, ch14, ch17.
- **Errors / exceptions** → ch07, ch14.
- **F.I.R.S.T. / fakes / feedback** → ch08, ch09.
- **Formatting / function size** → ch03, ch05.
- **Heuristic IDs** → ch17, Appendix C.
- **JUnit / lengths versus indexes** → ch15.
- **Learning tests / legacy tests** → ch08, ch16.
- **Names / newspaper metaphor** → ch02, ch05.
- **OCP / One Switch / output arguments** → ch03, ch10, ch17.
- **Polymorphism / producer-consumer** → ch06, ch13.
- **Refactoring / responsibility** → ch10, ch14–ch16.
- **Serialization / shutdown / side effects** → ch16, ch13, ch03.
- **Simple Design / Special Case / Stepdown** → ch12, ch07, ch03.
- **TDD / Template Method / temporal coupling** → ch09, ch12, ch15.

## Source and Limits

The files synthesize the supplied book into decisions, procedures, and compact reconstructed examples. They do not contain the book PDF or a full text extraction. Refer to [source.json](source.json) for local source identity, provenance, and extraction limitations.

Detailed claims should come from the linked chapter. Reconstructed snippets are teaching fragments unless explicitly stated otherwise; check types, APIs, concurrency guarantees, and actual behavior before adapting them. The book's Java and framework examples reflect its publication era.

Treat numeric size targets, wildcard imports, serialization choices, and uses of `final` as source preferences to assess in context. Full diagrams were not systematically interpreted; four structure-sensitive pages were visually checked. Appendix C in this PDF contains stale references, so use the corrected guidance provided here. Front matter, epilogue, and the original back-of-book index are not separate technique files.
