# Chapter 4: Comments

Source: printed pp. 53–74; PDF pp. 84–105. Robert C. Martin.

## Core Idea

Use code to express what the implementation can say clearly, and comments to preserve information it cannot. A comment's value depends on accuracy, relevance, and proximity to the decision it explains.

## Frameworks Introduced

### Explain Yourself in Code

Use this when a comment translates an expression into the domain's vocabulary.

1. Determine whether the comment names a condition, operation, or intermediate result.
2. Give that concept a meaningful name in code.
3. Read the caller without the comment.
4. Remove the comment if it now repeats the same information.

Why it works: names and operations move with executable code more reliably than detached explanations. Failure mode: hiding a complicated expression under an uninformative helper merely relocates confusion.

### Keep the Information That Code Cannot Carry

Use this when deciding whether a comment survives a cleanup.

| Information | Keep or improve it when… | Prefer another home when… |
|---|---|---|
| Intent | It explains why an unusual decision was made | The name already expresses the complete intent |
| Warning | It records a consequence a future change could accidentally violate | The consequence can be enforced directly and the comment adds no rationale |
| Clarification | An external API or fixed format cannot be made clearer at the source | An owned API can carry the meaning itself |
| Algorithm rationale | It explains a non-obvious bound or invariant | It restates each statement in the algorithm |
| Legal notice | It supplies a required notice or concise license reference | It reproduces a long unrelated legal document |
| Public API contract | Callers need behavior, constraints, and return semantics | It is a mechanically generated repetition of names |
| TODO | It identifies concrete remaining work | It functions as a permanent excuse for an unresolved defect |

The chapter strongly criticizes comments while explicitly retaining these useful categories. Applying it as “remove all comments” loses its own distinctions.

### Verify a Comment Like a Behavioral Claim

Use this when a comment seems authoritative or has drifted away from the code it describes.

1. Identify the precise claim: timing, default, ordering, unit, invariant, or rationale.
2. Check it against the implementation and the relevant contract.
3. Correct any mismatch rather than making the code conform to an unverified comment.
4. Keep the explanation local to the code that controls the behavior.
5. Place broader changing facts in their appropriate source of truth.

Failure mode: a reassuring comment can discourage the reader from noticing incomplete handling. The chapter's properties-file example shows that “defaults loaded” is useless if it is unclear who loads them or whether they are loaded at all.

## Key Concepts

- **Intent comment**: a rationale for a choice, particularly one that may seem strange.
- **Clarifying comment**: an explanation of a constrained interface or representation.
- **Amplification**: emphasis on an easily overlooked but significant detail.
- **Nonlocal information**: a claim about behavior controlled elsewhere and liable to change independently.
- **Noise comment**: text that adds no useful information beyond the code.
- **Mumbling**: an incomplete explanation that requires further investigation to interpret.
- **Journal comment**: source-embedded change history better represented by version control.
- **Obsolete comment**: an explanation that no longer describes the current implementation.

## Worked Example

The chapter examines a prime-number generator using the Sieve of Eratosthenes. The initial version mixes an extensive historical preface, redundant variable notes, internal phase banners, and the algorithm itself. The refined version gives the phases meaningful names and keeps a concise algorithm explanation plus the reason for using the square root as a bound.

Reconstruct the decision process:

1. Preserve tests for an upper bound below the first prime, the first prime itself, composite boundaries, and ordinary ranges.
2. Name the operations that initialize candidates, eliminate multiples, and collect remaining primes.
3. Remove comments such as “declarations” that merely label syntax.
4. Keep an explanation of why factors beyond the square root need not be examined, if that fact remains non-obvious in the chosen implementation.
5. Remove historical material that does not help maintain the algorithm.

Illustrative rationale comment for a corresponding implementation:

```java
// Every composite has a factor no larger than its square root.
for (int candidate = 2; candidate <= upperBound / candidate; candidate++) {
    eliminateMultiples(candidate);
}
```

This rewritten fragment illustrates the rationale, not the book's complete implementation. The division-based bound is an adaptation; the original discussion uses the square root. A review must still verify that `eliminateMultiples` and the loop implement the intended sieve.

## A Subtle Timing Example

The chapter contrasts a comment saying a method returns “when” a condition becomes true with an implementation that checks, waits, then checks again. Those descriptions are not equivalent. Correct the explanation only after deciding whether the desired behavior is immediate notification, timeout waiting, or a condition-based protocol. A wording cleanup should not conceal a concurrency defect.

## Mental Models

- Treat a comment as an additional maintained claim, with its own opportunity to become wrong.
- Ask what the next maintainer would lose if it disappeared. If the answer is “nothing beyond the method name,” removal is usually safe.
- Keep rationale near the decision that would otherwise tempt someone to make a harmful simplification.

## Anti-patterns

- **Mandatory prose for every member**: generated repetition can bury useful documentation.
- **Commented-out code**: readers cannot distinguish obsolete debris from a still-important plan; version control preserves history.
- **Remote defaults in a local setter comment**: the setter does not control the value being documented.
- **Closing-brace markers**: often signal a body whose structure is too hard to follow.
- **Copy-pasted documentation**: apparently consistent text can confidently describe the wrong field.
- **Comments as substitute recovery**: an empty handler remains empty even when its comment sounds reassuring.

## Key Takeaways

1. Prefer a meaningful name when the comment merely translates code.
2. Preserve contracts, rationale, consequences, and other information that adds value.
3. Verify comments during nearby changes; age and distance increase drift risk.
4. Use version control for history and deleted implementations.
5. Review public API documentation for precision rather than maximizing its volume.

## Connects To

- [Chapter 2](ch02-meaningful-names.md): replacing decoding comments with clear vocabulary.
- [Chapter 3](ch03-functions.md): extracting an operation instead of narrating a long body.
- [Chapter 17](ch17-smells-and-heuristics.md): C1–C5.
