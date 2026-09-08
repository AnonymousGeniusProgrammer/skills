# Chapter 3: Sensing and Separation

Source: printed pp. 21-28; PDF pp. 44-51.

## Core idea

Classify a dependency problem by what the test lacks: the ability to run code independently (**separation**), the ability to observe its effects (**sensing**), or both. A replacement collaborator can solve both problems, but a no-op replacement supplies separation alone.

## Frameworks

**Separation:** use it when a constructor or method drags in hardware, a network, a database, a large object graph, or another unsuitable dependency. Locate the interaction, provide a substitutable boundary, and arrange a test-specific implementation through that boundary.

**Sensing:** use it when an interesting result disappears into another subsystem. Replace the receiving collaborator with one that records the values or events the test needs. Assert the observation after exercising the real logic.

For each replacement ask:

1. What production behavior does this collaborator supply?
2. Which part prevents the test from running?
3. Which interaction must the test observe?
4. What is the smallest interface and recording state that serves this test?
5. What real integration behavior remains outside its evidence?

This works because the algorithm under test stays real while the inconvenient boundary becomes controllable. It fails when the fake reimplements the same algorithm or merely returns the desired answer without observing what the real unit did.

## Worked example

Compact reconstruction of the chapter's cash-register display example. A `Sale` sends a formatted item line to a display. The real display talks to a device; the fake records a line.

```java
interface Display {
    void showLine(String line);
}

final class RecordingDisplay implements Display {
    private String lastLine = "";

    public void showLine(String line) {
        lastLine = line;
    }

    String lastLine() {
        return lastLine;
    }
}
```

Use the actual `Sale` with this display, scan the known product, then compare the recorded line with the baseline output. The source example uses barcode `"1"` and the line `"Milk $3.99"`.

The fake has **two sides**:

- Production-facing: `showLine`, exposed through `Display`, is all `Sale` needs.
- Test-facing: `lastLine` lets the test inspect what happened.

Keep the inspection API on the fake, rather than expanding the production display interface solely for assertions. Retain the fake's concrete type in the test while passing it through the production interface to `Sale`.

A last-value recorder is enough for a single meaningful write. If order or repeated writes matter, use a list of events; otherwise a final correct value could hide an earlier incorrect action. This extension applies the sensing model to a richer observation requirement.

## Fakes and mocks

The chapter uses **fake object** broadly for a substitute collaborator and describes a **mock object** as a fake with internal assertions. With a mock, set the expected call, exercise the real unit, and verify the expectation. Keep this vocabulary distinct from later taxonomies that reserve "fake" for a working simplified implementation.

Choose a simple recording fake when it makes the observation clear. A mock framework is optional; the technique does not depend on one. Expectations on interactions are useful when the interaction is the relevant behavior, but excessive assumptions about internal call sequences can obstruct legitimate refactoring.

## Mental models

- A fake is an observation instrument, not evidence that the replaced hardware or service works.
- A passing isolated test reduces the places where an integration defect could reside; it need not validate the entire system to be useful.
- A dependency can be easy to replace yet hard to observe, or easy to observe yet impossible to construct safely. Diagnose these independently.

## Apply it

State the boundary removed, the event/value recorded, and the remaining integration obligation. The test should execute the production logic, reach the chosen seam, and fail if its relevant output or interaction changes.

## Connects to

- [Ch 4](ch04-seam-model.md): the seam and its enabling point.
- [Ch 10](ch10-method-into-test-harness.md): inaccessible side effects.
- [Ch 19](ch19-procedural-code.md): record effects through replacement functions in procedural programs.
