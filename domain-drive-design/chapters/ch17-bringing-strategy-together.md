# Chapter 17: Bringing the Strategy Together

Source: supplied PDF, pages 301-309. The source extraction placed the beginning of the Conclusion's epilogues after this chapter; those belong in the separate [Conclusion guide](conclusion.md).

## Core Idea

Context boundaries, distillation, and large-scale structure solve different problems and reinforce one another. Strategic design must grow from application experience, reach the people doing the work, and remain open to feedback.

## Three Complementary Views

| View | Primary question | What it contributes |
|---|---|---|
| Context and Context Map | Where does each model apply, and how do models connect? | Consistency boundaries and explicit integration relationships |
| Distillation | Which concepts create the most distinctive value? | A visible Core Domain and focused effort |
| Large-scale structure | What role does each major part play in the whole? | Organizing concepts that coordinate design across many pieces |

One does not substitute for the others. A context can be logically consistent yet hard to grasp; a well-layered design can still obscure its core; a highlighted core does not settle translation ownership.

### Combining structure with contexts

A structure may organize a single complex context or the relationships across a Context Map. State the scope in which its names apply.

A legacy system need not be internally refactored to fit every selected layer. Describe which responsibilities it spans. If accessed through a facade, individual offered Services may fit the layers more precisely than the system as a whole.

Different local structures can coexist in different contexts, but too many unrelated organizing schemes weaken the common vocabulary. Prefer useful shared concepts while respecting model differences and control boundaries.

### Combining structure with distillation

Layers can clarify relationships within the core and between supporting subdomains. A major structural distinction can itself capture core knowledge even when no single domain-object model spans all contexts.

For example, separating resources, operations, policy, and decision support may express an important business insight across a system whose local models differ. Core importance is not restricted to a list of classes.

## Assessment First

Start strategic work with six questions, using evidence from the actual project:

1. **Context Map:** can the existing models and relationships be mapped consistently? Where is ownership or meaning ambiguous?
2. **Language:** is there a shared domain language rich enough to guide development?
3. **Importance:** is the Core Domain identified? Is there a useful Domain Vision Statement, or enough understanding to draft one?
4. **Technology:** does the implementation environment support or obstruct Model-Driven Design?
5. **Technical skill:** can the team implement and evolve the intended model?
6. **Domain knowledge and interest:** do developers understand the business and have a way to deepen that knowledge?

Initial answers will be incomplete. Make uncertainty visible, choose an urgent improvement, and refine the assessment as the team learns. Do not wait for a comprehensive architecture exercise before acting.

### Reconstructed assessment record

```text
Observed problem:
Evidence and affected scenarios:
Current context / relationship:
Core-domain relevance:
Language or implementation mismatch:
Proposed small change:
People and agreements involved:
Validation and feedback:
What would make us revise the choice:
```

This is a practical synthesis of the chapter's assessment and feedback principles, not a source template. Fill it with specific findings rather than pattern labels alone.

## Who Sets the Strategy?

The chapter presents two workable arrangements rather than one mandatory organization.

### Emergent structure from application development

A disciplined team with strong communication can develop shared principles through its own work. A hands-on leader or small oversight group may help keep the result coherent by arbitrating and communicating, without becoming the sole source of ideas.

Across a few cooperative teams with compatible needs and capabilities, representatives can assess proposals together and coordinate adoption. This works through real commitment and feedback, not because an informal committee automatically eliminates coordination costs.

### Customer-focused architecture team

A dedicated architecture team can help several application teams coordinate boundaries, structures, and technical issues if it treats those teams as customers and peers. It collaborates in discovery, experiments, and implementation rather than issuing detached designs.

The organization chart may resemble a conventional centralized architecture function. The difference is its working relationship with application development and its willingness to change based on experience.

## Six Essentials for Strategic Design Decisions

### 1. Decisions must reach the entire team

A strategy has value only if relevant developers understand and use it. Formal authority cannot compensate for impractical rules that people bypass. Communicate the meaning, scope, rationale, and implications of changes through the team's actual channels.

### 2. The process must absorb feedback

Application developers encounter the details that determine whether a structure works. Keep a tight loop between proposed strategy and hands-on use. Rotating architecture-team members through application teams is one example of learning in both directions, not a compulsory staffing policy.

### 3. The plan must allow evolution

Higher-level rules must remain revisable as knowledge and requirements change. Preserve enough local design freedom for developers to solve their problems. New application obstacles and opportunities are inputs to strategy rather than departures to suppress.

### 4. Keep strong designers on application teams

Removing every skilled designer into architecture or infrastructure makes the strategy hard to implement. Strategic work also needs people with deep domain knowledge, even when those people are not the strongest technical designers. Effective architecture requires effective application teams as partners.

### 5. Practice minimalism and humility

Keep only organizing principles and abstractions whose clarity benefits justify the constraints they impose. An attractive rule can obstruct a local design the proposer has not seen. Examine the cost at that point of use before adding further rules.

### 6. Objects specialize; developers need broader contact

Narrow object responsibilities are useful. Extending that separation to people can cut off necessary learning. Developers should explore infrastructure, architects should work with application code, and domain and technical knowledge should mix through collaboration.

Strategic and detailed design are different activities, not necessarily different kinds of people. Deep models need both a broad view and precise implementation knowledge.

## Technical Frameworks Follow the Same Principles

Infrastructure can isolate the domain and remove repeated technical work. It can also obstruct expressive models even without intending to shape the domain.

Favor frameworks that give capable developers useful abstractions and power, with feedback from their actual applications. Encapsulating irrelevant detail is valuable. Prepackaging every decision on the assumption that application developers should not design deprives them of tools they need and damages collaboration.

Assess a framework by what it lets a competent application developer express, change, and understand. Respect for the user's work is a better signal than promises to eliminate the need for judgment.

## Worked Application: Coordinating a Shipping Strategy

This synthesized example combines the chapter's views:

1. Map Booking, Transport Network, and legacy Tracking according to their actual models and ownership.
2. Identify reliable delivery under customer requirements as the application's core purpose.
3. Use a few Responsibility Layers to explain capabilities, operational plans, and decision support.
4. Preserve the legacy system's real scope even if it spans several layers. Offer focused Services where feasible.
5. Keep the route translator and its tests explicit at the Booking/Network boundary.
6. Let application experience determine whether a proposed policy belongs in the core, whether a dependency fits the layers, and whether translation remains affordable.
7. Communicate an accepted change to the teams affected and update the current map after the real change.

The result is a coordinated set of decisions. It does not require turning every context into a separately deployed service or reorganizing all teams before useful work can begin.

## Beware the Master Plan

Evans draws on Christopher Alexander's critique of rigid master plans: they can prescribe the whole too precisely while failing to guide local relationships well enough. Changing reality then makes them obsolete or forces awkward conformity.

Use adaptable principles that support incremental growth. This is not an argument against planning, cross-team decisions, or constraints; it is an argument for a planning process that learns through local acts of design.

## Anti-Patterns and Key Takeaways

- An architecture document that never reaches daily decisions is not an effective strategy.
- A clean future-state map cannot replace a truthful description of current integration.
- A central team without domain and application feedback lacks the evidence needed for strategic choices.
- Uniformity becomes costly when rules exceed their demonstrated value.

Begin with assessment, combine the three strategic views, keep application and strategic learning connected, and revise the minimum useful structure as evidence changes.

Related: [refactoring process](ch13-refactoring-toward-deeper-insight.md), [context integrity](ch14-maintaining-model-integrity.md), [distillation](ch15-distillation.md), [large-scale structure](ch16-large-scale-structure.md), [long-term outcomes](conclusion.md).
