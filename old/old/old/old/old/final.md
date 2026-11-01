This file defines a language that can express anything computable.

The definition of computable is left circular: computable means anything this language can express.

The language is made of types and their values. There are three kinds of types:

| Kind of type   | Its values                          |
|----------------|-------------------------------------|
| Type           | function types, data types and Type |
| function types | functions                           |
| data types     | data                                |

Imagine you have a value and a type. To say that the value is a value of that type, you write:

    ⟨value⟩ : ⟨type⟩

where ⟨value⟩ and ⟨type⟩ are placeholders. Placeholders let this file show how something is written for every value and type at once, instead of one at a time. When you use the language, you fill each placeholder with something of the kind written inside it.

For example, Type is a value of the type Type, so you could write:

    Type : Type

A function takes a value, its input, and gives back a value, its output. A function type says what type each one is:

    ⟨input type⟩ → ⟨output type⟩

This is the type of functions that take a value of ⟨input type⟩ and give back a value of ⟨output type⟩.

| Placeholder   | What can fill it |
|---------------|------------------|
| ⟨input type⟩  | any type         |
| ⟨output type⟩ | any type         |

Since a function type is a type, it can itself fill ⟨input type⟩ or ⟨output type⟩. So from Type alone there are endless function types:

    Type → Type                 takes a type, gives back a type
    (Type → Type) → Type        takes a function, gives back a type
    Type → (Type → Type)        takes a type, gives back a function

You might want the output type to depend on the input. For example, a function could take any type and give back a function from that type to itself:

    given Type,             it gives back a (Type → Type)
    given Type → Type,      it gives back a ((Type → Type) → (Type → Type))
    ...                     and so on, a different output type for each input

To write this for every input at once, instead of one at a time, you give the input a name:

    (⟨input name⟩ : ⟨input type⟩) → ⟨output type⟩

| Placeholder   | What can fill it  |
|---------------|-------------------|
| ⟨input name⟩  | a word you choose |

The input's name is called a variable: a name that stands for a value of its type without saying which one, here whichever input is given. Inside ⟨output type⟩, it can fill any placeholder that asks for a value of its type.

The example above is written:

    (A : Type) → (A → A)

⟨input type⟩ → ⟨output type⟩ is short for (⟨input name⟩ : ⟨input type⟩) → ⟨output type⟩, for when ⟨output type⟩ doesn't use the name.

Imagine you have a function and a value of its input type. To give the function that value as its input, you write:

    ⟨function⟩(⟨input⟩)

This is called a call.

| Placeholder | What can fill it                       |
|-------------|----------------------------------------|
| ⟨function⟩  | any function                           |
| ⟨input⟩     | any value of the function's input type |

A call is a value: the output the function gives back for that input. Its type is the function's output type. If the function's type gives the input a name, that variable is replaced by the input.

For example, imagine a function f of type (A : Type) → (A → A):

    f : (A : Type) → (A → A)

f is a variable: it stands for a function of that type without saying which one. The list from before is written with calls:

    f(Type) : Type → Type                                  A replaced by Type
    f(Type → Type) : (Type → Type) → (Type → Type)         A replaced by Type → Type

Since a call is a value, it can fill any placeholder that asks for a value of its type, including the ⟨function⟩ or ⟨input⟩ of another call. f(Type) is a function of type Type → Type, so it can be given an input too:

    f(Type)(Type) : Type

So f takes two inputs, one after the other. For this there is a shorter way to write both the type and the call, with the inputs listed together and separated by a comma:

    (⟨first input name⟩ : ⟨first input type⟩, ⟨second input name⟩ : ⟨second input type⟩) → ⟨output type⟩
    is short for
    (⟨first input name⟩ : ⟨first input type⟩) → ((⟨second input name⟩ : ⟨second input type⟩) → ⟨output type⟩)

    ⟨function⟩(⟨first input⟩, ⟨second input⟩)
    is short for
    ⟨function⟩(⟨first input⟩)(⟨second input⟩)

The same goes for three inputs or more, and each type can use the names written before it. So, with its second input named x, f is written:

    f : (A : Type, x : A) → A
    f(Type, Type) : Type

f stood for a function without saying which one. To have a word for one particular value instead, you define a name:

    ⟨name⟩ : ⟨type⟩
    ⟨name⟩ = ⟨value⟩

Both lines start with the same name: the first says its type, and the second which value it stands for. Together they are called a definition.

| Placeholder | What can fill it                          |
|-------------|-------------------------------------------|
| ⟨name⟩      | a word you choose                         |
| ⟨type⟩      | any type                                  |
| ⟨value⟩     | any value of ⟨type⟩, which can use ⟨name⟩ |

A defined name can fill any placeholder that asks for a value of its type, and means the same as its value written there:

    TypeToType : Type
    TypeToType = Type → Type

    TypeToType → Type        the same as (Type → Type) → Type

So a defined name is unlike a variable: a variable stands for a value without saying which one, and a defined name says which.

⟨value⟩ can use ⟨name⟩ itself, so a definition can refer to what it defines. This is how a function can call itself, which comes later.

A data type is a new type, made together with its values, which are called data. You define it like any name, with Type as its type, and list the ways to make its data, separated by |, which means "or":

    ⟨type name⟩ : Type
    ⟨type name⟩ = ⟨constructor⟩ | …

Each way is called a constructor, and | … means as many more as you like. A data type can have any number of constructors, even none, in which case nothing is written after the =. Each ⟨constructor⟩ is written in one of two ways:

    ⟨constructor name⟩ : ⟨type name⟩                                    is itself data
    ⟨constructor name⟩ : (⟨input name⟩ : ⟨input type⟩) → ⟨type name⟩    gives back data that holds its input

| Placeholder        | What can fill it                |
|--------------------|---------------------------------|
| ⟨type name⟩        | a word you choose               |
| ⟨constructor name⟩ | a word you choose               |
| ⟨input name⟩       | a word you choose               |
| ⟨input type⟩       | any type, including ⟨type name⟩ |

As before, several inputs are written with commas, and the input name can be left out when nothing uses it.

A constructor is a third kind of name. As with a variable, only its type is said, but it doesn't stand for some other value: it makes data of ⟨type name⟩, and that is the only data ⟨type name⟩ has.

Bool's constructors take no inputs, so each one is itself data:

    Bool : Type
    Bool = true : Bool | false : Bool

"A Bool is true or false", and nothing else is a Bool.

A constructor that takes an input is a function. Since its input can be of type ⟨type name⟩, data can hold other data of its own type. Nat, short for natural number, is made this way:

    Nat : Type
    Nat = zero : Nat | succ : Nat → Nat

succ, short for successor, takes a number and gives back the one after it. So Nat's data is made by calls:

    zero                    0
    succ(zero)              1
    succ(succ(zero))        2
    …                       and so on, one for each number

A call to a constructor doesn't become anything else: succ(zero) is itself data, which holds zero.

A data type can also take inputs, called parameters, giving one data type for every parameter at once:

    ⟨type name⟩ : (⟨parameter name⟩ : ⟨parameter type⟩) → Type
    ⟨type name⟩ = ⟨constructor⟩ | …

| Placeholder      | What can fill it  |
|------------------|-------------------|
| ⟨parameter name⟩ | a word you choose |
| ⟨parameter type⟩ | any type          |

Each ⟨constructor⟩ is written in one of the two ways above, but takes the parameter first, so that the rest of its type can use it, and gives back ⟨type name⟩(⟨parameter name⟩) in place of ⟨type name⟩. So ⟨type name⟩ is a function: given a parameter, it gives back a data type. Several parameters are written with commas, as before.

For example, a pair holds two values, and there is a type of pairs for every two types:

    Pair : (A : Type, B : Type) → Type
    Pair = pair : (A : Type, B : Type, a : A, b : B) → Pair(A, B)

Pair(Bool, Nat) is the data type of pairs that hold a Bool and a Nat:

    pair(Bool, Nat, true, zero) : Pair(Bool, Nat)

So far, the only functions have been constructors that take inputs, data types that take parameters, and variables like f, which stand for a function without saying which one. To write a function that works something out, you say what it gives back for each input. A data type's only data is what its constructors make, so this can be said one case at a time, one for each way the input could have been made:

    (⟨pattern⟩) ↦ ⟨result⟩ | …

Each case says: for an input that matches ⟨pattern⟩, the function gives back ⟨result⟩. ↦ reads "gives back", | again means "or", and | … means as many more cases as you like. Written where a value of ⟨input type⟩ → ⟨output type⟩ is asked for, each ⟨pattern⟩ describes a value of ⟨input type⟩, and each ⟨result⟩ is a value of ⟨output type⟩.

| Placeholder | What can fill it                                                  |
|-------------|-------------------------------------------------------------------|
| ⟨pattern⟩   | a description of how the input was made, in one of the ways below |
| ⟨result⟩    | any value of ⟨output type⟩, in one of the ways below              |

A pattern is written like data, with a name in place of each part you leave open:

    succ(succ(zero))     matches just 2
    succ(succ(m))        matches 2 or more
    succ(m)              matches 1 or more
    m                    matches any number

So a pattern is written in one of three ways:

    ⟨name⟩                            matches any input
    ⟨constructor name⟩                matches that constructor, if it takes no inputs
    ⟨constructor name⟩(⟨pattern⟩)     matches data that constructor made, if its input matches ⟨pattern⟩

| Placeholder        | What can fill it                              |
|--------------------|-----------------------------------------------|
| ⟨name⟩             | a word you choose                             |
| ⟨constructor name⟩ | the name of a constructor of the input's type |

The ⟨pattern⟩ inside the third way is again written in any of the three ways, just as succ's input is again a Nat, so a pattern can look as deep inside data as you like.

A ⟨name⟩ in a pattern is a variable: it stands for whatever is in its place, without saying which, and only its own case's ⟨result⟩ can use it. These names are new: they don't have to be the input names in the function's type.

Several inputs, of a function or of a constructor, are written with commas, as in (true, b) or pair(a, b). A constructor's parameters are left out of patterns, since the input's type already says what they are. Only data is made by constructors, so an input that is a function or a type can only be matched by a ⟨name⟩.

Every input must match exactly one case. Since a data type has no data other than what its constructors make, a case for each constructor covers every input.

A result can be any value of ⟨output type⟩, and every value is written in one of seven ways:

    Type                                              the type of types
    (⟨input name⟩ : ⟨input type⟩) → ⟨output type⟩     a function type
    ⟨variable⟩                                        stands for a value without saying which
    ⟨defined name⟩                                    stands for its value
    ⟨constructor name⟩                                a constructor
    ⟨function⟩(⟨input⟩)                               a call
    (⟨pattern⟩) ↦ ⟨result⟩ | …                        a function

For example, one of each:

    Type                     the type of types
    Nat → Nat                a function type
    m                        a variable
    TypeToType               a defined name
    zero                     a constructor
    succ(m)                  a call
    (b) ↦ m                  a function

These are the forms from before, together with the one for functions. The placeholders inside them ask for values again, so values, like patterns, are built from smaller ones. Unlike a pattern, a result can contain calls: a pattern only describes how the input was made, while a result works out something new. If ⟨output type⟩ uses an input name, the result's type has it replaced by the pattern, just as a call's type has it replaced by the input.

A function is usually given a name with a definition. For example, not gives back the other Bool:

    not : Bool → Bool
    not = (true) ↦ false | (false) ↦ true

"Given true, give back false. Given false, give back true." With several inputs, a case has a pattern for each, and the inputs match the case if each one matches its pattern:

    and : (a : Bool, b : Bool) → Bool
    and = (true, b) ↦ b | (false, b) ↦ false

and(true, b) gives back b, whatever it is, and and(false, b) gives back false. A function with several inputs chooses its case only once it has them all: given only its first input, it gives back a function that takes the rest.

A result can call the function being defined, since a definition can use its own name. Adding numbers works this way: adding zero to n gives n, and adding one more than m gives one more than adding m:

    add : (m : Nat, n : Nat) → Nat
    add = (zero, n) ↦ n | (succ(m), n) ↦ succ(add(m, n))

Each time add calls itself, m is one smaller, until it is zero.

In add's second case, where the pattern gives m and n, each of these would be a result of the right type:

    n                    a variable
    succ(m)              a call to a constructor, with a variable inside
    add(m, n)            a call
    succ(add(m, n))      a call with a call inside, which is what add uses

f, from before, stood for some function of type (A : Type, x : A) → A. Now one can be written, the function that gives back its input unchanged:

    identity : (A : Type, x : A) → A
    identity = (A, x) ↦ x

The output type is A, and x is a value of A, as it asks. A pattern for a pair leaves out its parameters:

    first : (A : Type, B : Type, p : Pair(A, B)) → A
    first = (A, B, pair(a, b)) ↦ a

A function written with cases is a value, so it doesn't need a name: it can fill any placeholder that asks for a value of its type. In particular, a result can itself be a function:

    twice : (Bool → Bool) → (Bool → Bool)
    twice = (g) ↦ ((b) ↦ g(g(b)))

twice takes a function g, and gives back a new function that gives its input to g twice. g is a variable that stands for a function, so it can fill the ⟨function⟩ of a call.

A call is worked out step by step, until there is nothing left to work out. This file writes ⟨value⟩ >> ⟨value⟩ to mean that the first works out to the second; like placeholders, >> is not part of the language. A call is worked out like this:

1. Its function and its input are worked out first.
2. The case the input matches is found. If the function is a defined name, the cases of its value are used.
3. The call becomes that case's result, with each of the pattern's variables replaced by what it matched.
4. The result is worked out the same way.

For example:

    not(and(true, false))
    >>  not(false)                       first the input: (true, b) ↦ b, with b replaced by false
    >>  true                             then (false) ↦ true

    add(succ(zero), succ(zero))
    >>  succ(add(zero, succ(zero)))      (succ(m), n) ↦ …, with m replaced by zero and n by succ(zero)
    >>  succ(succ(zero))                 (zero, n) ↦ n, with n replaced by succ(zero)

    twice(not)
    >>  (b) ↦ not(not(b))                (g) ↦ …, with g replaced by not

The second is 1 + 1 = 2. The third gives back a function, which is finished as it is: a function's results are only worked out when it is called. A call to a constructor or to a data type doesn't become anything else either. So working out stops at data, types and functions.

Working out can go on forever, when a function keeps calling itself:

    loop : Bool → Bool
    loop = (b) ↦ loop(b)

loop(true) >> loop(true) >> … never finishes. It is still something the language can write; it just never gives back a value. Since inputs are worked out first, a call never finishes if one of its inputs doesn't, even when it wouldn't use it:

    and(false, loop(true))        never finishes, though (false, b) ↦ false ignores b

Only the result of the matching case is worked out, so something that might never finish is kept from being worked out by putting it in a case's result.

A function can give back a type, so which type it gives back can be chosen by cases:

    Choice : Bool → Type
    Choice = (true) ↦ Nat | (false) ↦ Bool

Another function's output type can then use it:

    pick : (b : Bool) → Choice(b)
    pick = (true) ↦ zero | (false) ↦ false

pick(true) gives back a Nat and pick(false) a Bool, since Choice(true) >> Nat and Choice(false) >> Bool. So checking a type can mean working out a call, and a type can depend on anything the language can compute.

That is the whole language. Here are all its forms in one place:

    a value of a type     ⟨value⟩ : ⟨type⟩

    values                Type
                          (⟨input name⟩ : ⟨input type⟩) → ⟨output type⟩
                          ⟨variable⟩
                          ⟨defined name⟩
                          ⟨constructor name⟩
                          ⟨function⟩(⟨input⟩)
                          (⟨pattern⟩) ↦ ⟨result⟩ | …

    definitions           ⟨name⟩ : ⟨type⟩
                          ⟨name⟩ = ⟨value⟩

    data types            ⟨type name⟩ : Type
                          ⟨type name⟩ = ⟨constructor⟩ | …

    constructors          ⟨constructor name⟩ : ⟨type name⟩
                          ⟨constructor name⟩ : (⟨input name⟩ : ⟨input type⟩) → ⟨type name⟩

    patterns              ⟨name⟩
                          ⟨constructor name⟩
                          ⟨constructor name⟩(⟨pattern⟩)

As before, several inputs are written with commas, an input name that nothing uses can be left out, and a data type's parameters go in front of its type and of every constructor's type.

Every placeholder that asks for a value is filled in one of the seven ways, and those have placeholders of their own, filled the same way. Every way of filling them is something the language can write, and there is no end to them, because the forms combine in four ways:

    nesting               a form can fill a placeholder of another form              not(and(true, false))
    self-reference        a definition can use its own name                          add, Nat
    dependency            a type can use a variable, so depend on a value            (A : Type) → (A → A)
    functions as values   a function can be an input, an output, or held in data     twice

Everything the language computes is built from four things:

    making                a constructor puts values together into data               pair(Bool, Nat, true, zero)
    choosing              cases give back something different for each constructor   not
    combining             a call can fill a placeholder of another call              not(and(true, false))
    repeating             a definition can use its own name                          add
