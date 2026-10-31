# Saying what is true

The language of Sections 1–15 computes. It can check a claim in one case: `even(succ(succ(zero)))` works out to `true`. But it cannot say that a claim holds in every case, or show that it does. The sections below add this.

They are not extensions in the sense of Sections 9–15, since they do not translate back into Sections 1–8. They let types say more, so that checking a program's type can check what the program shows. They add nothing to what can be computed: every program means what it meant before. Section 22 gathers the new forms and rules in one place.

## 16. Claims as types

A type already says something. `A → B` says: given an A, there is a B. Read as a **claim**, it says: if A holds, then B holds. A function of that type shows it, by turning any reason for A into a reason for B.

So every type can be read as a claim, and a value of the type as a reason the claim holds. An expression of the type is a **proof** of the claim when working it out is sure to reach a value; Section 21 says when that is. The forms of Sections 1–8 already combine claims:

| Claim | Type | Its proofs |
|---|---|---|
| A and B | `Pair(A, B)` | `pair(a, b)`, with a proof `a` of A and `b` of B |
| A or B | `Either(A, B)` | `this(a)`, with a proof of A, or `that(b)`, with a proof of B |
| if A, then B | `A → B` | a function from proofs of A to proofs of B |
| for every type X, A | `∀X. A` | a proof that works for every X |

where:

    Either(X, Y) = this(X) | that(Y)

A program is then a proof of its type. `swap` is a function, and also a proof that, for every `X` and `Y`, if `X` and `Y` hold, then `Y` and `X` hold:

    swap : Pair(X, Y) → Pair(Y, X)
    swap = (pair(a, b)) ↦ pair(b, a)

`choice` shows that "or" can be turned around:

    choice : Either(X, Y) → Either(Y, X)
    choice = (this(a)) ↦ that(a) | (that(b)) ↦ this(b)

A claim that always holds is a type with a value that needs nothing: `Unit`, from Section 14, proved by `unit`. A claim that never holds is a type with no values. A declaration can list no constructors:

    T = ∅

For example:

    Never = ∅

Nothing proves `Never`. A function can still take one: since no input can ever arrive, it needs no cases. A function with no cases is also written `∅`:

    ∅ : A → B            when A has no values

For example:

    absurd : Never → X
    absurd = ∅

From something that never holds, anything follows. "Not A" is the claim `A → Never`: if A held, something that never holds would follow. A claim and its opposite cannot both hold:

    clash : Pair(X, X → Never) → Never
    clash = (pair(a, f)) ↦ f(a)

A proof must reach a value. This has type `Never`:

    cheat : Never
    cheat = cheat

but `cheat >> cheat >> …` never reaches one, so it proves nothing. Section 21 says which definitions are sure to finish. Only those count as proofs.

## 17. Claims about values

The claims so far are about types. To say something about particular values, a claim must mention them. The simplest such claim says that two expressions are the same:

    Same(e, e')

`e` and `e'` must have one type. The claim has one proof, `same`, when the two work out to the same value:

    same : Same(e, e')       when e and e' work out to the same

For example, 1 + 1 = 2:

    onePlusOne : Same(add(succ(zero), succ(zero)), succ(succ(zero)))
    onePlusOne = same

Both sides work out to `succ(succ(zero))`. `Same(zero, succ(zero))` has no proof: Section 18 shows it has no values at all.

A claim about every value names the value. A function's inputs can be named, and the type after the arrow can use the names:

    (x : A, …) → B

This reads “for every x of type A, B.” Each input's type can also use the names of the inputs before it, and an input that needs no name is written as before. A call fills in the names with its inputs:

    f(e, …) : B[x := e, …]      when f : (x : A, …) → B and e, … : A, …

Here each input's type also has the names before it filled in. A case's result has the type `B` with each name replaced by the case's pattern for it:

    zeroPlus : (n : Nat) → Same(add(zero, n), n)
    zeroPlus = (n) ↦ same

The case's result must have type `Same(add(zero, n), n)`, where `n` is a variable. Inside a type, an expression is worked out as far as it goes, even when it contains variables. A call takes a step once its function and inputs are worked out as far as they go, when the first of its cases that might match does match. A variable pattern matches anything. A constructor pattern fails against a different constructor, and against a variable it cannot be decided. `add(zero, n)` takes a step by `add`'s first case, `(zero, n) ↦ n`: `zero` matches `zero`, and anything matches the pattern `n`. So it works out to `n`, both sides are `n`, and `same` proves the claim.

`add(n, zero)` cannot take a step: whether `n` matches the pattern `zero` depends on which number `n` is. Two types are **the same** when the expressions in them, worked out as far as they go, are the same. So `same` does not prove `Same(add(n, zero), n)`; Section 19 proves it.

A claim can also name a value that is not an input. `∀x : A. B` reads “for every x of type A, B,” with `x` chosen at each use, as a type variable is:

    e : B[x := e']      when e : ∀x : A. B, for any e' : A

Each use chooses `x` from what it is given, or from the type its place requires.

## 18. Using sameness

A proof of `Same(e, e')` can be matched with the pattern `same`. Within that case, `e` and `e'` are the same, and the case's types can use it. Matching makes them the same in one of these ways:

- if they already work out to the same, nothing changes
- if one is a variable `y` of the definition, not used in the other, `y` is replaced by the other in every type of the case
- if both are made with the same constructor, each pair of contents is made the same
- if they are made with different constructors, the claim has no values, and no case is needed

Any other pair cannot be matched with `same`.

The usual rules about sameness follow as definitions. Sameness turns around:

    turn : ∀x : X. ∀y : X. Same(x, y) → Same(y, x)
    turn = (same) ↦ same

Matching `same` replaces `y` by `x`, so the result must have type `Same(x, x)`, which `same` proves. Sameness chains:

    chain : ∀x : X. ∀y : X. ∀z : X. (Same(x, y), Same(y, z)) → Same(x, z)
    chain = (same, same) ↦ same

The first match replaces `y` by `x`, so the second input has type `Same(x, z)`; the second match replaces `z` by `x`. A function gives the same result for the same input:

    apply : ∀x : X. ∀y : X. (f : X → Y, Same(x, y)) → Same(f(x), f(y))
    apply = (f, same) ↦ same

Different constructors are never the same, so a function taking such a proof needs no cases:

    apart : ∀n : Nat. Same(zero, succ(n)) → Never
    apart = ∅

The same constructor means the same contents:

    shrink : ∀m : Nat. ∀n : Nat. Same(succ(m), succ(n)) → Same(m, n)
    shrink = (same) ↦ same

Matching makes `succ(m)` and `succ(n)` the same by making `m` and `n` the same: `n` is replaced by `m`.

## 19. Proof by recursion

To prove a claim about every number, a proof can take the number apart by cases, and prove the case for `succ(m)` using the proof for `m`. This is recursion, as in Section 4. Read as a proof, it is called proof by **induction**:

    plusZero : (n : Nat) → Same(add(n, zero), n)
    plusZero = (zero) ↦ same
             | (succ(m)) ↦ apply((k) ↦ succ(k), plusZero(m))

The first case must prove `Same(add(zero, zero), zero)`, and both sides work out to `zero`. The second must prove `Same(add(succ(m), zero), succ(m))`. Its left side works out by `add`'s second case to `succ(add(m, zero))`. `plusZero(m)` proves `Same(add(m, zero), m)`, and `apply` puts `succ` around both sides:

    apply((k) ↦ succ(k), plusZero(m)) : Same(succ(add(m, zero)), succ(m))

since `((k) ↦ succ(k))(add(m, zero))` works out to `succ(add(m, zero))`.

Recursion over a list proves a claim about every list:

    mapSame : (l : List(X)) → Same(map(identity, l), l)
    mapSame = (empty) ↦ same
            | (cons(x, rest)) ↦ apply((r) ↦ cons(x, r), mapSame(rest))

In the second case, `map(identity, cons(x, rest))` works out to `cons(x, map(identity, rest))`, and `mapSame(rest)` proves `Same(map(identity, rest), rest)`. `apply` puts `cons(x, …)` around both sides.

Sameness and recursion combine. No number is one more than itself:

    noLoop : (n : Nat, Same(n, succ(n))) → Never
    noLoop = (zero, p) ↦ apart(p)
           | (succ(m), p) ↦ noLoop(m, shrink(p))

In the first case, `p : Same(zero, succ(zero))`, which `apart` turns into `Never`. In the second, `p : Same(succ(m), succ(succ(m)))`, so `shrink(p) : Same(m, succ(m))`: the same claim, for a smaller number.

Each recursive call above is given a part that a pattern took out of the input: `m` from `succ(m)`, and `rest` from `cons(x, rest)`. That is what makes these proofs finish, as Section 21 explains.

## 20. There is

To claim that some value exists, a claim names it:

    Some(x : A, B)

This reads “there is an x of type A for which B holds.” A proof gives the value and a proof about it:

    some(e, e') : Some(x : A, B)      when e : A and e' : B[x := e]

For example, every number has one more:

    above : (n : Nat) → Some(m : Nat, Same(m, succ(n)))
    above = (n) ↦ some(succ(n), same)

A proof of `Some(x : A, B)` can be taken apart with the pattern `some(p, p')`, which gives the value and its proof. If a number is one more than some number, it is not zero:

    notZero : ∀n : Nat. (Some(m : Nat, Same(succ(m), n)), Same(n, zero)) → Never
    notZero = (some(m, p), q) ↦ apart(turn(chain(p, q)))

Here `p : Same(succ(m), n)` and `q : Same(n, zero)`, so `chain(p, q) : Same(succ(m), zero)`, and `turn` gives `Same(zero, succ(m))`, which `apart` turns into `Never`.

## 21. What must finish

A proof is worth only as much as the promise that it finishes. Three things can break that promise. The first is recursion that never ends, as in `cheat`. The second is a data type that holds functions taking itself:

    Bad = bad(Bad → Never)

    open : Bad → Never
    open = (bad(f)) ↦ f(bad(f))

    boom : Never
    boom = open(bad(open))

`boom` uses no recursion at all, yet `open(bad(open)) >> open(bad(open)) >> …` never ends. The third is a request: `fail(unit)`, from Section 14, stands in for a value of any type, `Never` included.

So a definition **finishes** when:

- each name it uses finishes, apart from itself and the names it is defined together with: names that call each other, like `even` and `odd` in Section 4
- each call to itself, or to a name it is defined together with, gives a **smaller part** in one input place, the same place for each call: a variable that a pattern took out of the contents of that input, or out of a smaller part
- each data type it uses is **positive**: the type's own name appears in its contents only outside the inputs of function types
- it uses no operations, handlers, or skips

A **proof** is an expression whose definitions all finish. Claims, too, use only definitions that finish, since checking a claim works out the expressions in it, and that must end. `add`, `map`, `identity`, and `even` finish and can appear in claims; `loop` cannot. Every definition still computes as before.

Claims here are about values and types. A claim cannot be about claims: there is no “for every claim P,” and no function gives back a claim. A property of a value is stated through a function that decides it: “n is even” is `Same(even(n), true)`. Going further means letting claims be values too, with a type for claims, a type for that type, and so on, so that no type contains itself. This part does not take that step.

## 22. Claims and proofs in one place

Each list adds forms to the list of the same kind in Sections 8 and 15, and each group of rules adds to the group of the same name there, or is new. Rows are as in Section 15; since proofs and claims make no requests, the rules here are written without them. Together with Sections 8 and 15, the lists and rules are complete.

    Expressions
        e ::= same                         the proof that two expressions are the same
              some(e, e')                  a value, with a proof about it
              ∅                            a function with no cases

    Values
        v ::= same                         the proof that two expressions are the same
              some(v, v')                  a value, with a proof about it
              ∅                            a function with no cases

    Patterns
        p ::= same                         a proof of sameness, making its two sides the same
              some(p, p')                  a value, and a proof about it

    Types
        A ::= (x : A, …) → B               a function type naming its inputs; each input's type, and B,
                                           can use the names before it; an input can also be unnamed
              ∀x : A. B                    for every value x of type A, chosen at each use
              Same(e, e')                  the claim that e and e' are the same
              Some(x : A, B)               the claim that there is an x of type A for which B holds

    Definitions
        T = ∅                              a data type with no constructors; also T(X, …) = ∅

The old kinds now contain more:

    A        also e, inside Same(e, e')

    Reading
        ∀x : A. B                          reaches as ∀X. A does: to the next comma or closing
                                           parenthesis around it
        x : A, in (x : A, …) → B           ends at the next comma or closing parenthesis

    Scope
        (x : A, …) → B                     each x reaches the types after it, and B
        ∀x : A. B and Some(x : A, B)       x reaches B
        a case's patterns                  the variables they introduce reach the types of the later
                                           inputs and of the result

    Names
        in Same(e, e')                     e and e' are expressions, and a name in them is the variable
                                           of the nearest named input, ∀x, Some(x : …), or pattern that
                                           introduces it; otherwise, as in Section 8

    Working out in types
        an expression in a type            is worked out as far as it goes: a call takes a step once its
                                           function and inputs are worked out as far as they go, when the
                                           first of its cases that might match does match; steps can be
                                           taken anywhere inside the expression
        a variable pattern                 matches anything
        a constructor pattern              matches the same constructor with matching contents, fails
                                           against a different constructor, and is undecided against
                                           anything else
        a case                             is used when it matches and every case before it fails
        a name                             is replaced where it does not stand for a function, or where
                                           it is called and the call can take a step
        two types                          are the same when, worked out as far as they go, they are
                                           written the same, with variables renamed

    Making the same
        e and e'                           already the same: nothing changes
        y and e, or e and y                y a variable of the definition, not in e: y is replaced by e
                                           in every type of the case
        c(e, …) and c(e', …)               each pair of contents is made the same
        c(…) and c'(…)                     different constructors: the claim has no values
        any other pair                     cannot be made the same

    Typing
        e : A'                             when e : A, and A and A' are the same
        e(e', …) : B[x := e', …]           when e : (x : A, …) → B and each e' has its A, with the
                                           names before it replaced by the inputs before it
        (k | …) : (x : A, …) → B           when each case's patterns fit A, …, each A with the names
                                           before it replaced by the patterns before it; each result has
                                           type B with each name replaced by its pattern, using the types
                                           and replacements the patterns give; and every input matches
                                           some case
        e : B[x := e']                     when e : ∀x : A. B and e' : A
        e : ∀x : A. B                      when e : B, with x a new variable of type A, in the type of no
                                           variable e uses from around it
        Same(e, e')                        is a type when e : A and e' : A, and both use only definitions
                                           that finish
        same : Same(e, e')                 when e and e', worked out as far as they go, are the same
        some(e, e') : Some(x : A, B)       when e : A and e' : B[x := e]
        ∅ : A → B                          when A has no values
        A has no values                    when A is a data type that lists no constructors, or a
                                           Same(e, e') whose sides, being made the same, meet different
                                           constructors

    Fitting
        same fits Same(e, e')              when e and e' can be made the same, giving the replacements
        some(p, p') fits Some(x : A, B)    when p fits A and p' fits B with x replaced by p, giving what
                                           each gives

    Matching
        same matches same
        some(v, v') matches some(p, p')    when v matches p and v' matches p', giving what each gives

    Finishing
        a definition finishes              when each name it uses finishes, apart from itself and the
                                           names it is defined together with; each call to one of those
                                           gives a smaller part in one input place, the same for each
                                           call; each data type it uses is positive; and it uses no
                                           operations, handlers, or skips
        names defined together             names that call each other
        a smaller part                     a variable that a pattern took out of the contents of that
                                           input, or out of a smaller part
        a positive data type               one whose own name appears in its contents only outside the
                                           inputs of function types
        a proof                            an expression whose definitions all finish

    Checking definitions
        n : A and n = e                    as in Sections 8 and 15, with every Same in A using only
                                           definitions that finish
        T = ∅                              a data type with no values

With these additions, a proof is a program that finishes, and its type is what it proves. A proof makes no requests and finishes, so working it out reaches a value of its type. `Never` has no values, so nothing proves it, and no claim and its opposite can both be proved. The rest of the language computes exactly as before. What has changed is what can be said about it.
