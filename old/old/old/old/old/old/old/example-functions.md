Everything functions made with ↦ in [base.md](base.md) allow, one kind at a time. The types used are the ones in [example-types.md](example-types.md). Each section uses only what comes before it.

## Cases

One case, whose pattern is a variable: it matches any input.

    zero_of : (n : Nat) → Nat
    zero_of = (n) ↦ zero                               ignores its input

    zero_of(succ(zero))  >>  zero

One case for each constructor.

    not : (b : Bool) → Bool
    not = (true) ↦ false | (false) ↦ true

    not(true)  >>  false

Several inputs.

    and : (a : Bool, b : Bool) → Bool
    and = (true, b) ↦ b | (false, b) ↦ false

    and(true, false)  >>  false

A pattern with a constructor that takes inputs: its variables stand for the parts.

    pred : (n : Nat) → Nat
    pred = (zero) ↦ zero | (succ(k)) ↦ k               for succ(succ(zero)), k stands for succ(zero)

    lit : (p : Pixel) → Bool
    lit = (pixel(l, c)) ↦ l

    shining : (x : Light) → Bool
    shining = (off) ↦ false | (on(c)) ↦ true

    pred(succ(succ(zero)))  >>  succ(zero)
    lit(pixel(true, red))  >>  true

A pattern inside a pattern.

    small : (n : Nat) → Bool
    small = (zero) ↦ true | (succ(zero)) ↦ true | (succ(succ(k))) ↦ false      0 and 1 are small

## Inputs with no values

No cases: there is no input to match.

    never : (e : Empty) → Nat
    never =

A constructor whose input has no values needs no case: no value is made by it.

    open : (s : Shut) → Bool
    open = (closed) ↦ false                            blocked(e) would need a value of Empty

## Cases that do not fit

    bad0 : (b : Bool) → Bool
    bad0 = (true) ↦ zero | (false) ↦ true              ✗ zero is not of type Bool

    bad1 : (b : Bool) → Bool
    bad1 = (true) ↦ false                              ✗ false matches no case

    bad2 : (b : Bool) → Bool
    bad2 = (b) ↦ true | (true) ↦ false                 ✗ true matches both cases

    bad3 : (a : Bool, b : Bool) → Bool
    bad3 = (x, x) ↦ true                               ✗ x appears twice

    bad4 : (A : Type) → Bool
    bad4 = (Nat) ↦ true | (A) ↦ false                  ✗ Nat is not a constructor

    bad5 : (f : (n : Nat) → Nat) → Bool
    bad5 = (succ) ↦ true | (f) ↦ false                 ✗ succ does not give back a value of type (n : Nat) → Nat

## Working out

Inputs are worked out first, then the case is found.

    not(not(true))  >>  not(false)  >>  true

A call of a constructor is finished once its inputs are.

    succ(pred(succ(zero)))  >>  succ(zero)

## Functions that call themselves

Through the parts of their input.

    double : (n : Nat) → Nat
    double = (zero) ↦ zero | (succ(k)) ↦ succ(succ(double(k)))

    double(succ(zero))  >>  succ(succ(double(zero)))  >>  succ(succ(zero))

With several inputs.

    plus : (m : Nat, n : Nat) → Nat
    plus = (zero, n) ↦ n | (succ(k), n) ↦ succ(plus(k, n))

    plus(succ(zero), succ(zero))  >>  succ(plus(zero, succ(zero)))  >>  succ(succ(zero))

Through a type that takes itself more than once.

    leaves : (t : Tree) → Nat
    leaves = (leaf) ↦ succ(zero) | (node(l, r)) ↦ plus(leaves(l), leaves(r))

    leaves(node(leaf, leaf))  >>  plus(leaves(leaf), leaves(leaf))  >>  plus(succ(zero), succ(zero))  >>  succ(succ(zero))

Two functions that call each other: both names appear before either is given its value.

    even : (n : Nat) → Bool
    odd : (n : Nat) → Bool
    even = (zero) ↦ true | (succ(k)) ↦ odd(k)
    odd = (zero) ↦ false | (succ(k)) ↦ even(k)

    even(succ(succ(zero)))  >>  odd(succ(zero))  >>  even(zero)  >>  true

Without end.

    forever : (n : Nat) → Nat
    forever = (n) ↦ forever(n)

    forever(zero)  >>  forever(zero)  >>  ...                      never finishes

    zero_of(forever(zero))  >>  zero_of(forever(zero))  >>  ...    never finishes: the input is worked out first

    loop : Nat
    loop = succ(loop)                                              never finishes: loop stands for succ(loop), whose input is loop

## Functions as values

A function without a name.

    natfun((n) ↦ double(n)) : NatFun

A function as an input.

    twice : (f : (n : Nat) → Nat, n : Nat) → Nat
    twice = (f, n) ↦ f(f(n))                           an input of function type is matched by a variable

    twice(succ, zero)  >>  succ(succ(zero))
    twice((n) ↦ double(n), succ(zero))  >>  double(double(succ(zero)))  >>  succ(succ(succ(succ(zero))))

A function given back: the inner function uses the outer one's input.

    adder : (m : Nat) → ((n : Nat) → Nat)
    adder = (m) ↦ ((n) ↦ plus(m, n))

    adder(two)  >>  (n) ↦ plus(succ(succ(zero)), n)              m is put in inside the inner function, which is finished
    adder(two)(succ(zero))  >>  plus(succ(succ(zero)), succ(zero))  >>  succ(succ(succ(zero)))

Opening a constructor that holds a function, and calling what it holds.

    run : (h : NatFun, n : Nat) → Nat
    run = (natfun(f), n) ↦ f(n)

    run(natfun(succ), zero)  >>  succ(zero)

## Values for constructors that take functions

One child for each Bool.

    node2((true) ↦ leaf2 | (false) ↦ leaf2) : Tree2                       a node with two leaves
    node2((true) ↦ leaf2 | (false) ↦ node2((b) ↦ leaf2)) : Tree2          a leaf on the left, a node on the right

One child for every Nat.

    toOrd : (n : Nat) → Ord
    toOrd = (zero) ↦ ozero | (succ(k)) ↦ osucc(toOrd(k))                 each Nat, as an Ord

    fromOmega : (n : Nat) → Ord
    fromOmega = (zero) ↦ limit(toOrd) | (succ(k)) ↦ osucc(fromOmega(k))   counting on after all the Nats

    limit(toOrd) : Ord                                 after every Nat
    osucc(limit(toOrd)) : Ord                          one more
    limit(fromOmega) : Ord                             after all of those

A function on values that are numbers or functions.

    inc : (v : Value) → Value
    inc = (num(n)) ↦ num(succ(n)) | (func(g)) ↦ func(g)

    apply : (g : Value, x : Value) → Value
    apply = (func(f), x) ↦ f(x) | (num(n), x) ↦ num(zero)                a number cannot be called: 0 instead

    self : Value
    self = func((v) ↦ apply(v, v))                     gives its input to itself

    apply(func(inc), num(zero))  >>  inc(num(zero))  >>  num(succ(zero))
    apply(self, self)  >>  apply(self, self)  >>  ...                     never finishes, though no function calls itself

A function that works for every type.

    poly((A, x) ↦ x) : Poly

## Types with inputs

The type's inputs are inputs of the function too.

    length : (A : Type, l : List(A)) → Nat
    length = (A, nil(B)) ↦ zero | (A, cons(B, x, rest)) ↦ succ(length(A, rest))

    map : (A : Type, B : Type, f : (x : A) → B, l : List(A)) → List(B)
    map = (A, B, f, nil(C)) ↦ nil(B) | (A, B, f, cons(C, x, rest)) ↦ cons(B, f(x), map(A, B, f, rest))

    withDefault : (A : Type, d : A, m : Maybe(A)) → A
    withDefault = (A, d, nothing(B)) ↦ d | (A, d, just(B, x)) ↦ x

    length(Nat, cons(Nat, zero, nil(Nat)))  >>  succ(length(Nat, nil(Nat)))  >>  succ(zero)
    map(Nat, Nat, succ, cons(Nat, zero, nil(Nat)))  >>  cons(Nat, succ(zero), map(Nat, Nat, succ, nil(Nat)))  >>  cons(Nat, succ(zero), nil(Nat))
    withDefault(Bool, false, just(Bool, true))  >>  true

## Types as results

A function that gives back a type.

    Pick : (b : Bool) → Type
    Pick = (true) ↦ Nat | (false) ↦ Bool

    Pick(true)  >>  Nat

A result whose type uses an input: each r is of the result type, with the input standing for its pattern.

    choose : (b : Bool) → Pick(b)
    choose = (true) ↦ zero | (false) ↦ true            zero : Pick(true), which works out to Nat; true : Pick(false), which works out to Bool

    id : (A : Type, x : A) → A
    id = (A, x) ↦ x

    id(Nat, zero)  >>  zero

## Same and different

Written differently, but the same: both work out to the same value.

    not(true)              false
    double(succ(zero))     two
    Pick(true)             Nat
    Labelled(pred(two))    Labelled(succ(zero))            the same type
