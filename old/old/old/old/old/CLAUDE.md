# The Book

This repository is *The Book: An Introduction*: a book that introduces a primitive medium of expression, a small language in which anything computable can be said and anything simple to think is simple to say. Start from `README.md`, which links the introduction and then the book.

## The aim

The medium is meant to be learned once and trusted as a base for thinking, so it must be right: minimal, but complete.

Two kinds of completeness matter:

- **Power:** anything computable can be said. Turing completeness gives this, and is not enough. The goal is to avoid the "Turing tar-pit, in which everything is possible but nothing of interest is easy" (Perlis).
- **Ease:** anything simple to think is simple to say.

## Criteria

A medium is easy to express in when:

- a thing can be said once
- a thing can be said in one place
- anything can go anywhere
- it has few primitives, freely combined
- it can grow: words defined in it work as well as built-in ones
- it reads like the idea
- it means one thing, and can be checked and reasoned about

When something simple is awkward to say, one of these is failing, and that points to a missing idea.

## Core, extensions, and claims

The book presents the medium in three parts: first the core, then the extensions, then claims and proofs.

- The core can say anything computable, and contains only ideas that cannot be translated away one definition at a time while keeping what types say: removing them would need the whole program at once, or would let a type hold values it should not, as when data is encoded as functions. Each idea comes in its general form: its parts nest freely, and where one part is allowed, several are, limited only where checking requires it. So patterns inside patterns, or a function with several inputs, are not separate ideas, while a new way of writing an existing idea, such as naming a value inside an expression, is an extension.
- The extensions make the core easy to use. Each translates back into the core one definition at a time, so it adds ease, never power. Each adds forms to the core's lists and names the criterion it serves.

An extension is one of three kinds:

- **Convenience:** it translates into forms that already exist, changing only the definition where it is used.
- **Abstraction:** it lets a thing be said once, at the right level, and keeps change contained.
- **Expressiveness:** without it, one local idea forces changes to every definition it passes through.

Claims and proofs build on the core and the extensions. They let a type state what is true of values, and a program that is checked to finish prove it. They do not translate back, so they are not an extension: they add to what can be stated, never to what can be computed.

Something belongs in the medium only if no definition written in the medium can make it easy to say. Everything else is written *in* the medium, as definitions, not added *to* it.

## Decisions

- The medium covers computation first. Claims and proofs extend the base rather than replacing it: every program means what it meant before.
- Claims are about values and types, not about claims. Letting claims be values, with types of types, is a further step not taken; it would again extend rather than replace.
- Evaluation is strict, types are static, and the core is pure.
- Where a choice has to be made, the medium makes the usual one, the one most typed functional languages make. It departs only where the usual choice fails one of the criteria.

## Writing style

- Move in small steps: one idea at a time, each built only on what is already shown. Be most careful at the jump from one idea to the next, where a reader gets lost; a step that needs a missing idea is a defect, however true it is.
- Define every term before using it.
- Show each form by its general shape, with placeholders (`e`, `v`, `A`, `x`, …), then fill the shape in with an example.
- List forms with `::=`.
- `>>` and `[x := v]` are explanatory notation, not part of the language.
- Plain words, and no outside references; the introduction gives the framing.
- Each part ends with a section gathering all its forms and rules in one place. That section must stay complete: nothing exists that it does not list.

## About this file

This file describes purpose and conventions, not progress or how the files are currently arranged. Do not record status, contents, or plans here.

After changing the repository, check whether anything in this file has stopped being true, such as a changed convention or a new decision, and update it in the same change. Progress alone never needs an update.
