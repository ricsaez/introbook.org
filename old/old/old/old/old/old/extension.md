This is [base.md](base.md), changed so that a type can also be read as a claim, and a value of it as evidence that the claim holds. Each change below replaces or narrows one rule of base; every other rule stays as it is.

## Claims and evidence

A type is a claim, and a value of it is evidence that the claim holds. A claim holds when it has evidence. This is a way of reading types, not a new rule: checking evidence is checking that a value is of the type stated, by working out both and comparing.

    Empty                    a claim with no evidence: it never holds
    (a : A) → B              if A then B: turns any evidence for A into evidence for B
    (x : A) → P(x)           for every x of type A, P(x)

## Levels

This replaces Type : Type. A type that holds itself lets evidence be made for any claim, so types come in levels instead:

    Type₀ : Type₁        Type₁ : Type₂        Type₂ : Type₃        ...

Type is Type₀. Each level is a value of the next one.

A function type is a value of the highest level among the types of its Aᵢ and B.

A type made with | is a value of a level at least as high as the type of each constructor input, except the inputs it shares with T.

## Constructors that give back T with other inputs

This replaces "T here means T(y₁, y₂, ...)". If T takes inputs, each constructor's type is a function type that gives back T called with any inputs, written in its type:

    T : (y₁ : B₁, y₂ : B₂, ...) → Typeᵢ
    T = c₁ : (x₁ : A₁, x₂ : A₂, ...) → T(e₁, e₂, ...) | ...

Each eⱼ can use the xᵢ. A constructor then makes values only of T called with those inputs, so a type T(b₁, b₂, ...) can have no values even when T has constructors.

Eq is the claim that two values are the same:

    Eq : (A : Type, x : A, y : A) → Type
    Eq = refl : (A : Type, x : A) → Eq(A, x, x)

A case is needed only for the constructors that can give back the input's type.

## What counts as evidence

This narrows base: a value counts as evidence only if it is made using

- functions that always finish: each call a function makes to itself, or to a function that calls it back, is on a part of one of its inputs: a variable of a pattern, inside a constructor;
- types that do not take themselves inside the input of a function: no constructor of T takes a function whose input's type uses T.

A value that fails either is still a value of base, but it is no evidence: a function that never finishes, or a type that lets a function be given itself, would make every claim hold.

Nothing here says whether two pieces of evidence for the same Eq are the same. That is left open.

All the claims these rules allow, with examples: [example-claims.md](example-claims.md)
