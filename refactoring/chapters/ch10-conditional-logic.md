# Chapter 10: Simplifying Conditional Logic

Source: printed pp. 259-304; PDF pp. 281-326. Entry locators use printed pages; add 22 for PDF pages.

## Core idea

Make the reason for a branch visible. Choose named predicates, guard clauses, polymorphism, or special cases according to the kind of variation; preserve the logic and order of the original decision.

## Catalog mechanics

### Decompose Conditional — p. 260

**Use when:** a test or branch body says how it works but conceals what it means.

**How:** Extract Function for the predicate and each branch, choosing names for their intentions; test after each extraction.

**Watch:** preserve captured values, inclusive boundaries, short-circuit behavior, and which branch computations run. A helper adds value through meaning, not merely through moving lines.

### Consolidate Conditional Expression — p. 263

**Use when:** several checks belong to one conceptual decision and produce the same outcome.

**How:** first ensure conditions have no side effects; combine sequential equivalent-result guards with OR, or nested qualifying conditions with AND; test after each combination; name the combined predicate where helpful.

**Watch:** use parentheses for mixed operators and retain short-circuit order. Independent business checks that happen to return the same result need not be consolidated. Do not combine branches whose outputs, reason codes, or effects differ.

### Replace Nested Conditional with Guard Clauses — p. 266

**Use when:** an exceptional case obscures the function's ordinary path.

**How:** turn the outermost suitable exceptional condition into a returning guard; test; repeat; consolidate guards only when their meanings and results permit it.

**Watch:** preserve precedence when conditions overlap. A guard must not skip cleanup or other work that used to run afterward. Ordinary alternatives may remain clearer as if/else. A single return is not a universal clarity requirement.

### Replace Conditional with Polymorphism — p. 272

**Use when:** several operations vary by the same category, or a base computation has coherent variants.

**How:** create the class structure and a factory; move callers to the factory; isolate and move the conditional operation into the base; transfer one branch at a time into overriding methods; test each move; retain the appropriate base default or abstract responsibility.

**Watch:** preserve the previous default for unrecognized categories unless deliberately changing behavior. Do not duplicate dispatch across all operations in the new hierarchy. A factory switch can remain. Choose polymorphism for a recurring dimension of variation, not every isolated if statement.

### Introduce Special Case — p. 289

**Use when:** many clients recognize a sentinel, null, or exceptional value and mostly supply the same response.

**How:** give the ordinary subject an indicator returning false and create the special subject with a true indicator; centralize sentinel comparisons; introduce the special object through the container or a transform; adapt the comparison function; move shared fallback behavior into the object or enriched record; simplify clients incrementally.

**Watch:** retain explicit checks for clients whose responses are truly different. Define update behavior as well as reads. A literal record is enough for fixed read-only values; a class may be needed for richer behavior. A Null Object is one form of Special Case, not the whole pattern.

### Introduce Assertion — p. 302

**Use when:** correct execution assumes a condition whose violation indicates a programmer error.

**How:** make the required condition explicit at a useful boundary; keep the assertion expression free of required side effects; consolidate duplicated invariant logic.

**Watch:** an assertion communicates a necessary invariant, not a new rule for accepting user input. Supported behavior should work equally with assertions disabled. If real supported inputs violate the proposed condition, adding a throwing assertion changes behavior. Handle expected external-input failures through ordinary validation and error handling.

## Worked example: unknown utility customer

The source represents an unknown occupant with the string `"unknown"`. Clients repeatedly substitute an occupant name, a basic billing plan, zero delinquent weeks, and special handling for plan changes.

1. Encapsulate the recognition rule so sentinel comparisons have one home.
2. Introduce an unknown-customer object while preserving the container's usable interface.
3. Move the standard name and billing-plan fallback into that object.
4. Supply the appropriate payment-history object so callers need not repeat a nested fallback.
5. Move or preserve the intended treatment of updates. If unknown-customer plan assignments previously had no effect, introducing a normal mutable setter would be a change.
6. Keep a special-case indicator for an exceptional client with a different presentation rule.

The payoff is localized default behavior, not the elimination of every check. Test ordinary and unknown customers, repeated reads, and update behavior. If one shared special object is used, its mutable nested state must not leak across sites.

## Code example: guard precedence

Compact reconstruction of the source's employee-payment example:

```javascript
function payAmount(employee) {
  if (employee.isSeparated) return {amount: 0, reasonCode: "SEP"};
  if (employee.isRetired) return {amount: 0, reasonCode: "RET"};
  return ordinaryPay(employee);
}
```

`ordinaryPay` stands for the unchanged normal calculation. If both flags are true, separation still wins. Combining the guards into a single boolean and one return would lose the distinct reason code.

## Selection table

| Shape of problem | Starting move | Why |
|---|---|---|
| Dense seasonal pricing predicate | Decompose Conditional | Names the domain decision |
| Several exclusions with one outcome | Consolidate Conditional Expression | Expresses one eligibility rule |
| Exceptional paths surround the main calculation | Guard Clauses | Makes the normal path prominent |
| Bird type controls both plumage and flight behavior | Polymorphism | Gathers related variation by type |
| Many clients repeat the same missing-value default | Special Case | Gives the default behavior one owner |
| Internal code requires a condition always to hold | Assertion | Exposes a programmer invariant |

## Takeaways and connections

Prefer the smallest structure that explains the actual variation. Test overlapping conditions and defaults as well as representative branches. Keep invariants separate from input validation. See [Ch 3](ch03-code-smells.md) for Repeated Switches, [Ch 6](ch06-first-refactorings.md) for extraction, [Ch 11](ch11-refactoring-apis.md) for query/modifier separation, and [Ch 12](ch12-inheritance.md) for creating or replacing hierarchies.
