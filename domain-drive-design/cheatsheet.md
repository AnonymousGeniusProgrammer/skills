# Design Decision Cheatsheet

References: [chapter index](SKILL.md#chapter-index).

## Choose the modeling move

| If you observe... | Prefer this move, because... |
|---|---|
| Experts repeatedly explain around a term | Revisit the concept and code together; language friction reveals mismatch. (2, 9) |
| Sameness must survive changed attributes | Define Entity identity and its scope. Equal state alone is insufficient. (5) |
| Only descriptive meaning matters | Prefer immutable Value Objects; avoid unnecessary identity and life-cycle tracking. (5) |
| A domain operation fits no Entity or value naturally | Model a focused Service; keep other behavior on its natural objects. (5) |
| Separate updates can jointly break a rule | Align the Aggregate's consistency boundary with that invariant. (6) |
| The boundary pulls in an entire catalog | Recheck which values must stay synchronized; snapshot or reconcile only when domain rules allow. (6) |
| Creation obscures an object's purpose | Use a Factory; keep restoring an existing identity distinct from new creation. (6) |
| Access exposes tables rather than concepts | Offer Repository queries for objects needing direct retrieval; retain awareness of cost. (6) |
| One criterion appears in several tasks | Make a Specification; choose task-specific execution mechanisms. (9) |
| Clients must simulate side effects mentally | Separate calculation from commands; state assertions and intent. (10) |
| A familiar pattern almost fits | Keep its useful concepts, remove irrelevant structure, preserve name meanings. (11-12) |

## Choose the relationship

| Conditions | Candidate |
|---|---|
| Integration offers little value | Separate Ways |
| One model and sustained close coordination are feasible | One Bounded Context with Continuous Integration |
| Limited shared concepts and joint stewardship are feasible | Shared Kernel |
| Downstream needs can influence upstream planning | Customer/Supplier Development Teams |
| Upstream will not cooperate; its model is suitable | Conformist |
| Independent local meaning is necessary | Anticorruption Layer |
| Many consumers need coherent capabilities | Open Host Service |
| Parties need stable, understandable interchange | Published Language, possibly with Open Host Service |

Map actual conditions and test contracts. These choices concern semantics and cooperation. (14)

## Spend design effort where it matters

- If refactoring pain traces to the Core Domain, address that cause rather than repeatedly polishing peripheral symptoms. (15)
- If important concepts cannot be located, start with a Domain Vision Statement and Highlighted Core before expensive restructuring. (15)
- If generic support consumes core learning time, establish requirements and compare existing solutions, published models, outsourcing, and implementation. (15)
- If algorithms obscure business intent, seek a better model, then isolate a Cohesive Mechanism when computation has its own coherent meaning. (15)
- If a large consistent model is hard to comprehend, try distillation and minimal structure before splitting its context. (14-16)
- If structural rules repeatedly force awkward decisions, revise or discard them. (16)
- If configuration merely removes all constraints, introduce the domain's permitted rules; use Knowledge Level only where needed. (16)
- If proposing a component framework before several working applications teach stable abstractions, defer it. (16)
- Repair application feedback before adding strategic rules. (17)

## Precision checks

- A build passing does not establish shared model meaning; integrate language too. (14)
- Module, Bounded Context, Aggregate, and deployment unit answer different questions. (5-6, 14)
- Side-effect-free does not imply independence from hidden inputs. (10)
- In Evans's subsumption example: `stronger.subsumes(weaker)` means stronger implies weaker. A limited proof method failing is not proof of non-implication. (10)
- A failed construction heuristic is not proof that no satisfying solution exists. (9)
- Published Language need not be the host's internal model. (14)
- Generic does not require implementing every possible use. Core does not mean every essential component. (15)
- Operations must describe actual events, including policy violations; higher-level decisions handle corrective action. (16)

## Useful proportions, not universal rules

- Keep a Domain Vision Statement about one page and a distillation document about three to seven sparse pages. (15)
- Use short exploration sessions; reduce scope when progress stalls. (13)
- Keep structures simple; roughly four or five layers is already plenty. (16)
- Refactor as understanding improves, but avoid disruptive changes immediately before a release. (13)
