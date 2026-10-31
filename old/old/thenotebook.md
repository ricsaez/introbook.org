# The Note Book

The whole of `thebook.md`, in its shape. Every table is a complete list.

## 1. Question and answer

An **expression** is a question or an answer. An answer is a **value**. **Carrying out** turns a question into its answer in **steps**, each saying the same thing more directly:

    not(true)   becomes   ((false) ↦ true | (true) ↦ false)(true)   becomes   false

## 2. Two kinds of answer

Every value is one of two kinds, and each kind is made one way and used one way. This table is the whole language; everything after is how its parts meet.

                 made by                                   used by
    data         a constructor, holding values or nothing  a pattern, which takes it apart
                 true    round(rock, paper)    succ(zero)  (round(a, b)) ↦ beats(a, b)

    function     cases, each a pattern ↦ a body            applying it to inputs
                 (false) ↦ true | (true) ↦ false           not(true)

And two ways to refer to a value without writing it out:

    placeholder    a name in a pattern, standing for what the case was given     (a, b) ↦ a
    defined name   n = e: n stands for e, and e may use n itself                  add = … add(a, b) …

## 3. The forms

So an expression is one of five things, and a pattern one of three:

    e ::= x                 placeholder
          n                 defined name
          c                 constructor
          (p, …) ↦ e | …    function: its cases, tried in order
          e(e, …)           application: a function to its inputs, or a constructor to its contents

    p ::= x                 matches anything, and gives it to x
          c                 matches c
          c(p, …)           matches c holding what matches p, …

What we write: data types with their constructors, `T = c(A, …) | …`; names with their types and definitions, `n : A` and `n = e`; then one expression to carry out.

## 4. The steps

    n                          becomes   what n is defined as
    ((p, …) ↦ e | …)(v, …)     becomes   the body of the first case whose patterns v, … match,
                                         with what they matched in place of its placeholders

Where: in an application, first the function, then the inputs from left to right, each worked out to a value before it goes in. Nothing happens inside a function until it is applied.

It ends in one of three ways:

    a value      the answer
    stuck        a value where it doesn't fit             not(rock)
    forever      repeating that never stops               loop = (a) ↦ loop(a)

## 5. Types

A type says which kind of value goes where, so there are the same two kinds, and a third that leaves the type open:

    T(A, …)        data type        true : Bool     round(rock, paper) : Round
    (A, …) → B     function type    not : (Bool) → Bool
    X              any type, chosen by each use     identity : (X) → X

A constructor has the type that lists it. An application has the type its function gives back, when its inputs have the types it takes. A function has its type when every case fits it, and every input matches some case. An expression that has a type is **checked**, and a checked expression never gets stuck: it comes to a value of its type, or goes on forever.

## 6. Every combination

Every value is data or a function, so every place a value can go (an input, an output, contents) holds one or the other. These are all the combinations there are:

    (Bool) → Bool                       data to data             not
    (Hand) → (Hand) → Bool              data to function         (a) ↦ (b) ↦ beats(b, a)
    ((Hand) → Hand) → Hand              function to data         (f) ↦ f(rock)
    ((Hand) → Hand) → (Hand) → Hand     function to function     (f) ↦ (a) ↦ f(f(a))

    Round = round(Hand, Hand)               data holding data
    Task = task((Hand) → Hand, Hand)        data holding a function
    Nat = zero | succ(Nat)                  data holding its own kind, so it reaches any size
    List(X) = empty | cons(X, List(X))      data holding any type

Every type is built from these, nested as deep as needed.

## 7. Why it is complete

Functions alone can already do every job (`thebook.md`, section 10):

    choosing               (a, b) ↦ a                of two, the first
    holding                (f) ↦ f(rock, paper)      two things, waiting for a choice
    counting               (f, a) ↦ f(f(a))          f, twice
    repeating              (a) ↦ a(a)                a function handed itself
    working on anything    (a) ↦ a                   gives back anything

With these, functions compute anything that can be computed. Church (1936) described computing this way, and Turing (1936) with machines; the two compute the same things. So the rest of the language adds no power. It gives each job a form of its own, so that it can be told apart and checked:

    choosing               constructors and cases
    holding                contents and patterns
    counting               data holding its own kind
    repeating              a name used in its own definition
    working on anything    X in types

## 8. State

The other picture of computing, Turing's, keeps a fixed program beside a **memory** that changes. Its completeness is Böhm and Jacopini's (1966): any such program can be written with three ways of combining statements, namely sequence, selection and iteration. Here, the memory is written out as a value, and each statement is a function from the memory before to the memory after:

    x = x + 1             (s) ↦ succ(s)                                               the memory s is a Nat
    s1; s2                (s) ↦ s2(s1(s))
    if c: s1 else s2      (s) ↦ ((true) ↦ s1(s) | (false) ↦ s2(s))(c(s))
    while c: body         loop = (s) ↦ ((true) ↦ loop(body(s)) | (false) ↦ s)(c(s))

Selection is cases, and iteration is a name used in its own definition: both were already here. Sequence is the one new thing. Nothing in the medium says "then": no step changes what an expression says, so the order of steps belongs to carrying out, not to what is said. With a memory, order is what is said: `x = 1; x = 2` and `x = 2; x = 1` end differently. Written as values, that order becomes `s2` being given what `s1` gave back. So a language like Python is this one with the memory hidden, and neither can compute more than the other.

Writing the memory out has a cost: every function between where the state starts and where it is used must take it and give it back. **Effects** say it once. A function makes a request (read the memory, change it, fail, print), and a **handler** around it answers. Underneath, a program with effects means the same as one that passes the state along; effects spare us writing that everywhere. This is how a functional base thinks through state.

## The whole model

    an expression is a question or an answer
    an answer is data or a function
    data is made by constructors, and used by patterns
    a function is made by cases, and used by applying
    placeholders and names refer; a name that refers to itself repeats
    types say which kind goes where, so nothing gets stuck
    functions alone can choose, hold, count and repeat: enough for anything
    the rest gives each job its own form, so it can be checked
    state is a memory passed along as a value; order is passing it on; effects say it once
