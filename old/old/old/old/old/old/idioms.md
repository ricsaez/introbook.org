This file gathers the usual ways of expressing an idea in the language of [consolidation.md](consolidation.md). Each section names an idea, and shows how it is written. Each section uses only what comes before it.

## A fixed set of options

A thing that is one of a few options is a data type whose constructors take no inputs:

    T : Type
    T = c₁ : T | c₂ : T | ...

Two options is yes or no:

    Bool : Type
    Bool = true : Bool | false : Bool

## Nothing to say

A data type with one constructor and no inputs carries no information. It is used where a value is needed, but which one does not matter:

    Unit : Type
    Unit = unit : Unit

## Several things together

Several things held together are a data type with one constructor, which takes each of them:

    T : Type
    T = make : (x₁ : A₁, x₂ : A₂, ...) → T

A part is got back with a pattern:

    first : (t : T) → A₁
    first = (make(x₁, x₂, ...)) ↦ x₁

## One of several kinds, each with its own parts

A data type with several constructors, each taking the parts of its kind:

    T : Type
    T = c₁ : (x : A) → T | c₂ : (y : B, z : C) → T | ...

A function on T has a case for each kind:

    f = (c₁(x)) ↦ r₁ | (c₂(y, z)) ↦ r₂ | ...

## A choice

A choice is a function made of cases on what decides it. To choose by yes or no, write cases on Bool:

    f : (b : Bool) → B
    f = (true) ↦ r₁ | (false) ↦ r₂

Give each case what it needs, not a result worked out already: inputs are worked out first, so a result given as an input is worked out even when its case is not chosen.

## Two things of any types

A data type can take types as inputs, and hold values of whichever types are given:

    Pair : (A : Type, B : Type) → Type
    Pair = pair : (A : Type, B : Type, a : A, b : B) → Pair(A, B)

## Maybe there, maybe not

    Maybe : (A : Type) → Type
    Maybe = nothing : (A : Type) → Maybe(A) | just : (A : Type, x : A) → Maybe(A)      a value of A, or none

## Counting

    Nat : Type
    Nat = zero : Nat | succ : (n : Nat) → Nat          0, and one more than any Nat

## A sequence

    List : (A : Type) → Type
    List = nil : (A : Type) → List(A) | cons : (A : Type, x : A, rest : List(A)) → List(A)      empty, or a first value and the rest

## A hierarchy

A thing that contains smaller things of its own kind is a data type that takes itself:

    Tree : (A : Type) → Type
    Tree = leaf : (A : Type) → Tree(A) | node : (A : Type, left : Tree(A), x : A, right : Tree(A)) → Tree(A)

## Doing something again

Repetition is a function that calls itself on a part of its input. It has a case for the smallest input, where it stops, and a case that calls itself on the part:

    f : (n : Nat) → B
    f = (zero) ↦ r | (succ(k)) ↦ ... f(k) ...

The same works on any data type that takes itself: on a List, the rest; on a Tree, each side.

## Doing the same to every part

A function given as an input says what to do; another function carries it through every part:

    map : (A : Type, B : Type, g : (x : A) → B, l : List(A)) → List(B)
    map = (A, B, g, nil(C)) ↦ nil(B) | (A, B, g, cons(C, x, rest)) ↦ cons(B, g(x), map(A, B, g, rest))

## Combining all the parts into one

    fold : (A : Type, B : Type, start : B, g : (x : A, acc : B) → B, l : List(A)) → B
    fold = (A, B, start, g, nil(C)) ↦ start | (A, B, start, g, cons(C, x, rest)) ↦ g(x, fold(A, B, start, g, rest))

A sum, a count, the largest, whether all hold: each is fold, with a different start and g.

## A recipe as a value

A function is a value, so a way of doing something can be given, kept, and given back:

    (g : (x : A) → B) → C           takes a recipe
    (a : A) → ((x : B) → C)         gives one back, made from a

## A question about a thing

A property, or a set of things, is a function to Bool:

    P : (x : A) → Bool              P(a) is true for the things that have it

## Looking something up

An association from keys to values is a function that gives back maybe a value:

    lookup : (k : K) → Maybe(V)

When it is built up one key at a time, it is a List(Pair(K, V)) instead, searched from the front. Searching needs a way to compare keys:

    eq : (a : K, b : K) → Bool

## Something that changes

Nothing in the language changes. A thing that changes is a value passed from step to step: each step takes the current value, and gives back the next:

    step : (s : S) → S

A step that also gives back a result gives back both:

    step : (s : S) → Pair(A, S)

## A process that runs until it is done

A loop is a function that calls itself on the next state. It has cases for the states that are finished, which give back the result, and cases for the rest, which call it again:

    run : (s : S) → R

If no state is ever finished, run never gives back a value: the loop goes on forever.

## Something that might fail

    Result : (A : Type, E : Type) → Type
    Result = ok : (A : Type, E : Type, a : A) → Result(A, E) | fail : (A : Type, E : Type, e : E) → Result(A, E)      an answer, or what went wrong

Each step that can fail gives back a Result. The next step has a case for ok, where it goes on, and a case for fail, where it passes the failure along.

## Something not worked out yet

A value is worked out before it is given. To hold it off, give a function that makes it when given unit:

    later : (u : Unit) → A          later(unit) works it out

## Something without end

An endless sequence holds its first value, and the rest not worked out yet:

    Stream : (A : Type) → Type
    Stream = more : (A : Type, x : A, rest : (u : Unit) → Stream(A)) → Stream(A)

Only as much of it is worked out as is asked for.

## A conversation with the outside

A program that needs something from outside stops, says what it needs, and holds the rest of itself, waiting for the answer. For any types Q of questions, N of answers, and R of results:

    Talk : Type
    Talk = done : (r : R) → Talk | ask : (q : Q, rest : (n : N) → Talk) → Talk

Whatever answers, a person, a file, or another program, is a function with cases on Talk. Input and output, errors, waiting, and giving values one at a time all have this shape.

## A description and its meaning

A language of any kind, a list of instructions, a formula, a programming language, is a data type for what can be said in it, and a function that gives each thing said its meaning:

    Expr : Type
    Expr = c₁ : A₁ | c₂ : A₂ | ...      one constructor for each form

    run : (e : Expr) → M                M is what an expression means

A form that holds smaller expressions is a constructor that takes Expr, and run calls itself on them.

## Expressing a new idea

Questions to ask, in order:

    What are the things?            data types
    What can each one be?           its constructors
    What is each made of?           the constructors' inputs
    What happens to them?           functions, with a case for each constructor
    What is done again?             a function that calls itself
    What changes?                   a value passed from step to step
    What is left open?              inputs: types and functions given in
