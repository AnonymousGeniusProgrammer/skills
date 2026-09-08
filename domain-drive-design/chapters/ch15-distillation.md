# Chapter 15: Distillation

Source: supplied PDF, pages 247-271, with the introductory distillation discussion immediately before the chapter heading.

## Core Idea

Identify and clarify the part of the model that creates the application's distinctive value. Allocate learning, modeling, and design effort accordingly. Necessary supporting software can be technically difficult and valuable without being the Core Domain.

Strategic distillation helps the team understand the whole, share a manageable central model, choose refactoring targets, allocate skilled people, and decide what to buy or outsource.

## Core Domain

The Core Domain contains concepts distinctive and central to the intended application's purpose. Make it small, visible, and sufficiently supple to deliver that purpose.

1. State the application's distinguishing value in domain terms.
2. Identify the concepts and relationships directly responsible for that value.
3. Separate necessary but generic or supporting responsibilities.
4. Put skilled, committed developers together with experts to deepen the core model.
5. Revisit the choice as knowledge and product goals develop.

Core is contextual. Currency conversion may be generic support in one application and part of the differentiating model in a trading system. Even there, generic money behavior may be factored away from specialized trading concepts. Widely referenced objects, business importance in the abstract, and algorithmic difficulty do not independently determine core status.

### People and control

Long-term developers must accumulate specialized knowledge. Assigning all strong developers to infrastructure while leaving the core poorly modeled undermines the software's value. Temporary expertise can help through mentoring or focused supporting work; a transient team is less suited to being the sole repository of core knowledge.

When a framework tightly constrains the supposed core, ask whether it is obstructing the distinctive asset, whether that area was misclassified as core, or whether the needs are generic enough that purchasing software makes more sense. The source's assessments of specific historical frameworks describe its era; use the decision principle with current evidence for an actual procurement choice.

## An Escalation of Distillation Techniques

| Technique | What changes | Use it when |
|---|---|---|
| Domain Vision Statement | Shared description of value | Direction is needed before or beyond detailed modeling |
| Highlighted Core | Navigation and visible marking | The core is hard to identify, but structural change is premature or costly |
| Generic Subdomains | Model and module boundaries | Supporting domain concepts obscure the distinctive model |
| Cohesive Mechanisms | Encapsulation of computation | The algorithmic how overwhelms the expressive what |
| Segregated Core | Direct separation and refinement of core code | Removing peripheral pieces alone leaves the core entangled |
| Abstract Core | Fundamental abstractions and cross-module interactions | A deeper model can express the whole through a small coherent abstraction set |

These are not mandatory maturity stages or a fixed sequence. Select the investment that addresses the actual obstruction.

## Generic Subdomains

Factor a cohesive supporting domain model into separate Modules and keep specialized business concepts out. Develop only the coherent subset needed now. Generic does not mean fully generalized or designed for future code reuse.

Organization charts, ordinary accounting, and date/time behavior can be generic in a particular project while remaining essential and nontrivial. Their correctness still matters; their development should consume resources in proportion to how they support the core.

### Choosing an implementation source

| Option | Potential benefit | Cost to examine |
|---|---|---|
| Off-the-shelf implementation | Less code and possibly mature maintenance | Evaluation, correctness, excess features, integration, platform and model dependencies |
| Published model/design | Experienced concepts and documentation | Poor local fit or unnecessary generality; still requires implementation |
| Outsourced implementation | Protects core learning time; encourages an explicit interface | Specification, acceptance tests, quality variation, and ownership transfer |
| In-house implementation | Direct fit and easier integration | Underestimated development, training, and continuing maintenance |

Published designs can be combined with either in-house or outsourced implementation. A self-consistent subset is often sufficient. For external implementation, clarify acceptance criteria and test expectations rather than treating the interface description as self-evident.

### Worked example: Two time-zone efforts

The shipping team first develops its core with provisional time handling, then establishes a concrete need for accurate international scheduling conversions. After investigating available solutions, it assigns a capable temporary developer to adapt existing time-zone knowledge and data. The generic implementation is separate from shipping concepts.

The insurance team starts a general time-zone model before it has a working application or clear conversion requirements. A junior developer and then a senior developer invest heavily while the core remains unexplored. The application may only have needed reference times tagged with zones. Both teams separated generic code correctly; their priorities and evidence differ sharply.

This historical example does not recommend rebuilding time-zone libraries or assuming particular regions have simple rules today. Its lesson is to establish actual requirements, assess existing options, and protect the team's opportunity to learn the core.

### Risk management

An early end-to-end slice should test the difficult domain assumptions as well as the technical architecture. A peripheral feature can demonstrate infrastructure while leaving the project's main risk untouched. Unless domain and modeling capability are already well proven, include at least a small part of the core in that first slice.

## Domain Vision Statement

Write roughly one page explaining what is distinctive about the domain model, the value it enables, and how it balances relevant interests. Keep it narrow and revise it with learning.

For an airline booking model, this might describe balancing passenger priorities with airline policies and representing the customer relationship needed for those choices. For factory automation, it might describe material and equipment state sufficient for traceability and automated routing.

UI polish, server layout, implementation technology, generic authentication, and response-time requirements may be important project requirements. They are not the central content of this particular document. A Domain Vision Statement must guide modeling and investment, not become a complete project specification.

## Highlighted Core

Use a low-cost navigation aid when a vision statement is too abstract to identify the actual core elements.

### Distillation Document

Describe the principal core concepts and interactions in a very short document; Evans suggests three to seven sparse pages. It is an entry point into the real model and code, not a duplicate exhaustive design specification. Use a few explanations, scenarios, and selective diagrams that nontechnical collaborators can understand.

### Flagged Core

Mark the relevant elements in the primary model representation: code, diagrams, or existing documentation. In the insurance example, identifying the essential portions of a huge purchased model lets a focused prototype emerge without first rewriting the entire document.

Use whatever marking the team can recognize easily. The objective is reliable membership visibility, not a prescribed annotation syntax.

### Coordination signal

If a proposed model change alters the distillation document's core elements, relationships, or boundary, it signals a strategically significant change that requires team consultation and communication. This is a suggested project coordination practice, not a requirement to ask for approval on every code edit. Keep existing project authority and change processes in force.

## Cohesive Mechanisms

When a conceptually coherent computation obscures the domain model, isolate it behind an intention-revealing interface. First seek a better model that makes the computation simpler; extract a mechanism when its own coherent responsibility becomes apparent.

1. State the domain fact, rule, or problem the expressive model owns.
2. Identify a bounded computation or known formalism that resolves it.
3. Implement only the needed capabilities in a lightweight component or framework.
4. Expose meaningful operations and assertions; keep domain-specific interpretation in the domain model.
5. Test the mechanism independently and verify the domain's use of it.

Avoid a catch-all Calculator containing unrelated algorithms. A Generic Subdomain represents an aspect of the domain; a Cohesive Mechanism solves a computational problem posed by that representation.

### Worked example: Organization chart and graph traversal

The organization model expresses reporting relationships and asks who has authority or capability in a branch. A graph mechanism performs traversal using nodes and edges. The expressive model supplies the organizational meaning; the graph supplies the computation.

A later refactoring folds node behavior into organizational Entities to reduce object and package overhead. It preserves the declarative public interface and encapsulated mechanism. This is not simply reversal: the intermediate separation taught the team which responsibilities had to remain distinct.

A mechanism can itself be core when a specialized algorithm creates the software's distinctive value. Further insight may eventually separate underlying rules from the solver, but that next refactoring still needs a plausible model and a worthwhile cost-benefit case.

## Segregated Core

Use this when a large important model's core remains obscured by mixed responsibilities and supporting residue.

1. Identify a coherent core subdomain.
2. Move its concepts into a Module named for their domain meaning.
3. Separate data and behavior not directly expressing that core; place them outside it.
4. Clarify core interactions and dependencies, then reduce unnecessary coupling.
5. Repeat for other core portions and refine the shared definition as the team learns.

The supporting residue need not all become beautifully modeled before the core can be clarified. Some local cohesion may be sacrificed to strengthen the more valuable core cohesion. Acknowledge the resulting cross-package complexity and refactoring effort. Moving classes without separating responsibilities may not achieve the objective.

### Worked example: Delivery as shipping's core

The application vision emphasizes reliable fulfillment of customer requirements for frontline operations, so billing plays a supporting role. A Delivery package brings the relevant core concepts together.

Customer Agreement now explicitly constrains Handling Step and is attached directly to Cargo. Delivery scenarios no longer navigate through a Customer in the correct role merely to find the agreement. Customer itself can therefore be supporting even though satisfying customer requirements motivates the system.

Leg initially stays with scheduling and routing support because that grouping has strong cohesion. If important core scenarios repeatedly need it, its classification should change. These are choices made for this application's purpose, not universal placements of Customer or Leg.

## Abstract Core

Where major interactions can be expressed through fundamental domain abstractions, put those abstractions in a small Module and leave specialized implementations in subdomain Modules. Specialized pieces can then depend on the Abstract Core instead of each other.

This is a conceptual horizontal slice, not a bucket of every frequently referenced interface. Require meaningful domain abstractions and useful behavioral contracts. Developing it usually needs substantial insight and redesign; it cannot be generated by counting imports or moving all base classes.

## Choosing Refactoring Targets

For an immediate pain, trace whether its root involves the core or the core's relationship to supporting code. Address that cause when feasible instead of repeatedly polishing symptoms. When choosing freely, favor clearer core modeling, better segregation, and purer supporting subdomains.

Do not attempt to improve everything uniformly. The strategic question is where a clearer model will most improve the software's distinctive behavior and the team's ability to evolve it.

## Key Takeaways

1. Necessary is not synonymous with core; core follows the application's distinctive purpose.
2. Protect sustained domain learning and put strong developers where it matters most.
3. Start with visible direction and navigation when deep restructuring is not yet justified.
4. Separate expressive domain concepts from supporting computation without worshiping the resulting package structure.
5. Let deep models and actual scenarios refine the definition of the core over time.

Related: [analysis patterns](ch11-applying-analysis-patterns.md), [supple design](ch10-supple-design.md), [context boundaries](ch14-maintaining-model-integrity.md), [large-scale structure](ch16-large-scale-structure.md).
