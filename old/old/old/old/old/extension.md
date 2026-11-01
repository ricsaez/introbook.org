This file extends language.md so that a type can state an idea, and a value of the type can show that the idea holds.

It changes three things: data types gain indices, definitions become total or partial, and Type becomes a ladder of universes. The examples use the data types and functions of constructions.md.

## Indices

In language.md, every constructor takes its data type's inputs first and gives them back unchanged. Now those inputs come in two kinds:

    parameters    come first; every constructor takes them and gives them back unchanged, as before
    indices       come after; each constructor gives back whatever values it likes in their place

A list whose type says its length:

    Vec : (A : Type, n : Nat) → Type
    Vec = nil : (A : Type) → Vec(A, zero)
        | vcons : (A : Type, n : Nat, a : A, rest : Vec(A, n)) → Vec(A, succ(n))

A is a parameter. n is an index: nil gives back a Vec of length zero, and vcons one longer than rest.

Two values made by different constructors are never the same, and two made by the same constructor are the same only if their inputs are. So a type can rule a constructor out, and a function needs a case only for the constructors that could have made its input. nil gives back Vec(A, zero), which is never Vec(A, succ(n)), so head has no case for it:

    head : (A : Type, n : Nat, v : Vec(A, succ(n))) → A
    head = (A, n, vcons(A, n, a, rest)) ↦ a

head can't be given an empty list: its type rules it out before anything runs.

As with A in pair(A, B, a, b), a variable can appear again in a pattern where the type forces that part to be the same value. Here the n inside vcons must be the n head was given, since only then is the list a Vec(A, succ(n)).

## Equality

    Equal : (A : Type, x : A, y : A) → Type
    Equal = refl : (A : Type, x : A) → Equal(A, x, x)

refl, short for reflexive, says that x equals itself. A and x are parameters; y is an index, which refl fills with x. So Equal(A, x, y) has data only when x and y are the same value.

Checking a type works out the calls in it, so values that work out to the same thing are equal by refl:

    refl(Nat, two) : Equal(Nat, add(one, one), two)        since add(one, one) >> two

Matching refl shows that y is x, so whatever holds of x holds of y:

    cast : (A : Type, P : (a : A) → Type, x : A, y : A, e : Equal(A, x, y), p : P(x)) → P(y)
    cast = (A, P, x, x, refl(A, x), p) ↦ p

In the case, y is x, so P(y) is P(x), and p fits. Other facts about equality are built from cast. cong, short for congruence, says a function given equal inputs gives back equal outputs:

    cong : (A : Type, B : Type, f : (a : A) → B, x : A, y : A, e : Equal(A, x, y)) → Equal(B, f(x), f(y))
    cong = (A, B, f, x, y, e) ↦ cast(A, (z) ↦ Equal(B, f(x), f(z)), x, y, e, refl(B, f(x)))

## Statements

A type can be read as a statement, and a value of it as a reason the statement holds. A statement with no value has no reason, and is not shown.

    statement                     type
    true                          Unit
    false                         Empty
    A and B                       Pair(A, B)
    A or B                        Either(A, B)
    if A then B                   (a : A) → B
    not A                         (a : A) → Empty
    for every x : A, P(x)         (x : A) → P(x)
    some x : A has P(x)           Exists(A, P)
    x equals y                    Equal(A, x, y)

A reason for "A and B" holds a reason for each. A reason for "if A then B" turns any reason for A into one for B, and a reason for "for every x" gives one for each x.

Empty is a data type with no constructors, so nothing is written after its =, and it has no data. A function given an Empty needs no cases either, since no such input exists:

    Empty : Type
    Empty =

    absurd : (A : Type, e : Empty) → A
    absurd =

Either holds a reason for one side, and Exists holds a particular x together with p, a reason that P holds of it:

    Either : (A : Type, B : Type) → Type
    Either = this : (A : Type, B : Type, a : A) → Either(A, B)
           | that : (A : Type, B : Type, b : B) → Either(A, B)

    Exists : (A : Type, P : (x : A) → Type) → Type
    Exists = witness : (A : Type, P : (x : A) → Type, x : A, p : P(x)) → Exists(A, P)

Some number doubled is two:

    halfOfTwo : Exists(Nat, (n) ↦ Equal(Nat, add(n, n), two))
    halfOfTwo = witness(Nat, (n) ↦ Equal(Nat, add(n, n), two), one, refl(Nat, two))

Zero is not one. refl gives back Equal(Nat, x, x), which would need zero and one to be the same, but they are made by different constructors. So no input exists, and no case is needed:

    zeroNotOne : (e : Equal(Nat, zero, one)) → Empty
    zeroNotOne =

Adding zero to any number gives back the number:

    addZero : (n : Nat) → Equal(Nat, add(n, zero), n)
    addZero = (zero) ↦ refl(Nat, zero)
            | (succ(m)) ↦ cong(Nat, Nat, succ, add(m, zero), m, addZero(m))

In the first case, add(zero, zero) >> zero. In the second, add(succ(m), zero) >> succ(add(m, zero)), and addZero(m) shows that add(m, zero) equals m, so cong with succ gives the rest. A reason for every n is a function that calls itself on a smaller n: proof by induction is recursion.

Any check c : (n : Nat) → Bool can be stated for every number at once:

    (n : Nat) → Equal(Bool, c(n), true)

Running c checks one number at a time, and never all of them; a value of this type would. With c(n) checking that n, if even and above two, is the sum of two primes, this is Goldbach's conjecture, which no one has yet shown or refuted.

## Total and partial

With only what language.md allows, every statement has a reason, even a false one:

    fake : (n : Nat) → Equal(Nat, n, succ(n))
    fake = (n) ↦ fake(n)

fake never gives back a value, so it is no reason at all. So every definition is now total or partial.

A partial definition is marked partial, and can do anything language.md allows, including never finishing:

    partial loop : (b : Bool) → Bool
    loop = (b) ↦ loop(b)

A total definition always finishes. Three rules make sure of it:

1. It uses its own name only to call itself, and there is one input that every such call fills with a part of what was given there: a variable its pattern took out, or what that variable gives back, if it is a function. All data is made in a finite number of steps, so the parts can't get smaller forever.
2. It uses only total definitions, and none that use it.
3. If it is a data type T, a constructor can take T whole, or a function that gives back T, but T can't appear in what such a function takes.

Without the third rule, a value of Empty could be made with no call to itself:

    Bad : Type
    Bad = bad : (f : (b : Bad) → Empty) → Bad

    selfApply : (b : Bad) → Empty
    selfApply = (bad(f)) ↦ f(bad(f))

selfApply(bad(selfApply)) : Empty, and it works out to itself forever.

Every type, even a partial definition's, uses only total definitions, so checking a type always finishes. Only a total value is a reason.

The rules refuse some definitions that do finish, such as from in constructions.md, and no rules could accept exactly the ones that finish, since no program can tell of every program whether it finishes. Whatever is refused can be marked partial, so the language computes as much as before. In constructions.md, while and again, from, turns, run and exec, and eval and apply are partial; the rest is total.

## Universes

Type : Type also lets a value of Empty be made with no call to itself, in a longer way than Bad (Girard's paradox). So instead of one type of types there is a ladder:

    Type₀ : Type₁ : Type₂ : ...

Type alone is short for Type₀. Each level holds the one below it, and everything made from it:

    Typeᵢ : Typeᵢ₊₁
    a type in Typeᵢ is also in Typeᵢ₊₁
    (x : A) → B is in Typeᵢ if A and B are
    a data type is in Typeᵢ if the types of its constructors' inputs are, leaving out its parameters

Pair(Nat, Bool) is in Type₀, since Nat and Bool are. (A : Type₀) → Type₀ is in Type₁, since Type₀ is. Everything in constructions.md is in Type₀, so it is unchanged; only Type : Type, in language.md, is replaced.

A definition that works at every level is written once, with the level left open:

    identity : (A : Typeᵢ, x : A) → A
    identity = (A, x) ↦ x

## Together

    partial    computes anything; a value may never finish
    total      always finishes; a type is a statement, and a value of it is a reason it holds

A partial definition can use total ones, but not the other way round, so reasons stay sound while the language still computes anything.

Some statements can't be settled: neither they nor their not have a value. Gödel showed this holds for every language like this one, so it is a limit of all of them, not a gap in this one.
