# Chapter 3: Working Code Isn't Enough

Source: first edition (2018), PDF pages 25-30. Subtitle: Strategic vs. Tactical Programming. Reference notes.

## Core idea

Treat a working implementation as necessary, while also investing in the structure that future changes will depend on. Repeated shortcuts accumulate costs that make later work progressively harder.

## Frameworks

**Tactical programming** optimizes completing the current feature or fix quickly. Small compromises accumulate, then require further patches; cleanup is repeatedly postponed because the next deadline appears more urgent.

**Strategic programming** includes continuous investment in a design that remains easy to extend. It combines proactive work, such as considering alternative interfaces and documenting their contracts, with reactive work, such as repairing a newly discovered design weakness.

**Tactical tornado** names the extreme case: visible feature output looks impressive while maintenance costs are transferred to colleagues. Evaluate productivity with those downstream costs included.

## How to apply the investment model

1. Identify a source of future change friction in the current work.
2. Consider a small structural improvement or a clearer contract that addresses it.
3. Make the improvement while the relevant context is available.
4. Preserve regular investment through successive increments, rather than relying on a future cleanup phase.

Ousterhout suggests roughly **10-20% of development time** for design investment. Attribute this as his rule of thumb. The productivity curves in the chapter are explicitly qualitative, without empirical measurements of their precise shapes; they do not establish a guaranteed payback date or rate.

## Mental models and limits

- Think of design improvement as an investment whose benefits accumulate across later changes.
- Judge team output across the lifetime of the code, including the work needed to understand and maintain it.
- Keep investments incremental. The strategic approach does not call for designing the entire system before learning from implementation.
- Historical company anecdotes illustrate the author's argument; they are not evidence about those companies' current engineering practices.

## Anti-patterns

- Scheduling all cleanup after the next release: the next release often creates the next reason to defer it.
- Rewarding rapid feature output while ignoring complexity left for others.
- Equating strategic programming with a large speculative rewrite or elaborate advance design.

## Key takeaways

1. Include ease of future modification in design quality.
2. Prefer many small improvements made continuously.
3. Use the investment percentage as guidance, not a universal quota.

## Connects to

- [Ch 2](ch02-nature-of-complexity.md): why small costs accumulate.
- [Ch 11](ch11-design-it-twice.md): proactive design comparison.
- [Ch 16](ch16-modifying-existing-code.md): strategic maintenance.
