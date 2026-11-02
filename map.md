# Where we arrived

**The question.** To know something we have to be able to say it, so knowledge rests on a medium of expression. What is the smallest set of building blocks that, once learned, lets everything else be thought and said through it?

**The answer.** For anything that can be described as things and steps, the answer is a **disentangled lambda calculus**: the lambda calculus, separated into a few distinct parts, with types to say what kinds of things there are, and extended with effects.

**The shape:**

| Layer | What it is | Why it's there |
|---|---|---|
| **Base** | functions alone: make one, apply one | The *theoretical* minimum. It can already compute anything (Church, 1936). |
| **Core** | **data**, made by constructors and used by patterns; **functions**, made by cases and used by applying; **names** that can refer to themselves; **types**, which describe kinds of things | The *natural* minimum. Each job that functions were secretly doing gets its own form, so it can be seen. Types and function signatures make the kinds of contents, inputs and results visible. |
| **Effects** | requests (state, errors, search, taking turns, input/output) answered by handlers | The one thing the core can't say locally: *doing*, as opposed to *saying* |

**Types and function signatures** belong to the written medium. A signature such as `better : (Hand) → Hand` shows what goes in and what comes out; effect information shows what a function may ask. These descriptions help us see how pieces fit together. Types were already present; restoring signatures makes them visible alongside definitions without adding a computational primitive.

**Current edition.** Among the current editions, only `medium-revised.md` uses this restored presentation. The other books and PDFs have not been updated to match.

**Conveniences** make saying shorter: they're shorter ways to write what's already there. **Checking and proofs** establish whether descriptions and other claims are correct. Writing a signature does not establish its correctness, and omitting it does not change how the program runs. The medium includes these useful descriptions; checking them and proving claims remain outside its scope.

**The key decision.** Computing has two founding pictures. In Church's, steps act on the expression itself (*saying*). In Turing's, steps change a memory beside the program (*doing*). They compute the same things. The medium puts Church at the base and brings Turing's picture back on top, as effects.

**The rule for what's primitive.** An idea becomes part of the medium only if it can't be expressed locally through what's already there (Felleisen). Anything else is written *in* the medium, not added *to* it. Types are on a different axis: they don't change what any program does. They say what kinds of things there are.

**The way of thinking.** Every concept is a pattern of the blocks. When you meet anything new, whether a language feature, a whole language or a precise idea, ask what data, what functions and what effects it involves, what kinds of things they are, and how they're combined. A language is the core, plus a choice of features, plus a way of writing them (Landin).

**How solid it is:**

- **Settled:** functions as the base; data, pattern matching and types as the core; Felleisen's test.
- **Settled in direction, still moving in detail:** effects. The field hasn't decided between handlers and monads.
- **A choice:** types describe kinds of values, and signatures describe functions. We use them to make the shape of what we express clearer. Checking those descriptions and proving claims are separate from the presentation and from execution.
- **A synthesis:** arranging all this as one medium. Every part is standard, but the arrangement is not a textbook convention.

**Its limits.** The medium makes ideas exact; it doesn't make them true. Whether a description fits the world is settled outside it. And ideas that aren't precise, like what red looks like or what's fair, don't reduce to it, though the medium makes disagreements about them explicit.

**In one sentence:** anything that can be stated precisely can be built from data and functions, with types to say what kinds of things there are and effects for doing, and learning that base once turns every language, and every precise idea, into a combination of things you already know.
