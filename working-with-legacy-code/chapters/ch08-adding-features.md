# Chapter 8: How Do I Add a Feature?

Source: printed pp. 87-104; PDF pp. 110-127.

## Core idea

Get the relevant existing code under test, add the new behavior in small test-driven steps, and then use the tests to integrate the design. Temporary duplication or a temporary subclass can give the new behavior a clear starting point.

## TDD in legacy code

The chapter extends the normal cycle with an initial step: get the class under test. Then write a failing test, make it compile, make it pass while initially avoiding unnecessary changes to existing code, remove duplication, and repeat.

Keep functional change and refactoring distinct in attention and evidence. A test for a new case tells you what must be added. Characterization tests protect the old cases. Passing both gives room to unify implementation afterward.

When the design for a new feature is unclear, allow a small amount of visible duplication while establishing behavior. Immediately inspect it once tests pass; duplication is a temporary aid to seeing the common structure, not the desired endpoint.

## Programming by Difference

Use **Programming by Difference** when an existing tested class has an overridable decision that can supply the new variation.

1. Identify the specific decision that differs.
2. Make an appropriate override point with a small preserving change if necessary.
3. Write the new behavior in a subclass and pin it down with a test.
4. Examine how this variation must combine with others and what clients assume about the base type.
5. Use the tests to move to configuration, delegation, or a clearer hierarchy as the design requires.

**Why it works:** an existing implementation supplies most of the behavior, while a small override expresses the difference. **Failure mode:** repeatedly encoding independent feature dimensions as subclasses makes combinations proliferate and can silently violate client expectations.

## Worked example

Reconstruction of the message-forwarding example:

- Existing forwarding determines a sender address from the message or a default.
- New behavior forwards anonymously, using a mailing-list address instead.
- A tested `AnonymousMessageForwarder` overrides the sender-address decision.
- The next feature adds recipients outside the normal list.

If anonymity and additional recipients live in separate subclasses, combining them becomes awkward. Keep the test that observes the forwarded sender address, then move the sender decision into configurable behavior. Give the collaborator responsibility for determining the sender and building the recipient list.

As its responsibilities become active, a name such as `MailingConfiguration` becomes less accurate; the source evolves it toward `MailingList`. This is an example of tests enabling exploration of a domain abstraction after the feature works.

Compact schematic endpoint:

```java
Address sender = mailingList.getFromAddress(message);
List<Address> recipients = mailingList.buildRecipientList(originalRecipients);
```

This illustrates the responsibility boundary rather than a complete mailing implementation. Retain tests for normal and anonymous forwarding, and for the recipient combinations the task actually introduces.

## Liskov Substitution Principle

A subtype must satisfy the expectations of code using its base type. The source's mutable Rectangle/Square example shows why a plausible domain "is-a" relation is insufficient: changing width and height independently can conflict with preserving a square.

Review the relevant clients and contracts when an override changes meaning. Feathers offers heuristics of avoiding concrete overrides where possible and considering whether an override can call the base implementation. These are heuristics, not a proof: calling `super` can still violate the contract, and deliberately replacing a policy can be correct when the contract permits it.

## Normalized hierarchy

In a **normalized hierarchy**, concrete implementations do not override inherited concrete implementations; variation is expressed through abstract operations implemented below them. This makes the question "which implementation supplies this behavior?" easier to answer.

Consider moving toward this form when repeated concrete overrides obscure behavior or responsibilities are being separated. The chapter does not require normalizing every hierarchy or replacing all existing inheritance.

## Anti-patterns and takeaways

- Test the new contract through meaningful output or effects, not just the existence of the new subclass.
- Use tests to retire temporary structures when feature combinations reveal a better boundary.
- Preserve old cases when removing duplication; a successful new test alone is insufficient.
- Check actual client expectations before treating a subtype as interchangeable.

## Connects to

- [Ch 6](ch06-not-much-time.md): create tested additions when the surrounding class remains difficult.
- [Ch 13](ch13-characterization-tests.md): protect the existing behavior.
- [Ch 21](ch21-duplication.md): use duplication to discover common structure.
