# Chapter 2: Communication and the Use of Language

Source: supplied PDF, pages 12-24. Worked examples and decision tables are synthesized from the chapter.

## Core Idea

Use a model-based language in conversation, documents, tests, and code. Difficulties expressing a real situation are evidence about the model; improving the vocabulary and the relationships it expresses is part of design.

## Frameworks and Application

### Ubiquitous Language

Use when experts and developers translate between business explanations and implementation terminology, or different team members use the same word differently.

1. Identify the domain concepts and rules currently represented in the model. Include important operations and relationships, not only class names.
2. Describe an actual scenario in that vocabulary. Ask whether an expert can express the distinctions needed to make the business decision.
3. Resolve ambiguity by trying alternative terms and models. Developers look for inconsistent or underspecified rules; experts look for meanings that misrepresent the domain.
4. Carry the chosen changes into code names, behavior where necessary, diagrams, and active documents. A changed definition can require more than a rename.
5. Use the language in ordinary development and planning conversations. Continue refining it when new situations expose weaknesses.

Why it works: a common vocabulary reduces translation loss, while the model supplies precise relationships with which words can be combined. Persistent use forces misunderstandings into the open before they become incompatible implementations.

The language is not merely a glossary, a list of nouns, or all the experts' existing jargon. It is based on a shared model, refined to serve the application's scope. Technical implementation vocabulary and the experts' broader professional vocabulary can extend beyond it. Within the domain scope being modeled, competing developer and business dialects conceal problems.

This chapter initially assumes one model. For multiple models and teams, use Chapter 14: a language has a bounded context, and one project need not force a single model onto every subsystem.

### Modeling Out Loud

Use spoken scenarios as a lightweight test of candidate models.

- Try a short sentence expressing the purpose of an operation and the domain condition it must meet.
- Notice where speakers fall back on vague phrases, a long enumeration of fields, or storage details.
- Propose a concept that compresses the explanation while preserving the business distinction.
- Replay the scenario and its exception with that concept. Take improvements back into diagrams and code.

This complements diagrams and programming; it does not replace them. A sentence can sound fluent while describing behavior the system does not implement. Test the shared explanation against executable behavior.

### Documents That Contribute to the Project

Choose documents by what they help people understand and do, rather than by a fixed document inventory.

| Need | Useful medium | Check for continued value |
|---|---|---|
| Exact implemented behavior | Code and executable tests | Names and organization still describe what executes |
| Concept meaning and design intent | Short prose with selected diagrams | Developers and experts use its distinctions in discussion |
| Important relationships or an interaction | Small class or interaction sketch | It focuses attention without requiring the whole design |
| Broader domain education | An explicitly explanatory model | Readers can distinguish the teaching view from the implementation model |
| Historical rationale | Archived material with historical status | Readers do not mistake obsolete concepts for current design |

Keep active documents connected to the language used now. If their terms never enter conversation or code, investigate whether they are too large, peripheral, or unclear. If the language has moved on and nobody uses the document, archiving it may be better than maintaining it by force.

## Worked Example: Cargo Routing and a New Customs Requirement

Consider cargo with an existing itinerary. An operator adds a customs clearance point. A storage-oriented discussion immediately proposes deleting shipment rows and rebuilding them whenever fields change. This sounds implementable, but it hides the cost of changing an already coordinated itinerary.

Introduce two separate concepts:

- **Route Specification** states the conditions the route must meet.
- **Itinerary** is the current plan that may or may not satisfy those conditions.

Now the conversation can ask whether a changed specification invalidates the current itinerary. The domain expert explains that replanning creates operational work and should happen only when necessary. The resulting decision is uniform: keep a satisfying itinerary and seek a replacement for one that fails the specification.

| Change | Existing itinerary | Decision |
|---|---|---|
| A customs point is specified for the first time | Already passes through the required point | Keep the itinerary |
| A customs point changes | Does not satisfy the revised constraint | Find a new itinerary |
| Origin or destination changes | Fails the revised specification | Apply the same satisfaction check; then replan |

Reconstructed pseudocode, showing the idea rather than an API prescribed by the book:

```text
when route requirements change:
    revise the Route Specification
    if the current Itinerary does not satisfy it:
        obtain an Itinerary from the Routing Service
```

The improvement is not just replacing database names with business nouns. The model reveals a condition that changes program behavior and eliminates special-case reasoning about which individual field changed. The sentence describing the operation, the acceptance example, and the implementation can now express the same rule.

To apply this in another domain, find a conversation where a proposed technical shortcut loses a meaningful exception. Separate the desired conditions from the proposed solution when the domain supports that distinction; do not mechanically introduce a specification class everywhere.

## Worked Example: Explaining a Shipping Route

A detailed object model can describe port operations, voyages, and the construction of a route, yet remain difficult for a newcomer to understand. A separate visual explanation can show cargo being loaded, waiting in storage, traveling, and being unloaded.

Use the explanatory view to establish domain meaning and then connect it to the more rigorous design view. Explain which parts correspond and where the teaching picture simplifies the situation. The two views can reinforce each other without having identical elements.

This distinction prevents a useful illustration from becoming a second, competing specification. Explanatory models may include broader context deliberately excluded from the implementation model. Label their purpose so an agent or developer does not infer classes from every shape.

## Key Concepts

- **Ubiquitous Language**: the shared model-based language used throughout a team's work within its applicable context.
- **Modeling out loud**: testing model alternatives by expressing scenarios through their concepts and relationships.
- **Explanatory model**: a teaching view of the domain that need not correspond precisely to the software model.
- **Executable bedrock**: running code and tests as the most direct evidence of implemented behavior.
- **Design document**: supporting material that explains meaning, important structure, or intent that is difficult to see in code.
- **Linguistic split**: divergent meanings or vocabularies that force translation and conceal incompatible models.

## Mental Models and Anti-patterns

- Treat an awkward domain sentence as a modeling clue. Investigate the concept before merely polishing the prose.
- Treat code, conversation, and diagrams as complementary ways to exercise one model.
- Avoid **a bilingual bottleneck**: one person translating between teams cannot substitute for shared understanding.
- Avoid **shielding experts from the domain model**: their inability to understand its central abstractions can reveal a modeling failure.
- Avoid **comprehensive diagrams as the whole model**: relationship pictures omit much meaning, behavior, and constraints while becoming difficult to read.
- Avoid **stale documents with current authority**: old terminology can reintroduce a discarded model.
- Avoid **equating execution with truthful naming**: a passing method can still have a misleading name or test narrative.

## Key Takeaways

1. Give domain rules and relationships a shared language, not just the domain's obvious nouns.
2. Change the implementation when a clarified meaning changes the model.
3. Use small diagrams and prose to illuminate what code makes hard to see.
4. Keep a document active because it contributes to current work, not because it once existed.
5. Mark educational views clearly and connect them to the implemented model.

## Connects To

- [Chapter 1](ch01-crunching-knowledge.md): conversations are part of the learning loop.
- [Chapter 3](ch03-binding-model-and-implementation.md): the implementation must express the shared model.
- [Chapter 9](ch09-making-implicit-concepts-explicit.md): discover and express specifications.
- [Chapter 14](ch14-maintaining-model-integrity.md): delimit language and model consistency across contexts.
