# Saying things easily

The language of Sections 1–8 can say anything that can be computed. Some things that are simple to think are still awkward to say in it. Each section below adds forms for one of them.

A language is easy to say things in when:

- a thing can be said once
- a thing can be said in one place
- anything can go anywhere
- it has few primitives, freely combined
- the words we define work as well as the ones it starts with
- it reads like the idea
- it means one thing, and can be checked and reasoned about

The language of Sections 1–8 already has few primitives, freely combined. Its words, like `Bool` and `Nat`, are defined like any others, and its rules say exactly what each expression means and when it is well typed. Each section below names the point it serves.

Each addition can be **translated** back into the forms of Sections 1–8, one definition at a time, and a program means the same as its translation. So no addition lets us compute anything new: each makes something easier to say. Additions are of three kinds:

- **Convenience:** the translation changes only the definition where the new form is used, adding new names if it needs them.
- **Abstraction:** a thing can be said once, at the right level, and a change to it stays in one place.
- **Expressiveness:** without it, one local idea forces changes to every definition it passes through.

Section 15 gathers the new forms and rules in one place.

## 9. Local definitions

*Convenience. Anything can go anywhere: a name can be given inside an expression.*

So far, every name is defined on its own, outside any expression. To use the result of a calculation twice, we write it twice:

    quadruple : Nat → Nat
    quadruple = (n) ↦ add(add(n, n), add(n, n))

or give it a name by calling a function on the spot:

    quadruple = (n) ↦ ((d) ↦ add(d, d))(add(n, n))

A **local definition** names an expression where it is used:

    let x = e' in e

This reads “let x be e' in e.” The name `x` is a variable: within `e`, it stands for the value of `e'`. For example:

    quadruple : Nat → Nat
    quadruple = (n) ↦ let d = add(n, n) in add(d, d)

`x` has the type of `e'`:

    (let x = e' in e) : B          when e' : A and e : B, with x : A

`e'` is worked out first. Its value then replaces `x` in `e`:

    let x = e' in e >> let x = e'' in e      when e' >> e''
    let x = v in e >> e[x := v]

So `add(n, n)` is worked out once:

    quadruple(succ(zero))
    >> let d = add(succ(zero), succ(zero)) in add(d, d)
    >> let d = succ(add(zero, succ(zero))) in add(d, d)
    >> let d = succ(succ(zero)) in add(d, d)
    >> add(succ(succ(zero)), succ(succ(zero)))

This is 2 + 2, which works out to 4.

`x` reaches only `e`: an `x` in `e'` means whatever `x` means around the local definition. `e'` ends at `in`, so it can be a function with several cases. The expression after `in` continues as far as it can: up to the next `|`, comma, `in`, or `by` around the local definition, a closing parenthesis around it, or the end of the definition.

The translation is the function called on the spot:

    let x = e' in e      is      ((x) ↦ e)(e')

Both work out `e'` first, then replace `x` in `e` with its value.

A local function can also call itself. A **recursive local definition** lets `x` reach `e'` too, and `e'` must be written as a function, with one or more cases:

    let rec x = e' in e

A local function can then repeat, using the variables around it:

    times : (Nat, Nat) → Nat
    times = (m, n) ↦
        let rec go = (zero) ↦ zero | (succ(i)) ↦ add(n, go(i))
        in go(m)

`go` adds `n` once for each layer of `m`, so `times` multiplies.

Inside its own cases, `go` must again stand for the whole function. So before the function replaces `go` in `e`, each `go` inside it is replaced by the local definition itself. Using `v` for the function:

    let rec x = v in e >> e[x := v[x := let rec x = v in x]]

When a case reaches `let rec x = v in x`, it works out by the same rule to the same function again.

The translation of a local function moves it out to a defined name of its own. The variables it uses from around it become extra first inputs, which every use passes along; a use that is not a call becomes a function that passes them, such as `(i) ↦ go(n, i)`. Here, using `go` as the new name:

    go : (Nat, Nat) → Nat
    go = (n, zero) ↦ zero | (n, succ(i)) ↦ add(n, go(n, i))

    times : (Nat, Nat) → Nat
    times = (m, n) ↦ go(n, m)

The translation still changes only the definition that holds the local function.

Either kind of local definition can declare its type, as a name does:

    let x : A = e' in e
    let rec x : A = e' in e

Then `e'` must have type `A`, and `x` has type `A` wherever it reaches. The type variables in `A` are those of the declaration around it, so to work for every type, `A` says so with `∀`:

    twins : (Bool, Nat) → Pair(Pair(Bool, Bool), Pair(Nat, Nat))
    twins = (b, n) ↦
        let twin : ∀X. X → Pair(X, X) = (x) ↦ pair(x, x)
        in pair(twin(b), twin(n))

Without its declared type, `twin` would have one type, and could not be used for both a Bool and a number: as everywhere in the language, a `∀` is never guessed.

A function called on the spot cannot give `x` a declared type, so the translation moves `e` out to a new defined name instead. Using `h` for it, `y, …` for the variables `e` uses from around the local definition, `A', …` for their types, and `B` for the type of `e`:

    h : (A, A', …) → B
    h = (x, y, …) ↦ e

`h` is given whatever the declaration around it is given, and the local definition becomes `h(e', y, …)`, which still works out `e'` first. For a local function with a declared type, the defined name's type is `A` with the extra inputs added first.

## 10. Names for types

*Convenience. A thing can be said once: a long type can be given a short name.*

The same long type can be needed in many places:

    List(Pair(Bool, Nat))

A **type abbreviation** gives a type a name. Using `T` for the name:

    type T(X, …) = A

With no parameters, it is written `type T = A`. Wherever `T(B, …)` is written, it means `A[X := B, …]`. For example:

    type Counts(X) = List(Pair(X, Nat))

    tally : Counts(Bool)
    tally = cons(pair(true, succ(zero)), cons(pair(false, zero), empty))

`Counts(Bool)` means `List(Pair(Bool, Nat))`: the two are the same type, and either can be written. An abbreviation is not a new data type, so it has no constructors of its own.

The translation writes out what each abbreviation means, then removes its declaration. So an abbreviation may not use itself, directly or through others, since writing it out would never end. It is always used with all its parameters: filling only some would leave something that is not a data type's name, which no type variable can stand for.

## 11. Named contents

*Convenience. It reads like the idea: contents are known by name.*

A constructor's contents are known by their place:

    Size = size(Nat, Nat)

Nothing says which is the width. To read one content, we write a function with a pattern; to change one, we make new data and repeat the others.

A declaration can name each content. Using `l` for a content's name:

    c(l : A, …)

A constructor names all its contents or none, and each name differs. For example:

    Size = size(width : Nat, height : Nat)

Data can then be written with the names:

    c(l = e, …)

This is the same data as `c(e, …)`, with each expression in its name's place:

    wide : Size
    wide = size(width = succ(succ(zero)), height = succ(zero))

The names can come in any order: `size(height = succ(zero), width = succ(succ(zero)))` is the same data.

A content can be read by its name:

    e.l

This reads “the l of e”:

    wide.width >> succ(succ(zero))

A copy can replace some contents by name and keep the rest:

    e with (l = e', …)

For example:

    wide with (height = zero) >> size(succ(succ(zero)), zero)

A pattern can match contents by name. The contents it leaves out match anything:

    c(l = p, …)

For example:

    area : Size → Nat
    area = (size(width = w, height = h)) ↦ times(w, h)

    flat : Size → Bool
    flat = (size(height = zero)) ↦ true | (s) ↦ false

In each of these forms, each name appears at most once, in any order, and `c(l = e, …)` gives every one. The expressions are worked out in the order they are written. Content names appear only in these forms, so they can share their spelling with other names.

Reading and replacing by name require a type with only one constructor. With several, the data might have been made by one without that content:

    c(l = e, …) : T              when T lists c with l : A, …, every content named once, and e, … : A, …
    e.l : A                      when e : T and T lists only c(…, l : A, …)
    e with (l = e', …) : T       when e : T, T lists only a constructor with l : A, …, and e', … : A, …
    c(l = p, …) fits T           when T lists c with l : A, …, and p, … fit A, …, giving what each gives

Each form translates within the definition that uses it. Using new variables, `x, …` for a constructor's contents and `y, …` for the expressions given, and a new defined name `h` with a declared type, so that a content that works for every type keeps its `∀`:

    c(l = e, …)            is   h(e, …), with h : (A, …) → T and h = (y, …) ↦ c(…), each y in its name's place
    e.l                    is   ((c(x, …)) ↦ x')(e), with x' the variable in l's place
    e with (l = e', …)     is   h(e, e', …), with h : (T, A, …) → T and h = (c(x, …), y, …) ↦ c(…), each y in its name's place, and each other place keeping its x
    c(l = p, …)            is   c(p, …), with each p in its name's place, and a new variable in each other

Here `A, …` are the types of the expressions given, in the order they are written. `h` takes them in that order, so they are worked out in that order, then puts each in its place. Named contents are written out as their translations before anything is worked out, so they take exactly the steps of their translations.

## 12. Interfaces

*Abstraction. A thing can be said once: one name, with an implementation for each type.*

Some ideas apply to many types, but work differently for each. Equality is one: two Bools are equal in a different way from two numbers. So far, each type must have its own name for it, and a definition that uses equality for any type must take it as an input:

    member : ((X, X) → Bool, X, List(X)) → Bool

Every use passes the right comparison by hand. `flip`, in Section 7, had to be given `map` in the same way.

An **interface** names a group of definitions and their types, leaving one type open. Using `I` for the interface's name:

    interface I(X):
        n : A
        …

The types `A`, … use `X`. For example:

    interface Equal(X):
        equal : (X, X) → Bool

An **implementation** gives the definitions for one data type:

    I(T):
        n = e
        …

Each definition has the type its interface gives, with `X` replaced by `T`:

    Equal(Bool):
        equal = (true, b) ↦ b | (false, b) ↦ not(b)

    Equal(Nat):
        equal = (zero, zero) ↦ true
              | (succ(m), succ(n)) ↦ equal(m, n)
              | (m, n) ↦ false

The last case covers every pair the first two leave out. Each interface has at most one implementation for each data type, so the types at a use choose its implementation:

    equal(true, true) >> true              uses Equal(Bool)
    equal(zero, succ(zero)) >> false       uses Equal(Nat)

A name's declaration can say that it works for every type with an implementation:

    n : A given I(X), …

For example, using `or` from Section 2:

    member : (X, List(X)) → Bool given Equal(X)
    member = (x, empty) ↦ false
           | (x, cons(y, rest)) ↦ or(equal(x, y), member(x, rest))

    member(zero, cons(succ(zero), cons(zero, empty)))
    >> or(equal(zero, succ(zero)), member(zero, cons(zero, empty)))

The second item equals `zero`, so this works out to `true`. Within `member`, `equal` at `X` uses whichever implementation the use of `member` chose. So inside `member`, `X` is that one chosen type, and nothing there can be given a `∀` over `X`. A use can choose only a type that has one:

    member(zero, cons(zero, empty))        X := Nat, using Equal(Nat)
    member(not, cons(not, empty))          not allowed: Bool → Bool has no implementation of Equal

An implementation for a data type with parameters can require implementations for them. Using `Y`, … for its parameters:

    I(T(Y, …)) given I'(Y), …:
        n = e
        …

For example:

    Equal(List(X)) given Equal(X):
        equal = (empty, empty) ↦ true
              | (cons(x, xs), cons(y, ys)) ↦ and(equal(x, y), equal(xs, ys))
              | (xs, ys) ↦ false

Here `equal(x, y)` compares two items, using `Equal(X)`, and `equal(xs, ys)` compares the rest, using this implementation again. Lists of numbers, lists of lists of numbers, and so on can now be compared. An implementation is **available** for a type when one exists for it, with those it is given available in turn, or when the declaration being checked is given it.

Each use must settle the types that choose its implementations. In `equal(empty, empty)`, nothing says what the lists hold, so no implementation for the items can be chosen, and the use is not allowed. In `equal(empty, cons(zero, empty))`, the second list settles it.

An interface can require another. Using `I'` for the interface required:

    interface I(X) given I'(X), …:
        n : A
        …

Then each implementation of `I` needs one of `I'` for the same type, and a declaration given `I(X)` is given `I'(X)` too. For example, ordering a type needs its equality:

    interface Order(X) given Equal(X):
        before : (X, X) → Bool

    Order(Nat):
        before = (zero, succ(n)) ↦ true
               | (succ(m), succ(n)) ↦ before(m, n)
               | (m, n) ↦ false

    atMost : (X, X) → Bool given Order(X)
    atMost = (a, b) ↦ or(before(a, b), equal(a, b))

`atMost` is given only `Order(X)`, and can use `equal` because `Order` requires `Equal`.

An interface's open type can need parameters, as in Section 7. Then the interface leaves the kind of container open:

    interface Container(F):
        each : (X → Y, F(X)) → F(Y)

    Container(List):
        each = map

Here the implementation is for `List` itself, with no parameters filled, since `F` needs one parameter and so does `List`. In general, an implementation fills a data type's first parameters, each with a different type variable, until what is left needs what the open type needs.

`each` works on whichever kind of container it is given, so `flip` no longer needs `map` passed to it:

    negate : F(Bool) → F(Bool) given Container(F)
    negate = (items) ↦ each(not, items)

    negate(cons(true, empty)) >> each(not, cons(true, empty))

`F := List` chooses `Container(List)`, so `each` is `map`, and this works out to `cons(false, empty)`.

Interfaces translate one definition at a time. Each implementation becomes defined names, one for each of its definitions; one given other implementations takes their names, and those of the interfaces they require, as extra first inputs. Each `given I(X)` in a declaration becomes extra first inputs too: one for each name of `I`, and of each interface `I` requires, with the type the interface gives it and a `∀` for each of its own type variables. Each use of a name passes the implementations its chosen types require; a use that is not a call becomes a function that passes them. So `member` becomes the version we began with:

    member : ((X, X) → Bool, X, List(X)) → Bool
    member = (eq, x, empty) ↦ false
           | (eq, x, cons(y, rest)) ↦ or(eq(x, y), member(eq, x, rest))

and `negate` becomes `flip`, except that the function it is given works for every type:

    negate : (∀X. ∀Y. (X → Y, F(X)) → F(Y), F(Bool)) → F(Bool)
    negate = (m, items) ↦ m(not, items)

Interfaces take the steps of their translations; in examples, we leave out the implementations being passed.

## 13. Hiding

*Abstraction. A thing can be said in one place: how a type is made.*

Any definition can make data with a type's constructors and look inside it with patterns. So any definition can depend on how a type is made, and nothing keeps what its makers intended.

For example, a **set** is a collection in which no item appears twice. A list can hold one:

    Set(X) = set(List(X))

    insert : (X, Set(X)) → Set(X) given Equal(X)
    insert = (x, set(items)) ↦
        ((true) ↦ set(items) | (false) ↦ set(cons(x, items)))(member(x, items))

`insert` adds an item only when it is not already there: the function called on the spot chooses by the result of `member`. But any definition can also write `set(cons(zero, cons(zero, empty)))`, which holds `zero` twice.

A **hidden** data type lets only the definitions indented below it use its constructors:

    hidden T = d | …
        n : A
        n = e
        …

For example:

    hidden Set(X) = set(List(X))

        none : Set(X)
        none = set(empty)

        insert : (X, Set(X)) → Set(X) given Equal(X)
        insert = (x, set(items)) ↦
            ((true) ↦ set(items) | (false) ↦ set(cons(x, items)))(member(x, items))

        has : (X, Set(X)) → Bool given Equal(X)
        has = (x, set(items)) ↦ member(x, items)

Everywhere else, `Set`, `none`, `insert`, and `has` can be used, but `set` cannot, in an expression or a pattern:

    has(zero, insert(zero, insert(zero, none)))      allowed
    set(cons(zero, cons(zero, empty)))               not allowed: set is hidden

Only `insert` adds items, and it checks first, so no set holds an item twice. How a set is made is said in one place: changing it means changing only the definitions below the declaration. Reading or replacing a hidden type's named contents counts as using its constructor, and implementations of interfaces for it can be written below it too.

Hiding only takes possibilities away. Its translation removes the word `hidden`, and the program means the same.

## 14. Effects and handlers

*Expressiveness. A thing can be said in one place: an expression can ask for something, and a handler around it decides what the asking means.*

Suppose we want to count how many times `not` is used while `twice(not, true)` works out. A function can only give back a value, so the count must be given back too, and taken in by the next call. `not` would become a function of type:

    (Bool, Nat) → Pair(Bool, Nat)

and `twice` would have to become:

    twice : ((Bool, Nat) → Pair(Bool, Nat), Bool, Nat) → Pair(Bool, Nat)
    twice = (g, b, n) ↦ ((pair(a, m)) ↦ g(a, m))(g(b, n))

So would every function that uses `twice`. One small idea, counting, changes the type of every definition it passes through, and how each is written.

An **effect** names a group of **operations**: functions whose meaning is decided elsewhere. Using `L` for an effect's name and `o` for an operation:

    effect L:
        o : (A, …) → B
        …

Every function takes at least one input, so an operation that needs nothing takes a value that holds nothing:

    Unit = unit

    effect Count:
        tick : Unit → Unit

Calling an operation with values, as in `tick(unit)`, makes a **request**. A function can make one to count itself:

    invert : Bool → Bool ! {Count}
    invert = (b) ↦ let u = tick(unit) in not(b)

The local definition makes the request first, then works out `not(b)`. The type says that `invert` takes a Bool, gives back a Bool, and may make requests of `Count` along the way. A function type now says which effects calling it may use:

    (A, …) → B ! R

Here `R` is a **row**: its effects, written in braces, such as `{}` or `{Count}`. An `! R` written after an arrow belongs to that arrow.

A request takes no step by itself. It waits for a **handler** around it:

    handle e by h | …

`e` ends at `by`. A handler has cases of two kinds. Using `h` for one:

    h ::= (p) ↦ e                  for the value the expression finishes with
          (o(p, …), x) ↦ e         for a request of o, with x for the rest of the work

In the second kind, `x` stands for a function: given what `o` gives back, it carries on from where the request was made, with the same handler around it. No variable appears twice in a handler's case. For example, this handler counts:

    handle twice(invert, true) by
          (b) ↦ pair(b, zero)
        | (tick(u), rest) ↦ ((pair(b, n)) ↦ pair(b, succ(n)))(rest(unit))

When the expression finishes with a Bool `b`, no requests are left, so the count is zero. When it ticks, the handler carries on with `rest(unit)`, which gives back a pair, and adds one to its count. `twice` itself is unchanged.

Writing `h | …` for the cases above:

    handle twice(invert, true) by h | …
    >> handle invert(invert(true)) by h | …
    >> handle invert(let u = tick(unit) in not(true)) by h | …

The next step would work out `tick(unit)`, a request. The handler's case for `tick` takes over, with `u := unit` and `rest` the rest of the work:

    rest   is   (y) ↦ handle invert(let u = y in not(true)) by h | …

So the next step gives:

    >> ((pair(b, n)) ↦ pair(b, succ(n)))(rest(unit))

`rest(unit)` carries on: `not(true)` gives `false`, and the outer `invert(false)` ticks again, which adds another one. Then `not(false)` finishes with `true`, and the first case gives `pair(true, zero)`. With the two additions, the result is:

    pair(true, succ(succ(zero)))

`not` was used twice.

To say exactly what the rest of the work is, we describe the expression around the place of its next step: its **waiting work**. Using `W` for an expression with a hole, `_`, at that place:

    W ::= _                        the place itself
          W(e, …)                  inside the function being worked out
          v(…, W, …)               inside an input, with every part before it a value
          c(…, W, …)               inside a content, with every part before it a value
          let x = W in e           inside a local definition being worked out
          handle W by h | …        inside the expression a handler is around

`W⟨e⟩` is `W` with `e` in its hole. In the example, `W` was `invert(let u = _ in not(true))`. Named contents are already written out as their translations, so a request inside them sits in one of these forms.

A handler takes over when its expression waits on a request of the handler's effect, and no handler for that effect is in between. It uses the first of its cases for requests that the request matches:

    handle W⟨o(v, …)⟩ by h | … >> e[x := w, …, x' := (y) ↦ handle W⟨y⟩ by h | …]

when `o` is an operation of the handler's effect, `W` has no handler for that effect around its hole, and `(o(p, …), x') ↦ e` is the first such case that `o(v, …)` matches, giving `x := w, …`. Here `y` is a new variable. Because the rest of the work puts the handler back around itself, the handler also answers the requests that come later.

When its expression finishes, the handler uses the first of its cases for values that the value matches:

    handle v by h | … >> e[x := w, …]        when (p) ↦ e is the first such case that v matches, giving x := w, …

Otherwise, the expression takes its own step:

    handle e by h | … >> handle e' by h | …        when e >> e'

The rest of the work is a function, so a case can use it once, as `tick`'s does, never, or several times.

An operation's type can use type variables of its own, chosen at each request. A case must answer every request, so it must then work for every choice:

    effect Fail:
        fail : Unit → X

`fail(unit)` can stand in for a value of any type, because it never gives one back: its case has no `X` to give the rest of the work, so it cannot carry on. Here `fail(unit)` stands in for a list:

    handle member(zero, fail(unit)) by (b) ↦ b | (fail(u), rest) ↦ false
    >> false

A case can also carry on more than once:

    effect Choose:
        choose : Unit → Bool

    append : (List(X), List(X)) → List(X)
    append = (empty, ys) ↦ ys | (cons(x, xs), ys) ↦ cons(x, append(xs, ys))

    handle and(choose(unit), choose(unit)) by
          (b) ↦ cons(b, empty)
        | (choose(u), rest) ↦ append(rest(true), rest(false))

This handler answers each choice both ways and collects every result:

    cons(true, cons(false, cons(false, cons(false, empty))))

one for each of the four ways to choose. It searches through every possibility. Another handler could give the same expression another meaning: always answering `true`, or weighing each answer to find how likely each result is. The expression says what is asked; the handler says what the asking means.

An effect can take parameters, as a data type can, so one declaration serves every type:

    effect L(X, …):
        o : (A, …) → B
        …

The operations' types can use `X`, …. For example, a value can be kept and changed:

    effect State(X):
        get : Unit → X
        put : X → Unit

    next : Unit → Nat ! {State(Nat)}
    next = (u) ↦ let n = get(unit) in let u = put(succ(n)) in n

`next` gives back the number kept, and keeps one more. A handler gives keeping its meaning by giving back a function that waits for the value kept so far:

    (handle let a = next(unit) in let b = next(unit) in pair(a, b) by
          (x) ↦ ((s) ↦ x)
        | (get(u), rest) ↦ ((s) ↦ rest(s)(s))
        | (put(t), rest) ↦ ((s) ↦ rest(unit)(t)))(zero)

`get` carries on with the value kept, and keeps it; `put` carries on with `unit`, and keeps the new value. Started with `zero`, this works out to `pair(zero, succ(zero))`. The same `State` can keep a Bool, a list, or a function.

Typing now keeps track of rows. We write:

    e : A ! R

This reads “e has type A, and working it out may make requests of the effects in R.” An operation has its declared type, with its effect as the row, and the effect's parameters chosen at each use:

    tick : Unit → Unit ! {Count}
    get : Unit → Nat ! {State(Nat)}

A request goes to the nearest handler for its effect, whatever its parameters. So a row lists its effects nearest handler first, and can hold one effect more than once: in `{State(Nat), State(Bool)}`, a `get` gives back a number. Only the order of one effect's entries matters: `{Count, State(Nat)}` and `{State(Nat), Count}` are the same row. `R'` is **within** `R` when, for each effect, `R`'s entries for it begin with those of `R'`, in the same order. Then the nearest handler for each effect is the one `R'` expects, so what may ask `R'` can be used where `R` may be asked:

    e(e', …) : B ! R                  when e : ((A, …) → B ! R') ! R, e', … : A, … ! R, and R' is within R
    e : A ! R                         when e : A ! R', and R' is within R
    e : ((A, …) → B ! R) ! R''        when e : ((A, …) → B ! R') ! R'', and R' is within R

The first says that a call may make the requests of the function it calls. The second lets an expression that asks less be used where more may be asked. The third does the same for a function's own row: `invert` can be held in a list with a function that may also fail. Every other rule of Section 8 holds for any row, with each part of an expression in the row of the whole, except that a function's case results are checked with the row of the function's type.

A handler takes its effect out of the row, by its first entry:

    handle e by h | … : B ! R

when `L` is the effect of the handler's cases for requests, `e : A ! R'` with `R'` within `R` with an entry `L(C, …)` put first among its entries for `L`, and:

- each case `(p) ↦ e''` has `p` fitting `A`, and `e'' : B ! R`
- each case `(o(p, …), x) ↦ e''` has `o : (A', …) → B'` an operation of `L`, with `L`'s parameters replaced by `C, …`, `p, …` fitting `A', …`, and `e'' : B ! R` with `x : B' → B ! R`, for every choice of `o`'s own type variables
- every value of type `A`, whatever types its type variables stand for, matches some case of the first kind, and every request of each operation of `L` matches some case of the second kind

So the counting handler has type `Pair(Bool, Nat) ! {}`: nothing is left to ask.

A row can also end with an **effect variable**, `E`, which stands for any row, chosen at each use as a type variable is: a name's declared type has a `∀` for each effect variable in it, as for each type variable. Replacing it puts that row's entries in its place, after the others: `{Count, E}[E := {Fail}]` is `{Count, Fail}`. In a name's declaration, an arrow written without a row has the row `{E}`, with the same `E` for every such arrow in the declaration, and the same as an `E` written there that no `∀` introduces. So the declarations of Sections 1–8 still hold, except those described below, and now leave effects open:

    twice : (Bool → Bool, Bool) → Bool

means:

    twice : (Bool → Bool ! {E}, Bool) → Bool ! {E}

`twice(invert, true)` chooses `E := {Count}`, and `twice(not, true)` can choose `E := {}`. Elsewhere, an arrow written without a row has the row of its surroundings: in a local definition's declared type, the row of the expression the local definition is in; in a data type's declaration, and inside an operation's type, the row `{}`. An operation's declared type has no row of its own, and an abbreviation is written out first, so its arrows follow the rule of the place it is written out into.

A data type's parameters can include effect variables, so its contents can hold functions with any row:

    Job(E) = job(Unit → Bool ! {E})

Such a parameter is filled by a row: `Job({Count})` holds a function that may tick. Since an `E` written in a declaration is the unwritten one, `hold : (Unit → Bool) → Job(E)` with `hold = (g) ↦ job(g)` is allowed: `g` and the job share a row. Without such a parameter, a function held in data has the row `{}`.

A row ending with `E` could stand for any row, so nothing can safely be put before its entries: such a row is within only itself. To handle an effect around an expression whose row ends with `E`, that expression's requests must pass the new handler by. A **skip** says so:

    skip L in e

The requests of `L` that `e` makes pass by the nearest handler for `L` around the skip, and go to the next one:

    (skip L in e) : A ! R     when e : A ! R', and R is R' with an entry L(C, …) put first among its entries for L

For example, a definition can count its own ticks while running a function it is given, whose ticks are meant for a handler further out:

    withTick : (Unit → X) → Pair(X, Nat)
    withTick = (f) ↦
        handle let u = tick(unit) in skip Count in f(unit) by
              (x) ↦ pair(x, zero)
            | (tick(u), rest) ↦ ((pair(x, n)) ↦ pair(x, succ(n)))(rest(unit))

`withTick` always counts one: its own tick. If `f` ticks, those requests pass `withTick`'s handler by, to the next handler for `Count` around it. Without the skip, `f(unit)` would have the row `{E}` where `{Count, E}` is needed, and `withTick` would not be allowed.

Waiting work gains a form:

    W ::= skip L in W              inside a skip

and a request now looks past skips. A handler for `L` takes over a request in `W` when `W` **passes requests of `L` out**: going out from its hole, it has as many skips of `L` as handlers for `L`, and at each point at least as many skips as handlers. With no skips, this means `W` has no handler for `L` around its hole, as before. A skip around a value is done:

    skip L in e >> skip L in e'      when e >> e'
    skip L in v >> v

A `∀` can introduce an effect variable too: `∀E. A` works for every row `E`. For example, translating `negate` from Section 12 gives it the input `∀X. ∀Y. ∀E'. (X → Y ! {E'}, F(X)) → F(Y) ! {E'}`, so it can use `each` with functions of any row; `E'` is a new variable, apart from `negate`'s own `E`. With requests, `∀` needs one limit: an expression is given a type `∀X. A` or `∀E. A` only when it makes no requests, that is, when it has the row `{}`. Without this limit, a handler that carries on more than once could use one result at two different types. The limit is only on giving an expression a `∀`: using one that a declaration, a content, or a call already gives needs none.

These two limits make the exceptions among the declarations of Sections 1–8. A declaration there is rejected when it relies on a function it is given making no requests: when it puts that function into data whose functions have the row `{}`, as `wrap : (Bool → Bool) → Task` with `wrap = (g) ↦ task(g, true)` does, or when it gives a `∀` to the result of a call that is given the function. It can say that the function makes no requests by writing `! {}` on its arrow: `wrap : (Bool → Bool ! {}) → Task`.

A defined name makes no requests when it is used: in `n = e`, `e : A ! {}`. A function makes no requests until it is called, so every function is allowed.

In Section 4, the order of steps could only decide whether a value is reached. With requests, it also decides which request comes first, and so can change the value reached. Left to right is now part of what an expression means: `let x = e' in e` makes the requests of `e'` before those of `e`.

A whole program checked with the row `{}` asks nothing, so, as in Section 8, it never gets stuck. A program whose row is not empty asks something of whatever runs it: its unanswered requests are how it reads input and shows output, and whatever runs it acts as their handler.

Effects also translate back, one definition at a time. A request becomes data holding which operation was used, its inputs, and a function for the rest of the work. Each definition whose type has a row gives back such data instead of making requests, and a handler becomes a function that looks inside the data with its cases, calling itself on what the rest of the work gives back. For `Count`:

    Counting(X) = finished(X) | ticked(Unit, Unit → Counting(X))

    tick : Unit → Counting(Unit)
    tick = (u) ↦ ticked(u, (y) ↦ finished(y))

    count : Counting(X) → Pair(X, Nat)
    count = (finished(x)) ↦ pair(x, zero)
          | (ticked(u, rest)) ↦ ((pair(x, n)) ↦ pair(x, succ(n)))(count(rest(unit)))

`count` is the counting handler of the first example, written as a function. Three parts of the translation use the larger ideas of the core. Where an effect variable leaves a row open, the data must leave open which requests it can hold, which takes a type variable that needs a parameter, as in Section 7; and a definition with such a row takes, as an extra first input, a function that rebuilds those requests with a new rest of the work, passed as an implementation's names are. An operation with type variables of its own, like `fail`, hides which type was chosen behind a `∀`, as in Section 6. And a skip moves a request past one handler's data. The translation reaches every definition the effect passes through: that is exactly the writing effects save.

## 15. The extensions in one place

Here `l` means a content's name, `I` an interface's name, `L` an effect's name, `o` an operation, `h` a handler's case, `R` a row, `r` an entry of a row, `E` an effect variable, and `W` waiting work. In the rules, `L(C, …)` can also be `L`, for an effect with no parameters, and in `[X := B, …, E := R', …]` and `∀E. …` a `…` can also stand for none. Each list adds forms to the list of the same kind in Section 8. A group of rules with the same name as one there adds to it, except where a rule here says it limits or replaces one of Section 8's; the other groups are new. Together with Section 8, the lists and rules are complete.

    Expressions
        e ::= let x = e' in e              a local definition
              let rec x = e' in e          a local function that can call itself
              let x : A = e' in e          a local definition with its type
              let rec x : A = e' in e      a local function with its type
              c(l = e, …)                  data with its contents named
              e.l                          the content named l
              e with (l = e', …)           a copy with the named contents replaced
              o                            an operation
              handle e by h | …            e, with a handler around it
              skip L in e                  e, with its requests of L passing the nearest handler for L

    Handler cases
        h ::= (p) ↦ e                      for the value the expression finishes with
              (o(p, …), x) ↦ e             for a request, with x for the rest of the work; in either,
                                           no variable twice

    Values
        v ::= o                            an operation

    Patterns
        p ::= c(l = p, …)                  contents matched by name; the others match anything

    Types
        A ::= (A, …) → B ! R               a function type with its row
              ∀E. A                        a type that works for every row E
              T(…, R, …)                   a data type with a row filling an effect variable parameter

    Rows
        R ::= {}                           no effects
              {r, …}                       these entries, nearest handler first; an E only last
        r ::= L                            an effect with no parameters
              L(A, …)                      an effect with its parameters filled
              E                            an effect variable: any row

    Definitions
        d ::= c(l : A, …)                  a constructor with its contents named

        T(X, …) = d | …                    as in Section 8, with effect variables also allowed as
                                           parameters, each filled by a row

        type T = A                         a name for a type
        type T(X, …) = A                   a name for a type, with parameters
        interface I(X):                    an interface: names and their types, with X open
            n : A
            …
        interface I(X) given I'(X), …:     an interface that requires others
            n : A
            …
        I(T):                              an implementation of I for T; also I(T(Y, …)):
            n = e
            …
        I(T(Y, …)) given I'(Y), …:         an implementation given others for T's parameters
            n = e
            …
        n : A given I(X), …                a name's type, for every X with an implementation
        hidden T = d | …                   a data type whose constructors only the definitions below it
            …                              can use; also hidden T(X, …) = d | …
        effect L:                          an effect and its operations
            o : (A, …) → B
            …
        effect L(X, …):                    an effect with parameters
            o : (A, …) → B
            …

    Waiting work
        W ::= _                            the place of the next step
              W(e, …)                      inside the function being called
              v(…, W, …)                   inside an input, with every part before it a value
              c(…, W, …)                   inside a content, with every part before it a value
              let x = W in e               inside a local definition, with or without its type
              handle W by h | …            inside the expression of a handler
              skip L in W                  inside a skip

    W⟨e⟩ is W with e in its hole. W passes requests of L out when, going out from its hole, it has
    as many skips of L as handlers for L, and at each point at least as many skips as handlers.

Each new kind can contain these kinds, and the old kinds now contain more:

    h        p, e
    W        W, e, v, h, L, A
    R        r
    r        A
    e        also A, h, L, l
    p        also l
    d        also l
    A        also R

    Scope
        let x = e' in e                    x reaches e
        let rec x = e' in e                x reaches e' and e
        a local definition's type A        uses the type variables of the declaration around it,
                                           and those a ∀ in A introduces, and counts as a declared type

    Names
        in an expression                   instead of Section 8's rule: a name is the variable of the nearest
                                           case, handler case, or local definition around it that introduces
                                           it and reaches it; otherwise, an operation, a constructor, or a
                                           defined name
        in a type                          an abbreviation's name is that abbreviation
        in a row                           a declared effect's name is that effect; any other name is an
                                           effect variable
        a data type's parameter            is an effect variable when it is used in a row, and a type
                                           variable otherwise
        l                                  appears only in the forms for named contents

    Reading
        let x = e' in e                    e' ends at in; e ends at the next |, comma, in, or by around the
                                           local definition, a closing parenthesis around it, or the end
                                           of the definition
        handle e by h | …                  e ends at by, and the cases continue as a function's do
        skip L in e                        e ends as the e of a local definition does
        ! R after an arrow                 belongs to that arrow; an expression's own row is written
                                           after its whole type, grouped where needed, as in
                                           ((A, …) → B ! R') ! R and (∀X. A) ! R

    Abbreviations
        T                                  is A, when type T = A
        T(B, …)                            is A[X := B, …], when type T(X, …) = A

    Available
        I(T) or I(T(B, …))                 when an implementation I(T) or I(T(Y, …)) exists, and each I'(Y')
                                           it is given is available with Y, … replaced by B, …
        I(X)                               when the declaration being checked is given I(X), or is given an
                                           interface that requires I(X), directly or through others

    Lists
        T(…, R, …) lists d[…, E := R, …]   when d is in the declaration of T(…, E, …)

    Needs
        L(A, …)                            each A needs what that parameter of L needs, and has no ∀ in it
        an effect variable parameter       of a data type is filled by a row

    Typing
        e : A ! R                          e has type A, and may make requests of the effects in R
        Every rule of Section 8 holds for any row, with each part in the row of the whole,
        except as below; a function's case results are checked with the row of its type.
        x : A ! R                          when the nearest case or local definition around x that reaches it
                                           introduces x with type A
        a name n : A of interface I(X)     is declared n : A given I(X)
        n : A[X := B, …, E := R', …] ! R   when n : A given I(Y), … is declared, each B can replace its X,
                                           and each I(Y)[X := B, …] is available
        n : (∀X. … ∀E. … A) ! R            instead of Section 8's rule for names: when n : A is declared
                                           with no given; a ∀ for each type and effect variable that
                                           occurs in A outside every ∀ that introduces it, the unwritten
                                           E included
        e : A[E := R'] ! R                 when e : (∀E. A) ! R
        e : (∀X. A) ! R, e : (∀E. A) ! R   instead of Section 8's rule: when e : A ! {}, X or E is in the
                                           type of no variable e uses from around it, and X is in no I(X)
                                           that the declaration being checked is given, directly or
                                           through interfaces they require
        e : A ! R                          when e : A ! R', and R' is within R
        e : ((A, …) → B ! R) ! R''         when e : ((A, …) → B ! R') ! R'', and R' is within R
        o : ((A, …) → B ! {L(C, …)}) ! R   when o : (A, …) → B is an operation of L(X, …), with X, …
                                           replaced by C, …, and its own type variables chosen
        e(e', …) : B ! R                   when e : ((A, …) → B ! R') ! R, e', … : A, … ! R, and R' is within R
        (let x = e' in e) : B ! R          when e' : A ! R and e : B ! R, with x : A wherever x reaches;
                                           the same for let rec, and with a declared type, A is that type
        c(l = e, …) : T ! R                when T lists c with l : A, …, every content named once, and
                                           e, … : A, … ! R
        e.l : A ! R                        when e : T ! R and T lists only c(…, l : A, …)
        (e with (l = e', …)) : T ! R       when e : T ! R, T lists only a constructor with l : A, …,
                                           and e', … : A, … ! R
        (handle e by h | …) : B ! R        when L is the effect of the cases for requests, e : A ! R', and
                                           R' is within R with an entry L(C, …) put first among those for L;
                                           each (p) ↦ e'' has p fitting A and e'' : B ! R;
                                           each (o(p, …), x) ↦ e'' has o : (A', …) → B' an operation of L,
                                           with L's parameters replaced by C, …, p, … fitting A', …, and
                                           e'' : B ! R with x : B' → B ! R, for every choice of o's own type
                                           variables; each v : A, whatever types its type variables stand
                                           for, matches some case (p) ↦ e''; and each
                                           request of each operation of L matches some case (o(p, …), x) ↦ e''
        (skip L in e) : A ! R              when e : A ! R', and R is R' with an entry L(C, …) put first
                                           among those for L
        R' is within R                     when R' does not end with an effect variable, and for each effect,
                                           R's entries for it begin with those of R', in the same order;
                                           or when R' and R are the same row
        two rows are the same              when they hold the same entries, those of each effect in the same
                                           order

    Fitting
        c(l = p, …) fits T                 when T lists c with l : A, …, and p, … fit A, …, giving what
                                           each gives

    Matching
        c(v, …) matches c(l = p, …)        when the value in each named place matches its p, giving what each gives
        o(v, …) matches o(p, …)            when v, … match p, …, giving what each gives

    Replacing
        (let x = e' in e)[x := w]          is let x = e'[x := w] in e
        (let rec x = e' in e)[x := w]      is let rec x = e' in e
        ((o(p, …), x') ↦ e)[x := w]        is the case unchanged, when p, … or x' introduce x;
                                           otherwise, the case with e[x := w]
        R[E := R']                         is R with its last entry E replaced by the entries of R';
                                           a row without E is unchanged
        ∀E. A                              is renamed and replaced as ∀X. A is in Section 8
        A local definition with a declared type is replaced in as one without.

    Steps
        let x = e' in e >> let x = e'' in e                when e' >> e''
        let x = v in e >> e[x := v]
        let rec x = v in e >> e[x := v[x := let rec x = v in x]]
        A local definition with a declared type takes the same steps as one without.
        handle e by h | … >> handle e' by h | …            when e >> e'
        handle v by h | … >> e[x := w, …]                  when (p) ↦ e is the first case for values that
                                                           v matches, giving x := w, …
        handle W⟨o(v, …)⟩ by h | …
            >> e[x := w, …, x' := (y) ↦ handle W⟨y⟩ by h | …]
                                                           when o is an operation of the effect of h | …,
                                                           W passes requests of that effect out, and
                                                           (o(p, …), x') ↦ e is the first case for requests
                                                           that o(v, …) matches, giving x := w, …;
                                                           y is a new variable
        skip L in e >> skip L in e'                        when e >> e'
        skip L in v >> v
        Named contents and interfaces are written out as their translations before anything is
        worked out, and take their steps.

    Translations
        let x = e' in e                    is ((x) ↦ e)(e')
        let x : A = e' in e                is h(e', y, …), with a new defined name h : (A, A', …) → B ! R
                                           and h = (x, y, …) ↦ e, where y, … are the variables e uses from
                                           around it, A', … their types, and B and R the type and row of e;
                                           h is given what the declaration around it is given
        let rec x = e' in e                becomes a defined name for e', with the variables it uses from
                                           around it as extra first inputs, passed at every use; a use
                                           that is not a call becomes a function that passes them
        let rec x : A = e' in e            as without its type, with A, its extra inputs added first, as
                                           the defined name's type
        type T(X, …) = A                   each use is written out as what it means, and the declaration removed
        c(l = e, …)                        is h(e, …), with a new defined name h : (A, …) → T and
                                           h = (y, …) ↦ c(…), the expressions in the order written, each y
                                           in its name's place
        e.l                                is ((c(x, …)) ↦ x')(e), with new variables, x' in l's place
        e with (l = e', …)                 is h(e, e', …), with a new defined name h : (T, A, …) → T and
                                           h = (c(x, …), y, …) ↦ c(…), each y in its name's place, and each
                                           other place keeping its x
        c(l = p, …)                        is c(p, …), with each p in its name's place, and a new variable
                                           in each other
        an implementation                  becomes defined names, one for each of its definitions; one that
                                           is given others takes their names, and those of the interfaces
                                           they require, as extra first inputs
        n : A given I(X), …                gains extra first inputs: one for each name of I, and of each
                                           interface I requires, with its type from the interface and a ∀ for
                                           each of its own type and effect variables, named anew
        a use of a name                    passes the implementations its chosen types require; a use that is
                                           not a call becomes a function that passes them
        hidden T = d | …                   is T = d | …
        a request                          becomes data holding the operation, its inputs, and a function for
                                           the rest of the work; a definition with a row gives back such
                                           data, and one whose row ends with E also takes a function that
                                           rebuilds the requests E holds; a handler becomes a function
                                           that looks inside the data, calling itself on what the rest of
                                           the work gives back; an operation's own type variables are
                                           hidden behind a ∀; and a skip moves a request past one
                                           handler's data

    Checking definitions
        type T(X, …) = A                   A uses only X, …, declared types, and type variables a ∀ in A
                                           introduces; T does not use itself,
                                           directly or through others, and is always used with all its
                                           parameters
        c(l : A, …)                        names all its contents or none, each name once
        c(l = e, …)                        gives every name; e with (l = e', …) and c(l = p, …) give some;
                                           each name at most once, in any order
        interface I(X)                     has one parameter, which may need parameters
        I(T) or I(T(Y, …)), given …        defines every name of I, with I's type for it and X replaced by
                                           T or T(Y, …); Y, … are different type variables filling T's first
                                           parameters, until what is left needs what X needs; its definitions
                                           are checked as given what it is given
        I(T) or I(T(Y, …))                 for each I'(X) that I requires, I'(T) or I'(T(Y, …)) is available
        each interface                     has at most one implementation for each data type
        a use of a name given I(X), …      settles each X: only one choice gives the use the type its place
                                           requires
        hidden T = d | …                   T's constructors, and its named contents, are used only in the
                                           definitions below it
        effect L(X, …)                     its operations' types use only X, …, declared types, and type
                                           variables of their own
        a handler                          has at least one case for requests, all for operations of one
                                           effect
        let rec x = e' in e                e' is written as a function, k | …, with or without a declared
                                           type
        n : A and n = e                    when e : A ! {}
        an arrow written without a row     has the row {E} in a name's declaration, with the same E
                                           throughout it and as an E written there that no ∀ introduces;
                                           in a local
                                           definition's declared type, the row of the expression it is in;
                                           and the row {} in a data type's declaration and inside an
                                           operation's type; an operation's declared type has no row of
                                           its own, and an abbreviation's arrows follow the place it is
                                           written out into
        T, I, L, o                         each defined once, an abbreviation's name like a data type's;
                                           no operation shares a name with a defined name or constructor

With these additions, an expression that is not a value still has exactly one next step, or waits on a request that no handler inside it takes over. A program checked with the row `{}` has no request left to wait on, so it never gets stuck: it reaches a value or continues forever. Since every addition translates back into Sections 1–8, the language computes exactly what it computed before. What has changed is how easily things are said.
