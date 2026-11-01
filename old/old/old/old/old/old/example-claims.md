Claims and their evidence, using the rules of [extension.md](extension.md). The types and functions used are the ones in [example-types.md](example-types.md) and [example-functions.md](example-functions.md). Each section uses only what comes before it.

## Levels

    Type : Type₁
    (A : Type) → Type : Type₁                          takes a type: one level up

    Packed : Type₁
    Packed = packed : (A : Type, x : A) → Packed       holds a type, so it is one level above the types it holds

    List : (A : Type) → Type
    List = nil : (A : Type) → List(A) | cons : (A : Type, x : A, rest : List(A)) → List(A)      A is shared with List, so List(A) stays in Type

## Constructors that give back T with other inputs

    Vec : (A : Type, n : Nat) → Type
    Vec = vnil : (A : Type) → Vec(A, zero) | vcons : (A : Type, n : Nat, x : A, rest : Vec(A, n)) → Vec(A, succ(n))      lists of exactly n values of type A

    vcons(Nat, zero, succ(zero), vnil(Nat)) : Vec(Nat, succ(zero))      exactly one value

    first : (A : Type, n : Nat, v : Vec(A, succ(n))) → A
    first = (A, n, vcons(B, m, x, rest)) ↦ x           no case for vnil: it gives back Vec(A, zero), never Vec(A, succ(n))

## Claims built from types

    And : (A : Type, B : Type) → Type
    And = both : (A : Type, B : Type, a : A, b : B) → And(A, B)                    evidence for both

    Or : (A : Type, B : Type) → Type
    Or = left : (A : Type, B : Type, a : A) → Or(A, B) | right : (A : Type, B : Type, b : B) → Or(A, B)      evidence for one of them, saying which

    Not : (A : Type) → Type
    Not = (A) ↦ ((a : A) → Empty)                                                   evidence for A would give evidence for Empty

    Some : (A : Type, P : (x : A) → Type) → Type
    Some = some : (A : Type, P : (x : A) → Type, x : A, p : P(x)) → Some(A, P)      a particular x, with evidence for P(x)

## Claims with their evidence

    double_one : Eq(Nat, double(succ(zero)), succ(succ(zero)))                     double of 1 is 2
    double_one = refl(Nat, succ(succ(zero)))                                        double(succ(zero)) works out to 2

    both_hold : And(Eq(Bool, not(true), false), Eq(Nat, double(zero), zero))
    both_hold = both(Eq(Bool, not(true), false), Eq(Nat, double(zero), zero), refl(Bool, false), refl(Nat, zero))

    one_holds : Or(Eq(Nat, zero, zero), Empty)
    one_holds = left(Eq(Nat, zero, zero), Empty, refl(Nat, zero))                  right would need a value of Empty

    true_not_false : Not(Eq(Bool, true, false))                                    works out to (a : Eq(Bool, true, false)) → Empty
    true_not_false =                                                               no cases: refl never gives back Eq(Bool, true, false)

    some_double_two : Some(Nat, (x) ↦ Eq(Nat, double(x), succ(succ(zero))))       some Nat has double 2
    some_double_two = some(Nat, (x) ↦ Eq(Nat, double(x), succ(succ(zero))), succ(zero), refl(Nat, succ(succ(zero))))

    not_not : (b : Bool) → Eq(Bool, not(not(b)), b)                               for every b, not(not(b)) is b
    not_not = (true) ↦ refl(Bool, true) | (false) ↦ refl(Bool, false)             one case for each b

## Claims without evidence

    wrong : Eq(Nat, double(succ(zero)), succ(succ(succ(zero))))                    double of 1 is 3
    wrong = refl(Nat, succ(succ(succ(zero))))                                       ✗ of type "3 is 3", but the claim works out to "2 is 3"

## Values that are no evidence

A function that never finishes.

    bad : (n : Nat) → Empty
    bad = (n) ↦ bad(n)                                 ✗ calls itself on the whole of n, not a part

A type that takes itself inside the input of a function.

    Bad : Type
    Bad = make : (f : (b : Bad) → Empty) → Bad         ✗ Bad is inside the input of f

    selfish : (b : Bad) → Empty
    selfish = (make(f)) ↦ f(make(f))                   never calls itself, yet selfish(make(selfish)) never finishes
