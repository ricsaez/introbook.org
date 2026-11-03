# Notes

Insights and open questions the book has not reached yet. When the book says something, or a question is settled, it leaves this file.

## What we are doing

The book introduces a primitive medium of expression: an irreducible set of ideas through which every idea that can be described as data and steps can be thought and said. To know something we must be able to say it, so the medium comes first. It must be minimal but complete: it can say anything that can be computed (power), and anything simple to think is simple to say (ease).

It is built in layers, each the previous one plus exactly what that one cannot do: the base (functions alone: power), the core (structure: kinds and shapes, so what is said can be checked), the extensions (ease: nothing simple is awkward), and later claims and proofs (saying what is true of values). Each layer is closed as a shape in the Note Book and explained in the Book. The old version of the full core is in `old/old/language.md`.

## Why this matters

To know something, we have to be able to say it. So expression comes before knowledge, and a language comes before expression. This could open the introduction.

Why the book is needed: the medium is already at the heart of logic, proof checkers and functional programming, but it is taught as technical machinery, not as a medium of thought, and its basics are hard to present cleanly. Wittgenstein: "The limits of my language mean the limits of my world." A medium of saying moves that limit.

Two questions stay separate: what an idea says, which the medium makes precise, and how it maps to the world, which is modelling, settled by evidence and by choices such as what counts as fair. The medium does not make those choices, but it makes them explicit: a disagreement about fairness becomes a disagreement about which definition to use.

## Carrying out, seen from outside

- An expression that is not yet a value is like a question, and the value it comes to is like the answer: "the 1000th prime" and `7919`. They are about the same thing, but only the answer lets us see it. Computing is getting from one to the other by steps anyone can follow.
- Every language has different expressions for the same thing, such as "the capital of France" and "Paris". Only some languages have steps inside the language to get from one to the other.

## Change versus carrying out

- Carrying out: the same thing, said more directly. Nothing happens.
- Change: different situations, one after another. The dog at A, then at B.
- They meet when a change is phrased as an expression: "where the dog ends up" comes to "B".

## The oldest fork (parked)

- Both founding pictures of computing are built from steps by rules.
- Church: the steps act on the expression itself. Turing: the program stays fixed, and the steps act on something beside it, a tape.
- Both compute exactly the same things. Whichever is the base, the other comes back built from it: the old book chose Church, and change came back as an extension.
- That extension is effects: requests made from inside a computation and answered outside it, such as reading and changing a state. Without them, one local change, like a counter, forces every definition it passes through to carry the state along. With them, it is said once. They translate back, but by touching every definition on the way, so they add expressiveness rather than mere ease. This is where the book closes the fork: Church at the base, Turing's change rebuilt on top.
- Why Church is the base of a medium for saying: not power, since both compute the same things, but meaning. In Church's picture an expression means one thing at all times, so a name can always be replaced by what it stands for. State breaks this: in Python, `x` means different things at different moments. The medium describes change instead of undergoing it: the memory before and the memory after are two values, and a stateful program is a function from one to the other. State is a hidden input and output; the medium writes them out. (SICP 3.1.3, "The Costs of Introducing Assignment", makes the same point.)
- Any language, Python included, can be described in the medium: its forms as a data type, its steps as a function on that data, which is an interpreter. The Note Book is itself such a description, so the medium can describe its own rules. Describing a design is not deriving it: the medium makes each design choice exact and comparable, but does not make the choice.
- A test for the book: one day it should be able to state this fork clearly in its own words.
- What a handler does, in one line: it receives a request and the rest of the computation, and chooses how to go on. Answering once is state, never going on is an error, going on later is a generator, going on several times is search.
- Concurrency only matters once there is state: with none, order changes nothing that is said. With state, it brings choices back into carrying out.
- Objects: data holding functions that share a memory. With the memory written out, a method is a function from the memory and its inputs to an answer and the new memory.
- The whole in one line: every idea is saying or doing. The core says (Church untangled); effects do (Turing's picture brought in: state, errors, input/output). So the extensions split in two: conveniences, which only make saying shorter and stay on Church's side (local definitions, interfaces), and effects, the one bridge. Objects and concurrency are combinations of the two sides, not a third.
- Every language is the core plus a choice of these ideas (state, effects, objects, concurrency, checking before or while running), and a way of writing them. A map of languages as rows of that choice could close `thenotebook.md`.

## Open questions

- Values that act the same: beyond renaming, `(b) ↦ (c) ↦ c` and `(b) ↦ ((c) ↦ c)((c) ↦ c)` are different values that act the same. So are `(a, b) ↦ b` and `(a) ↦ (b) ↦ b`, of different types, each of which can be turned into the other. When are two values the same? This matters once claims about sameness arrive.
- What a placeholder really is: a link between what a function takes and the places that use it. The name only shows the link, which is why a case can be renamed to a new name without changing anything, and why it must be, when a value put in place would have a defined name taken for its placeholder.
- The whole expression, outside every function, is an idea the page now names only from the side: an expression that uses no placeholder from around it. Carrying out happens only there, so a value put in place never uses a placeholder from around it, and only a defined name in it can be taken by an inner case. Whether this idea deserves its own word.
- Telling a thing apart: to talk about something at all, we single it out from everything else. It may be a primitive under value.
- Still and moving is exact for expressions of a language we compute in. Whether all ideas split this way is a bigger question.
- A type with no constructors, and a function with no cases. Constructors and cases are one or more, while placeholders and inputs can be none. A type with no values may be needed once claims arrive (a claim with no proof), and then a function on it would have no cases.
- Partial filling fills a type's first placeholders only, as most typed functional languages do. A pair with its second side fixed, and its first open, still needs its own declaration. Check whether this makes some simple container awkward to say.
- The types are given by rules saying when `e : A`, not by a way of finding `A`. The two limits are the usual ones for this kind of language (no `∀` put in place of a placeholder, and a function's placeholders get a `∀` only where its type is known: checking against a known type, as in higher-rank Haskell), and they are what lets checking be carried out. Whether the book should show how, and that it always finishes.
- A `∀` is set aside only in front of a type, so `keep = const` is not checked, though `keep = (a) ↦ const(a)` is. This is the usual choice today (GHC since 9.0, "simplified subsumption"); an earlier design looked past arrows and accepted both. Recorded so the choice stays deliberate.
- Covering, as the book states it, says when cases cover, not how to find out that they do not: putting constructors in place can go on forever, as for `(zero) ↦ true | (succ(zero)) ↦ false`. Putting them in place only where some case has a constructor ends, since patterns are finite. Whether the book needs to say so.
- `checked` is not kept by steps: a step can leave the limits, so the guarantee is stated for `:`. Whether there is a notion between the two, kept by every step and decidable.
- Constructors cannot be hidden. A case's placeholder can now hide a defined name, so that a new definition never changes a function written before it. But declaring a new constructor still changes what a pattern written before means: `(red) ↦ …` turns from a placeholder into a constructor. The same holds for a new data type and a placeholder in a type of that name. Most typed functional languages avoid this by writing constructors and data types with a capital letter. Open: how the medium should keep these changes contained.
- A type can be given only by a declaration, so a function's placeholders get a `∀` only where a declared type reaches them. Most typed functional languages also let an expression be given a type where it is written. Here that would be an extension, and it translates into a definition only when the expression uses no placeholder from around it. Open whether it is needed.
- The guarantee (a checked expression never gets stuck) is read out with its reasons, not proved. Its proof belongs with claims and proofs.
- Placeholders in types are the same idea as placeholders in expressions: something left open, decided later, belonging to what takes it (a case, a `∀`, a data type), and renamed in the same way. "Takes" is used for all three and only for them; "fill" and "needs" only for what a type is given; "in place of" for what goes where a placeholder was. Watch that this stays one idea.

## Departures from the usual design

The core makes the usual choice of a typed, strict, pure functional language, except in these places, each kept on purpose:

- Every definition declares its type. So every name's type is known before any definition is checked, definitions can use each other in any order, and a `∀` inside a type, which cannot always be found from the definition, is always written. Leaving the type out is an extension (convenience) where it can be found: it translates back by writing the type in.
- A definition is carried out again at each use. A defined name stands for its expression, with one rule, `n` becomes `e`, and no memory of values already found or order among definitions. Most strict languages carry out a definition that is not a function once, before anything else; the two differ only in cost, and when such a definition goes on forever and is never used.
- Functions take a fixed number of inputs, given all at once. Where one input is allowed, several are, so taking several is the general form, not one input at a time; asking for exactly as many inputs as placeholders means each step uses every input, and the wrong number is caught. A function that gives back a function says the usual one-at-a-time form when it is wanted. Giving fewer inputs and getting back a function waiting for the rest would be an extension.
- `∀` may appear inside types, not only in front. Without it, a function that uses its input at two types, such as `both`, or contents that work for every type, such as `Poly`, cannot be said: with `∀` only in front, `both` must instead be given one function for each type it uses. The two limits keep checking possible, as in higher-rank Haskell.

## Groups (not needed yet)

- Some values of a type taken together, like the even numbers.
- Keep "is one of" (a value and its type) apart from "is part of" (a group and its type).
- In our language, a group will be said by a test: a function answering true or false.

## The arc

The arc is the field's own layering, found by different communities in different decades: functions (Church, 1936); structure (type theory and ML, 1970s, now the core of Rust, Swift, Kotlin, and added to Python and Java as pattern matching); effects (algebraic effects and handlers, Plotkin, Power and Pretnar, 2003–2009, built into OCaml since 2022); claims and proofs (propositions as types, Martin-Löf, 1970s; Lean, Agda). Each layer's texts assume the layers below in their own community's notation, and effects are too recent to be taught from zero, so no one has presented the whole from no background. That is the gap the book fills, and the Introduction can say who found each layer.

Three layers, three barriers:
- The base: functions alone. It can compute anything.
- The core: the base with structure. Types say what kinds of things there are, and data says what shape each kind has; self-reference and "for every" come with them. All of it can be translated down to the base to run, but the translation erases exactly the structure. So the first barrier is structure, and checking is what structure makes possible.
- The extensions: each translates back into the core, one definition at a time, keeping the types. So the second barrier is translation, and crossing it adds only ease.
- Above them, a third barrier: anything that can be written as a definition in the language is not added to it at all; it is written in it. So an extension must translate back, and must also be something no definition can do (it binds names, needs the checker, or touches every definition).
- The tests decide where an idea belongs, but not that the list is complete. Only the base is complete by proof: it computes everything. The core and extensions are complete as far as we know: no simple idea is known to be awkward to say. A counterexample would show a missing idea. The tests also admit endless sugars for particular cases, such as special syntax for one data type; what keeps the extensions few is that each must be one idea in its general form.
- Why extensions are a category at all: definitions add words, but cannot add a new way of writing or a new job for the checker. Extensions are exactly the ideas that need one. A way to define new forms (macros) would turn the purely written ones, such as local definitions, into ordinary definitions; the ones that change what the checker does, such as interfaces and effects, would still have to be built in. Open: should the medium have a way to define forms?
- The aim of the extensions, in the author's words: nothing is awkward to write. Made precise, "awkward" means one of these fails: a thing can be said once; in one place; anything can go anywhere; few primitives, freely combined; words we define work as well as built-in ones; it reads like the idea; it means one thing and can be checked. When something simple is awkward, a criterion is failing, and that points to a missing idea. (From the old CLAUDE.md, for when the book reaches extensions.)
- The precise version exists: Felleisen, "On the Expressive Power of Programming Languages" (1990). A construct is mere sugar if a macro can translate each use of it in place, leaving the rest of the program untouched. Otherwise it adds expressive power, and the test is objective: adding it lets some programs that were indistinguishable before be told apart. Local definitions are sugar. State, exceptions and similar effects are not: removing them needs a rewrite of the whole program. This sharpens "translates one definition at a time" into "translates in place", and gives "expressiveness" a definition. Ease within sugar is still judged by the criteria above.
- The closest existing work to the book's layering: Van Roy and Haridi, *Concepts, Techniques, and Models of Computer Programming* (2004), and Van Roy's short "Programming Paradigms for Dummies" (2009). Each paradigm is a small kernel language plus one concept, and the creative extension principle adds a concept only when it cannot be encoded with local transformations: the same test as Felleisen's, and as "touches every definition". It goes from functional to state and beyond, but it is untyped and comes before effect handlers.
- So any new idea is placed by asking, in order: can a definition say it? Then it is written in the language. Does it translate back into the core, one definition at a time, keeping the types? Then it is an extension. Would translating it lose structure? Then it is core.

Each kind of value comes with a way to make it and a way to use it. For functions, `↦` makes one and applying uses it. For data, building from parts makes it and taking apart uses it.

What types take away: self-reference (definitions) and working on anything (placeholders in types, `∀`) give back, with structure, two roles that functions played above the line and that the first types ruled out. A test for any later idea: does it give back something types took away? Types do not take self-reference away entirely: a data type whose contents are functions on itself, such as `D = d((D) → D)`, lets a function be handed itself again, without definitions.

Böhm–Jacopini (1966) is the completeness of Turing's picture, not of the medium. The medium's own: functions alone choose, hold, count and repeat, and the core gives each job a form so it can be checked. Böhm–Jacopini belongs where state comes in, as the bridge. With the memory written out as a value, selection is cases and iteration is a name used in its own definition, both already there: `while c: body` is `loop = (s) ↦ ((true) ↦ loop(body(s)) | (false) ↦ s)(c(s))` (checked by running). Sequence is the one thing state adds. In the medium no step changes what is said, so order belongs to carrying out, not to meaning; with a memory, order is meaning, and it becomes `s2` given what `s1` gave back: `(s) ↦ s2(s1(s))`. Calling nesting "sequence" would carry time into the medium. This, with the table of two kinds of value, each made one way and used one way, is the shape of `thenotebook.md` on the Desktop.

The movements the core has: applying, building (a constructor applied to its contents), taking apart (a case's patterns), and a defined name's step. The old book named four: make, choose, combine, repeat. Whether the book should name its movements is open.
