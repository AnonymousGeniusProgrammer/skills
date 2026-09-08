# Chapter 5: Formatting

Source: printed pp. 75–92; PDF pp. 106–123. Robert C. Martin.

## Core Idea

Formatting communicates relationships before a reader understands every statement. Use a consistent team style, visual grouping, and proximity to reveal the organization of code.

## Frameworks Introduced

### The Newspaper Metaphor

Use this when organizing a source file. Let the name identify the subject, place the high-level story near the beginning, and reveal supporting detail as the reader proceeds.

1. Identify the public or principal operation.
2. Arrange its immediate supporting operations so their relationship is easy to follow.
3. Keep implementation details below the narrative where language rules allow.
4. Group related operations that share a purpose even when they do not call each other.

Why it works: readers can decide how deeply to investigate without first understanding the entire file. Failure mode: rigid ordering can separate conceptually related overloads or conflict with local language conventions.

### Vertical Openness, Density, and Distance

Use whitespace to distinguish concepts and proximity to show relationships.

- **Openness**: blank lines separate different concepts or phases.
- **Density**: closely related statements remain visually together.
- **Distance**: declarations and supporting operations are located near the code that needs them.
- **Conceptual affinity**: related variants, such as assertion overloads, belong together even without a strong call relationship.

Place local variables near their use. Put loop control variables in the loop where possible. Follow the project's predictable convention for fields; the book's Java convention puts class state near the top. Keep callers and small supporting functions close enough that understanding a single operation does not require prolonged scrolling.

### Team Rules

Use the team's agreed formatting conventions rather than imposing a personal preference. Encode those choices in the existing formatter where available.

1. Inspect how neighboring files express the same constructs.
2. Use the project's formatting configuration and normal command.
3. Keep the changed code visually consistent.
4. Treat proposals to change the convention as an explicit team decision.

The chapter recounts Martin following a team format that differed from his own preference. Consistency carries meaning across files; an individual style switch introduces another interpretation burden.

## Reference Table

| Visual issue | Apply | Avoid |
|---|---|---|
| Different concepts blend together | Blank lines at meaningful boundaries | One blank line after every statement |
| Related fields are separated by redundant prose | Remove redundant comments and keep declarations together | Deleting a needed invariant explanation |
| A helper is far from its only caller | Bring the dependency close where practical | Reordering an entire repository for one local change |
| Indentation understates control structure | Indent the nested block consistently | Compressing several nested actions onto one line |
| Operators are hard to parse | Use spacing that reveals grouping and precedence | Relying on decorative alignment instead of names |
| An empty loop body is easy to miss | Make the empty body explicit in the team style | An inconspicuous trailing semicolon |
| Lines require extensive horizontal scanning | Wrap at meaningful boundaries | Shrinking text or cramming unrelated expressions together |

## Worked Example

The chapter uses two versions of a reporter configuration class. Redundant comments separate two related fields, increasing the space and eye movement needed to understand a small structure. Removing those comments and keeping the property-adding method nearby makes the class's organization immediately visible.

Reconstructed illustration:

```java
class ReporterConfiguration {
    private String listenerClassName;
    private final List<Property> properties = new ArrayList<>();

    void addProperty(Property property) {
        properties.add(property);
    }
}
```

The useful change is the relationship among the declarations and behavior. This is an incomplete illustrative class, not a proposed production configuration API. A comment that documents an ordering requirement for `properties` would still add information and should be retained if such a requirement exists.

For a longer file, perform the same review at larger scale: identify the main concept, inspect which helpers it needs, and remove unnecessary distance between those pieces. Verify formatting mechanically; if statements or declarations move, also verify that the language's execution and initialization order are preserved.

## Thresholds and Defaults

The chapter surveys Java file and line lengths. It treats files around 200 lines, often below 500, as a desirable possibility rather than a strict cap. It favors short lines, allowing roughly 100–120 characters, while warning that a monitor's width is not a useful readability standard.

These figures are the author's contextual guidance. Use the project's formatter as the operative rule, and investigate responsibilities or expression structure when size makes reading difficult. Do not split a cohesive unit merely to meet a historical line target.

## Key Concepts

- **Vertical openness**: visual separation between concepts.
- **Vertical density**: visual grouping of closely related statements.
- **Vertical distance**: how far a reader must travel between related definitions and uses.
- **Conceptual affinity**: shared purpose that justifies proximity.
- **Horizontal openness**: spacing that exposes expression grouping.
- **Indentation**: a visual representation of scope and hierarchy.
- **Dummy scope**: an intentionally empty statement body that needs to remain visible.

## Mental Models

- Think of a source file as a layered explanation whose first lines help decide whether to read further.
- Treat whitespace as a map of conceptual boundaries.
- Treat the team format as a stable reading convention rather than an aesthetic contest.

## Anti-patterns

- **Horizontal alignment as structure**: maintaining columns of spaces can distract from names and create unnecessary changes.
- **Unrelated style churn**: large formatting diffs obscure the actual behavior change.
- **Compactness at the expense of hierarchy**: compressing control flow destroys useful scope cues.
- **Distance unrelated to meaning**: a function's closest dependencies are scattered among unrelated members.

## Key Takeaways

1. Use proximity and whitespace to communicate relationships.
2. Present the main story before its details where practical.
3. Follow the team's consistent formatting conventions.
4. Treat size measurements as diagnostic clues, not evidence of correctness.

## Connects To

- [Chapter 3](ch03-functions.md): the Stepdown Rule.
- [Chapter 10](ch10-classes.md): organization around responsibility.
- [Chapter 17](ch17-smells-and-heuristics.md): G10 and G24.
