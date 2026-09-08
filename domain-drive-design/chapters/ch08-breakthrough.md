# Chapter 8: Breakthrough

Source: supplied PDF, pages 115-122. This short chapter develops one project example rather than a catalog of techniques.

## Core Idea

A deeper model can remove a mistaken assumption that incremental fixes have been preserving. Breakthrough is an event to recognize and evaluate, not a technique that can be scheduled or demanded.

## Recognizing a Candidate Breakthrough

Use this lens when repeated exceptions and technical complications cluster around one organizing assumption.

1. Identify the domain distinction the current model prevents you from expressing directly.
2. State the alternative model and the constraint it removes.
3. Replay both ordinary and previously awkward scenarios with domain experts.
4. Check which constraints remain valid. A simpler model must still preserve the business's actual rules.
5. Estimate the implementation transition, then compare it with the risk and growth cost of staying with the current model.

Why it works: repeated special cases may be symptoms of a bad abstraction rather than separate exceptional situations. A revised model can make formerly awkward requirements ordinary consequences of its concepts.

Evidence includes experts finding the new explanation natural, multiple troublesome scenarios becoming straightforward, and special-case mechanisms becoming unnecessary. A visually simpler class diagram or a new abstraction name alone is insufficient evidence.

## Worked Example: Syndicated Loans and Share Pie

### Initial assumption

A syndicate's lenders commit shares of a Facility. The initial model treats each lender's share of a Loan as derived from that Facility share. This is convenient for the common case but constrains the model more than the business does.

When lenders vary participation in a drawdown, the team adds Loan Adjustments. More exceptions and difficult rounding behavior follow. The developers' term Loan Investment never becomes comfortable for the domain experts, who assume it is a necessary technical concept.

### Counterexample

The borrower first draws $50 million against a $100 million Facility, with lenders participating according to their Facility shares. On a further $30 million drawdown, one lender declines participation and another takes its portion. The outstanding Loan is now $80 million, but its ownership proportions differ from the Facility commitments.

| Movement | Relevant basis |
|---|---|
| Participation in a new drawdown | Actual negotiated participation, which can depart from Facility proportions |
| Repayment of principal | Shares in the outstanding Loan |
| Interest distribution | Loan shares |
| Fee for availability of the Facility | Facility shares |

### New model

Recognize shares as a general domain concept: a divisible value can be apportioned among parties. Share Pie expresses those portions. A Loan and a Facility each have their own share structure instead of one being forced to derive from the other.

The new model removes the inappropriate proportionality constraint while retaining valid total and distribution constraints. Loan Adjustment becomes unnecessary because the Loan's shares can be represented directly. Shared operations on shares make calculations easier to express and combine.

The point is not to use Share Pie outside domains where it has meaning. Identify the assumption your own exceptions work around, and test whether a domain abstraction expresses the real distinction more directly.

### Why the language matters

The experts immediately understand the revised diagrams and use the new vocabulary themselves. That is evidence of improved domain correspondence. The disappearance of a developer-invented term is meaningful because its role was based on a misunderstanding, not because technical abstractions are inherently wrong.

## Evaluating the Transition

The team faced a late project, exhaustion, substantial code changes, and few convenient intermediate stopping points. The manager's decision considered four questions:

| Decision question | What to assess in your project |
|---|---|
| How long to recover existing functionality? | Transition work, dependencies, verification, and interruption |
| Can the current problems be solved without the change? | Credible alternatives and uncertainty, rather than enthusiasm alone |
| Can the next release progress without it? | Future feature cost and whether an installed base will make change harder |
| Is it the right design? | Business fit, simpler reasoning, and comparative long-term risk |

The historical team estimated and completed three weeks of work. That is a fact about this example, not a recommended duration or acceptable downtime target. The story describes a disruptive transition; it is not blanket authorization for a rewrite or bypassing the project's delivery requirements.

Prefer a demonstrable case: show the old and new treatment of representative scenarios, name the removed mechanism, and make the transition risk visible. When implementation changes are involved, retain the project's testing and migration practices.

## Preparing the Ground

Continue ordinary refactoring rather than waiting for a dramatic insight. Knowledge crunching, fluent shared language, explicit domain concepts, supple design, and distillation all make important contradictions easier to see.

After the Share Pie change, transaction concepts become conspicuous by their absence. Drawdowns and fee payments have their own rules, negotiation, and approval processes, yet that behavior is distributed across Loan and Facility methods. Modeling Transactions explicitly then simplifies those objects further.

One breakthrough therefore does not complete the model. It can reveal the next missing concept by reducing the noise around it.

## Key Concepts

- **Breakthrough**: a substantial shift in domain understanding and design, sometimes emerging abruptly after gradual refinement.
- **Deep model**: an abstraction that explains important domain behavior more directly and usefully.
- **Facility**: a commitment making borrowing available under agreed terms.
- **Drawdown**: taking funds against that commitment.
- **Share Pie**: a representation of the distribution of a divisible value among parties.
- **Implicit concept**: domain meaning that code uses without an explicit model element carrying it.

## Mental Models and Anti-patterns

- Treat exceptions as evidence about a model's assumptions before extending the exception machinery.
- Treat increased expressiveness and removed constraints as different things: remove only a constraint the domain does not require.
- Avoid **breakthrough hunting** that delays useful small improvements.
- Avoid **keeping a misunderstood term because experts defer to technical authority**.
- Avoid **automatic rewrite enthusiasm**: the transition must be judged alongside the benefits.
- Avoid **declaring the final model** after one successful change; clearer designs expose further opportunities.

## Key Takeaways

1. Investigate recurring awkwardness that survives local fixes.
2. Test a new perspective with troublesome scenarios and domain experts.
3. Compare transition cost and future development cost explicitly.
4. Keep ordinary learning and refactoring active while remaining open to larger insights.

## Connects To

- [Chapter 1](ch01-crunching-knowledge.md): accumulated domain learning makes deeper models possible.
- [Chapter 9](ch09-making-implicit-concepts-explicit.md): discover concepts hidden in language and behavior.
- [Chapter 10](ch10-supple-design.md): shares and expressive operations in detailed design.
- [Chapter 13](ch13-refactoring-toward-deeper-insight.md): decide when and how to pursue model improvements.
