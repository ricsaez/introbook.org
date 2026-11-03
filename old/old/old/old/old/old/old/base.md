A value is a thing the language talks about.

A type is a value that says which values belong to it. a : A means a is a value of type A.

Every value has a type.

Type is the type whose values are types. Type is itself a type, so Type : Type.

A name stands for a value. It is given in two lines:

    n : A          n first appears, with its type, which must be a type
    n = v          n stands for v, which must be of type A; v can use any name that has already appeared, n included

From then on, writing n means the same as writing v.

A function is a value that takes inputs and gives back a value. Its type is a function type:

    F : Type
    F = (x₁ : A₁, x₂ : A₂, ...) → B      for types A₁, A₂, ... and B: the type of functions that take inputs of types A₁, A₂, ... and give back a value of type B

Each xᵢ is a variable: a name with no =, standing for whichever input is given. Only the Aᵢ after it, and B, can use it.

A function type is a value, so it can also be written without a name, anywhere a type can.

If f : (x₁ : A₁, x₂ : A₂, ...) → B and each aᵢ : Aᵢ, then the call f(a₁, a₂, ...) stands for the value f gives back for a₁, a₂, ..., and f(a₁, a₂, ...) : B. In the Aᵢ and in B, each xᵢ stands for aᵢ.

A type can also be made by listing its values, separated by |, which means "or". The list can have one value, or none:

    T : Type                         or, if T takes inputs, T : (y₁ : B₁, y₂ : B₂, ...) → Type
    T = c₁ : T₁ | c₂ : T₂ | ...

Each cᵢ is a name with no =, called a constructor, and it stands for nothing else. Its type Tᵢ is either T, and then cᵢ is itself a new value of T; or a function type (x₁ : A₁, x₂ : A₂, ...) → T, and then each call cᵢ(a₁, a₂, ...) is itself a new value of T. If T takes inputs, each constructor takes them first, as y₁, y₂, ..., and T here means T(y₁, y₂, ...).

These are all the values of T: the ones its constructors make in finitely many steps. Two are the same only if made by the same constructor with the same inputs.

All the types these rules allow, with examples: [example-types.md](example-types.md)

A function can also be made by listing cases, separated by |. Each case is written with the sign ↦. The list can have one case, or none:

    f : (x₁ : A₁, x₂ : A₂, ...) → B
    f = (p₁, p₂, ...) ↦ r | (q₁, q₂, ...) ↦ s | ...     for inputs that match p₁, p₂, ..., f gives back r; for inputs that match q₁, q₂, ..., s; ...

A function made with ↦ is a value, so it can also be written without a name, anywhere a value of its type can.

Each pᵢ is a pattern of type Aᵢ: it is written the way a value of type Aᵢ is made, except that any part can be left open. A pattern is one of:

    c(p₁, p₂, ...)     a constructor c that gives back a value of type Aᵢ, with patterns as its inputs; just c if it takes none
    x                  a new name, called a variable, which leaves that part open: the whole pattern, or one of the inputs pᵢ of a constructor

A value matches a pattern if it is made the same way, except where the pattern has a variable: there it can be anything. Each variable stands for the part of the value in its place. So a case can leave a constructor's input open, and its r can use it:

    f = (c(x)) ↦ r          x is a new name for the input of c, and r can use it
    f(c(a))                 stands for r, with x standing for a everywhere in r

Each variable appears once in a case, and is written without a type, which its place already gives.

Each r is of type B. It can use the variables of its case, and in it each xᵢ stands for its pattern.

Every list of inputs must match exactly one case.

A call f(a₁, a₂, ...) is worked out in steps: first each aᵢ is worked out; then the case they match is found, and the call stands for its r, with each variable standing for what it matched, everywhere in r. A call of a constructor is finished once its inputs are. Working out can also happen inside any part, a function's included. a >> b means a works out to b. Working out can go on without end.

Two values are the same if they work out to the same thing. A value of a type is also a value of any type that is the same.

All the functions these rules allow, with examples: [example-functions.md](example-functions.md)
