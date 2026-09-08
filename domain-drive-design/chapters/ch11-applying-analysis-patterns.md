# Chapter 11: Applying Analysis Patterns

Source: supplied PDF, pages 181-190. Evans draws on Martin Fowler's *Analysis Patterns*; this guide covers the examples Evans uses, not that other book's full catalog.

## Core Idea

An analysis pattern supplies experienced model concepts, vocabulary, alternatives, and implementation consequences. Use it to accelerate investigation. Adapt the structure to the local domain while preserving the meaning of any borrowed names.

## Applying an Analysis Pattern

Use this approach when a difficult model resembles an established business construction, or when procedural code hides domain logic that the team cannot yet name.

1. Describe the local problem in domain terms before selecting a pattern.
2. Read the pattern's motivation, alternatives, and implementation consequences along with its diagram. A diagram alone omits much of the experience you are trying to reuse.
3. Map candidate concepts onto concrete scenarios. Mark mismatches rather than inventing correspondences merely to complete the diagram.
4. Walk the candidate model through those scenarios with a domain expert. Clarify terms and temporal relationships.
5. Remove unnecessary pattern elements; adapt collaborations where local constraints demand it.
6. Implement a small slice and exercise it through tests. Let implementation experience refine the model.
7. Retain a borrowed name only while its central meaning remains intact. Rename a concept that evolves away from that meaning.

Why it works: good patterns capture expensive lessons from implemented systems, including effects on maintenance. They offer a better starting point without replacing knowledge crunching or Model-Driven Design.

## Worked Example: Accounts, Accruals, and Payments

### Initial difficulty

A specialty accounting application calculates loan interest and fees, tracks payments, and sends nightly postings to a legacy accounting system. Its calculator mixes accrual logic with relationships to payments. It works, but changes are awkward and the model communicates poorly.

### Candidate accounting concepts

| Concept | Meaning in the example | Design consequence |
|---|---|---|
| Account | Collects changes to an amount of value | Offers a balance and history through one interface |
| Entry | Records a change in an Account | Positive and negative entries contribute to balance; recorded history remains |
| Transaction | Moves value between Accounts in a conserved, closed accounting system | Groups matching effects created together; not a generic label for any two related events |
| Accrual | Recognizes income or expense when incurred | Can occur independently of cash payment |
| Payment | Records the payment event | Does not necessarily correspond one-to-one with an accrual |

The basic Account model computes balance from Entries, or caches an equivalent result behind its interface. Adding an opposing Entry changes balance while preserving history. This is a conceptual model; a production design must separately establish persistence, correction, and audit requirements.

The developers initially connect an interest accrual and its later payment as a Transaction. Two mismatches appear: both affect the same Account rather than moving value between Accounts, and they may occur days apart rather than forming one complete transaction. The domain expert also rejects a rigid one-accrual/one-payment relationship.

The useful insight survives removal of Transaction: an Account holds both accrual and payment Entries, with a balance and visible history. This satisfies the expert's need without the false relationship. Double-entry conservation is valuable where the modeled system includes the relevant sources and sinks; the chapter does not demand it for every application that uses an Account.

### Refined implementation

Payment and Accrual become separate Entry subtypes because their responsibilities and business meanings differ. Interest versus fee does not initially justify additional behavioral subtypes: the appropriate Account already distinguishes them.

An infrastructure constraint then forces a compromise. Tables must be interpretable independently of the application, and the project's particular mapping framework requires concrete classes to map separate fee and interest tables. Extra subclasses appear even though the domain abstraction would otherwise be simpler.

Record such a compromise explicitly. It is evidence of a particular mapping limitation, not a DDD rule that every table needs a class or every category needs a subtype. Preserve the useful domain distinctions and continue development rather than abandoning the model because implementation is imperfect.

### Reconstructed calculation shape

```text
accrual = asset.calculateAccrual(period)   // calculation can be side-effect-free
asset.interestAccount.record(accrual)     // command changes recorded state
balance = asset.interestAccount.balance()
```

This sketch captures the separation of calculation and recording; it does not prescribe a complete accounting API. Test the calculation independently, then test assertions about the recording command and resulting balance.

## Worked Example: Discovering a Model in the Nightly Batch

The batch had accumulated rules that appeared to be mere application plumbing. Posting Rules reveal that some of its complexity is domain logic: selecting a ledger and deriving a posting are meaningful policies.

### Posting Rule

A Posting Rule expresses a dependency between an input Account and an output Account. New input Entries lead to derived output Entries. In a generic example, an income entry may generate a calculated tax entry. A calculation Method determines the derived amount where such a separate concept is useful.

The rule establishes a conceptual dependency, but its execution timing must also be explicit:

| Firing mode | Initiator and responsibility | Practical consequence |
|---|---|---|
| Eager firing | Inserting an Entry immediately triggers dependent rules | Updates are immediate but strongly coupled to insertion |
| Account-based firing | A message asks the Account to process entries added since its last firing | Defers work; Account controls discovery of its new entries |
| Posting-Rule-based firing | An external agent starts the rule; the rule finds relevant new entries | Supports batch control without requiring the batch to own the rule's domain logic |

Different modes may coexist in a system, but each rule set needs one clear initiation point and responsibility for identifying input Entries. Include these distinctions in the Ubiquitous Language; object names alone do not resolve execution ambiguity.

### Adapting the pattern

In the chapter's batch example, both accrued interest and payments eventually need posting. Payments arrive during the day, so immediate firing tied to batch-created accruals does not fit the whole process. Posting-Rule-based firing keeps the batch in control of timing while rules find appropriate entries.

The developers also discover that they misused Method. The full entry amount is posted, so no separate amount-calculation policy is currently needed. The real variation is ledger selection based on asset class and whether the Account holds fees or interest. That responsibility belongs to the Posting Rule.

The resulting application script iterates through Assets, sends meaningful messages, and commits transactions. Domain objects carry the complicated business choices. A Posting Service facade provides an intention-revealing interface to the legacy accounting API.

The final example lets Asset select the relevant rules and pass the appropriate Accounts because Asset already knows their roles and is the batch's natural entry point. The source uses singleton access for those rules. Treat that as a local implementation choice, not an endorsement of global singleton state or a required pattern component. The developers recognize that direct Account-to-rule associations would express a cleaner conceptual division, but prefer the more straightforward implementation until they find a better way.

The source describes processing entries since the previous firing, but it does not supply a complete crash recovery or duplicate-posting protocol. When applying this design, establish how progress is recorded and retried rather than assuming the conceptual rule guarantees operational correctness.

## Mental Models and Anti-Patterns

- **Patterns as accumulated experiments:** use prior reasoning to skip avoidable mistakes, then continue experimenting locally.
- **Model fragments rather than a framework:** analysis patterns can be recombined and adapted without importing a complete reusable implementation.
- **Names as shared commitments:** a familiar pattern term earns its value from recognizable meaning. Reusing the name while discarding the meaning creates false confidence.
- **Blind pattern fitting:** the initial Transaction mistakes show that matching diagram shapes can encode false business relationships.
- **Keeping every element:** Method is unnecessary when there is no meaningful amount calculation to vary. Remove it rather than manufacturing complexity.
- **Dismissing scripts as non-domain:** inspect complicated routing, calculation, selection, or accounting decisions before concluding a batch has no model.
- **Ignoring implementation:** an elegant abstraction that cannot survive the actual persistence and maintenance constraints has not yet become a working Model-Driven Design.

## Application Checklist

Before adopting a pattern, identify the local pain, at least one validating scenario, the borrowed concepts, the mismatches, and the implementation implications. After implementation, verify that names still mean what the pattern says and that domain experts can explain the resulting behavior.

## Key Takeaways

1. Reuse modeling knowledge and reasoning, not just class shapes.
2. Keep valuable concepts even when the full pattern does not fit.
3. Make execution timing part of the language when it changes responsibility.
4. Treat pragmatic compromises as visible design decisions that can be revisited.

Related: [implicit concepts](ch09-making-implicit-concepts-explicit.md), [supple design](ch10-supple-design.md), [design patterns](ch12-design-patterns-and-the-model.md), [refactoring process](ch13-refactoring-toward-deeper-insight.md).
