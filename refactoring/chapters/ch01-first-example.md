# Chapter 1: Refactoring: A First Example

Source: printed pp. 1-44; PDF pp. 23-66. Examples below reconstruct the book's theatrical billing example compactly; they are not a transcription of its program.

## Core idea

Improve structure through individually checked changes that make a concrete feature easier to add. The example proceeds from one statement function to named calculations, then a calculation/rendering boundary, then polymorphic pricing.

## The three stages

### 1. Decompose the statement function

The starting function mixes play lookup, charges, volume credits, currency formatting, totals, and text assembly. Two desired changes expose the cost: HTML output would duplicate calculations, and new play types would require coordinated edits to pricing and credits.

1. Establish self-checking tests for representative invoices. Compare the complete statement with hand-checked expected strings, including formatting.
2. Apply **Extract Function** to the amount calculation. Identify inputs read from the surrounding scope and values written by the fragment. Move purely local initialization inside; return the changed result.
3. Rename parameters and locals to express their roles. These are separate small changes, not an invitation to rewrite the algorithm.
4. Turn play lookup into a query. **Replace Temp with Query** and **Inline Variable** remove local dependencies that obstruct further extraction. Remove a redundant parameter only after the callee can obtain the same value itself.
5. Extract volume credits and give currency conversion an intention-revealing name.
6. For an accumulated total, **Split Loop**, **Slide Statements**, **Extract Function**, then **Inline Variable**. Each step has a distinct purpose: isolate one job, gather its initialization, name it, remove the obsolete temporary.
7. Run relevant tests after each meaningful move. A failure now points to a small, recent change.

**Why it works:** names preserve discoveries that would otherwise remain in the programmer's memory. Removing local dependencies makes the next structural move easier. A temporarily larger or more repetitive implementation can be a useful intermediate state.

**Failure mode:** extracting a whole tangle at once leaves many mutable outputs and a long parameter list. First separate the responsibilities or simplify their data dependencies.

### 2. Split calculation from presentation

Use **Split Phase** when several output formats need the same calculations.

1. Extract the rendering code, initially preserving its existing dependencies.
2. Introduce an intermediate statement-data record.
3. Transfer the customer, performances, play details, amount, and volume credits into that record incrementally. Enrich copies of performance records so preparation does not unexpectedly mutate the original invoice.
4. Move calculations into data preparation as rendering stops needing their raw inputs.
5. Derive totals from the prepared performance data.
6. Extract the preparation phase into `createStatementData`; both text and HTML renderers can consume its result.

The intermediate record hides how the data was calculated. It also gives calculation tests a useful observation point. A data-only record is appropriate here because it is a phase result, used as effectively immutable data.

### 3. Organize calculations by play type

Use **Replace Conditional with Polymorphism** when several calculations vary together by category.

1. Introduce a performance-calculator class and move the existing calculations into it without changing their rules.
2. Replace direct construction with a factory so selection of a concrete calculator has one home.
3. Introduce tragedy and comedy subclasses using **Replace Type Code with Subclasses**.
4. Move one amount-calculation branch at a time into its matching subclass. Verify each move before removing the old branch.
5. Keep ordinary volume credits in the base calculator; override the comedy variation where it adds extra credits.
6. Continue returning the prepared data record. Renderers need not know that polymorphism now implements the calculations.

**Why it works:** each play category's varying rules become colocated, while rendering remains independent. The factory can still contain a switch; eliminating every conditional is not the objective.

## Worked example: preserve the bill while changing its structure

The source invoice has three performances: Hamlet with 55 attendees, As You Like It with 35, and Othello with 40. The original rules produce charges of $650, $580, and $500, totaling $1,730, with 47 volume credits. These are behavior checks, not pricing recommendations.

| Stage | What changes internally | What the existing text test protects |
|---|---|---|
| Extract calculations | Calculations gain names and explicit dependencies | Same individual charges, total, credits, and text |
| Split Phase | Rendering reads prepared data | Same data meaning and formatted statement |
| Introduce calculators | Pricing dispatch moves into subclasses | Same behavior for existing play types |
| Add HTML output | A new renderer is introduced | Existing text stays stable; new output gets its own checks |

The last row is feature work. The preceding rows prepare for it. Keeping those activities conceptually distinct makes failures easier to interpret.

Compact structural reconstruction:

```javascript
function statement(invoice, plays) {
  const data = prepareStatement(invoice, plays);
  return renderText(data);
}

function htmlStatement(invoice, plays) {
  const data = prepareStatement(invoice, plays);
  return renderHtml(data);
}
```

This illustrates a boundary, not a complete executable implementation. `prepareStatement` corresponds to the source's calculation phase; renderer names here are shortened for clarity.

## Judgment and anti-patterns

- Let the requested changes justify the new structure. Fowler explicitly says this tiny teaching program would not warrant every demonstrated refactoring by itself.
- Judge a good result by how readily a maintainer can find and safely make the next change, not by its class count or line count.
- Avoid duplicating pricing logic to support another output format.
- Treat repeated traversal as a performance question to measure. Do not promise that extra loops are always free; honor real latency and resource constraints.
- Stop when the immediate change is easier and the code is healthier. The example leaves other improvements possible.
- Keep the input invoice stable when enriching records. Copying the outer record does not imply a deep copy of every nested object.

## Key concepts

**Comprehension refactoring:** embody newly understood intent in the code. **Intermediate data:** a contract between calculation and rendering. **Polymorphic calculator:** an implementation selected by play type behind a stable interface. **Small steps:** transformations that keep the feedback window narrow. **Camping rule:** leave touched code healthier without requiring perfection.

## Takeaways and connections

1. Begin with the behavior to preserve and the change that structure should enable.
2. Use extraction to reveal boundaries, then move responsibilities across those boundaries.
3. Preserve working states throughout the sequence; intermediate code need not be the final design.

Read [Ch 2](ch02-principles.md) for scope and economics, [Ch 4](ch04-building-tests.md) for the testing foundation, [Ch 6](ch06-first-refactorings.md) for extraction and Split Phase, and [Ch 10](ch10-conditional-logic.md) for polymorphism.
