# Chapter 9: Better Together Or Better Apart?

Source: first edition (2018), PDF pages 69-83. Reference notes.

## Core idea

Split or combine functionality according to total complexity, information hiding, and independence. Subdivision introduces interfaces, coordination, separation, and sometimes duplication; small pieces are not automatically easier to use.

## Boundary decision table

| Evidence | Direction to explore | Check before accepting |
|---|---|---|
| Both pieces depend on the same design knowledge | Combine or give that knowledge one owner. | Is the knowledge hidden from the resulting interface? |
| Callers almost always need both capabilities | Combine their ordinary use. | Is the relationship bidirectional, rather than one reusable utility serving one client? |
| A combined operation removes intermediate state or sequencing obligations | Offer a complete operation. | Does the interface become easier? |
| Nontrivial logic is repeated | Extract a useful operation or reorganize control flow to execute it once. | Is the replacement interface simpler than the duplicated logic? |
| A general mechanism contains policy for one use | Move the policy to that use's module. | Can both evolve with less shared knowledge? |
| A separable subtask has a clear contract | Extract it. | Can parent and child be understood independently? |

A block cache depends on a hash table, but hash tables have many unrelated uses. That one-way relationship does not justify merging the two abstractions.

## General mechanism versus special-purpose code

Keep the reusable mechanism independent of its particular uses. This is relative to the mechanism in question: text-specific undo actions belong with text operations, even though the text module itself offers general-purpose text handling.

The source's undo design separates three kinds of knowledge:

- A history component manages actions, ordering, grouping markers, and traversal.
- Action implementations know how to undo and redo a particular kind of change.
- Higher-level UI code chooses which changes belong to one user-visible undo group.

The history need not know text, cursors, or selections. The text model need not know cursor undo policy. The source's grouping markers are called **fences**; the policy placing them remains above the generic mechanism.

## Splitting methods

**Extract a subtask** when it forms a useful abstraction. The parent keeps its interface and calls the new child. A reader should understand the child without its parent's implementation and understand the parent without entering the child.

**Divide the public operation** when its original interface bundles unrelated tasks and each new operation is simpler. It is a warning if every caller must invoke both new methods and shuttle state between them.

**Join methods** when joining hides shared knowledge, eliminates intermediate structures or duplication, or replaces shallow interfaces with one complete operation.

Method length is evidence to inspect, not a fixed threshold. A long method with a simple signature and understandable phases may be deeper than several intertwined helpers.

## Source examples as quick checks

- Splitting HTTP reading from parsing duplicates framing knowledge: combining improves the abstraction.
- A separate class with one-off, one-line error logging methods adds navigation without useful hiding: place those statements with their error sites.
- Combining cursor and selection can require awkward flags without simplifying UI use: ordinary position values can represent them more directly.
- Repeated cleanup may be centralized through control-flow restructuring. The book shows a C-style jump to common cleanup; the underlying technique is one clear cleanup path, not a universal recommendation to use `goto`.

## Red flags

**Repetition** suggests a missing abstraction when nontrivial logic recurs. **Special-General Mixture** couples a reusable mechanism to a particular use. **Conjoined Methods** require readers to examine separate implementations together to understand either.

## Key takeaways

1. Account for new interfaces and caller coordination when splitting.
2. Keep shared knowledge together and independent concepts apart.
3. Use independently understandable contracts as the extraction test.
4. Separate a mechanism's reusable core, specific actions, and policy where their knowledge differs.

## Connects to

- [Ch 5](ch05-information-hiding.md): leakage and temporal decomposition.
- [Ch 6](ch06-general-purpose-modules.md): generality without speculation.
- [Ch 10](ch10-define-errors-out-of-existence.md): consolidating exceptional paths.
