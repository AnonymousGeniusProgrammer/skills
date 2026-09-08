---
name: refactoring
description: "Apply Martin Fowler's Refactoring (second edition) to behavior-preserving code changes, code-smell diagnosis, and selection of refactoring mechanics. Also use for chapter or concept lookup in the book."
---

# Refactoring: Improving the Design of Existing Code

Martin Fowler, with contributions by Kent Beck | Second edition, copyright 2019 | 445 PDF pages | 12 chapters | Generated 2026-09-08

## Use this skill

- For **implementation**, identify the change obstacle and behavior to preserve; use the core workflow below and read the relevant catalog chapter before choosing detailed mechanics.
- For **review**, explain the observed smell, its concrete maintenance cost, a candidate move, and its tradeoff. Preserve the user's requested review scope.
- For **a concept or named refactoring**, use the indexes and read the linked chapter before giving detailed book-specific guidance.
- For **`chNN`**, load that chapter. With no topic, provide the core approach and navigation; chapter references load on demand.
- For a quick decision, read [cheatsheet.md](cheatsheet.md). Use [glossary.md](glossary.md) for terms and [patterns.md](patterns.md) for sequences of moves.

Use the user's language while retaining recognizable catalog names. Attribute the book's judgments and distinguish reconstructed source examples from new application advice. The JavaScript examples illustrate transformations; they do not require using JavaScript or the book's libraries in a project.

## Core workflow

1. **Identify the concrete obstacle.** Find the next change or comprehension task that current structure makes expensive. Use smells as evidence to investigate, not as automatic violations.
2. **Establish the behavior boundary.** Inspect affected callers, outputs, state changes, and applicable compatibility constraints. Include identity, ordering, errors, and evaluation timing when they are observable in this system.
3. **Establish feedback.** Run the relevant existing checks and fill material gaps before risky manual moves. Prefer self-checking tests that protect behavior. Static checks and reliable semantic refactoring tools can supplement them.
4. **Choose a small move.** Read its motivation, mechanics, and cautions. Simplify obstructing dependencies first; preserve the working interface through forwarding when callers must migrate gradually.
5. **Change, check, and continue.** Keep the feedback window small. If an edit breaks behavior, inspect or undo that edit and reduce the step size, preserving unrelated work.
6. **Stop at a useful working state.** Finish the requested change once the relevant obstacle is removed. Report the resulting structure, the behavior checks, and any unresolved limits.

### The Two Hats

Separate restructuring from changing functionality. A refactoring step preserves the chosen behavior; a feature or bug fix intentionally changes it. Switch hats explicitly even when the work is interleaved. Keep existing assertions meaningful instead of changing expected results to make a structural edit pass. The book does not require separate commits for each hat. See [Ch 2](chapters/ch02-principles.md).

### Refactor to sustain useful change

**Preparatory refactoring** makes the imminent change easier. **Comprehension refactoring** stores discovered intent in names and structure. **Litter-Pickup Refactoring** improves nearby obstacles within the current task. The **Design Stamina Hypothesis** explains the economic motivation; it is an experience-based hypothesis, not a quantified law. Stable code that need not be understood or modified can stay behind its interface. See [Ch 1](chapters/ch01-first-example.md) and [Ch 2](chapters/ch02-principles.md).

### Prefer intent over size metrics

Extract a function when its name explains a useful intention; inline one when it adds no useful explanation or blocks better boundaries. The authors supply smell heuristics rather than universal size limits. The Rule of Three is a prompt to examine recurring duplication, not a mandatory abstraction threshold. See [Ch 3](chapters/ch03-code-smells.md) and [Ch 6](chapters/ch06-first-refactorings.md).

### Put related change in one place

**Divergent Change:** one module changes for unrelated reasons; separate its contexts. **Shotgun Surgery:** one change requires scattered edits; gather the relevant knowledge. Use the pattern of changes to judge the boundary. Data proximity helps diagnose Feature Envy, but intentionally separate strategies can still be appropriate. See [Ch 3](chapters/ch03-code-smells.md) and [Ch 8](chapters/ch08-moving-features.md).

### Control mutation and identity

Encapsulation must protect relevant updates, including nested data and collection membership. Replace redundant mutable derivations with queries after checking equivalence. Use a transform for an effectively immutable prepared result; use live queries when inputs can change. Choose value semantics for independent immutable values and reference semantics for shared entities whose updates must be visible. See [Ch 7](chapters/ch07-encapsulation.md) and [Ch 9](chapters/ch09-organizing-data.md).

### Make variation explicit where it pays

Use guard clauses for exceptional paths, named predicates for dense decisions, polymorphism for related recurring variation, and Special Case for common exceptional-value behavior. A single switch is not automatically a problem. Assert programmer invariants; validate expected external input through ordinary program behavior. See [Ch 10](chapters/ch10-conditional-logic.md).

### Balance convenience against dependencies

Remove a parameter when the callee can obtain it through an appropriate existing dependency. Introduce a parameter when an internal query creates unwanted coupling. Preserve Whole Object only when the callee should depend on that object. Name distinct caller choices rather than hiding them in literal flags. Prefer plain functions until a command's additional lifecycle or state is useful. See [Ch 11](chapters/ch11-refactoring-apis.md).

### Let structure evolve

**Yagni** avoids speculative machinery while retaining design thought about costly future changes. Inheritance can be a simple useful starting point; switch to delegation when subtype semantics, changing roles, or independent variation make it awkward. Preserve published interfaces while clients migrate. Measure performance instead of inferring it from code shape, honoring explicit resource budgets. See [Ch 2](chapters/ch02-principles.md) and [Ch 12](chapters/ch12-inheritance.md).

## Chapter index

Page ranges are inclusive. PDF pages are the viewer's one-based pages; printed page 1 starts at PDF page 23.

| Chapter | Original title | Printed pages | PDF pages | Focus |
|---|---|---|---|---|
| [ch01](chapters/ch01-first-example.md) | Refactoring: A First Example | 1-44 | 23-66 | Statements, phases, calculators |
| [ch02](chapters/ch02-principles.md) | Principles in Refactoring | 45-70 | 67-92 | Two Hats, economics, Yagni, migration |
| [ch03](chapters/ch03-code-smells.md) | Bad Smells in Code | 71-84 | 93-106 | All 24 smells and their distinctions |
| [ch04](chapters/ch04-building-tests.md) | Building Tests | 85-100 | 107-122 | Feedback, fixtures, boundary tests |
| [ch05](chapters/ch05-catalog.md) | Introducing the Catalog | 101-104 | 123-126 | Names, motivation, mechanics |
| [ch06](chapters/ch06-first-refactorings.md) | A First Set of Refactorings | 105-160 | 127-182 | 11 foundational transformations |
| [ch07](chapters/ch07-encapsulation.md) | Encapsulation | 161-196 | 183-218 | 9 boundary transformations |
| [ch08](chapters/ch08-moving-features.md) | Moving Features | 197-238 | 219-260 | 9 moves and loop transformations |
| [ch09](chapters/ch09-organizing-data.md) | Organizing Data | 239-258 | 261-280 | 5 data and identity transformations |
| [ch10](chapters/ch10-conditional-logic.md) | Simplifying Conditional Logic | 259-304 | 281-326 | 6 conditional transformations |
| [ch11](chapters/ch11-refactoring-apis.md) | Refactoring APIs | 305-348 | 327-370 | 10 interface transformations |
| [ch12](chapters/ch12-inheritance.md) | Dealing with Inheritance | 349-404 | 371-426 | 11 hierarchy transformations |

## Topic index

| Topic | Read |
|---|---|
| Architecture, Branch By Abstraction, CI, database migration | ch02 |
| Assertions, invariants, special cases, null objects | ch10 |
| Behavior preservation, Two Hats, refactoring versus rewrite | ch02, ch04 |
| Class versus transform, intermediate data, Split Phase | ch01, ch06 |
| Collection ownership, aliases, nested records | ch07 |
| Commands, query/modifier separation, flag arguments | ch11 |
| Data Clumps, Divergent Change, Feature Envy, Shotgun Surgery | ch03 |
| Derived state, equality, identity, snapshots, value objects | ch09 |
| Encapsulation, Message Chains, Middle Man | ch03, ch07 |
| Extraction, inlining, naming, parameter objects | ch06 |
| Factories, published interfaces, parameter dependencies | ch02, ch11 |
| Guard clauses, repeated dispatch, overlapping conditions | ch10 |
| Inheritance, Refused Bequest, delegation, type codes | ch03, ch12 |
| Legacy code, preparatory and comprehension refactoring | ch02 |
| Loops, pipelines, statement ordering, moving responsibilities | ch08 |
| Performance, Rule of Three, Yagni | ch02 |
| Test boundaries, fixture isolation, coverage, TDD | ch04 |

All 24 smell names are directly searchable in ch03. The catalog below indexes every named entry in this supplied PDF. Read only the chapter needed for the current operation.

## Refactoring catalog

| Refactoring | Chapter | Printed p. |
|---|---|---|
| Change Function Declaration | [ch06] | 124 |
| Change Reference to Value | [ch09] | 252 |
| Change Value to Reference | [ch09] | 256 |
| Collapse Hierarchy | [ch12] | 380 |
| Combine Functions into Class | [ch06] | 144 |
| Combine Functions into Transform | [ch06] | 149 |
| Consolidate Conditional Expression | [ch10] | 263 |
| Decompose Conditional | [ch10] | 260 |
| Encapsulate Collection | [ch07] | 170 |
| Encapsulate Record | [ch07] | 162 |
| Encapsulate Variable | [ch06] | 132 |
| Extract Class | [ch07] | 182 |
| Extract Function | [ch06] | 106 |
| Extract Superclass | [ch12] | 375 |
| Extract Variable | [ch06] | 119 |
| Hide Delegate | [ch07] | 189 |
| Inline Class | [ch07] | 186 |
| Inline Function | [ch06] | 115 |
| Inline Variable | [ch06] | 123 |
| Introduce Assertion | [ch10] | 302 |
| Introduce Parameter Object | [ch06] | 140 |
| Introduce Special Case | [ch10] | 289 |
| Move Field | [ch08] | 207 |
| Move Function | [ch08] | 198 |
| Move Statements into Function | [ch08] | 213 |
| Move Statements to Callers | [ch08] | 217 |
| Parameterize Function | [ch11] | 310 |
| Preserve Whole Object | [ch11] | 319 |
| Pull Up Constructor Body | [ch12] | 355 |
| Pull Up Field | [ch12] | 353 |
| Pull Up Method | [ch12] | 350 |
| Push Down Field | [ch12] | 361 |
| Push Down Method | [ch12] | 359 |
| Remove Dead Code | [ch08] | 237 |
| Remove Flag Argument | [ch11] | 314 |
| Remove Middle Man | [ch07] | 192 |
| Remove Setting Method | [ch11] | 331 |
| Remove Subclass | [ch12] | 369 |
| Rename Field | [ch09] | 244 |
| Rename Variable | [ch06] | 137 |
| Replace Command with Function | [ch11] | 344 |
| Replace Conditional with Polymorphism | [ch10] | 272 |
| Replace Constructor with Factory Function | [ch11] | 334 |
| Replace Derived Variable with Query | [ch09] | 248 |
| Replace Function with Command | [ch11] | 337 |
| Replace Inline Code with Function Call | [ch08] | 222 |
| Replace Loop with Pipeline | [ch08] | 231 |
| Replace Nested Conditional with Guard Clauses | [ch10] | 266 |
| Replace Parameter with Query | [ch11] | 324 |
| Replace Primitive with Object | [ch07] | 174 |
| Replace Query with Parameter | [ch11] | 327 |
| Replace Subclass with Delegate | [ch12] | 381 |
| Replace Superclass with Delegate | [ch12] | 399 |
| Replace Temp with Query | [ch07] | 178 |
| Replace Type Code with Subclasses | [ch12] | 362 |
| Separate Query from Modifier | [ch11] | 306 |
| Slide Statements | [ch08] | 223 |
| Split Loop | [ch08] | 227 |
| Split Phase | [ch06] | 154 |
| Split Variable | [ch09] | 240 |
| Substitute Algorithm | [ch07] | 195 |

[ch06]: chapters/ch06-first-refactorings.md
[ch07]: chapters/ch07-encapsulation.md
[ch08]: chapters/ch08-moving-features.md
[ch09]: chapters/ch09-organizing-data.md
[ch10]: chapters/ch10-conditional-logic.md
[ch11]: chapters/ch11-refactoring-apis.md
[ch12]: chapters/ch12-inheritance.md

## Supporting files

- [glossary.md](glossary.md): definitions and common aliases.
- [patterns.md](patterns.md): reusable sequences, motivations, and tradeoffs.
- [cheatsheet.md](cheatsheet.md): compact decisions and stopping rules.

## Source and limits

Derived from the supplied `Refactoring.pdf`, *Refactoring: Improving the Design of Existing Code*, second edition, Martin Fowler with contributions by Kent Beck; ISBN 978-0-13-475759-9. This reference synthesizes the 12 chapters and 61 catalog entries in that PDF, retaining its numbering and terminology. It does not claim coverage of subsequent web-edition additions.

Native text extraction used pypdf after Docling's layout pipeline failed because a required compiler was unavailable. Title, contents, catalog mechanics, and selected examples were checked against page-specific extraction; one code page was also inspected visually. Figures and code-change colors were not systematically interpreted, and the extractor supplied no reliable unread-image count. Examples are compact reconstructions or explicitly labeled illustrations, not complete source programs. Additional language and contract cautions apply the book's behavior-preservation principle and should not be mistaken for quotations.

The book is a method and catalog reference, not current library documentation or a complete legacy-code, database-migration, testing, or deployment manual. Adapt implementation to the user's language, repository, and requested scope.
