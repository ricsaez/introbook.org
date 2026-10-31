# A language from a few parts

This language describes computation with two kinds of value: **data** and **functions**. Data can hold functions, functions can take and give back data or other functions, and these combinations can be used inside further combinations. **Types** describe which values fit where.

The rule throughout is the same: **each part of an expression can be any expression of the type its place requires**. Patterns and types are built the same way, each from its own forms. We write every form in notation, so how the parts combine can be read from the forms themselves.

## 1. Expressions, values, and types

An **expression** is something we write in the language. A **value** is an expression that is already finished. Computing means working an expression out, step by step. It may reach a value or continue forever.

We describe each form of the language by its general shape. A **placeholder** is a letter that stands for any part of the right kind: `e` for an expression, `v` for a value, and `A`, `B`, and `C` for types. A prime gives another placeholder of the same kind, so `e'` is another expression. Within one form, the same placeholder stands for the same part; different placeholders can stand for the same part or for different ones. Filling every placeholder in a general form gives one particular instance of it.

A `…` means the part before it can repeat, each time with a new placeholder. So `e, …` stands for `e`, or `e, e'`, or `e, e', e''`, and so on: one or more expressions, separated by commas.

A **type** says what kind of value an expression can give. We write:

    e : A

This reads “e has type A.”

So far, there is one kind of type for each kind of value:

| Type | Its values |
|---|---|
| A data type | Data made with its constructors |
| A function type | Functions with the specified inputs and output |

The simplest data type lists its values. Using `T` for the type's name and `c` for the name of a value:

    T = c | …

The `|` means “or.” These names are the type's **constructors**: the ways to make data of this type. We say `T` **lists** them. For example:

    Bool = false | true

A `Bool` is `false` or `true`; nothing else is a `Bool`. A constructor belongs to one type: no two declarations list the same one. Each constructor is an expression of its type:

    c : T                when T lists c

For example:

    true : Bool

We can give an expression a **name**. Using `n` for the name, the first line says its type, and the second what it stands for:

    n : A
    n = e

For example:

    answer : Bool
    answer = true

A name can stand for any expression of its declared type, and has that type wherever it is used. A definition can use its own name, or other names defined later. Each name is defined once, and no name is both a defined name and a constructor.

To list the forms a kind of part can take, we write its placeholder, then `::=`, then the forms, one per line. So far, expressions and types have these forms:

    e ::= c              a constructor
          n              a defined name

    A ::= T              a data type

Each section adds forms to these lists. A part always has one of the forms in its list. Each place in a form can be filled with any form of the right kind. When a form contains the placeholder of its own kind, parts of that kind can nest inside each other without limit.

## 2. Functions and calls

A **function** says which expression to use for an input. Its type says what it accepts and what it gives back:

    A → B

This reads “given an A, give back a B.” `A` and `B` are placeholders for types, so either can be any type, including another function type. Section 5 shows what that allows.

We make a function from **cases**. A case says which expression to use for one constructor:

    (c) ↦ e

This reads “given `c`, use `e`.” The arrow `→` describes a function's type; `↦` says what to give back. A function lists one or more cases, separated by `|`. Using `k` for a case, a function is:

    k | …

A function is an expression, so we can name it like any other. For a type that lists `c`, `c'`, …, a function with one case per constructor has this shape:

    f : T → B
    f = (c) ↦ e | (c') ↦ e' | …

Each result `e`, `e'`, … has type `B`. For example, `not` fills this shape with `Bool` for both `T` and `B`, `true` and `false` for `c` and `c'`, and `false` and `true` for `e` and `e'`:

    not : Bool → Bool
    not = (true) ↦ false | (false) ↦ true

Given `true`, give back `false`; given `false`, give back `true`.

Giving a function an input is a **call**. Using `f` for a function:

    f(e)

A call has the function's output type:

    f(e) : B             when f : A → B and e : A

For example:

    not(true) : Bool

To work out a call, use the first case that **matches** its input, which for now means the case for that constructor. We write `>>` to show a step; it is explanatory notation, not part of the language:

    f(c) >> e            when (c) ↦ e is the first case of f that c matches

For example:

    not(true) >> false

A case can also accept any value of the input type, by giving it a name that is not a constructor's. This name is a **variable**:

    (x) ↦ e

Within `e`, `x` stands for the value given. We write `e[x := v]` for `e` with each `x` replaced by `v`; like `>>`, this is explanatory notation:

    f(v) >> e[x := v]    when (x) ↦ e is the first case of f that v matches

For example:

    same : Bool → Bool
    same = (b) ↦ b

    same(true) >> true

Here `e` is `b`, and `b[b := true]` is `true`.

The part of a case before `↦` is its **pattern**; we use `p` for one. The part after `↦` is its **result**. So far:

    k ::= (p) ↦ e        a case: a pattern and a result

    p ::= x              any value; call it x
          c              a constructor

The result is `e`, so it can be any form of expression: a constructor like `false`, a variable like `b`, and the forms later sections add, including calls and other functions. Its type must be the function's output type, and it can use the variables its pattern introduces.

So a value matches a pattern when it has the shape the pattern describes: every value matches a variable, and a constructor matches only itself. A pattern **fits** a type when it describes values of that type:

    x fits A             for any A, giving x : A
    c fits T             when T lists c

Within the result, each variable has the type its pattern gives it.

A function has a function type when its cases fit it, and every input has a case to use:

    (k | …) : A → B      when each case's pattern fits A, its result has type B,
                         and each v : A matches some case's pattern

For `not`, both patterns fit `Bool`, both results have type `Bool`, and `true` and `false` each match a case.

A function can take several inputs. Its type lists their types, a call lists the inputs, and each case has one pattern per input:

    (A, …) → B                its type
    f(e, …)                   a call
    (p, …) ↦ e                a case

The inputs pair up with the input types in order, and so do a case's patterns:

    f(e, …) : B               when f : (A, …) → B and e, … : A, …
    (k | …) : (A, …) → B      when each case's patterns fit A, …, its result has type B,
                              and each v, … : A, … match some case's patterns

Here `e, … : A, …` means the first input has the first type, and so on. For example:

    and : (Bool, Bool) → Bool
    and = (true, b) ↦ b | (false, b) ↦ false

    and(true, false) >> false

Every possible combination of inputs must match **some** case. Here, two cases cover all four pairs of Bools:

    and(true, true)      uses (true, b) ↦ b
    and(true, false)     uses (true, b) ↦ b
    and(false, true)     uses (false, b) ↦ false
    and(false, false)    uses (false, b) ↦ false

Cases can overlap: an input can match more than one. Then the first is used, so a last case can cover everything the cases before it leave out:

    or : (Bool, Bool) → Bool
    or = (false, false) ↦ false | (a, b) ↦ true

    or(false, false)     uses (false, false) ↦ false
    or(true, false)      uses (a, b) ↦ true

Expressions, cases, and types now have these forms:

    e ::= c              a constructor
          n              a defined name
          x              a variable
          e(e', …)       a call
          k | …          a function: one or more cases

    k ::= (p, …) ↦ e     a case: one pattern per input, and a result

    A ::= T              a data type
          (A, …) → B     a function type; with one input, A → B

The function being called is itself an expression: a name like `not`, or any expression that gives back a function. In `e(e', …)`, the function and each input can be any expression of the right type.

## 3. Data and patterns

A constructor can hold values, called its **contents**. In the declaration, we list their types:

    c(A, …)

A declaration lists one or more constructors, each with or without contents. Using `d` for one constructor in a declaration:

    d ::= c              a constructor with no contents
          c(A, …)        a constructor with contents of types A, …

    T = d | …

Given contents of those types, the constructor makes data of type `T`:

    c(e, …) : T          when T lists c(A, …) and e, … : A, …

For example:

    BoolPair = bits(Bool, Bool)

    bits(true, false) : BoolPair

`bits` keeps both contents together as one value. `bits(true, false)` is finished: a constructor holds its contents and has no cases to run.

Expressions gain a form:

    c(e, …)              data with expressions for its contents

Each content can be any expression of its type.

Patterns can describe data with contents. A pattern now has three forms:

    p ::= x              any value; call it x
          c              a constructor with no contents
          c(p, …)        a constructor whose contents match the patterns p, …

The third form contains patterns. Each `p` inside it can be any of the three forms again, including another `c(p, …)`. A form that contains its own kind is **recursive**: filling it with itself again gives a deeper instance, as many times as we like:

    x
    c(x)
    c(c(x))
    c(c(c(x)))
    …

Next to each example below is the form it fills, and what it matches:

    bits(a, b)           c(x, x')         every pair
    bits(true, b)        c(c', x)         pairs whose first part is true
    bits(true, false)    c(c', c'')       only that pair

For example:

    left : BoolPair → Bool
    left = (bits(a, b)) ↦ a

    left(bits(true, false)) >> true

Matching now follows the same three forms, so it nests the same way:

    v matches x                  always, giving x := v
    c matches c
    c(v, …) matches c(p, …)      when v, … match p, …, giving what each gives

For example, `bits(true, false)` matches `bits(true, b)`: `true` matches `true`, and `false` matches `b`, giving `b := false`. Nothing else matches: `bits(false, false)` does not match `bits(true, b)`, because no rule lets `false` match `true`.

A pattern with contents fits a type in the same way:

    c(p, …) fits T               when T lists c(A, …) and p, … fit A, …, giving what each gives

Each variable appears once in a case's patterns and can be used any number of times in its result.

Only data can be inspected this way. The forms `c` and `c(p, …)` fit only data types, so an input of function type is matched by a variable, which the result can then call. No form of pattern is a call, so a pattern describes how data was made and contains nothing to compute. A case's result can be any expression of the required type.

A constructor's contents can be of any declared type, including the type being declared:

    T = c | c'(T)

Starting from `c`, this gives one value after another:

    c
    c'(c)
    c'(c'(c))
    …

For example, the natural numbers:

    Nat = zero | succ(Nat)

`succ` means “one more”:

    zero                         0
    succ(zero)                   1
    succ(succ(zero))             2
    …

Each value is finite, but there is no limit on how many layers we can build. Nested patterns can inspect those layers:

    zero                 c                only 0
    succ(n)              c(x)             every number at least 1
    succ(succ(n))        c(c(x))          every number at least 2

Data declarations combine three choices:

    T = c | c' | …         alternatives
    T = c(A, …)            contents held together
    T = c | c'(T)          another layer of the same type

One declaration can use all three at once, as the list in Section 7 does.

## 4. Working expressions out

A call can appear wherever an expression of its type is needed. In the forms `e(e', …)` and `c(e, …)`, each `e` can itself be a call, so expressions nest the way patterns do:

    not(true)                    n(c)
    not(not(true))               n(n(c))
    bits(not(true), true)        c(n(c'), c')

A name takes a step to the expression it names:

    n >> e                         when n = e

For example, `answer >> true`. In examples, we usually skip this step: we keep writing the name, and treat it as the value it names.

If `e >> e'`, the expression around it takes the same step. In these rules, a `…` on its own stands for the other parts, which stay as they are:

    e(…) >> e'(…)                  when e >> e'
    v(…, e, …) >> v(…, e', …)      when e >> e', and every part before e is a value
    c(…, e, …) >> c(…, e', …)      when e >> e', and every part before e is a value

The first rule works out the function being called. Once it is a value, the second works out its inputs from left to right. The third does the same for a constructor's contents. For example, both of these begin with the step `not(true) >> false`: inside a call by the second rule, with `not` for `v`, and inside data by the third, with `bits` for `c`:

    not(not(true)) >> not(false) >> true
    bits(not(true), true) >> bits(false, true)

Once the function and its inputs are values, the call uses the first case they match. Using `w` for the value each variable matched:

    f(v, …) >> e[x := w, …]        when (p, …) ↦ e is the first case of f whose patterns v, … match, giving x := w, …

If the patterns introduce no variables, nothing is replaced, and the call steps to `e` itself, as `not(true) >> false` does.

So to work out a call:

1. Work out the function being called, then its inputs, from left to right.
2. Find the first case they match.
3. Replace the call with that case's result, replacing its variables with the values they matched.
4. Continue working out the result.

Data is finished when its contents are values. A function is already a value: no rule takes a step inside it, so its case results wait until it is called, and only the chosen case's result is used. So values have these forms:

    v ::= c              data with no contents
          c(v, …)        data whose contents are values
          k | …          a function

`c(v, …)` contains values, so values nest too: `succ(succ(zero))` is `c(c(c'))`.

A function can call itself. This is **recursion**. It needs no new form: a case's result can be any expression, including a call to the name being defined. One shape for it, on a type `T = c | c'(T)`, for a function named `f` and using `g : B → B` for another function:

    f : T → B
    f = (c) ↦ e | (c'(x)) ↦ g(f(x))

The second case works out `f` on `x`, one layer inside the input, and gives the result to `g`. Each call removes a layer, until the first case applies. For example, `even` fills this shape with `Nat` for `T`, `Bool` for `B`, `true` for `e`, and `not` for `g`:

    even : Nat → Bool
    even = (zero) ↦ true | (succ(n)) ↦ not(even(n))

    even(succ(zero))
    >> not(even(zero))
    >> not(true)
    >> false

The same shape works with more inputs. Here a second input is carried along, and the result is wrapped in a constructor `c''` instead of given to `g`:

    f : (T, A) → B
    f = (c, x') ↦ e | (c'(x), x') ↦ c''(f(x, x'))

`add` fills this shape with `Nat` for `T`, `A`, and `B`, `n` for both `x'` and `e`, and `succ` for both `c'` and `c''`:

    add : (Nat, Nat) → Nat
    add = (zero, n) ↦ n | (succ(m), n) ↦ succ(add(m, n))

    add(succ(zero), succ(zero))
    >> succ(add(zero, succ(zero)))
    >> succ(succ(zero))

This is 1 + 1 = 2.

Since a definition can use names defined after it, functions can also call each other:

    f : T → B
    f = (c) ↦ e | (c'(x)) ↦ f'(x)

    f' : T → B
    f' = (c) ↦ e' | (c'(x)) ↦ f(x)

Instead of the definition above, we could define `even` together with `odd` this way, with `Nat` for `T`, `Bool` for `B`, and `true` and `false` for `e` and `e'`:

    even : Nat → Bool
    even = (zero) ↦ true | (succ(n)) ↦ odd(n)

    odd : Nat → Bool
    odd = (zero) ↦ false | (succ(n)) ↦ even(n)

    even(succ(zero)) >> odd(zero) >> false

Nothing requires a recursive call to use a smaller input:

    f : A → B
    f = (x) ↦ f(x)

Each step repeats the call:

    f(v) >> f(v) >> …

For example:

    loop : Bool → Bool
    loop = (b) ↦ loop(b)

    loop(true) >> loop(true) >> …

Having a type does not guarantee reaching a value. `and(false, loop(true))` also runs forever: both inputs must finish before its case is used, even if the case discards one.

Left to right is a choice. Working out the inputs in any other order would reach the same value. A different rule would change more: if a case could be used as soon as the inputs its patterns inspect are finished, `and(false, loop(true))` would become `false`. The order of steps can never change which value we reach, but it can decide whether we reach one at all.

To keep an expression from running, put it in a case that is not chosen:

    safe : Bool → Bool
    safe = (true) ↦ true | (false) ↦ loop(false)

    safe(true) >> true

## 5. Functions as values

A function type is a type, so it can fill any place a type can: an input, an output, or a constructor's contents. In `(A, …) → B` and `c(A, …)`, each `A` and `B` can itself be a function type:

    (A → B, …) → C         receives a function
    A → (B → C)            gives back a function
    T = c(A → B, …)        holds a function

The parentheses in `A → (B → C)` group: they show that the output is `B → C`. An arrow that is another arrow's input or output is always grouped like this. Types gain a form:

    (A)                  the same type, grouped

Grouping changes which type we mean:

    Bool → (Bool → Bool)       takes a Bool, gives back a function
    (Bool → Bool) → Bool       takes a function, gives back a Bool

The second is the one-input form of `(A, …) → B`, with `A` filled by `Bool → Bool`.

A function received as an input is matched by a variable, and the result can call it:

    twice : (Bool → Bool, Bool) → Bool
    twice = (g, b) ↦ g(g(b))

    twice(not, true) >> not(not(true)) >> not(false) >> true

A function written with `↦` is a value even without a name. Since `k | …` is a form of expression, a function can be written anywhere an expression can: as an input, as contents, or as a case's result. Expressions can be grouped too:

    (e)                  the same expression, grouped

Grouping only shows where a part ends: `(e)` is the same expression as `e`, and `(A)` the same type as `A`. We use it around a function that is called directly, and around a function that is a case's result, to show where its cases end. As an input or as contents, a function ends at the next comma or closing parenthesis around it. When a replacement puts a function where grouping is needed, we write the grouping.

A function as an input:

    twice((x) ↦ x, true)
    >> ((x) ↦ x)(((x) ↦ x)(true))
    >> ((x) ↦ x)(true)
    >> true

A function as a case's result:

    f : A → (B → C)
    f = (x) ↦ ((x') ↦ e)

The inner result `e` has type `C`, and can use both `x` and `x'`. For example, `constant` fills this shape with `Bool` for `A`, `B`, and `C`, `a` and `b` for `x` and `x'`, and `a` for `e`:

    constant : Bool → (Bool → Bool)
    constant = (a) ↦ ((b) ↦ a)

    constant(true) >> (b) ↦ true

The returned function keeps the supplied value of `a`, because replacing `a` reaches inside the inner function:

    ((b) ↦ a)[a := true]   is   (b) ↦ true

Its own input, `b`, is a separate variable. Since the function in a call `e(e', …)` can be any expression, we can call the result directly:

    constant(true)(false) >> ((b) ↦ true)(false) >> true

This call has the form `e(e')`, with `e` filled by another call, `constant(true)`.

An inner function can use variables from its surrounding case, as `(b) ↦ a` uses `a`. If it introduces a variable with the same name, that name refers to the inner variable within the inner case, and replacing the outer variable stops there:

    ((a) ↦ a)[a := true]   is   (a) ↦ a

A constructor can hold a function, and a pattern can take it out again:

    Task = task(Bool → Bool, Bool)

    perform : Task → Bool
    perform = (task(g, b)) ↦ g(b)

    perform(task(not, true)) >> not(true) >> false

The types distinguish a function taking several inputs from a function returning another function:

    (A, B) → C     called as f(e, e')
    A → (B → C)    called as f(e)(e')

There is no fixed depth to these combinations. A function can receive a function that returns a function:

    pick : (Bool → (Bool → Bool)) → Bool
    pick = (g) ↦ g(true)(false)

    pick(constant) >> constant(true)(false) >> ((b) ↦ true)(false) >> true

and so on.

## 6. Types with parameters

A type can leave the types of its contents open. Using `X`, `Y`, … for **type variables**, names that are not declared types, a data type can take them as **parameters**:

    T(X, …) = d | …

The contents' types in each `d` can use `X`, …. Each parameter stands for whichever type is chosen. For example:

    Pair(X, Y) = pair(X, Y)

This defines pairs for every choice of `X` and `Y`. Types gain two forms:

    X                    a type variable
    T(A, …)              a data type with its parameters filled

Each `A` in `T(A, …)` can be any type, so these nest like every other form:

    Pair(Bool, Nat)              a Bool and a number
    Pair(Bool → Bool, Nat)       a function and a number
    Pair(Bool, Pair(Nat, Nat))   a Bool and a pair of numbers

We write `A[X := B]` for the type `A` with each `X` replaced by `B`, and `d[X := B]` for `d` with each of its contents' types replaced this way. Filling a type's parameters fills them in each of its constructors:

    T(B, …) lists d[X := B, …]     when T(X, …) lists d

The rules for `c` and `c(e, …)` then apply to `T(B, …)` as to any other data type. To construct a pair, we supply the contents themselves, and their types choose the parameters:

    pair(true, zero) : Pair(Bool, Nat)

Here `X := Bool` and `Y := Nat`: `Pair(Bool, Nat)` lists `pair(Bool, Nat)`, and `true : Bool` and `zero : Nat`.

A name's type can use type variables too:

    first : Pair(X, Y) → X
    first = (pair(a, b)) ↦ a

The definition must work for every choice of `X` and `Y`. Each use can choose different types; within one use, the same letter always stands for the same type:

    n : A[X := B, …]     when n : A is declared, for any types B, …

This is **polymorphism**: one definition works at many types. Since the definition cannot know which type was chosen, an input whose type is a type variable is matched by a variable: `c` and `c(p, …)` fit only data types, not `X`. For the same reason, every input must match some case whatever types the type variables stand for.

    identity : X → X
    identity = (x) ↦ x

    identity(true) >> true
    identity(zero) >> zero
    identity(not) >> not

Each use chooses the types from what it is given, or from the type its place requires:

    identity(true)       X := Bool           identity : Bool → Bool
    identity(not)        X := Bool → Bool    identity : (Bool → Bool) → (Bool → Bool)

The same `first` takes a stored function back out, ready to call:

    first(pair(not, zero))(true) >> not(true) >> false

Here `X := Bool → Bool` and `Y := Nat`.

So far, a type variable is chosen where a name is used. Inside the definition, it stands for whatever that use chose, so a function received as an input takes only that one type. This does not work:

    both : (X → X, Pair(Bool, Nat)) → Pair(Bool, Nat)
    both = (g, pair(a, b)) ↦ pair(g(a), g(b))

`g(a)` would require `X` to be `Bool`, and `g(b)` would require it to be `Nat`. To say that the input itself works for every type, a type can say so wherever it is needed:

    ∀X. A

This reads “for every X, A.” An expression has this type when it has type `A` whatever type `X` stands for, and each use of it chooses `X`:

    e : A[X := B]        when e : ∀X. A
    e : ∀X. A            when e : A, and X is in the type of no variable e uses from around it

The condition makes sure `e` does not depend on one choice of `X`. In the first `both`, `g : X → X` cannot become `∀X. X → X`, because that `X` is the one the use of `both` chose.

Several variables nest: `∀X. ∀Y. A`. Like a function, a `∀` reaches to the next comma or closing parenthesis around it. With it, `both` works:

    both : (∀X. X → X, Pair(Bool, Nat)) → Pair(Bool, Nat)
    both = (g, pair(a, b)) ↦ pair(g(a), g(b))

    both(identity, pair(true, zero))
    >> pair(identity(true), identity(zero))
    >> pair(true, identity(zero))
    >> pair(true, zero)

Inside `both`, each use of `g` chooses its own `X`: `Bool` for `a`, and `Nat` for `b`. In return, `both` can only be given something that works for every type: `identity` can be given, but `not` cannot, since it works only for Bools.

A name's declared type already works this way, with the `∀` left unwritten: `identity : X → X` means `identity : ∀X. X → X`. Renaming the variable a `∀` introduces changes nothing: `∀X. X → X` and `∀Y. Y → Y` are the same type. So before replacing type variables inside a `∀`, we rename its variable to a new one: then a replacement can neither reach it nor be confused with it.

Two limits keep types checkable. A `∀` is never guessed: a variable has a type with `∀` only when a declared type gives it one, as the declaration of `both` gives `g`. And a type variable is replaced only by a type with no `∀` in it, so `Pair(∀X. X → X, Nat)` is not a type. To hold something that works for every type, a constructor's contents can say so instead:

    Poly = poly(∀X. X → X)

    pair(poly(identity), zero) : Pair(Poly, Nat)

## 7. Combining the pieces

A list combines alternatives, contents, another layer, and a type parameter:

    List(X) = empty | cons(X, List(X))

    T(X) = c | c'(X, T(X))

A list is empty, or an item followed by another list of the same kind:

    cons(true, cons(false, empty)) : List(Bool)

`map` combines that data with a function input, and applies the function to every item:

    map : (X → Y, List(X)) → List(Y)
    map = (f, empty) ↦ empty
        | (f, cons(x, rest)) ↦ cons(f(x), map(f, rest))

The empty list stays empty. For a nonempty list, transform its first item and repeat on the rest:

    map(not, cons(true, empty))
    >> cons(not(true), map(not, empty))
    >> cons(false, map(not, empty))
    >> cons(false, empty)

`X` and `Y` can differ: a function from numbers to Bools turns a list of numbers into a list of Bools. They can also be function types. With `X := Bool` and `Y := Bool → Bool`, `map` turns a list of Bools into a list of functions:

    map(constant, cons(true, empty))
    >> cons(constant(true), map(constant, empty))
    >> cons((b) ↦ true, map(constant, empty))
    >> cons((b) ↦ true, empty)

`map` works for every `X` and `Y`, but only on lists. A type variable can also need parameters, so the kind of container can be left open too:

    X(A, …)              a type variable with its parameters filled

Such a variable stands for something that still **needs** parameters: a data type's name, with none or some of its first parameters filled. `List` needs one parameter, `Pair` needs two, and `Pair(Bool)` needs one more; `Nat` and `List(Nat)` need none. A type variable with some of its parameters filled needs the rest in the same way. Replacing the variable fills in the rest:

    X(A, …)[X := T]          is   T(A, …)
    X(A, …)[X := T(B, …)]    is   T(B, …, A, …)

For example, using `F` for a type variable that needs one parameter, `F(Nat)[F := List]` is `List(Nat)`, and `F(Nat)[F := Pair(Bool)]` is `Pair(Bool, Nat)`. The same holds when `X` is replaced by another type variable, alone or with some of its parameters filled.

A function can then work on every kind of container, given a function that works for that kind:

    flip : ((Bool → Bool, F(Bool)) → F(Bool), F(Bool)) → F(Bool)
    flip = (m, items) ↦ m(not, items)

With `F := List`, `map` can fill `m`, since with `X := Bool` and `Y := Bool` it has type `(Bool → Bool, List(Bool)) → List(Bool)`:

    flip(map, cons(true, empty)) >> map(not, cons(true, empty))

This works out as in the first example above, to `cons(false, empty)`.

A data type's parameters can need parameters in the same way:

    Counted(F, X) = counted(F(X), Nat)

    counted(cons(true, empty), succ(zero)) : Counted(List, Bool)

`Counted(List, Bool)` lists `counted(List(Bool), Nat)`.

Each type variable needs the same thing throughout one declaration, and its uses show what. Here `F` is used as `F(X)`, so it needs one parameter, and `X` needs none. What something needs includes what its parameters need: `Counted` needs two, and the first of them needs one of its own. A parameter is filled, and a variable replaced, only by something that needs the same. So `List` can fill `F`, but `Nat` and `Pair` cannot. Everywhere else a type appears, such as an input, an output, or a content, it needs nothing.

Every possibility comes from combining these forms:

| Combine | Form | Example |
|---|---|---|
| Several constructors: alternatives | `T = d \| …` | `Bool = false \| true` |
| Several contents: values held together | `c(A, …)` | `pair(X, Y)` |
| Contents of the type being defined: data of any finite size | `T = c \| c'(T)` | `Nat = zero \| succ(Nat)` |
| Several cases: a different result for each way an input was made | `k \| …` | `not` |
| Patterns inside patterns: choices that look several layers into data | `c(p, …)`, with a `p` filled by `c(p, …)` | `succ(succ(n))` |
| Calls inside expressions: one computation feeding another | `e(e', …)` and `c(e, …)`, with an `e` filled by a call | `not(not(true))` |
| A definition using its own name, or later names: repetition for as long as needed | `n = e`, with `n` inside `e` | `even` |
| Function types inside types: functions received, returned, or stored | `(A, …) → B` and `c(A, …)`, with an `A` or `B` filled by a function type | `twice`, `constant`, `Task` |
| Functions inside expressions: functions written where they are used | `k \| …` filling any `e` | `(b) ↦ a` in `constant` |
| Type variables: the same definition used at different types | `T(X, …)` and `X` | `Pair(X, Y)`, `identity` |
| “For every” inside a type: an input or content that works at every type | `∀X. A` | `both`, `Poly` |
| Type variables with parameters: the same definition used for different containers | `X(A, …)` | `flip`, `Counted(F, X)` |

Each result can be combined again. A list can hold pairs of functions:

    List(Pair(Bool → Bool, Nat))

A function can return such a list:

    Nat → List(Pair(Bool → Bool, Nat))

A type variable can leave the functions' input type open:

    Nat → List(Pair(X → Bool, Nat))

A type variable with parameters can leave the container open too:

    Nat → F(Pair(X → Bool, Nat))

## 8. The language in one place

Here `e` means an expression, `v` and `w` values, `k` a case, `p` a pattern, `x` a variable, `n` a defined name, `c` a constructor, `d` a constructor in a declaration, and `T` a data type name. For types, `X` is a type variable, and `A` and `B` stand for any types, including ones that still need parameters. Within one form, the same placeholder stands for the same part. A `…` means the part before it can repeat; in `[x := w, …]` and `∀X. …` it can also stand for none, and in the rules, a `…` on its own stands for the other parts, unchanged. Each kind lists its forms after `::=`, one per line, and each place in a form can be filled with any form of the right kind. In the rules for typing and fitting, `T` can also be a data type with its parameters filled. The lists and rules are complete: nothing else is an expression, a value, a pattern, or a type, and nothing else lists, fits, matches, has a type, needs, is replaced, or takes a step.

    Expressions
        e ::= x                    a variable
              n                    a defined name
              c                    a constructor with no contents
              c(e, …)              data with expressions for its contents
              e(e', …)             a function call
              k | …                a function: one or more cases
              (e)                  the same expression, grouped

    Cases
        k ::= (p, …) ↦ e           one pattern per input, no variable twice, and a result

    Values
        v ::= c                    data with no contents
              c(v, …)              data whose contents are values
              k | …                a function, whose case results wait

    Patterns
        p ::= x                    any value; call it x
              c                    a constructor with no contents
              c(p, …)              a constructor whose contents match p, …

    Types
        A ::= X                    a type variable
              X(A, …)              a type variable with its parameters filled
              T                    a data type's name
              T(A, …)              a data type with its parameters filled
              (A, …) → B           a function type; with one input, A → B
              ∀X. A                a type that works for every X
              (A)                  the same type, grouped

    Definitions
        n : A                      a name's type
        n = e                      what the name stands for
        T = d | …                  a data type and its constructors
        T(X, …) = d | …            a data type with parameters

        d ::= c                    a constructor with no contents
              c(A, …)              a constructor with contents of types A, …

Each kind can contain these kinds:

    e        e, k
    k        p, e
    v        v, k
    p        p
    A        A
    d        A

Expressions, values, patterns, and types each contain their own kind, so each can nest without limit. A case contains an expression, which can be a function with cases of its own.

    Names
        in a pattern               a constructor's name is that constructor; any other name
                                   introduces a variable
        in an expression           a name is the variable of the nearest case around it that
                                   introduces it; otherwise, a constructor or a defined name
        in a type                  a declared data type's name is that type; any other name is
                                   a type variable

    Reading
        (e) and (A)                the same expression and the same type: grouping only shows
                                   where a part ends, and is written wherever reading needs it
        a function                 ends at the next comma or closing parenthesis around it, or at
                                   the end of its definition; called directly, or as a case's
                                   result, it is grouped
        ∀X. A                      reaches to the next comma or closing parenthesis around it
        an arrow                   that is another arrow's input or output is grouped

    Lists
        T lists d                  when d is in T's declaration, and T has no parameters
        T(B, …) lists d[X := B, …] when d is in the declaration of T(X, …)

    Typing
        x : A                      when the nearest case whose patterns introduce x gives x : A
        n : ∀X. … A                when n : A is declared: a ∀ for each type variable that occurs
                                   in A outside every ∀ that introduces it
        e : A[X := B]              when e : ∀X. A, for any B that can replace X
        e : ∀X. A                  when e : A, and X is in the type of no variable e uses from
                                   around it
        a variable's type          has a ∀ only when a declared type gives it one
        c : T                      when T lists c
        c(e, …) : T                when T lists c(A, …) and e, … : A, …
        e(e', …) : B               when e : (A, …) → B and e', … : A, …
        (k | …) : (A, …) → B       when each case's patterns fit A, …, its result has type B
                                   with the types they give, and, whatever types the type
                                   variables in A, … stand for, each v, … : A, … match some
                                   case's patterns

    Fitting
        x fits A                   for any A, giving x : A
        c fits T                   when T lists c
        c(p, …) fits T             when T lists c(A, …) and p, … fit A, …, giving what each gives

    Matching
        v matches x                always, giving x := v
        c matches c
        c(v, …) matches c(p, …)    when v, … match p, …, giving what each gives

    Replacing
        x[x := w]                  is w
        x'[x := w]                 is x', when x' is another variable
        ((p, …) ↦ e)[x := w]       is (p, …) ↦ e, when p, … introduce x
        ((p, …) ↦ e)[x := w]       is (p, …) ↦ e[x := w], otherwise
        any other form             replace in each of its parts
        e[x := w, …]               every replacement at once
        A[X := B, …]               the same, for type variables in a type or a d
        X(A, …)[X := T]            is T(A, …), with the replacements also made in each A
        X(A, …)[X := T(B, …)]      is T(B, …, A, …), with the replacements also made in each A
        with a type variable Y     the same two rules, with Y in place of T
        ∀Y. A                      is the same type as ∀Y'. A[Y := Y'], for a new variable Y'
        (∀Y. A)[X := B, …]         is ∀Y. A[X := B, …], with Y first renamed to a new variable

    Steps
        n >> e                            when n = e
        (k | …)(v, …) >> e[x := w, …]     when (p, …) ↦ e is the first case whose patterns v, … match, giving x := w, …
        e(…) >> e'(…)                     when e >> e'
        v(…, e, …) >> v(…, e', …)         when e >> e', and every part before e is a value
        c(…, e, …) >> c(…, e', …)         when e >> e', and every part before e is a value

    Needs
        T(B, …) and X(B, …)               need the parameters of T or X after B, …; T alone needs all
                                          of them, and X alone what it needs
        X                                 needs the same throughout one declaration, as its uses show,
                                          and nothing if they show no more
        X(A, …) and T(A, …)               each A needs what that parameter needs, and has no ∀ in it
        X := B                            only when B needs what X needs, and has no ∀ in it
        a type anywhere else              needs nothing
        needing the same                  needing as many parameters, each needing the same in turn

    Checking definitions
        n : A and n = e                   when e : A, whatever types the type variables in A stand for
        T = d | …                         when the contents' types use only declared types, and type
                                          variables that a ∀ around them introduces
        T(X, …) = d | …                   the same, and X, …
        n, T, c                           each defined once, and no name both a defined name and a
                                          constructor

`e, … : A, …` and `p, … fit A, …` pair the parts in order: the first with the first, and so on. So a call supplies the number of inputs the function expects, and a constructor the number of contents its declaration lists.

Every expression must have a type by these rules: the type its place requires. The step rules work out the function being called first, then its inputs or a constructor's contents, from left to right. No rule steps a value, so a value takes no steps.

Together, these rules mean an expression that is not a value always has exactly one next step. Types ensure that only functions are called, and only with inputs their cases expect. Every input matches some case, so there is always one to use, and the first is taken, so there is never a choice between two. So working an expression out never gets stuck: it reaches a value or continues forever.

Computation uses four operations: **make** data with constructors, **choose** with cases, **combine** with calls, and **repeat** through names. Together they can express any computation.
