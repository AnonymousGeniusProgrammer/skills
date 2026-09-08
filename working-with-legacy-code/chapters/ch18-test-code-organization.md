# Chapter 18: My Test Code Is in the Way

Source: printed pp. 227-229; PDF pp. 250-252. This short chapter concerns navigation and build layout.

## Core idea

Organize test code so developers can distinguish roles and move between a production unit and its tests cheaply. Choose conventions by their effect on daily work and packaging, not by visual tidiness alone.

## The book's naming example

| Role | Example | Reason |
|---|---|---|
| Production class | `CheckingAccount` | Names the domain object |
| Test cases | `CheckingAccountTest` | Sorts next to the production class |
| Substitute collaborator | `FakeAccountOwner` | Makes the replacement role clear |
| Subclass exposing/separating the subject | `TestingCheckingAccount` | Distinguishes a testing seam from a collaborator fake |

Feathers presents preferences, not mandatory spellings. Preserve an established project convention when it already supports navigation and role recognition.

## Worked example

Reconstruction of the chapter's Java layout: production and test roots can both contain the same package path. The IDE can present them together even though the build packages only the production root. This preserves navigational proximity and allows separate deployment.

Another option is keeping files together and using build selection to exclude tests. Inspect the existing build and packaging rules before changing location. Physical proximity, logical package membership, and deployment inclusion are separate choices.

## Decision rules

- If moving a test makes production/test navigation harder, account for that recurring cost.
- If tests must stay out of the delivered artifact, use explicit build/package boundaries.
- If a tool already navigates seamlessly between separate roots, separate directories need not impose the tax described in the book.
- Distinguish `Fake...` collaborators from `Testing...` subclasses so a reader can tell what is being replaced and what real behavior is still under test.

## Failure modes and completion

Avoid a large directory reorganization solely to follow a book example. For the relevant tests, verify discoverability, a working focused test command, and correct production packaging. Naming is useful when it makes those operations easier.

## Connects to

- [Ch 5](ch05-tools.md): harness and fixture behavior.
- [Ch 7](ch07-slow-changes.md): physical organization and build feedback.
