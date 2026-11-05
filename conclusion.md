# The shape of the new medium

The reader should be able to take a new problem, represent its things as data, write rules for them, and say exactly where the computation remembers something, stops or interacts with the outside. Data, functions and unrestricted recursion suffice for computable transformations of data. Fixed operations for memory, exceptions and input/output give ordinary programs direct ways to do those jobs.

Open with one distinction from the game. `points(win)` works out a value, `1`. Recording that win changes the score from `2` to `3`. This is what we mean by *saying* and *doing*. Introduce both on the first page, then explain each operation when the game first needs it.

## The main text

| Section | What the reader builds and learns |
|---|---|
| 1. Values and types | Represent hands, outcomes and rounds. A constructor builds a value. Its type says what belongs there. |
| 2. Functions and cases | Judge a round by matching its two hands. Explain application, names, scope and evaluation order. Give `play` a strategy as an input and use `always(rock)` to return a strategy. |
| 3. Repetition and collections | Use lists, pairs and recursion to play several rounds. Find the hands that beat rock by testing each hand with the same rule. Introduce type parameters through lists. |
| 4. Places and state | Keep the score between rounds. Create a place, read its current value and replace it. Trace memory beside the expression. Show what happens when two names refer to one place. |
| 5. Stopping | Let a player forfeit. Raising an exception skips the rest of the round until a matching catch. Earlier score updates stand. Show how to restore a saved score when a round should be undone. |
| 6. The outside | Read a hand from a person and show the outcome. Combine these operations with the scoring rules to play a complete match. |
| 7. The whole medium | Collect the forms and their rules on one page. Trace one turn through input, a pure calculation, a score update and output. Identify each effect in the relevant function signature. |
| 8. Into Python | Write the same game in idiomatic Python. Use it to connect patterns, loops, name rebinding and changes to shared objects to the rules the reader knows. |

Reuse the game and readable spellings from [medium-1.md](medium-1.md), including `def`, `case`, `let`, numbers and list literals. Give each new form a worked example and a short rule the reader can use to predict the next step. Put each type or signature beside the example it describes.

## The memory rule to try in the draft

Use immutable constructed data and explicit mutable places for the first draft. A place has an identity and holds a value. State records what each place currently holds. A `let` name keeps its binding within its scope. When it names a place, the contents of that place can change. Try this spelling for the score example.

```
let score = place(2)
let saved = score.value
score.value = saved + points(win)
```

After the update, `saved` is `2` and `score.value` is `3`. If `other` names the same place as `score`, reading `other.value` also gives `3`. This example distinguishes saving a value from sharing a place. Use it to assess the memory and notation choices left open in [map.md](map.md). The Python chapter can make the same distinction using two names for one mutable list. [Python's data model](https://docs.python.org/3/reference/datamodel.html#objects-values-and-types)

## Appendices

- **A. Requests and handlers.** Once the reader knows what storing a score and stopping a round mean, show how requests and handlers can model those behaviors. Trying several answers or saving the remaining work for later shows what the general mechanism adds.
- **B. Exact rules and abbreviations.** Collect the detailed rules for names, types and evaluation, and show how the readable spellings expand. The main text gives each fixed operation its meaning at first use.

Close the main text with the question the reader can carry to the next problem. What are the data and the rules, what needs to be remembered between steps, and where can the work stop or interact with the outside?
