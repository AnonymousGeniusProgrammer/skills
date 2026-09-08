# Appendix C: Cross References of Heuristics

Source: printed pp. 409–410; PDF pp. 440–441. Navigation appendix for [chapter 17](ch17-smells-and-heuristics.md).

## Core Idea

Use heuristic identifiers to connect a general rule to the case that motivated it. This appendix introduces no additional design framework; its value is navigation between principles and examples.

## Source Irregularity

Visual inspection of this PDF shows that its printed cross-reference table contains stale chapter/page pairs and at least one malformed entry (`??-44` under G23). These are present on the PDF pages, not merely artifacts of text extraction.

Use the verified chapter ranges in this skill and the actual bracketed annotations in the source text. Do not treat the printed table as an authoritative page index.

## Verified Examples

The locations below were checked against the actual annotated chapter text. Page numbers are printed pages; PDF page = printed page + 31.

| Heuristic | Verified location | What to inspect |
|---|---|---|
| G23: polymorphism | p. 39, chapter 3 | Discussion following the employee factory |
| G23: polymorphism | p. 231, chapter 14 | Argument-marshaler refinement |
| G23: polymorphism | p. 282, chapter 16 | Moving date-interval cases into their concept |
| G34: one abstraction level | p. 36, chapter 3 | Meaningful decomposition versus restating implementation |
| G34: one abstraction level | p. 99, chapter 6 | Mixing path details and higher-level context use |
| G31: temporal coupling | pp. 259–260, chapter 15 | Prefix analysis must precede suffix analysis |
| G33: boundary conditions | pp. 261–262, chapter 15 | Converting suffix arithmetic to an actual length |
| G22: logical dependencies | p. 282, chapter 16 | The weekday associated with ordinal zero |

## Topic-to-Example Route

| Question | Load |
|---|---|
| How can names expose the domain? | [Chapter 2](ch02-meaningful-names.md) |
| What makes a function do one thing? | [Chapter 3](ch03-functions.md) |
| When does an object leak its internal graph? | [Chapter 6](ch06-objects-and-data-structures.md) |
| How do I refine a growing type-based parser? | [Chapter 14](ch14-successive-refinement.md) |
| How do I expose ordering and boundary arithmetic? | [Chapter 15](ch15-junit-internals.md) |
| How do coverage and failures guide a legacy review? | [Chapter 16](ch16-refactoring-serialdate.md) |
| What does a specific C/E/F/G/J/N/T identifier mean? | [Chapter 17](ch17-smells-and-heuristics.md) |

## Worked Lookup

For a review about `G33`:

1. Read its definition in chapter 17: use a clear representation and localize boundary arithmetic.
2. Read chapter 15's suffix-length transformation.
3. Identify whether the reviewed value is a count, index, or exclusive endpoint.
4. Propose a representation that removes ambiguity and check zero/overlap cases.
5. Explain the concrete benefit in the review, with the ID available as supporting context.

The lookup should produce reasoning about the code, not just a label.

## Key Takeaways

1. Preserve identifiers for reliable conceptual lookup.
2. Verify page locations against the actual chapter text in this edition.
3. Read the worked case before generalizing its heuristic.
