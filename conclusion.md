**Short answer:** map.md is clear about the direction: why we're moving, what the trade is, and where handlers go. It doesn't yet say what the finished medium.md will contain. It also disagrees with where your last conversation ended up. A few lines would fix both.

## 1. Is map.md clear?

These parts are clear and can stay as they are: the aim, the trade, what "primitive, complete, natural" means, how we got here, where handlers go, Python's role, and the limits.

Gaps, most important first:

1. **It disagrees with the last conversation about data.** "Still open" says the preference is "shared mutable objects". Your last discussion pointed the other way: *data never changes; only state does*. A Python list that changes is then explained as a place holding a list. I recommend that version for two reasons:
   - It gives "say where you can, do where the idea needs it" a sharp line instead of a fuzzy one.
   - It makes undo simple, because the old value is still there.
2. **The table hides state.** It lists Data, Functions, Recursion and Fixed effects, but your latest framing is data, functions and state. Recursion belongs under functions. State deserves its own row, with stopping and the outside next to it.
3. **The notation isn't decided.** Nothing says whether the new medium.md keeps the `(rock) ↦ paper` spelling or switches to a Python-like one. That choice affects every page.
4. **Minor: some references will go out of date.** "The current medium.md…" and "§§8–12" will be wrong after the rewrite. "The general version (Appendix A)" would stay true.

## 2. The end shape of medium.md

**In one sentence:** a short book that teaches five pieces, each with a direct form and a clear rule. Two are for saying (data, functions) and three are for doing (places, stopping, the outside). It shows they're enough for any computation, and ends with the question you use to think through everything else.

The book's structure is the hybrid itself:

**Part 1: Saying, working out values** (today's §1–7, same content)

1. Things: types and constructors
2. Rules: functions by cases, variables, several inputs
3. Data holding data: nested patterns
4. Rules as values: passing, returning and holding functions
5. Repeating: recursion, numbers
6. Lists and pairs: playing a match, and "which hands beat rock?" written as ordinary code (search without handlers)
7. Saying, together: the full list of shapes, and the claim that this already computes anything

**Part 2: Doing, changing things and talking to the outside** (new writing)

8. Places: a place holds data, and you can read it or replace what it holds; data itself never changes.
   - Steps now show memory beside the expression.
   - Covers two names for one place.
   - A function's signature says whether it uses places, can stop, or talks to the outside.
   - Why places are a piece of their own: compare passing the score by hand (`recordBothWith`) with using one place.
9. Stopping: raise and catch, what a stop leaves in places, and undo as keeping the old value
10. The outside: asking for a hand, showing an outcome, then a whole game
11. The whole medium: all the shapes, the step rule, the table below, why these pieces, and the question to ask

**Part 3: Into Python** (new writing)

12. Python through the medium: a translation table, plus the places where Python's rules differ:
    - lists that change in place are places
    - names can be rebound
    - a bare name in `case` matches anything
    - a missed match returns `None` instead of getting stuck
    - functions don't say what they change

**Appendices, for later or never**

- A. Where the fixed pieces come from: requests and handlers (today's §8–12: state, stopping, search, generators, taking turns)
- B. Exact rules: types and membership, names and scope, evaluation step by step
- C. Limits and open questions: two copies of one effect, recursion through inputs, cost as a model you state

**The table the book ends on:**

| | Piece | Made by | Used by |
|---|---|---|---|
| Saying | Data | a constructor with its contents | a pattern that takes it apart |
| | Function | cases | applying it |
| Doing | Place | giving it a first value | reading it, replacing what it holds |
| | Stopping | raising | catching |
| | The outside | asking or showing | the outside answers |

Each piece is a pair of making and using. That's the evidence the set isn't arbitrary. Today's §13 makes the same argument with three rows.

**Left out of the main path:** defining your own effects, effect variables like `Asks`, generators (unless an example needs one), cost models, and the formal rules for when a value belongs to a type.

## 3. Three decisions that settle the rest

1. **Data never changes; only places do.** This is my recommendation, and it fixes gap 1.
2. **One Python-like spelling** (`def`, `case`, `let`, `2`, `[rock, paper]`), used from §1 but following the medium's own rules. medium-1.md already worked these spellings out, so you can reuse them. Its sections on named handlers (`get@mine`, `for Store.`) would go in the appendix at most. The alternative is to keep today's `↦` spelling and add only what doing needs.
3. **Can names be rebound** (`score = score + 1`), or do only places change? Leave this to the rock, paper, scissors draft of §8, which map.md already lists as the next step.
