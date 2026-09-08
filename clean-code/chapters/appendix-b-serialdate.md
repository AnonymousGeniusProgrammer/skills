# Appendix B: org.jfree.date.SerialDate

Source: printed pp. 349–408; PDF pp. 380–439. Supporting listings for [chapter 16](ch16-refactoring-serialdate.md).

## Core Idea

This appendix is a before/after code corpus rather than an independent theoretical chapter. Use it to compare contracts, tests, and responsibility placement during the SerialDate refactoring. The skill retains a navigation and study guide instead of reproducing the lengthy source listings.

## Listing Index

| Listing | Source title | Printed start page | Purpose |
|---|---|---:|---|
| B-1 | SerialDate.java | 349 | Original date abstraction and mixed utilities |
| B-2 | SerialDateTest.java | 366 | Original tests |
| B-3 | MonthConstants.java | 372 | Original integer constants |
| B-4 | BobsSerialDateTest.java | 374 | Expanded tests and behavioral questions |
| B-5 | SpreadsheetDate.java | 382 | Original concrete ordinal representation |
| B-6 | RelativeDayOfWeekRule.java | 390 | Consumer exposing dependencies on date construction/range |
| B-7 | DayDate.java (Final) | 394 | Refined abstraction |
| B-8 | Month.java (Final) | 397 | Month type and related behavior |
| B-9 | Day.java (Final) | 398 | Weekday type and related behavior |
| B-10 | DateInterval.java (Final) | 399 | Interval inclusion semantics |
| B-11 | WeekInMonth.java (Final) | 400 | Week selection alternatives |
| B-12 | WeekdayRange.java (Final) | 401 | Last/next/nearest alternatives |
| B-13 | DateUtil.java (Final) | 402 | Shared date utilities |
| B-14 | DayDateFactory.java (Final) | 403 | Construction abstraction |
| B-15 | SpreadsheetDateFactory.java (Final) | 404 | Concrete construction choice |
| B-16 | SpreadsheetDate.java (Final) | 405 | Refined representation implementation |

Add 31 to a printed page to locate it in this PDF. Consult the user's original PDF for complete code or exact listing line numbers.

## Study Routes

### Responsibility and Abstraction

Read B-1 and B-7 alongside chapter 16's discussion. For each member that moves, ask whether its meaning depends on the day concept, a month/weekday concept, the ordinal representation, or object construction. Then inspect the destination listing.

The goal is to understand why a boundary changes, not to measure improvement by the number of extracted files.

### Behavioral Changes

Compare B-2 and B-4 before studying the final classes. Separate tests for established behavior from the author's proposed new behavior and unresolved input conventions. A failing new test does not by itself establish that the original program is defective.

### Representation Dependencies

Compare B-5 and B-16, then follow B-6 through the factory listings. Watch the supported range, ordinal origin, leap-year calculations, and weekday derivation. An apparently general formula can still depend on the implementation's origin or range.

## Worked Example: Interval Inclusion

The source replaces `INCLUDE_NONE`, `INCLUDE_FIRST`, `INCLUDE_SECOND`, and `INCLUDE_BOTH` with a `DateInterval` enum expressing mathematical interval semantics.

| New concept | Left endpoint | Right endpoint |
|---|---|---|
| `OPEN` | Excluded | Excluded |
| `CLOSED_LEFT` | Included | Excluded |
| `CLOSED_RIGHT` | Excluded | Included |
| `CLOSED` | Included | Included |

For an ordered range `[start, end]`, evaluate a date immediately before the range, at each endpoint, inside the range, and immediately after it. The endpoint cases distinguish the alternatives; ordinary interior values do not.

Then inspect the old API's treatment of reversed arguments and equal endpoints. Preserve that behavior or make a deliberate contract change; the descriptive enum alone does not settle it.

## Regression Questions

- Do month and year arithmetic preserve or clamp the day as intended?
- Does “following” exclude the base date while “nearest” may select it?
- Are leap-year and month-end cases covered?
- Are invalid raw codes rejected at the conversion boundary?
- Does immutable arithmetic return a new date without silently mutating the original?
- Can the selected implementation's range and ordinal origin change without falsifying the abstraction?
- Are public and serialized compatibility requirements preserved?

## Anti-patterns

- Copying the final listings without the tests and contract discussion.
- Treating every source simplification as appropriate for an unrelated public library.
- Moving a formula while leaving assumptions implicit.
- Reproducing all listings inside a skill instead of preserving a focused study route.

## Key Takeaways

1. Compare tests and contracts before comparing class shapes.
2. Follow moved members into their new conceptual homes.
3. Use the original PDF when exact code matters.

## Connects To

- [Chapter 16](ch16-refactoring-serialdate.md): reasoning behind the transformations.
- [Chapter 6](ch06-objects-and-data-structures.md): abstraction versus representation.
- [Chapter 9](ch09-unit-tests.md): month arithmetic as single-concept tests.
