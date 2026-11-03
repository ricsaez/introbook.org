Everything the rules in [base.md](base.md) allow, one kind at a time. Each section uses only what comes before it.

## Type

    Type : Type                                        Type is a type, so it is a value, of type Type

## Types made with |, whose constructors take no inputs

    Empty : Type
    Empty =                                            no constructors: no values

    Unit : Type
    Unit = unit : Unit                                 one value

    Bool : Type
    Bool = true : Bool | false : Bool                  two values

    Color : Type
    Color = red : Color | green : Color | blue : Color       three values

## Function types

Function types between the types made so far. None of them has values yet: no constructor so far takes inputs.

One input.

    (b : Bool) → Bool : Type

Several inputs.

    (a : Bool, b : Bool) → Bool : Type

A function as an input.

    (f : (b : Bool) → Bool, b : Bool) → Bool : Type

A function given back.

    (a : Bool) → ((b : Bool) → Bool) : Type            given a Bool, gives back a function on Bools

An input or result with no values.

    (e : Empty) → Bool : Type                          takes no value, since Empty has none
    (b : Bool) → Empty : Type                          would give back a value of Empty

Type as an input, and as the result.

    (A : Type) → Type : Type                           takes a type and gives back a type
    (b : Bool) → Type : Type                           a type for each Bool

An input whose type uses an earlier input.

    (A : Type, x : A) → Bool : Type                    x must be of whichever type A is

A result whose type uses an input.

    (A : Type, x : A) → A : Type                       for any type A, gives back a value of that same type
    (A : Type) → ((x : A) → A) : Type                  for any type A, gives back a function on A

## Constructors with inputs

A constructor with inputs is a value of a function type that gives back the type being made.

One constructor with several inputs.

    Pixel : Type
    Pixel = pixel : (lit : Bool, color : Color) → Pixel

    pixel : (lit : Bool, color : Color) → Pixel        a value of a function type
    pixel(true, red) : Pixel

Several constructors, some with inputs.

    Light : Type
    Light = off : Light | on : (color : Color) → Light      off, or on with a color

    on(blue) : Light

A constructor whose input has no values: it can never be called, so it adds no values.

    Shut : Type
    Shut = closed : Shut | blocked : (e : Empty) → Shut     one value: closed

## Types that take themselves

A constructor that takes the type being made.

    Nat : Type
    Nat = zero : Nat | succ : (n : Nat) → Nat          zero, and succ of any Nat: the counting numbers

    succ(succ(zero)) : Nat                             2

A constructor that takes the type being made more than once.

    Tree : Type
    Tree = leaf : Tree | node : (left : Tree, right : Tree) → Tree

    node(leaf, node(leaf, leaf)) : Tree

Two types that take each other: both names appear before either is given its value.

    Rose : Type
    Forest : Type
    Rose = rose : (children : Forest) → Rose                                       a tree with any number of children
    Forest = fnil : Forest | fcons : (first : Rose, rest : Forest) → Forest       the children: none, or a first one and the rest

    rose(fcons(rose(fnil), fnil)) : Rose               a tree with one child, which has none

A type that takes itself, with no constructor that does not: no value is made in finitely many steps, so it has no values.

    Endless : Type
    Endless = more : (e : Endless) → Endless           no values: each one would need one made before it

## Constructors that take functions

A constructor that takes a function.

    NatFun : Type
    NatFun = natfun : (f : (n : Nat) → Nat) → NatFun

    natfun(succ) : NatFun

A constructor that takes a function giving back the type being made: one child for each Bool.

    Tree2 : Type
    Tree2 = leaf2 : Tree2 | node2 : (f : (b : Bool) → Tree2) → Tree2      f(true) and f(false) are the two children

    leaf2 : Tree2                                      the only value yet: no function of type (b : Bool) → Tree2 exists yet

One child for every Nat.

    Ord : Type
    Ord = ozero : Ord | osucc : (o : Ord) → Ord | limit : (f : (n : Nat) → Ord) → Ord      one child for every Nat

    osucc(ozero) : Ord

A constructor that takes a function taking the type being made.

    Value : Type
    Value = num : (n : Nat) → Value | func : (f : (v : Value) → Value) → Value      a number, or a function on values

    num(zero) : Value

A constructor that takes a function whose result type uses its input.

    Poly : Type
    Poly = poly : (f : (A : Type, x : A) → A) → Poly      holds a function that works for every type; no values yet

## Types with inputs

The same type without an input, and with an input.

    NatList : Type
    NatList = natnil : NatList | natcons : (x : Nat, rest : NatList) → NatList                     lists of Nats only

    List : (A : Type) → Type
    List = nil : (A : Type) → List(A) | cons : (A : Type, x : A, rest : List(A)) → List(A)    lists of any type A

    natcons(zero, natnil) : NatList                    the list holding 0
    List(Nat) : Type                                   lists of Nats
    cons(Nat, zero, nil(Nat)) : List(Nat)              the list holding 0: A stands for Nat

A type with several inputs.

    Pair : (A : Type, B : Type) → Type
    Pair = pair : (A : Type, B : Type, a : A, b : B) → Pair(A, B)

    pair(Nat, Bool, zero, true) : Pair(Nat, Bool)

A type with an input, and several constructors.

    Maybe : (A : Type) → Type
    Maybe = nothing : (A : Type) → Maybe(A) | just : (A : Type, x : A) → Maybe(A)      a value of type A, or none

    just(Bool, true) : Maybe(Bool)

A type whose input is a value: each value gives a different type.

    Labelled : (n : Nat) → Type
    Labelled = labelled : (n : Nat, x : Bool) → Labelled(n)      a Bool, labelled with n

    labelled(zero, true) : Labelled(zero)
    labelled(succ(zero), true) : Labelled(succ(zero))  not of type Labelled(zero): succ(zero) is not zero

A type whose inputs are a type and a value.

    Tagged : (A : Type, n : Nat) → Type
    Tagged = tagged : (A : Type, n : Nat, x : A) → Tagged(A, n)      a value of type A, tagged with n

    tagged(Bool, succ(zero), true) : Tagged(Bool, succ(zero))

A type whose second input's type uses the first.

    Fixed : (A : Type, x : A) → Type
    Fixed = fixed : (A : Type, x : A) → Fixed(A, x)      one value for each type A and value x of it

    fixed(Nat, zero) : Fixed(Nat, zero)

A type whose input is a function.

    Applied : (f : (n : Nat) → Nat) → Type
    Applied = applied : (f : (n : Nat) → Nat, x : Nat) → Applied(f)      an x, to be given to f

    applied(succ, zero) : Applied(succ)

A type whose input is a function from types to types.

    Wrap : (F : (X : Type) → Type, A : Type) → Type
    Wrap = wrap : (F : (X : Type) → Type, A : Type, x : F(A)) → Wrap(F, A)      a value of type F(A)

    wrap(List, Nat, nil(Nat)) : Wrap(List, Nat)        x must be of type List(Nat)
    wrap(Maybe, Bool, just(Bool, true)) : Wrap(Maybe, Bool)

## Types given as inputs to types

A type with inputs, given a type that also has inputs.

    List(List(Nat)) : Type
    cons(List(Nat), nil(Nat), nil(List(Nat))) : List(List(Nat))      a list holding one empty list

    Maybe(Maybe(Bool)) : Type
    just(Maybe(Bool), nothing(Bool)) : Maybe(Maybe(Bool))

A type with inputs, given a function type.

    List((n : Nat) → Nat) : Type
    cons((n : Nat) → Nat, succ, nil((n : Nat) → Nat)) : List((n : Nat) → Nat)      a list holding succ

A type with inputs, given Type: types are values, so they can be held.

    List(Type) : Type
    cons(Type, Nat, cons(Type, Bool, nil(Type))) : List(Type)      a list holding Nat, then Bool

    just(Type, Nat) : Maybe(Type)
    pair(Type, Nat, Bool, zero) : Pair(Type, Nat)      a type, and a Nat

## Constructor inputs whose type uses an earlier input

    Packed : Type
    Packed = packed : (A : Type, x : A) → Packed       a value of any type, together with its type

    packed(Nat, zero) : Packed
    packed(Bool, true) : Packed                        values of different types, both of type Packed
    packed(Type, Nat) : Packed                         Nat is a value of type Type

    cons(Packed, packed(Nat, zero), cons(Packed, packed(Bool, true), nil(Packed))) : List(Packed)      a list holding 0, then true

## Types that take themselves inside another type

With the same inputs.

    Nested : (A : Type) → Type
    Nested = nest : (A : Type, x : A, inner : Maybe(Nested(A))) → Nested(A)      a value, and maybe another Nested inside

    nest(Bool, true, nothing(Nested(Bool))) : Nested(Bool)

With different inputs.

    Perfect : (A : Type) → Type
    Perfect = flat : (A : Type, x : A) → Perfect(A) | deeper : (A : Type, rest : Perfect(Pair(A, A))) → Perfect(A)      an A, or a pair of A's, or a pair of pairs, ...

    flat(Bool, true) : Perfect(Bool)                                               one Bool
    deeper(Bool, flat(Pair(Bool, Bool), pair(Bool, Bool, true, false))) : Perfect(Bool)      a pair of Bools

## Second names

Any value made so far can be given another name.

A value of a type made with |.

    two : Nat
    two = succ(succ(zero))

    four : Nat
    four = succ(succ(two))                             uses a name already given

A constructor: a constructor with inputs is a value.

    inc : (n : Nat) → Nat
    inc = succ

A type.

    Truth : Type
    Truth = Bool

A function type.

    BoolToBool : Type
    BoolToBool = (b : Bool) → Bool

A type that takes inputs: it is a value of a function type.

    Seq : (A : Type) → Type
    Seq = List

A value whose type is a type called with an input.

    noNats : List(Nat)
    noNats = nil(Nat)

A name's type, stated again.

    two : Nat                                          two is of type Nat
    two : Bool                                         ✗ two is of type Nat, not Bool

## Same and different

Written differently, but the same: a name means the same as its value.

    four             succ(succ(succ(succ(zero))))      the same Nat
    inc(two)         succ(succ(succ(zero)))            the same Nat
    natfun(inc)      natfun(succ)                      the same NatFun
    Truth            Bool                              the same type
    Seq(Nat)         List(Nat)                         the same type
    Applied(inc)     Applied(succ)                     the same type

Made by different constructors, or with different inputs:

    red              blue                              different values of Color
    on(red)          on(blue)                          different values of Light
    Labelled(zero)   Labelled(succ(zero))              different types

Two types made with |, each new, even with the same shape:

    Switch : Type
    Switch = up : Switch | down : Switch               two values, like Bool, but a different type: true is not of type Switch
