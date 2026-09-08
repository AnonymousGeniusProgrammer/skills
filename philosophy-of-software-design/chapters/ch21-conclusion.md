# Chapter 21: Conclusion

Source: first edition (2018), PDF pages 176-177. Reference notes.

## Core idea

Keep complexity reduction central to design and develop the judgment to recognize it. This short concluding chapter consolidates earlier principles rather than introducing a new framework.

## Synthesis for decisions

- Find dependencies and obscurity behind difficult modifications.
- Use red flags to identify candidates for redesign: information leakage, unnecessary exceptional conditions, vague names, and shallow interfaces.
- Prefer deep, somewhat general-purpose modules and clear separation between interface contracts and implementation details.
- Invest steadily in design and in the ability to compare alternatives.

The expected benefit is easier future work: reusable abstractions, discoverable rationale, and less time spent untangling brittle code. The chapter presents this as the author's experience and argument, not a guaranteed productivity calculation.

## Mental model and limits

Treat design as the search for a simple structure that still solves the real problem. Elegance is supported by reduced caller obligations, clearer responsibilities, and understandable behavior, rather than stylistic preference alone.

Learning the techniques can initially slow work. Use repeated application and feedback to improve judgment, while respecting the qualifications in the individual chapters. The conclusion does not supersede their tradeoffs.

## Key takeaways

1. Revisit complexity as requirements evolve.
2. Practice finding and comparing simpler abstractions.
3. Preserve knowledge that makes future changes easier.

## Connects to

- [Ch 1](ch01-introduction.md): the continuous-design premise.
- [Ch 2](ch02-nature-of-complexity.md): the diagnostic framework.
- [Ch 3](ch03-working-code-isnt-enough.md): the investment mindset.
- [Decision cheatsheet](../cheatsheet.md): cross-chapter lookup.
