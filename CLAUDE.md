# The Book

`book.md` builds a small language one piece at a time, with a **map** (forms and rules) that grows section by section. Section 19 is the whole map on one page. `introbook.md` is the introduction. `old/` is an archive: don't edit it or treat it as current.

## The principle

At the end of every section, the map allows exactly what the text has explained and shown so far:

- **The map never goes beyond the text.** Whatever the forms and rules allow at that point has been explained, with an example. If a form would allow something the text only reaches later, hold it back with a rule and widen the rule where the text gets there (as with defined names on their own, held back until section 10).
- **The text never goes beyond the map.** Every example and claim fits the map at that point in the book.
- **Every general claim has an example where it is made.** Examples come first or right alongside; the general form follows. A summary, table or "every…" / "two ways…" sentence lists only things already shown.

## Checklist, after any change

1. For each new or changed form or rule: what does it combine with, including everything earlier? Is each combination explained and shown, or held back?
2. For each example: does it fit the map at that point (forms, names rules, arity, order of definitions)? Does its trace come to what the text says?
3. For each summary, table or claim about "all", "every", or how many: is it true of the map at that point, and is each item already shown?
4. Section 19 is the sum of the sections' map lines: nothing in it is new, and nothing from a section is missing.
5. Every map and rule line added in a section is tagged `new` or `widened` the way the surrounding lines are.

## Conventions

- Map lines use letters (`e`, `v`, `x`, `c`, `n`, `p`, `d`, `T`, `F`, `r`, `h`, `A`, `B`, `X`, `E`), never examples; examples never use those letters as names.
- "becomes" is one step; "comes to" is one step or more.
- When the book rewrites a declaration or a definition, the new one replaces the old.
- Claims (things that could be true or false about a program) are left out of the book.
- Plain, short sentences; examples from rock, paper, scissors where possible.
