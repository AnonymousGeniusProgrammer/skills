# Chapter 10: Define Errors Out Of Existence

Source: first edition (2018), PDF pages 84-98. Reference notes.

## Core idea

Reduce the places where exceptional conditions require special handling. First seek semantics that make a condition ordinary; otherwise recover locally or consolidate handling at a boundary that can respond coherently.

## Concepts

**Exception** includes any uncommon condition that alters control flow, whether represented by a language exception, status code, or sentinel return. Exception complexity includes recovery, rollback, secondary failures, and the knowledge required through multiple calling layers.

**Define errors out of existence** means change the contract so a formerly exceptional input or state has a useful normal result. It is a semantic design decision, not permission to catch and ignore a failed operation.

## Four techniques

| Technique | When useful | Required reasoning |
|---|---|---|
| Define the error away | A natural broader contract handles the case correctly. | Show that the promised result is still achieved. |
| **Exception masking** | A lower layer can recover without requiring the caller's involvement. | Preserve the higher-level contract; expose an ultimate failure when it matters. |
| **Exception aggregation** | Several failures need the same recovery or response. | Put common handling at the appropriate boundary, retaining useful specifics. |
| Termination when recovery is not viable | Continuing cannot provide a correct useful result. | Consider the actual system's recovery model and consequences. |

These are alternatives to examine, not a mandatory fallback chain ending in a crash.

## Defining a broader contract

The Tcl example contrasts removing an existing variable with ensuring the variable is absent. Under the latter contract, an already absent variable is success, which simplifies partial-initialization cleanup.

The file example distinguishes removing a name from reclaiming underlying storage. Existing users can retain access while reclamation waits for them to finish. Use this as the book's abstraction example; actual operating-system semantics require their own documentation.

The substring discussion proposes returning characters whose indices overlap a requested range, rather than rejecting every out-of-bounds endpoint. This is the author's proposed alternative to Java's API, not a claim that Java behaves that way. Preserve strict validation where the missing distinction matters to callers.

## Masking versus losing failures

A transport can recover from a lost packet internally. That differs from catching all network errors and continuing after an operation failed. The latter denies applications information needed for correctness; the chapter explicitly rejects it.

Ousterhout defends NFS retry-and-wait behavior in its context because most applications cannot usefully recover from temporarily inaccessible files. Treat that as a contextual tradeoff. Applying masking elsewhere requires checking latency, cancellation, availability, and caller obligations rather than prescribing unlimited retries.

## Aggregation and responsibility

In the web-server example, parameter accessors detect missing or malformed values and describe the problem. A request dispatcher handles those failures with common response logic. This separates parameter knowledge from response-generation knowledge and avoids a handler around every accessor.

Combine errors when the response is actually shared. Preserve distinctions that require different recovery. A general handler should not conceal failures that lie outside its intended contract.

For request-processing systems, the source describes a request-abort exception caught at the top of the request loop: clean up that request's state and continue with the next one. Distinguish it from a failure fatal to the whole system.

RAMCloud illustrates **error promotion**: rare object corruption triggers existing server-crash recovery instead of requiring a second recovery mechanism. The simpler recovery design costs more work per incident. The chapter explicitly rejects applying this tradeoff to frequent events such as lost packets. A replicated store still has to recover the data promised by its contract; termination alone is insufficient.

## Ordinary representations for special cases

Represent no visible text selection as an empty range. Copying zero characters and deleting an empty range can then follow the ordinary operations. This removes a separate existence flag and its repeated branches, provided range operations have correct empty-case semantics.

## Anti-patterns and limits

- Generating an exception mainly to transfer an unresolved problem to callers.
- A separate catch block for each operation when all catches perform the same response.
- Claiming success after discarding failure information.
- Broadening a contract while concealing a distinction clients need.

## Key takeaways

1. Count handling sites and obligations, not only exception types.
2. Redefine semantics only when the ordinary outcome remains useful and correct.
3. Recover where the necessary knowledge lives; aggregate where response policy lives.
4. Expose failures callers need to know about.

## Connects to

- [Ch 5](ch05-information-hiding.md): necessary information must stay visible.
- [Ch 8](ch08-pull-complexity-downwards.md): local recovery as caller simplification.
- [Ch 13](ch13-comments-and-abstractions.md): document the actual failure contract.
