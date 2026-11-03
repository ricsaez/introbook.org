## The big picture

The language has three kinds of value:

    data         plain information: true, false, a pair of values, a number
    functions    rules that take a value and give back another
    types        labels that say what shape a value has

On top of these there are names, which stand for values, and variables, which stand for inputs that haven't been given yet.

That's everything. With just these, the language can compute anything any computer can.

## Types: what shape a value has

Every value has a type. Writing

    a : A

means "a is a value of type A", or just "a is an A". So true : Bool reads "true is a Bool".

Types are values too, so they also have a type. It is called Type:

    Bool : Type      Bool is a type
    Type : Type      Type is a type too

There is one kind of type for each kind of value: Type for types, function types for functions, and data types for data.

## Data: listing the possibilities

A data type is made by listing every way to make one of its values. Each way is a constructor, and | means "or":

    Bool : Type
    Bool = true : Bool | false : Bool

"A Bool is true or false. Nothing else is a Bool."

A constructor can take inputs, and then it holds them. A data type can take inputs too, and its constructors take those same inputs first:

    Pair : (A : Type, B : Type) → Type
    Pair = pair : (A : Type, B : Type, a : A, b : B) → Pair(A, B)

Pair(Bool, Bool) is the type of pairs of Bools, and pair(Bool, Bool, true, false) is one of them: a single value that holds true and false.

A constructor can take a value of its own type, so data can hold more data like itself. This is how things of any size are made, such as numbers:

    Nat : Type
    Nat = zero : Nat | succ : (n : Nat) → Nat

succ means "one more than". So zero is 0, succ(zero) is 1, succ(succ(zero)) is 2, and so on.

Constructors don't do anything. They have no = and no rule; they only hold their inputs and remember which constructor made them. That is what makes them data.

## Functions: a rule for each possibility

The type of a function says what it takes and what it gives back:

    (x : A) → B      takes an A, gives back a B

x is a variable: it stands for whichever A the function is given.

A function is written as a list of cases, separated by | ("or"). ↦ reads "gives back":

    not : (b : Bool) → Bool
    not = (true) ↦ false | (false) ↦ true

"Given true, give back false. Given false, give back true."

The left side of each case is a pattern: what the input must look like for that case to apply. A pattern is data with variables in some places. A variable matches anything, and stands for what it matched, so the right side can use it:

    first : (A : Type, B : Type, p : Pair(A, B)) → A
    first = (A, B, pair(A, B, a, b)) ↦ a

Every pair matches pair(A, B, a, b), with a standing for whatever is in the first slot. So first gives back the first slot.

The rules for cases:

    exactly one      every possible input must match exactly one case; since a data type has
                     only the values its constructors make, one case per constructor covers them all
    only data        only data can be taken apart by a pattern; a function or type given as
                     input is matched by a plain variable
    no calls         a pattern can't contain calls: it only describes how the input was made
    any result       the right side can be any value of the right type, including calls to
                     other functions and to the function itself

A call gives an input to a function: if f : (x : A) → B and a : A, then f(a) : B, with x replaced by a wherever it appears in B. That last part matters only when B uses x; see "Types that depend on inputs" below.

## Several inputs

    and : (a : Bool, b : Bool) → Bool
    and = (true, b) ↦ b | (false, b) ↦ false

The cases match all the inputs at once: and(true, b) gives back b, whatever it is, and and(false, b) gives back false.

Several inputs are shorthand. A function of two inputs is really a function that takes the first, and gives back a function waiting for the second:

    (a : Bool, b : Bool) → Bool      is short for    (a : Bool) → ((b : Bool) → Bool)
    and(true, false)                 is short for    and(true)(false)

## Functions are values too

A function type can go anywhere a type can, so functions can take functions, give back functions, and give back types:

    (f : (x : A) → B) → C      takes a function
    (x : A) → ((y : B) → C)    gives back a function: this is what several inputs are
    (A : Type) → Type          gives back a type: Pair is one of these, with two inputs

A function given as an input is matched by a variable, and can be called in the result:

    twice : (f : (x : Bool) → Bool, b : Bool) → Bool
    twice = (f, b) ↦ f(f(b))

    twice(not, true)
    >>  not(not(true))
    >>  not(false)
    >>  true

A result can itself be a new function, written with ↦, and it keeps what its variables stood for:

    compose : (f : (x : Bool) → Bool, g : (x : Bool) → Bool) → ((x : Bool) → Bool)
    compose = (f, g) ↦ ((x) ↦ f(g(x)))

    compose(not, not)
    >>  (x) ↦ not(not(x))      a new function, made while running

So a program isn't limited to the functions written in advance: it can build new ones as it runs. And like any value, a function can be held in data: a pair can hold two functions.

## Names

A name is given in two lines: its type, then what it stands for. not and and above are names.

The right side can use the name being defined. This is how a function calls itself:

    add : (m : Nat, n : Nat) → Nat
    add = (zero, n) ↦ n | (succ(m), n) ↦ succ(add(m, n))

"0 + n is n. (m + 1) + n is (m + n) + 1." Each call to add has a smaller m, until m is zero.

A function doesn't need a name. (true) ↦ false | (false) ↦ true can be written anywhere a function is needed, for example as the input of another function.

## Running: replacing step by step

Running a call means rewriting it until no calls are left. a >> b means "a becomes b":

1. Work out the inputs first.
2. Find the case the inputs match.
3. Replace the call with that case's right side, with each variable replaced by what it matched.
4. Work out the result the same way.

    add(succ(zero), succ(zero))
    >>  succ(add(zero, succ(zero)))      matches (succ(m), n), with m = zero and n = succ(zero)
    >>  succ(succ(zero))                 matches (zero, n), with n = succ(zero)

1 + 1 = 2. Only the right side of the matching case is worked out; the other cases are ignored.

Running can go on forever, if a function keeps calling itself without reaching a case that stops:

    loop : (b : Bool) → Bool
    loop = (b) ↦ loop(b)

loop(true) >> loop(true) >> ... never ends. This is allowed; it just never gives back a value. Any language that can compute everything has to allow it.

Because inputs are worked out first, an input that never finishes stops the whole call, even when the call wouldn't use it:

    and(false, loop(true))      never finishes, though and(false, b) ignores b

To avoid working something out, put it on the right side of a case: that is worked out only if the case matches.

## Types that depend on inputs

This is the most advanced idea. Because types are values, a function can take a type as an input, and the rest of its type can use it:

    (A : Type) → ((x : A) → A)

"Give me any type A, and I'll give back a function from A to A." Given Bool, it gives back a (x : Bool) → Bool. What a function gives back, even its type, can depend on what it was given.

This is why the constructions pass types around. swap works on pairs of any two types, so it takes those types first:

    swap : (A : Type, B : Type, p : Pair(A, B)) → Pair(B, A)
    swap = (A, B, pair(A, B, a, b)) ↦ pair(B, A, b, a)

    swap(Bool, Nat, pair(Bool, Nat, true, zero))
    >>  pair(Nat, Bool, zero, true)

When type inputs make a line hard to read, erase them in your head:

    swap(pair(true, zero))
    >>  pair(zero, true)

A function can't take a type apart or choose a case by it (its pattern is always a variable), so in lines like these the types only say what is involved; erasing them leaves what happens unchanged.

The input a type depends on doesn't have to be a type. A function can give back a type chosen by cases on data, like any other result:

    Choice : (b : Bool) → Type
    Choice = (true) ↦ Nat | (false) ↦ Bool

Another function's result type can then be worked out from its input:

    pick : (b : Bool) → Choice(b)
    pick = (true) ↦ zero | (false) ↦ false

pick(true) gives back a Nat and pick(false) a Bool, because Choice(true) >> Nat and Choice(false) >> Bool. So checking types can mean working out calls, and a type can say anything the language can compute.

## The four building blocks

Every computation is made of four things:

    making       constructors put values together          pair(A, B, a, b), succ(n)
    choosing     cases do something for each constructor   not, and
    combining    one call's result is another's input      not(and(true, false))
    repeating    a function calls itself                   add

## Cheat sheet

    a : A          a is an A
    n = v          n stands for v
    |              or
    (x : A) → B    the type of functions that take an A and give back a B
    (p) ↦ r        when the input matches p, give back r
    f(a)           give a to f
    f(a, b)        short for f(a)(b)
    a >> b         a becomes b
    Type           the type of types
