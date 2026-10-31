# Where we arrived

**The question.** To know something we have to be able to say it, so knowledge rests on a medium of expression. What is the smallest set of building blocks that, once learned, lets everything else be thought and said through it?

**The answer.** For anything that can be described as things and steps, the answer is a **disentangled lambda calculus**: the lambda calculus, separated into a few distinct parts, and extended with effects.

**The shape:**

| Layer | What it is | Why it's there |
|---|---|---|
| **Base** | functions alone: make one, apply one | The *theoretical* minimum. It can already compute anything (Church, 1936). |
| **Core** | **data**, made by constructors and used by patterns; **functions**, made by cases and used by applying; **names** that can refer to themselves | The *natural* minimum. Each job that functions were secretly doing gets its own form, so it can be seen. |
| **Effects** | requests (state, errors, input/output) answered by handlers | The one thing the core can't say locally: *doing*, as opposed to *saying* |

On top of these sit two things that add nothing to what can be said. **Conveniences** make saying shorter: they're shorter ways to write what's already there. **Claims** let us know things about what's said: from types, which say that nothing goes where it doesn't fit, to proofs, which can say anything precise. Erase every claim and every program runs the same, so claims aren't part of the medium. They're written about it.

**The key decision.** Computing has two founding pictures. In Church's, steps act on the expression itself (*saying*). In Turing's, steps change a memory beside the program (*doing*). They compute the same things. The medium puts Church at the base and brings Turing's picture back on top, as effects.

**The rule for what's primitive.** An idea becomes part of the medium only if it can't be expressed locally through what's already there (Felleisen). Anything else is written *in* the medium, not added *to* it.

**The way of thinking.** Every concept is a pattern of the blocks. When you meet anything new, whether a language feature, a whole language or a precise idea, ask what data, what functions and what effects it involves, and how they're combined. A language is the core, plus a choice of features, plus a way of writing them (Landin).

**How solid it is:**
- **Settled:** functions as the base; data and pattern matching as the core; Felleisen's test.
- **Settled in direction, still moving in detail:** effects. The field hasn't decided between handlers and monads.
- **A choice:** putting types on top, as claims, rather than in the core. The ML tradition builds them into the core. The Lisp tradition leaves them out, and Typed Racket and TypeScript add them over a language that already runs. They go on top here because they add nothing to what can be said.
- **A synthesis:** arranging all this as one medium. Every part is standard, but the arrangement is not a textbook convention.

**Its limits.** The medium makes ideas exact; it doesn't make them true. Whether a description fits the world is settled outside it. And ideas that aren't precise, like what red looks like or what's fair, don't reduce to it, though the medium makes disagreements about them explicit.

**In one sentence:** anything that can be stated precisely can be built from data and functions, with effects for doing, and learning that base once turns every language, and every precise idea, into a combination of things you already know.
