---
name: philosophy-of-software-design
description: "Reference to A Philosophy of Software Design by John Ousterhout. Use when looking up the book's chapters or concepts, or applying its guidance on complexity, deep modules, information hiding, interface design, comments, naming, refactoring, and performance."
---

# A Philosophy of Software Design

John Ousterhout | First edition, 2018 (v1.01 printing listed) | 21 chapters | 188 PDF pages | Generated 2026-09-08

Concise chapter and concept reference derived from the supplied PDF. Page locators below refer to the PDF viewer's one-based pages, not the printed book's pagination.

## Use this reference

- For a **concept**, use the topic index and read the relevant chapter before giving detailed book-specific guidance.
- For **`chNN` or a chapter number**, open that chapter from the index. All 21 chapters retain their original numbering.
- For a **quick decision**, read [cheatsheet.md](cheatsheet.md). For a definition, use [glossary.md](glossary.md); for a reusable technique, use [patterns.md](patterns.md).
- With **no topic**, provide the core lens below and the available navigation. Load chapter files on demand.
- For **application to code**, identify a concrete symptom, the knowledge or dependency causing it, and the caller burden an alternative would remove. Cite the relevant chapter and state the tradeoff. A request for explanation or review remains an explanation or review unless implementation is requested.

Keep answers concise by default. Attribute Ousterhout's positions, distinguish the source's examples from newly constructed examples, and identify any adaptation beyond the book. Use the user's language for explanations while retaining recognizable names for the book's concepts.

## Core lens

### Diagnose experienced complexity

Look for **change amplification**, **cognitive load**, and **unknown unknowns**. Trace them to **dependencies** and **obscurity**. Evaluate the difficulty of real modification tasks from the reader's perspective rather than inferring quality from line count. Necessary dependencies should be simple and discoverable. See [Ch 2](chapters/ch02-nature-of-complexity.md).

### Make modules deep

Compare the useful complexity a module hides with the cost of its interface. The interface includes informal obligations such as sequencing, ownership, and error semantics. Hide details callers can ignore, and expose guarantees they need. A class is not deep merely because it is large. Avoid multiplying shallow interfaces through classitis. See [Ch 4](chapters/ch04-deep-modules.md).

### Organize around knowledge

Localize each important design decision. **Information leakage** can exist across implementations even when signatures look clean. **Temporal decomposition** is suspect when read/parse/write stages duplicate the same format knowledge. Defaults can hide uncommon choices from ordinary use. See [Ch 5](chapters/ch05-information-hiding.md).

### Use somewhat general-purpose interfaces

Implement current needs through a small set of useful operations that are independent of one caller's policy. Generality should simplify today's use; speculative capabilities and low-level primitives requiring much glue both miss the goal. Keep a mechanism's special-purpose policy with its particular use. See [Ch 6](chapters/ch06-general-purpose-modules.md) and [Ch 9](chapters/ch09-together-or-apart.md).

### Make each layer contribute

Inspect **pass-through methods** and **pass-through variables** for unnecessary awareness and duplicated interfaces. Useful dispatch and multiple implementations of a common interface are different cases. A context object may reduce parameter propagation, but introduces shared-state and dependency tradeoffs. See [Ch 7](chapters/ch07-layers-and-abstractions.md).

### Pull related complexity downward

Prefer a coherent implementation that solves a problem once over making every caller solve it. Absorb the responsibility when it belongs to the module, simplifies callers, and improves the interface. Expose configuration when callers have knowledge or control requirements the implementation lacks. See [Ch 8](chapters/ch08-pull-complexity-downwards.md).

### Simplify exceptional behavior through semantics

First ask whether an operation can naturally satisfy its contract for a wider range of cases. Otherwise consider local recovery or common handling at an appropriate boundary. **Defining errors out of existence** changes the contract; it does not mean concealing failure. Callers must receive information needed for correctness. See [Ch 10](chapters/ch10-define-errors-out-of-existence.md).

### Compare designs and document their abstractions

**Design it twice**: sketch structurally different options and compare caller effort, hidden knowledge, and implementation consequences. **Write the comments first** to test whether the contract is clear and complete before its implementation dominates thinking. Hard-to-describe contracts and hard-to-name entities can reveal design problems. See [Ch 11](chapters/ch11-design-it-twice.md), [Ch 14](chapters/ch14-choosing-names.md), and [Ch 15](chapters/ch15-comments-first.md).

### Preserve precision and reader understanding

Comments add either precise meanings absent from declarations or higher-level intuition about purpose and rationale. Keep interface contracts distinct from implementation explanations. Name unlike concepts differently; follow consistent meanings and make non-obvious control flow discoverable. See [Ch 13](chapters/ch13-comments-and-abstractions.md), [Ch 17](chapters/ch17-consistency.md), and [Ch 18](chapters/ch18-obvious-code.md).

### Invest within real constraints

**Strategic programming** makes continuous design improvements instead of accumulating tactical patches. Ousterhout's suggested 10-20% investment is a heuristic, not an empirical guarantee or required quota. For maintenance, compare the design you would choose with the new requirement known from the start, then respect scope, compatibility, and time constraints. See [Ch 3](chapters/ch03-working-code-isnt-enough.md) and [Ch 16](chapters/ch16-modifying-existing-code.md).

### Evaluate practices and performance by evidence

Judge a pattern or workflow by the complexity it removes and adds. Attribute the author's criticism of TDD; retain the user's requested development process. For performance, choose naturally efficient designs, measure bottlenecks, seek a structural fix, then simplify the measured common path while preserving behavior. Historical timing figures are examples, not current guarantees. See [Ch 19](chapters/ch19-software-trends.md) and [Ch 20](chapters/ch20-performance.md).

## Chapter index

| Chapter | Title | PDF pages | Lookup focus |
|---|---|---|---|
| [ch01](chapters/ch01-introduction.md) | Introduction | 13-17 | Continuous design, red flags, moderation |
| [ch02](chapters/ch02-nature-of-complexity.md) | The Nature of Complexity | 18-24 | Symptoms, causes, weighted exposure |
| [ch03](chapters/ch03-working-code-isnt-enough.md) | Working Code Isn't Enough | 25-30 | Strategic and tactical programming |
| [ch04](chapters/ch04-deep-modules.md) | Modules Should Be Deep | 31-39 | Interfaces, abstractions, classitis |
| [ch05](chapters/ch05-information-hiding.md) | Information Hiding (and Leakage) | 40-49 | Knowledge boundaries, defaults |
| [ch06](chapters/ch06-general-purpose-modules.md) | General-Purpose Modules are Deeper | 50-55 | General interfaces for current needs |
| [ch07](chapters/ch07-layers-and-abstractions.md) | Different Layer, Different Abstraction | 56-64 | Forwarding, decorators, context |
| [ch08](chapters/ch08-pull-complexity-downwards.md) | Pull Complexity Downwards | 65-68 | Caller burden, configuration |
| [ch09](chapters/ch09-together-or-apart.md) | Better Together Or Better Apart? | 69-83 | Split/join, undo history, policy |
| [ch10](chapters/ch10-define-errors-out-of-existence.md) | Define Errors Out Of Existence | 84-98 | Semantics, masking, aggregation |
| [ch11](chapters/ch11-design-it-twice.md) | Design it Twice | 99-101 | Alternative interfaces and implementations |
| [ch12](chapters/ch12-why-write-comments.md) | Why Write Comments? The Four Excuses | 102-106 | Documentation as abstraction |
| [ch13](chapters/ch13-comments-and-abstractions.md) | Comments Should Describe Things that Aren't Obvious from the Code | 107-128 | Precision, intuition, contracts |
| [ch14](chapters/ch14-choosing-names.md) | Choosing Names | 129-136 | Accurate images, predicate names |
| [ch15](chapters/ch15-comments-first.md) | Write The Comments First | 137-141 | Contracts as design artifacts |
| [ch16](chapters/ch16-modifying-existing-code.md) | Modifying Existing Code | 142-147 | Refactoring and documentation upkeep |
| [ch17](chapters/ch17-consistency.md) | Consistency | 148-151 | Conventions and invariants |
| [ch18](chapters/ch18-obvious-code.md) | Code Should be Obvious | 152-158 | Reader expectations, callbacks |
| [ch19](chapters/ch19-software-trends.md) | Software Trends | 159-165 | Inheritance, tests, agile, patterns |
| [ch20](chapters/ch20-performance.md) | Designing for Performance | 166-175 | Measurement and critical paths |
| [ch21](chapters/ch21-conclusion.md) | Conclusion | 176-177 | Design practice and investment |

## Topic index

Chapter identifiers resolve through the linked chapter index above.

- **Abstraction; false abstraction; interface versus implementation** -> ch04, ch06, ch07, ch13
- **Agile; abstraction as development increment** -> ch01, ch19
- **Change amplification; cognitive load; unknown unknowns** -> ch02
- **Classitis; deep/shallow modules** -> ch04, ch09
- **Comments; four excuses** -> ch12
- **Comments first; Hard to Describe** -> ch15
- **Comments: precision, intuition, interface contamination, repetition** -> ch13
- **Composition; inheritance** -> ch19
- **Configuration; defaults; Overexposure** -> ch05, ch08
- **Conjoined Methods; splitting/joining; Repetition** -> ch09
- **Consistency; conventions; invariants** -> ch14, ch17
- **Context object; pass-through variables** -> ch07
- **Critical path; microbenchmarks; RAMCloud Buffer** -> ch20
- **Cross-module documentation; designNotes** -> ch13, ch16
- **Decorators; dispatch; pass-through methods** -> ch07
- **Dependencies; obscurity** -> ch02, ch05
- **Design it twice** -> ch11
- **Design patterns; getters and setters** -> ch19
- **Error promotion; request abort; termination** -> ch10
- **Errors defined away; exception masking/aggregation; special cases** -> ch10
- **Event-driven programming; generic containers; Nonobvious Code** -> ch18
- **General-purpose modules; text-range API** -> ch06
- **HTTP parsing and parameters** -> ch05, ch09, ch10
- **Information hiding; leakage; temporal decomposition** -> ch05
- **Names; Vague Name; Hard to Pick Name; boolean predicates** -> ch14
- **Pull complexity downwards** -> ch08
- **Refactoring; comment maintenance; commit rationale** -> ch16, ch19
- **Special-General Mixture; mechanism versus policy** -> ch06, ch09
- **Strategic programming; tactical programming; tactical tornado** -> ch03, ch16
- **TDD; unit tests; regression tests** -> ch19
- **Undo/redo; history actions; fences** -> ch09

## Supporting files

- [glossary.md](glossary.md): alphabetical definitions with chapter numbers.
- [patterns.md](patterns.md): techniques, application conditions, and tradeoffs.
- [cheatsheet.md](cheatsheet.md): compact decision rules and red-flag lookup.

## Scope and source limits

Based on `A philosophy of software design.pdf`, John Ousterhout, first edition (2018); the supplied printing history lists November 2018 v1.01. The book's closing design-principle and red-flag lists were used to check topic coverage. Later-edition additions are outside this reference.

Notes are synthesized, with compact adapted or explicitly new examples; the full source text and figures are not bundled. Extraction used native PDF text after layout extraction failed, with selected visual checks of the complexity formula, module-depth illustration, context diagram, and performance figures. This is a concise reference, not a full transcription of code or diagrams.

Treat red flags as prompts for investigation and preserve the qualifications in each chapter. Apply the book within the user's requested scope and project conventions. Verify current APIs, platform behavior, and performance facts separately when implementation depends on them.
