# Where we arrived

**The aim.** A small, explicit medium for thinking about and expressing computations, and any idea that can be stated precisely as things and steps. Learn its basic pieces, then understand and build more elaborate ideas by combining them. The reader should be able to use the medium without first learning how to design a language.

**The trade.** The current `medium.md` starts effects with one general mechanism, requests and handlers, which can express memory, stopping and many other effects. That generality is useful when defining new control behavior, including in languages and libraries. The proposal starts instead with the common effects: memory, exceptions and input/output get fixed rules and direct operations, such as `score = score + 1`. The general mechanism remains available as advanced material, outside the starting vocabulary. The trade concerns which operations the starting vocabulary makes direct and what the reader must understand to use them. Completeness is unchanged, and the base doesn't get smaller. The intended gain is simpler expression of ordinary programs.

**The proposed starting vocabulary: data, functions and recursion, plus fixed operations for memory, exceptions and input/output.**

| Piece | What it lets us express |
|---|---|
| **Data** | Things, made with constructors and taken apart by patterns |
| **Functions** | Rules that take inputs and produce results; functions can themselves be inputs, results or data contents |
| **Recursion** | Repetition, through functions referring to themselves or one another |
| **Fixed effects** | Reading and changing stored values, stopping on failure through exceptions, and interacting with the outside |

**The question to ask.** Faced with a new idea, ask what data, what functions and what effects it involves, what kinds of things they are, and how they combine.

**What “primitive, complete and natural” means.**

- **Primitive:** a small set of starting operations with explicit rules, chosen to make central ideas direct. Data gets constructors and patterns even though functions can encode it; memory gets read and update operations even though handlers can model it. A useful primitive need not add computational power. This is a chosen basis, not a unique minimum.
- **Complete:** data, functions and unrestricted recursion can express any computable transformation of data. Input/output additionally provides interaction with the outside.
- **Natural:** ordinary programming ideas should have direct forms and be expressible where they are used, without forcing changes through unrelated code. This locality criterion draws on [Felleisen's work on expressibility](https://doi.org/10.1016/0167-6423(91)90036-W). Larger ideas should be understandable through their parts. These are aims to test on examples, not guarantees for every idea.

**How we got here.** Pure lambda calculus (functions only) uses functions to encode data and repetition. The current medium gives data its own forms, then adds requests and handlers as a general way to express effects. The proposed version gives selected effects fixed rules and direct operations, such as `score = score + 1`. The reader can understand those rules without first constructing memory through handlers. Handlers can already be reused; the change is to make chosen operations part of the starting vocabulary.

**Saying and doing.** The proposed medium is a hybrid: **saying** works out values through data and functions; **doing** changes memory, stops work or interacts with the outside. Pure evaluation rewrites expressions; when memory changes, steps also show its current state. Say where you can; do where the idea needs it.

**Where handlers belong.** The working proposal is to keep requests and handlers available as advanced material, explained in an appendix. This simplifies the starting vocabulary while retaining their general capabilities. Removing them entirely would be a separate decision: computational completeness would remain, but some ways of customizing and combining effects would become less direct.

**How the pieces are explained.** Types describe data, function inputs and results, and possible effects. These descriptions do not enforce themselves; checking and proofs remain outside the medium. Evaluation has a specified order, as it already does in `medium.md`.

**Python's role.** Python is the chosen language for practice. Understand computations through the medium and write them using Python's conventions. The medium's rules and Python's rules must be connected explicitly; they are not assumed to be identical.

**Standing and limits.** The building blocks have established precedent, including [SICP's treatment of functions and state](https://sicp.sourceacademy.org/chapters/3.1.3.html); their selection and arrangement as one medium for thought is our synthesis. The medium makes descriptions exact, not true. Computational completeness does not mean that every precisely stated question can be answered by a computation.

**Still open.** Whether names can be rebound or only explicit storage places change; whether shared data can change in place; whether generators need a direct form; whether handlers can change how the fixed effects behave, or only define new kinds of effect. The current preference is Python-like rebinding and shared mutable objects, to be assessed through examples.

**Next step.** Write a short candidate using rock, paper, scissors: data, a function, a changing score, repetition, input/output and failure. Choose one coherent set of rules and explain each when it appears. `medium.md` still presents requests and handlers in §§8–12; this map records the new direction, not a completed rewrite.
