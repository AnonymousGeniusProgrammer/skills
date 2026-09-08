# Chapter 14: Dependencies on Libraries Are Killing Me

Source: printed pp. 197-198; PDF pp. 220-221. This is a short judgment chapter; detailed mechanics are in Ch 15 and Ch 25.

## Core idea

Use libraries through boundaries that preserve the ability to substitute their behavior. Scattered concrete library calls make both testing and later replacement expensive.

## Decision rules

- When a library already offers a suitable interface, consume the interface at the collaboration that needs substitution.
- When the relevant class is sealed/final, non-overridable, or inaccessible to construction, introduce a thin project-owned wrapper for the operations needed.
- When a singleton cannot be replaced inside the library, wrap access to it on the application side.
- When considering a restrictive language feature in your own code, account for production and test use. Use a convention instead when it provides sufficient discipline without obstructing necessary substitution.

The **once dilemma** occurs when a design assumes one instance for an application but tests need independent instances or selections. The **restricted override dilemma** occurs when methods cannot be replaced to achieve sensing or separation.

## Worked example

Reconstruction of the chapter's vendor-dependence scenario: a team scatters direct calls to one commercial library throughout its application. When the vendor's terms become unfavorable, replacement amounts to reconstructing large parts of the program.

For a local change in such a system, find the specific library interaction needed by the affected logic. Put that interaction behind an owned protocol and delegate to the existing vendor implementation. Use a substitute to test the logic. Expand the boundary as relevant work reaches neighboring calls; a local testing need does not require wrapping the vendor's entire API.

## Trade-offs and takeaways

A wrapper adds code and must itself delegate correctly. It protects application decisions and testability without proving that the real library works. Keep integration evidence for important adapter behavior.

Feathers' critique of restrictive APIs is a testability argument. It is not a general instruction to remove meaningful security or lifecycle constraints from third-party code. Work at a boundary the project owns.

## Connects to

- [Ch 10](ch10-method-into-test-harness.md): adapting inaccessible parameters.
- [Ch 15](ch15-all-api-calls.md): compare thin wrapping with responsibility-based extraction.
