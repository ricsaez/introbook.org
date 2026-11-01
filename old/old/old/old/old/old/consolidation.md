This file defines a language that can express anything computable.

The definition of computable itself is left circular, that is, anything this language can express.

The language has six central pieces: values, types, functions, data, names and variables.

A value is a type, a function, or data.

Every value has a type. The type specifies the shape of its values.

a : A means a is a value of type A.

There are three kinds of type, one for each kind of value:

    Type              its values are types
    function types    their values are functions
    data types        their values are data

Type is the type whose values are types. Type is of type Type, so Type : Type.

A function type is written as

    (x : A) → B     the type of functions that take a value of type A and give back a value of type B

x is a variable: it stands for a hypothetical value given as an input of type A.

A and B can be any types, including Type and function types, which can contain function types in turn:

    (g : (x : A) → B) → C        A is a function type: takes a function
    (x : A) → ((y : B) → C)      B is a function type: gives back a function
    (A : Type) → Type            B is Type: gives back a type

B can use x, so what a function gives back can depend on what it is given. Here the input is a type A, and B is a function type that uses it:

    (A : Type) → ((x : A) → A)      given a type A and a value of A, gives back a value of A

A function f : (x : A) → B is given an input a : A by writing f(a). This is a call, and f(a) : B, with x standing for a in B.

A function that gives back a function can be written as a function of several inputs:

    (x : A, y : B) → C    is short for    (x : A) → ((y : B) → C)
    f(a, b)               is short for    f(a)(b)

A name stands for a value. It is given in two lines:

    n : A        n has type A
    n = v        n stands for v, which must be of type A; v can use n

A data type is made together with its data, by listing its constructors, separated by |, which means "or":

    T : Type
    T = c₁ : A₁ | c₂ : A₂ | ...

This makes two things: the type T, which is a value of Type; and its constructors c₁, c₂, ..., which make the data of T. Each constructor is a new name with no =.

Each Aᵢ is either T, or a function type that gives back T:

    cᵢ : T                       cᵢ is itself data of T
    cᵢ : (x₁ : B₁, ...) → T      cᵢ takes inputs, and cᵢ(a₁, ...) is data of T

A constructor that takes inputs is like a function, but it does nothing else: c(a₁, a₂, ...) is itself new data, made by c from a₁, a₂, ....

A data type can also take inputs. Then T : (y₁ : B₁, y₂ : B₂, ...) → Type, and each constructor takes the same inputs first, and gives back T(y₁, y₂, ...).

A function is made by listing cases, separated by |, which again means "or":

    f : (x : A) → B
    f = (p₁) ↦ r₁ | (p₂) ↦ r₂ | ...

Each case says: for an input that matches pᵢ, f gives back rᵢ. Each rᵢ is of type B, with x standing for pᵢ.

A pattern is data with variables in place of some of its parts:

    c(a₁, a₂)        data: made by c from a₁ and a₂
    c(x, a₂)         a pattern: the first input left open
    c(x, y)          both inputs left open
    x                the whole value left open

A value matches a pattern if it was made the same way, except where the pattern has a variable: there it can be anything, and the variable stands for what is there. rᵢ can use the variables of its pattern.

Only data can be matched by how it was made. If the input is a function or a type, its pattern is a variable.

Every input must match exactly one case. The data of a type is only what its constructors make, so a function that has a case for each constructor has a case for every input.

A function of several inputs lists its cases on all of them at once:

    f : (x : A, y : B) → C
    f = (p₁, q₁) ↦ r₁ | (p₂, q₂) ↦ r₂ | ...

The inputs match a case if each one matches its pattern.

A function made with ↦ is a value, so it can also be written without a name, anywhere a value of its type can.

A call gives back a value by being worked out, one step at a time. a >> b means a works out to b:

    f(a)
    >>  f(v)        first a is worked out, to v
    >>  rᵢ          then the case v matches is found, and the call becomes its rᵢ, with each variable standing for what it matched
    >>  ...         then rᵢ is worked out the same way

Data is finished once its inputs are worked out.

Working out can go on without end: a function can call itself, and keep doing so forever. It is still an expression of the language; it just never gives back a value.
