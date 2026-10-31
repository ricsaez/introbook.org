# Summary

The map of the whole idea, for rebuilding it from scratch. It assumes the details are known, and gives the shape, the order, and the reason for each step.

## The aim

A primitive medium of expression: a small set of ideas through which anything that can be described as things and steps can be said exactly, and worked out. It is minimal but complete: it can say anything that can be computed (power), and anything simple to think is simple to say (ease).

An expression is a question or an answer. Carrying out gets from one to the other in steps anyone can follow, each saying the same thing more directly.

## The one split

Every idea is saying or doing.

    saying    Church (1936)    steps act on the expression itself; nothing changes; an expression means one thing at all times
    doing     Turing (1936)    a fixed program acts on a memory beside it; order is meaning

Both compute the same things. The medium takes saying as its base, because a medium for saying needs what is said to keep its meaning. Doing is brought in on top, as effects.

## The layers

    base            functions alone                  power: computes anything
    core            the base untangled, and checked  structure
    conveniences    saying, made shorter             ease: nothing new
    bridge          effects                          doing, brought in
    combinations    objects, concurrency             both sides together
    later           claims and proofs                saying what is true of values

Each layer is the one before, plus exactly what the one before cannot do.

## 1. Base: functions alone

- Forms: placeholder `x`, function `(x, …) ↦ e`, application `e(e, …)`. The values are the functions.
- Steps: a function applied to values becomes its body, with the inputs in place. First the applied part, then the inputs from left to right; nothing happens inside a function.
- Four choices: inputs are worked out before they go in; bodies wait; the order is fixed; there are exactly as many inputs as placeholders.
- Ends: a value; stuck (wrong number of inputs); forever.
- Every role: choose `(a, b) ↦ a`; hold `(f) ↦ f(x, y)`; count `(f, a) ↦ f(f(a))`; repeat `(a) ↦ a(a)`; work on anything `(a) ↦ a`. Enough for anything computable.
- What goes wrong: one function plays many roles and nothing tells them apart, and a value where it doesn't fit can carry on with no sign.

## 2. Core: the base untangled

Each role gets a form of its own:

    choosing               constructors and cases
    holding                contents and patterns
    counting               data holding its own kind
    repeating              a name used in its own definition
    working on anything    placeholders in types

The shape: two kinds of value, each made one way and used one way, and two ways to refer.

                 made by                          used by
    data         a constructor, holding values    a pattern, taking it apart
    function     cases: pattern ↦ body            applying

    placeholder    stands for what a case was given
    defined name   stands for its definition, which may use the name itself

- Combinations: every input, output and content is data or a function. So: data to data, data to function, function to data, function to function; data holding data, a function, its own kind, any type.
- Checking: types are the same two kinds, data types and function types, plus placeholders. There is one rule per form, and a function must have a case for every input. The guarantee: a checked expression never gets stuck. It comes to a value of its type, or goes on forever.
- What types take away, the core gives back: a function can no longer be handed itself, so definitions can use their own name; a name has one declared type, so types can have placeholders. A test for later ideas: does it give back something types took away?
- The hard edge: `∀` inside a type (an input that must work for every type), with two limits so checking always decides (no `∀` fills a placeholder; a `∀` is never guessed); and types that take types, `F(X)`, filled only by a type that takes the same.
- The core translates down to the base to run, but the translation erases exactly the structure. So structure is the first barrier.

## 3. Conveniences

What makes saying shorter and adds nothing: local definitions, leaving out a type that can be found, interfaces ("works on anything that has a map"). Each translates back one definition at a time, keeping the types. The purely written ones would become ordinary definitions if the medium could define new forms (macros). The ones that need the checker, like interfaces, must be built in.

## 4. Bridge: effects

- State said directly: the memory is a value, and a statement is a function from the memory before to the memory after. State is a hidden input and output; the medium writes them out.
- Böhm–Jacopini (1966) is the completeness of Turing's picture: sequence, selection, iteration. With the memory as a value, selection is cases and iteration is a name used in its own definition, both already there. Sequence is the one new thing. In the medium, order belongs to carrying out, not to meaning; with a memory, order is meaning, and it becomes `s2` given what `s1` gave back: `(s) ↦ s2(s1(s))`. Calling nesting "sequence" would carry time into the medium.
- The cost: the memory must pass through every definition between where it starts and where it is used.
- Effects say it once. A computation makes a request; a handler around it receives the request and the rest of the computation, and chooses how to go on: once (state), never (an error), later (a generator), several times (search). Input and output are a handler outside the program. Effects translate back only by touching every definition, so they add expressiveness, not ease.

## 5. Combinations

- Objects: data holding functions that share a memory. A method is a function from the memory and its inputs to an answer and the new memory. Classes make many objects with the same functions.
- Concurrency: several computations, their steps interleaved. It matters only with state, and then carrying out has choices again.

## 6. Languages

Every language is the core, plus a choice of these ideas, plus a way of writing them:

    Python     functions, data, state, objects, errors; checked while running
    Java       objects, state, checking; functions and patterns added recently
    Haskell    functions, data, checking; effects written as values
    OCaml      functions, data, checking, state, objects; effect handlers since 2022, not checked
    Rust       functions, data, checking, state, and ownership: checking applied to state
    Koka       functions, data, checking, and effects checked: the map's shape most directly (research language)
    Lean       functions, data, checking, and claims and proofs

A new language is a new row. Any language can be described in the medium: its forms as a data type, its steps as a function on that data, which is an interpreter. The Note Book is such a description, so the medium can describe itself. Describing a design is not deriving it: the medium makes each design choice exact, not right.

## The shape, in the field's terms

**Saying: the core.** A call-by-value lambda calculus, plus:

- algebraic data types: constructors holding contents, possibly of their own type, `T(X, …) = c(A, …) | …`
- pattern matching: cases, nested patterns, first match, every input covered
- general recursion: definitions, each declared with its type
- parametric polymorphism: `∀X. A`, predicative, higher-rank only where a declaration writes it
- higher-kinded types: `X(A, …)`

That is the core of ML and Haskell: a predicative System F with algebraic data types, general recursion and higher kinds. Its guarantee is type safety: each step keeps the type (preservation), and a checked expression that is not a value has a step (progress), so it never gets stuck.

**Doing: effects.** Algebraic effects and handlers (Plotkin and Pretnar), with effect types as in Koka:

- a request: an operation declared with its type, used like a function: `get()`, `put(s)`
- a handler: a case for each request, given the request and the rest of the computation, choosing how to go on
- effect types: a function type also says what it may request, `(A) → B ! E`; a handler removes what it answers

The guarantee extends: a checked program never gets stuck, and never makes a request nothing answers.

**Closed.**

    power        complete: functions alone compute anything
    parts        closed: five forms, three patterns, four type forms; effects add a request and a handler
    guarantee    never stuck; no request unanswered
    ease         open, only to conveniences, which translate back into the parts

**To proofs.** Let types depend on values. Then a claim is a type and a proof is a value of it. With that, the arc closes: power, structure, doing, truth. Both bridges are written out below.

## Effects, exactly

An effect is declared like a data type, but it lists requests, each with its type:

    Unit = unit
    effect State(S) = get : () → S | put : (S) → Unit
    effect Fail = fail : () → X
    effect Choose = choose : () → Bool

**Forms.** Two new forms of expression:

    op(e, …)                                               a request, op declared in an effect
    handle e with return(x) ↦ e | op(x, …; k) ↦ e | …      a handler

A request is not a value, and takes no step on its own. In a handler's case, `x, …` are what the request was given, and `k` is the rest of the computation: given an answer, it goes on.

**The rest.** An expression with a hole where the next step happens:

    E ::= [ ]  |  E(e, …)  |  v(v, …, E, e, …)  |  handle E with h

`E[e]` is `E` with `e` in the hole. The core's two rules for where a step happens are this, for an `E` without handlers.

**Steps.** Two new ones:

    handle v with h               becomes   e, with v for x, for h's case return(x) ↦ e
    handle E[op(v, …)] with h     becomes   e, with v, … for x, … and (y) ↦ handle E[y] with h for k,
                                            for h's case op(x, …; k) ↦ e, when no handler in E has op

**Order becomes meaning.** In the core, the fixed order of steps changes nothing that is said. With requests, it is what is said: since inputs are worked out left to right, `then = (a, b) ↦ b` is "this, then that".

**A handler chooses how to go on:**

    tick = () ↦ put(succ(get()))
    count = () ↦ then(tick(), then(tick(), get()))
    down = (zero) ↦ fail() | (succ(a)) ↦ a

    run = (c, s) ↦ (handle c() with return(x) ↦ ((t) ↦ x) | get(; k) ↦ ((t) ↦ k(t)(t)) | put(n; k) ↦ (t) ↦ k(unit)(n))(s)
    try = (c, other) ↦ handle c() with return(x) ↦ x | fail(; k) ↦ other
    all = (c) ↦ handle c() with return(x) ↦ cons(x, empty) | choose(; k) ↦ append(k(true), k(false))

    run(count, zero)                       comes to   succ(succ(zero))          once: state
    try(() ↦ down(zero), succ(zero))       comes to   succ(zero)                never: an error
    all(() ↦ and(choose(), choose()))      comes to   cons(true, cons(false, cons(false, cons(false, empty))))
                                                                                several times: search

`run` turns a computation into a function of the memory. `all` gives every answer: `true` only when both choices are `true`. With no handler, `down(zero)` is stuck at `fail()`. That is what effect types rule out.

**Types.** A function type also says what it may request:

    A ::= … | (A, …) → B ! E       E: a set of effects {F, …}, or {F, … | Z}, with Z a placeholder for "and whatever else"

    op(e, …) : B ! {F}                when F lists op : (A, …) → B, and e, … : A, …
    a function's type carries the effects of its body; an application has the effects of its parts and its function
    handle e with h : B ! E           when e : A ! {F | E}; in return(x) ↦ e', x : A and e' : B ! E;
                                      in each op(x, …; k) ↦ e', x, … have the request's input types,
                                      k : (its answer type) → B ! E, and e' : B ! E

    tick : () → Unit ! {State(Nat)}
    run : (() → X ! {State(S) | Z}, S) → X ! Z
    map : ((X) → Y ! Z, List(X)) → List(Y) ! Z         map works whatever requests f makes

**The guarantee becomes**: a checked expression is a value, has a step, or is a request its type lists. A program checked with `! {}`, or with only the requests the outside answers (input and output), never gets stuck and makes no request that nothing answers.

In the field's terms: deep handlers (Plotkin and Pretnar), with effect rows (as in Koka). The steps above were checked by running; the types were checked by hand.

## Proofs, exactly

One change of shape: types become expressions, so the type side of the core folds into the expression side.

**Forms.**

    Type                                   the type of types, in levels: Type₀ : Type₁ : …
    (x : A) → B                            a function type whose B may use the input x; (A) → B when it doesn't
    T(…) = c(…) : T(…) | …                 a data type whose constructors may fix its inputs

So `∀X. A` becomes `(X : Type) → A`, and a type that takes types becomes a function `F : (Type) → Type`. Placeholders in types and in expressions are one idea. The levels are the core's limit that no `∀` fills a placeholder: a type over every `Type₀` is in `Type₁`.

**Claims and proofs.** A claim is a type, and a proof is a value of it. Unwritten placeholders (`A`, `B`, `a`, `b` below) are filled by each use, as `X` was.

    Eq(A, a, b) = refl : Eq(A, a, a)                     the claim that a and b are the same

    leftZero : (n : Nat) → Eq(Nat, add(zero, n), n)
    leftZero = (n) ↦ refl                                add(zero, n) comes to n, by add's first case

    cong : (f : (A) → B, Eq(A, a, b)) → Eq(B, f(a), f(b))
    cong = (f, refl) ↦ refl                              matching refl teaches the checker that a is b

    rightZero : (n : Nat) → Eq(Nat, add(n, zero), n)
    rightZero = (zero) ↦ refl | (succ(m)) ↦ cong(succ, rightZero(m))

A proof by induction is a definition that uses itself: `rightZero(m)` proves the claim for `m`, and `cong` carries it to `succ(m)`.

**Checking.** Two types are the same when they come to the same expression, so checking carries out expressions, placeholders and all. That needs two conditions. Every definition must finish, so each use of itself must be on a smaller part, and must cover every case. And proofs make no requests. Otherwise `bad : Eq(Nat, zero, succ(zero))`, `bad = bad` would prove a false claim.

**The guarantee becomes**: a checked proof proves its claim.

In the field's terms: Martin-Löf type theory, the base of Lean, Agda and Rocq. Checked by hand only.

## Placing a new idea

Ask, in order:

1. Can a definition say it? Then it is written in the language, not added to it.
2. Does it translate back one definition at a time, keeping the types? Then it is a convenience.
3. Can it be said only by touching every definition? Then it adds expressiveness: an effect, or new checking. (Felleisen, 1990; Van Roy's creative extension principle.)
4. Would translating it down lose structure? Then it is core.

## How complete

- The base: complete by proof.
- The core: closed. Every value is data or a function, and every combination is listed.
- Above it: complete as far as anyone knows. New ideas still appear (effect handlers, ownership), and the test above places them. A simple idea that is awkward to say would show a missing one.
- Not organized by the shape, because they are choices: how a language is written, which designs are good, whether a description fits the world. The medium makes each choice explicit.

## Two orders

- The order of building: base, core, conveniences, bridge. Each layer is what the one before cannot do.
- The order of understanding: things first (`true`, `rock`), then functions by cases, steps, checking, holding, repeating, functions as values, types left open. Functions alone come late, as a discovery. Each piece starts from what it is for, every example means something, and the main idea comes before its details.
- Writing in the order of building is what made the old Book hard: it became the Note Book read aloud.
- The base is not needed to use the medium or to think in it: the core contains functions, and the core is complete on its own (data and definitions that use themselves compute anything). The base gives one "why": why the parts are so few, and why types are needed. So in an explaining book it is one late chapter, read once.

## Lineage and reading

- Church and Turing, 1936; Böhm and Jacopini, 1966; ML and System F, 1970s; claims as types, Curry and Howard, and Martin-Löf, 1970s; Felleisen on expressiveness, 1990; Plotkin, Power and Pretnar on effects and handlers, 2003–2009; Van Roy and Haridi on kernel languages, 2004.
- To read: SICP (saying, state, interpreters; no types); Van Roy, "Programming Paradigms for Dummies" (the layering); Pretnar, "An Introduction to Algebraic Effects and Handlers" (effects).

## Open

- When are two values the same, beyond renaming?
- Should the medium have a way to define new forms (macros)?
- Partial filling: `Pair(Bool)` as a type that still takes one.
- How checking is carried out, and why the two limits make it finish.
- Effects and proofs are written here, but not yet explained in `thebook.md`, nor written into the project's Note Book.
- The effect types and the proofs are checked by hand only.

## Files

- `thebook.md` (Desktop): the theory explained, in the order of understanding; every example checked by running.
- `thenotebook.md` (Desktop): the shape, in a page and a half.
- `summary.md` (Desktop): this map.
- `introthebook.com/`: the project's The Book and The Note Book, and `Notes.md`, which holds insights the book has not reached yet.
