# Chapter 21: I'm Changing the Same Code All Over the Place

Source: printed pp. 269–287; PDF pp. 292–310.

## Core idea

Remove duplication in small, tested steps. Naming repeated behavior and isolating differences often reveals a design in which each change has one clear home.

The goal is **orthogonality**: independent behaviors can be changed independently. Saving lines is useful, but reducing the number of places that must change together is the more important result.

## Incremental duplication removal

1. Cover the behavior of the duplicated implementations, including their differences.
2. Extract a small repeated operation and give it a meaningful name. Prefer a name that explains a coherent action over an arbitrary grouping of statements.
3. Replace one occurrence, run tests, then replace the others.
4. When methods are nearly alike, extract their differences. The remaining methods may become identical and shareable.
5. Move common behavior and common data to an appropriate shared home. Preserve distinct values through explicit parameters or variation points.
6. Look again: common field lists or other data representations may replace repeated control flow.
7. Evaluate likely change scenarios. Keep simple client interfaces and avoid forcing clients to reconstruct implementation details.

An initial grouping is reversible. A small extraction can reveal a better one later; do not postpone all progress until the final class diagram is known.

## Worked example: serialized commands

Feathers compares employee-addition and login command classes. Both serialize a header, message size, command identifier, null-terminated string fields, and a footer. Their identifiers and fields differ.

| Step | Extraction | What becomes easier to see |
|---|---|---|
| 1 | `writeField` writes one string and its terminator | Field representation is a single operation |
| 2 | Move the shared operation to `Command` | Both command types use the same encoding rule |
| 3 | Extract `writeBody` from each command | The outer serialization sequence is the same |
| 4 | Share header/footer constants and expose the varying command identifier | `write` can use one common implementation |
| 5 | Extract body-size and field-size calculations | Message size separates fixed overhead from body content |
| 6 | Store each command's ordered fields in a collection | Both writing and sizing can iterate over the same representation |
| 7 | Keep small named command subclasses where useful | Clients retain meaningful constructors and need not know numeric command identifiers |

The final shape is approximately this reconstructed pseudocode:

```text
write(stream):
    write header
    write encoded total size
    write command identifier
    for each field in its original order:
        write encoded field bytes
        write field terminator
    write footer
```

Tests should compare the serialized bytes, not merely the new helper's output. Include the correct command identifier, field order, separators, body size, and numeric-to-text conversion. Preserve the existing encoding and size conventions during the refactoring; changing them is a separate behavior change.

The chapter's listings illustrate successive designs and contain inconsistencies in types and visibility. Treat this reconstruction as design guidance, not a compilable protocol implementation. A new aggregate command needs writing and sizing behavior to agree; overriding a body writer alone does not establish that its message length is valid.

## Extract differences before merging similarities

Two methods may look identical while referring to different constants, instance state, or inherited operations. First make those differences explicit. Moving one body prematurely can accidentally bind both clients to one class's data.

Conversely, do not multiply abstract hooks when a simple ordered list expresses the variation. In the example, the string fields differ as data; iterating over them removes repeated writing and size calculation. A different body format may later justify a distinct implementation. Add that variation point when its concrete need is understood.

## Judge the emerging design

- If field termination changes, can one operation handle it consistently? Check the associated size calculation too.
- If a new command is added, can it reuse serialization without copying the whole algorithm?
- If a command has a genuinely different body, is that difference explicit and testable?
- Do named entry points still shield clients from protocol identifiers and ordering rules?

The Open/Closed Principle describes the direction: a good boundary allows many extensions with little modification to existing code. It is not a ban on editing shared code. Use tests and refactoring to keep extension mechanisms coherent, including removing duplication introduced by Programming by Difference.

## Anti-patterns and takeaways

- Avoid a large speculative framework before extracting the first repeated operation.
- Avoid eliminating tiny classes solely because they became tiny. They may preserve useful names and spare many client changes.
- Avoid assuming similar text means identical semantics. Check data, call order, conversions, and exceptions.
- Avoid inconsistent abbreviations that make related APIs difficult to find or guess.
- Reassess the number of independent change locations after refactoring; fewer lines alone does not prove improved design.

## Connects to

- [Chapter 8](ch08-adding-features.md): Programming by Difference and subsequent integration of shared behavior.
- [Chapter 20](ch20-large-classes.md): identify responsibilities and their shared data.
- [Chapter 22](ch22-monster-methods.md): small extractions expose larger structure.
