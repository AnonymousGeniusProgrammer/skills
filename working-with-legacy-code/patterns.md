# Technique inventory

Each row gives **when to use**, **how**, and the principal **trade-off**. Follow its chapter for the procedure and source pages.

## Change, test, and design techniques

| Technique | When / how | Trade-off |
|---|---|---|
| Legacy Code Change Algorithm; Cover and Modify | Locate changes and observations, break blockers, characterize, then change ([2](chapters/ch02-working-with-feedback.md)) | Initial seam edits may lack cover |
| Fake / Mock Object | Substitute a collaborator to supply behavior or record/check interactions ([3](chapters/ch03-sensing-and-separation.md)) | Real integration remains separate |
| Object / Link / Preprocessing Seam | Select behavior through objects or build configuration ([4](chapters/ch04-seam-model.md)) | Different scopes and enabling points |
| Sprout Method / Sprout Class | Test a new step separately, then connect it ([6](chapters/ch06-not-much-time.md)) | Old code and wiring need checks |
| Wrap Method / Wrap Class / Decorator | Add behavior around an existing operation/object ([6](chapters/ch06-not-much-time.md)) | Order and delegation matter |
| TDD | Failing behavior test, minimal implementation, refactor ([8](chapters/ch08-adding-features.md)) | Requires a workable harness |
| Programming by Difference | Add a subclass, then remove duplication ([8](chapters/ch08-adding-features.md)) | Preserve substitutability |
| Pass Null / Null Object Pattern | Probe unused arguments / provide legitimate neutral behavior ([9](chapters/ch09-class-into-test-harness.md)) | Distinct techniques; absence may matter |
| Command/Query Separation | Separate decisions from state-changing operations ([10](chapters/ch10-method-into-test-harness.md)) | Preserve sequencing |
| Effect Sketch | Follow change influence to observations ([11](chapters/ch11-what-methods-to-test.md)) | Include aliases and hidden effects |
| Interception / Pinch Point Selection | Cover several effects at a useful boundary ([12](chapters/ch12-many-changes-one-area.md)) | Broader tests localize less precisely |
| Characterization / Targeted Testing | Record baseline; select change and connection risks ([13](chapters/ch13-characterization-tests.md)) | Observation is not specification |
| Skin and Wrap | Isolate API calls behind local methods, then an interface ([15](chapters/ch15-all-api-calls.md)) | Wrapper can mirror a poor API |
| Responsibility-Based Extraction | Move coherent application work behind a boundary ([15](chapters/ch15-all-api-calls.md)) | Requires understanding that responsibility |
| Sketching / Listing Markup / Scratch Refactoring | Draw, annotate, or restructure a disposable copy ([16](chapters/ch16-understanding-code.md)) | Exploration is not a verified production edit |
| System Story / Naked CRC / Conversation Scrutiny | Explain structure, model objects, inspect assumptions ([17](chapters/ch17-system-structure.md)) | Diagrams need code evidence |
| Responsibility Heuristics / Feature Sketches | Group methods, inspect decisions and data use ([20](chapters/ch20-large-classes.md)) | Heuristics suggest, not prove, boundaries |
| Duplication Removal / Extract Differences | Name repeated operations; isolate variation before sharing ([21](chapters/ch21-duplication.md)) | Preserve distinct semantics |
| Introduce Sensing Variables | Observe an intermediate effect during extraction ([22](chapters/ch22-monster-methods.md)) | Temporary internal coupling |
| Extract What You Know | Extract tiny understood low-count fragments, then test ([22](chapters/ch22-monster-methods.md)) | Low count is not proof of safety |
| Gleaning Dependencies | Cover critical logic before extracting surrounding dependencies ([22](chapters/ch22-monster-methods.md)) | Remaining behavior stays less protected |
| Skeletonize Methods / Find Sequences | Expose control flow / group guarded operations ([22](chapters/ch22-monster-methods.md)) | Choose by current insight |
| Hyperaware / Single-Goal Editing | Know the behavioral intent; finish one transformation ([23](chapters/ch23-not-breaking-anything.md)) | Requires short feedback loops |
| Preserve Signatures / Lean on the Compiler | Retain declarations; navigate errors after structural edits ([23](chapters/ch23-not-breaking-anything.md)) | Silent binding changes escape compilation |
| Pair Programming | Have another reader track intent and edit details ([23](chapters/ch23-not-breaking-anything.md)) | Use available review practices |
| Extract Method | Move a fragment intact, connect inputs/results, verify ([appendix](chapters/appendix-extract-method.md)) | Data flow and types can change |

## Dependency-breaking catalog

Recipe groups: [A: extraction](chapters/ch25a-parameters-and-extraction.md), [B: interfaces/objects](chapters/ch25b-interfaces-and-supplied-objects.md), [C: relocation/substitution](chapters/ch25c-relocation-and-substitution.md). The [full catalog](chapters/ch25-dependency-breaking-techniques.md#complete-catalog) links every technique directly.

| Technique | When / how | Trade-off |
|---|---|---|
| Adapt Parameter (A) | External parameter: wrap behind a narrow interface | Translation errors |
| Break Out Method Object (A) | Long method: move work into an invocation object | Lifecycle and receiver coupling |
| Definition Completion (A) | Extreme link blockage: supply alternate definitions | Duplication and build isolation |
| Encapsulate Global References (A) | Related globals: group, then supply explicitly | Grouping alone leaves globals |
| Expose Static Method (A) | No instance context: extract static body | Static dependencies can remain |
| Extract and Override Call (A) | One external call: add a forwarding hook | Replaced call lacks real integration coverage |
| Extract and Override Factory Method (A) | Constructor creation: override factory | Constructor dispatch and field initialization |
| Extract and Override Getter (A) | Deferrable creation: use lazy access hook | Timing and ownership changes |
| Extract Implementer (B) | Keep abstraction name: move implementation down | Hierarchy and construction migration |
| Extract Interface (B) | Owned type: extract client-required operations | C++ virtual dispatch hazards |
| Introduce Instance Delegator (B) | Static service: add supplied object forwarder | Transitional mixed interface |
| Introduce Static Setter (B) | Shared service: replace global provider | Shared state and lifecycle |
| Link Substitution (B) | External definitions: select fakes in test build | Build/ABI correctness |
| Parameterize Constructor (B) | Internal collaborator: accept it, retain original forwarder | Initialization order and ownership |
| Parameterize Method (B) | Per-call collaborator: accept it, retain original forwarder | Creation timing and result use |
| Primitivize Parameter (B) | Entangled domain objects: compute over simple representation | Translation gap and copied data |
| Pull Up Feature (C) | Independent cluster: move into abstract superclass | Temporary inheritance structure |
| Push Down Dependency (C) | Environmental code: move into production subclass | Moved policy needs separate tests |
| Replace Function with Function Pointer (C) | Procedural calls: substitute initialized callbacks | Indirection and shared selection |
| Replace Global Reference with Getter (C) | Client uses global: override local accessor | Other clients retain global access |
| Subclass and Override Method (C) | Existing boundary: substitute test methods | Overridden logic is untested |
| Supersede Instance Variable (C) | Later operations need fake: replace after construction | Earlier side effects remain |
| Template Redefinition (C) | Type variability: instantiate with fake, alias production | Compilation cost and specialization gaps |
| Text Redefinition (C) | Replace loaded definitions in a test context | Replacement may persist across tests |
