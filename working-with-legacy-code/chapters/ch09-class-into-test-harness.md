# Chapter 9: I Can't Get This Class into a Test Harness

Source: printed pp. 105-136; PDF pp. 128-159.

## Core idea

Attempt a **construction test** before planning a broad redesign. Let compile/link errors and runtime failures reveal the actual blockers, then break only the dependencies necessary for the behavior you need to test.

A construction test can initially contain no assertion: its question is whether the object can be created in the harness. Once that is solved, replace or develop it into a meaningful behavior test. Successful construction is progress, not coverage of the class.

## Route by the obstacle

| Book scenario | Evidence | Candidate techniques |
|---|---|---|
| Irritating Parameter | A required collaborator is difficult to build or runs unsuitable operations | Extract Interface; Subclass and Override Method; narrowly applicable Pass Null |
| Hidden Dependency | The constructor internally obtains a service or resource | Parameterize Constructor; Extract and Override Factory Method/Getter |
| Construction Blob | Interdependent construction makes parameterizing everything invasive | Extract and Override Factory Method where dispatch permits; Supersede Instance Variable where construction is tolerable |
| Irritating Global Dependency | Construction or execution reaches a singleton/global | Introduce Static Setter for initial access; later parameterize or encapsulate references |
| Horrible Include Dependencies | C++ test compilation/linking pulls in extensive dependencies | Add needed declarations deliberately; Link Substitution or Definition Completion in a separate test target |
| Onion Parameter | Creating one argument recursively requires a large graph | Substitute the immediate collaboration through an interface |
| Aliased Parameter | One collaborator must satisfy several existing concrete/base-type uses | Keep the type relationship and replace only the problematic method |

Read [Ch 25](ch25-dependency-breaking-techniques.md) for the selected transformation's mechanics.

## Irritating parameters and Pass Null

Determine which behavior the test actually needs from each constructor argument. A fake through a narrow interface is useful when it needs controlled behavior. A subclass can work when only one operation is troublesome and construction itself is tolerable.

**Pass Null** is a test-setup probe for an argument that is not used on the exercised path, in a language where null dereferences reliably become caught test failures. Feathers discusses Java and C# and cautions against applying this reasoning to ordinary C/C++ pointer dereferencing.

Keep the scope explicit: it establishes that this path did not need that collaborator. It neither validates all paths nor justifies a new null protocol in production. When the collaborator becomes relevant, provide an actual suitable object. If constructor validation intentionally requires a non-null collaborator, preserve that contract and use a valid substitute.

The **Null Object Pattern** is a separate design choice: an object supplies legitimate neutral behavior so callers need no special case. It is appropriate only when callers need not distinguish absence from successful work. In the chapter's employee example, incrementing a "paid employees" count after every call is wrong if some objects represent no employee.

## Hidden dependency: parameterize construction

Reconstruction of the mailing-list dispatcher example. The original constructor creates a mail service internally. Add a constructor taking the service, and preserve the original entry point by delegating with the same production service:

```csharp
public MailingListDispatcher() : this(new MailService()) { }

public MailingListDispatcher(IMailService service)
{
    this.service = service;
    InitializeService();
}
```

The interface is included here as the next separation step described in the source. Tests supply a recording service; production retains its original setup route. Check construction order, registration calls, error handling, and resource ownership. Passing a fake is insufficient if field initializers or static initialization still create the real service.

## Construction blobs and dispatch

A constructor that builds a panel, configures it, and uses it to build a focus widget has a dependency embedded in a sequence. Moving that entire sequence into every client would enlarge the untested change.

An extracted factory hook can be useful in Java/C#, but an override may run before subclass initialization is complete. Keep it independent of uninitialized subclass state. C++ calls made during base construction do not dispatch to derived-class overrides, so that route does not solve the same problem there.

**Supersede Instance Variable** replaces a collaborator after construction. It helps only if constructing the original object is acceptable. It cannot prevent side effects that have already happened. In C++, determine who owns the old and new objects and what destruction does; in garbage-collected languages, replacing a reference still does not close unmanaged resources or unregister callbacks.

## Globals and singleton state

An **Introduce Static Setter** seam allows tests to select the singleton instance, possibly through an interface or test subclass. This provides a foothold when rewriting all callers would be excessive.

Treat each test as a small application: save and restore shared state, including on failure, and avoid concurrent tests that compete for the same global selection. A setter creates substitutability, not independence between tests.

Later, use constructor/method parameters or a localized context to reduce global reach. First search actual uses; globally accessible does not mean globally needed. If almost every class needs a database object, look for responsibilities that should operate on already-loaded data instead.

## Onion and aliased parameters

For a GUI pane requiring a scheduling task that in turn requires a scheduler and resolver, start at the immediate task boundary. The pane may need only a small public protocol. An interface can expose inherited operations as well, subject to the target language's implementation rules.

In the permit example, an `OriginationPermit` is also used as a base `Permit` and passed to a repository. Extracting an interface only for the leaf type creates incompatible assignments; extracting parallel interfaces for the entire hierarchy is expensive. If database access is isolated in `validate`, a test subtype overriding that operation retains the existing type relationships with much less change. Confirm that the overridden method is a dependency rather than the logic the test is meant to validate.

## C++ include and link obstacles

Start with the class header and add required includes one at a time. Use compiler/linker evidence to decide whether a missing dependency belongs in the test or should be replaced. A separate test executable can supply replacement method definitions, but each executable must contain the appropriate single definition. Keep the production implementation and test replacement in distinct link configurations.

## Completion and failure modes

The target can be constructed with controlled collaborators, production construction still follows its intended path, and a behavior test exercises the real logic. Record any ownership, shared-state, or untested-construction constraints. Avoid solving a local test obstacle by introducing an unnecessary interface hierarchy or moving a large unprotected construction algorithm.

## Connects to

- [Ch 10](ch10-method-into-test-harness.md): construction can succeed while execution remains blocked.
- [Ch 23](ch23-not-breaking-anything.md): preserve signatures and use compiler feedback.
- [Ch 25](ch25-dependency-breaking-techniques.md): detailed dependency-breaking catalog.
