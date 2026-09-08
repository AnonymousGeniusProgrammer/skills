# Chapter 17: My Application Has No Structure

Source: printed pp. 215-225; PDF pp. 238-248.

## Core idea

Keep a shared architectural understanding alive through explanations of real code and its interactions. A design known only by a few people, or disconnected from daily changes, cannot reliably guide the rest of the team.

## Telling the Story of the System

1. Explain the architecture to someone unfamiliar with it using only a few central concepts.
2. Describe what those concepts do together.
3. Identify the simplifications and exceptions the short account omitted.
4. Add the next most important concepts, refining the story incrementally.
5. Compare proposed changes against the story: do they reinforce the responsibilities or require additional unexplained exceptions?

The short story is an explicit abstraction, not a complete factual model. Keep simplifications visible. Some reveal accidental complexity that could be removed; others reflect necessary requirements. Do not change code merely to make a pleasing story literally true.

## Worked example: JUnit

Reconstruction of the source example: tests run and send results to a `TestResult`, which makes failures available for reporting. This account omits interfaces, test-case subclasses, reflection, listeners, errors, and suite construction. Listing those omissions reveals what the explanation treats as central.

Now consider a report of tests that execute no assertions. Putting report generation directly into the test-case class adds a distinct responsibility that the story does not explain. Reporting assertion counts through the existing result interaction and having a report listener consume them fits the existing division more closely.

The decision comes from responsibilities and actual interactions, not from preserving every original class. Verify the behavior against the implementation; the example describes the JUnit architecture discussed in the book, not all present versions.

## Naked CRC

Ordinary **CRC** cards name a class, its responsibilities, and its collaborators. **Naked CRC** uses blank cards and physical movement to explain interactions. Its two source guidelines are:

- Cards represent **instances**, not classes.
- Overlapping cards represent a collection.

Walk through a concrete scenario, placing and moving cards as objects appear and communicate. Different instances of one class may need separate cards, especially when understanding sessions, connections, or concurrent participants.

The source's voting-system example uses client sessions, server sessions, their connections, and a vote manager. A vote travels to a server session, an acknowledgement returns, the manager records it, and updated counts are sent to the clients. Movement and position make those relationships tangible.

For an agent-led explanation, a small sequence sketch can adapt this technique; label it as a representation of instances and verify its path against code. It is a teaching adaptation, not the source's literal card exercise.

## Conversation Scrutiny

Listen for concepts used naturally in discussion that have no clear counterpart in the code. Ask whether the code has failed to catch up with understanding, or whether the spoken concept is misleading.

In the source example, developers repeatedly discuss a **locking policy** while manipulating counts in arrays. A `LockingPolicy` object could own that policy and express it through meaningful operations. The name alone is not sufficient; locate the actual state and behavior that belong together.

Use a candidate concept when it improves the next change and can be grounded in responsibilities. Avoid turning every noun in a conversation into a class.

## Failure modes and takeaways

- Going to familiar hack points for every feature can reinforce a structure no one intended.
- A distant architecture diagram can be wrong because code diverged or because the design itself needs revision; investigate both.
- Design continues while software changes. New abstractions should remain available when the evidence warrants them.
- Keep the explanation short enough to share and accurate enough, with explicit omissions, to guide actual work.

## Connects to

- [Ch 16](ch16-understanding-code.md): local understanding before the broader story.
- [Ch 20](ch20-large-classes.md): turn candidate responsibilities into classes.
- [Ch 24](ch24-overwhelmed.md): shared progress in difficult systems.
