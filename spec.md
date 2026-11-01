# Spec

Internal reference for `book.md`: the same language in standard notation, so the book's prose can be checked rule by rule. It is not part of the book. If the two disagree, one of them has a bug.

The language is a call-by-value lambda calculus with pattern matching and algebraic data types, plus algebraic effects with deep handlers (as in Plotkin and Pretnar; Pretnar, *An Introduction to Algebraic Effects and Handlers*, 2015). Types are semantic: sets of values, not a checker.

## Programs

A program is declarations and definitions, in any order, then one main expression.

    T(X, …) = d | …                         type declaration
    d ::= c | c(A, …)                       constructor, holding nothing or contents of types A, …
    effect F(X, …) = r : (A, …) → B | …     effect declaration: each request, its inputs, its answer
    n = (p, …) ↦ e | …                      definition: always a function

## Syntax

    e ::= x                                 placeholder
        | c                                 constructor
        | n                                 defined name
        | (p, …) ↦ e | …                    function written out (one case or more)
        | e(e, …)                           application
        | r(e, …)                           request
        | handle e with h | …               handler

    p ::= x | c | c(p, …)

    h ::= (p) ↦ e                           value case
        | (r(p, …), x) ↦ e                  request case; x is bound to the rest

    A ::= T(A, …) | X | (A, …) → B ! Φ      T alone if it takes no types; ! Φ omitted when empty
    Φ ::= {F(A, …), …}

A body extends as far as possible; parentheses only group.

## Names

- Global names: constructors, requests, defined names. Each type, effect and defined name is declared once. Each constructor belongs to one type and each request to one effect. Definitions may use any global name, in any order, including themselves.
- In a pattern, a name is a constructor if declared as one, otherwise a placeholder. The placeholders of one case are distinct.
- **No placeholder has the name of a defined name or a request.** (No placeholder can have a constructor's name, by the previous rule.)
- In a body, a name means the placeholder of the innermost case around it that binds that name. Otherwise it must be a global name.
- Arity: the cases of one function all have the same number of patterns. A request is written with its declared number of inputs, in expressions and in handler cases. A pattern `c(p, …)` has as many patterns as `c` holds.
- In a type declaration, every type name is declared, or one of the declaration's own placeholders. A type may occur in its own constructors' contents, and to the right of an arrow, but never to the left of one (strict positivity).

## Values

    v ::= c                                 any constructor, alone
        | c(v₁, …, vₖ)                      c declared holding exactly k ≥ 1 contents
        | n
        | (p, …) ↦ e | …

A constructor that holds contents, written alone, is a function value. Applied to the right number of values it builds data and takes no step.

## Where steps happen

    K ::= [ ]
        | K(e, …)
        | v(v, …, K, e, …)
        | r(v, …, K, e, …)
        | handle K with h | …

Call by value, left to right, with what is applied first. There are no steps under `↦`. Every expression that is not a value has at most one decomposition `K[e₀]` with `e₀` a redex or an unhandled request, so carrying out is deterministic.

`K` handles `r` when some frame `handle K' with h | …` in `K` has a request case for `r`.

## Matching

    match(x, v)                    = {x ↦ v}
    match(c, v)                    = {}                       if v is c
    match(c(p₁, …, pₖ), c(v₁, …, vₖ)) = match(p₁, v₁) ∪ … ∪ match(pₖ, vₖ)
    otherwise                        no match

A function value is matched only by a placeholder.

## Substitution

`e[σ]` replaces each placeholder of `σ` wherever it occurs free in `e`, and stops at an inner case that binds the same name. This is capture-free with no renaming, for two reasons:

- Every value substituted is closed: programs are closed and no step happens under `↦`, so, by induction on steps, every value that reaches a case has no free placeholders.
- A global name inside a substituted value can never come to mean a placeholder, because no placeholder has a global name.

## Steps

    K[e₀] ⟶ K[e₀']                                   when e₀ ⟶ e₀' below

    ((p⃗₁) ↦ e₁ | … | (p⃗ₘ) ↦ eₘ)(v⃗)  ⟶  eᵢ[σ]      i least with match(p⃗ᵢ, v⃗) = σ
    n(v⃗)                               ⟶  eᵢ[σ]      the same, with the cases of n's definition

    handle v with h | …                ⟶  e[σ]       first value case (p) ↦ e with match(p, v) = σ

    handle K[r(v⃗)] with h | …          ⟶  e[σ, x ↦ ((y) ↦ handle K[y] with h | …)]
                                                     first request case (r(p⃗), x) ↦ e with match(p⃗, v⃗) = σ,
                                                     provided K does not handle r; y fresh

The rest, `(y) ↦ handle K[y] with h | …`, reinstalls the handler (deep handlers). Constructors take no steps.

## How carrying out ends

- **Value.**
- **Waiting:** `K[r(v⃗)]` where `K` does not handle `r`. The outside answers with a value `v'`, and carrying out goes on from `K[v']`.
- **Stuck:** not a value, not waiting, and no step applies. This covers: no case matches; the wrong number of inputs; applying data or a constructor that holds nothing; a handler with no value case that matches; a handler that has a case for `r` when none of its cases for `r` matches the inputs (`K` then handles `r`, so the request is not waiting, and it does not pass outward either).
- **Never:** an infinite sequence of steps.

## What types mean

Types are definitions of sets of values. Carrying out never uses them, and "`e` is of type `A`" is a claim on top of the language.

- `v ∈ T(A⃗)` when `v` is `c(v₁, …, vₖ)` (or `c`, if `c` holds nothing) for a constructor `c` of `T`, with each `vⱼ` in its declared content type, `A⃗` in place of `T`'s placeholders. Recursive types are read as the greatest set closed under this reading: following the definition never finds anything that does not fit. Strict positivity keeps that reading monotone, so the greatest set exists.
- `v ∈ (A⃗) → B ! Φ` when for all `u⃗` in `A⃗`, `v(u⃗)` never gets stuck and leaves unhandled only requests of effects in `Φ`, with inputs of their declared types. With each request answered by a value of its answer type, it ends, if it ends, in a value of `B`. Divergence is allowed (partial correctness).
- A constructor `c` declared `c(A⃗)` in `T(X⃗)` is in `(A⃗) → T(X⃗)`, for each choice of the `X⃗`.

## Where the book says each rule

| rule | book.md |
|---|---|
| definitions are functions; a defined name is a value; `n(v⃗)` step | §2, §3, §4 |
| `K`: where steps happen | §4 (one input), §6 (several), §13 (inside handlers, requests' inputs) |
| patterns, matching, first matching case | §5, §8 |
| naming rules: shadowing, no placeholder with a global name | §5, §10, §13, §19 |
| arity, getting stuck | §6, §7 |
| data, constructors as function values | §8 |
| recursion | §9 |
| functions written out, closures, substitution under `↦` | §10 |
| function types | §10, §18 (`! Φ`) |
| type and effect placeholders | §11, §15 |
| requests, handlers, the rest | §13 |
| waiting and the outside | §18, §19 |
| strict positivity, greatest-set reading | §17, §19 |

## Deliberate choices

- Every definition is a function. Names are values; there is no step from a name to its definition.
- Functions written out appear only from §10, where `against` needs one: a definition cannot use another function's placeholders.
- A handler that has a case for `r` owns `r`. If none of its `r` cases match, carrying out is stuck rather than passing the request outward.
- The book rewrites some definitions as it goes (`beaten`, `against`, `both`, `Player`). Each rewrite replaces the earlier one, so a finished program has one definition per name.
