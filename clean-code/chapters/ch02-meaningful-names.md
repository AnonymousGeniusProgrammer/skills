# Chapter 2: Meaningful Names

Source: printed pp. 17–30; PDF pp. 48–61. Tim Ottinger.

## Core Idea

Choose names that communicate purpose, distinguish real concepts, and work at the point of use. A reader should not need a private translation table, implementation inspection, or knowledge of the original author's jokes.

## Frameworks Introduced

### Use Intention-Revealing Names

Use this when a variable, method, or class requires an explanation before its role is clear.

1. Establish what the entity represents, why it exists, and how it is used.
2. Include the meaningful domain distinction or unit, such as elapsed days versus a timestamp.
3. Inspect actual uses and choose a name that reads correctly there.
4. Rename consistently through callers, tests, and related concepts.
5. Remove any comment whose sole purpose was to decode the old name.

Why it works: names keep necessary context next to the operation. Failure mode: making a wrong interpretation more confident by renaming before understanding the value's meaning.

### Pick One Word per Concept; Don't Pun

Use consistent vocabulary for the same operation, while preserving different names for different semantics.

- If `fetch`, `retrieve`, and `get` all mean the same thing in an owned interface, select a consistent word.
- If two operations differ, such as computing a combined value and inserting into a collection, do not force them under one word merely to look uniform.
- Evaluate consistency within the project's established vocabulary and platform conventions.

The paired rules prevent two opposite problems: different names for the same idea and the same name for different ideas.

### Meaningful Context

Use a class, namespace, or function to establish the context shared by a group of names. Add a prefix only when that context cannot otherwise be made apparent.

1. Ask whether an isolated name is ambiguous: `state` could mean an address region or lifecycle state.
2. Identify the concept that makes the surrounding fields meaningful, such as `Address`.
3. Place the fields within that concept when the grouping represents a real responsibility.
4. Remove repeated application prefixes that distinguish nothing inside the application.

Failure mode: adding a generic wrapper or putting temporary values into shared mutable fields solely to avoid explaining them at the call site. The book's naming examples demonstrate contextual naming; they are not a requirement to reproduce their state management.

## Key Concepts

- **Intention-revealing name**: a name that communicates why an entity exists and what it does.
- **Disinformation**: a name that implies an incorrect type, relationship, or behavior.
- **Meaningful distinction**: a difference in names that corresponds to a difference in purpose.
- **Searchable name**: a distinctive term that can be located reliably across its relevant scope.
- **Mental mapping**: translating arbitrary abbreviations or encodings into their actual meaning while reading.
- **Solution domain**: algorithms, mathematical terms, and established programming concepts.
- **Problem domain**: concepts used by the people whose work the system supports.
- **Gratuitous context**: additional qualifiers that consume attention without resolving ambiguity.

## Reference Table

| Naming situation | Decision rule | Reason |
|---|---|---|
| Value has a physical or business unit | Include the unit when it is not evident from its type | Prevent mistaken calculations and comparisons |
| Short loop-local identifier | A short conventional name can be sufficient | Scope supplies context |
| Long-lived or widely used entity | Prefer a distinctive, descriptive name | Readers encounter it far from its declaration |
| Type is already encoded by the language/tooling | Prefer semantic meaning over Hungarian notation | Implementation changes should not falsify the name |
| Class models a thing or concept | Prefer a noun or noun phrase | The name should describe its responsibility |
| Method performs an action | Prefer a verb or verb phrase | The call should explain what happens |
| Known algorithm or pattern is actually implemented | Use its accepted technical name | Readers can reuse existing knowledge |
| Business behavior has no established technical term | Use the domain's term | Readers can resolve meaning with a domain expert |
| `Info`, `Data`, numbering, or redundant type words distinguish peers | Replace them with the actual semantic distinction | Noise words leave callers guessing |
| Prefix appears on essentially every class | Remove it where scope already supplies that context | It adds little search or completion value |

## Worked Example

The chapter's game-board example starts with a method returning selected integer arrays from a vaguely named list. Its difficulty comes from four hidden facts: what the collection contains, what element zero represents, what the value four means, and how the result is used.

A faithful reconstruction of the reasoning:

1. Identify the collection as the game board and each entry as a cell.
2. Name the operation for the domain result: finding flagged cells.
3. Name the status field and its flagged value instead of asking readers to decode an index and number.
4. If cells are behavioral objects, move the representation-dependent test behind `isFlagged()`.

Illustrative Java, rewritten for this skill:

```java
List<Cell> findFlaggedCells(List<Cell> gameBoard) {
    List<Cell> flaggedCells = new ArrayList<>();
    for (Cell cell : gameBoard) {
        if (cell.isFlagged()) {
            flaggedCells.add(cell);
        }
    }
    return flaggedCells;
}
```

The loop's algorithm is unchanged. The reader now knows the domain operation without memorizing representation details. A domain object is useful when it owns behavior; merely renaming the index and constant can be the appropriate smaller change in a data-oriented representation.

## Mental Models

- Treat a name as a small contract: compare what it promises with what the implementation actually does.
- Think of vocabulary as an interface shared by the whole team. Consistency should preserve semantics.
- Use scope as the first source of context. Longer names earn their length when scope is broad or ambiguous.

## Anti-patterns

- **False collection names**: calling something a `List` when it does not have list semantics.
- **Near-identical names**: slight spelling changes or long common prefixes that hide the meaningful difference.
- **Number-series parameters**: names such as `a1` and `a2` distinguish positions without explaining their roles.
- **Clever or cultural references**: humor and slang can become an undocumented dependency.
- **Encodings that outlive implementation**: member prefixes or type codes remain after the corresponding implementation changes.
- **One-word consistency without semantic consistency**: assigning one verb to incompatible actions.

## Key Takeaways

1. Establish meaning before renaming.
2. Read proposed names in real call sites.
3. Preserve the distinction between domain concepts and implementation terms.
4. Let context reduce repetition without hiding important information.
5. Improve names as understanding improves; the first name need not be permanent.

## Connects To

- [Chapter 3](ch03-functions.md): function names, arguments, and hidden side effects.
- [Chapter 6](ch06-objects-and-data-structures.md): choosing whether representation should be hidden.
- [Chapter 17](ch17-smells-and-heuristics.md): N1–N7 and related naming heuristics.
