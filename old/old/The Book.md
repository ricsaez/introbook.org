When we say something in a language, what we write is an **expression**. Sometimes we want to leave something open, to be decided later. For that we write a **placeholder**: a name, such as `a`.

In this language, an expression has one of three forms. To describe them we use letters that are not part of the language: `e` stands for any expression and `x` for any placeholder, and `e, …` or `x, …` for any number of them, even none.

    x               a placeholder
    (x, …) ↦ e      a function
    e(e, …)         an application

The letters in a form are independent, so the two `e`s in `e(e, …)` can be different expressions. In examples we write particular placeholders instead, such as `a` and `b`.

A **function** takes placeholders and has a **body**, the expression after `↦`. For example, `(a) ↦ a` is a function, read "take `a`, give back `a`", and `(a, b) ↦ b` reads "take `a` and `b`, give back `b`". A function can also take no placeholders, as in `() ↦ (a) ↦ a`. The placeholders a function takes are all different: in `(a, a) ↦ a`, we could not tell which one the body means.

An **application** applies an expression to others, its **inputs**: they decide what the placeholders stand for. A body reaches as far as it can, so a function that is applied is written in parentheses: `((a) ↦ a)((b) ↦ b)` applies `(a) ↦ a` to the input `(b) ↦ b`. Without them, `(a) ↦ a((b) ↦ b)` would be a function whose body is `a((b) ↦ b)`. Parentheses like these only show how an expression is grouped. An application can also have no inputs, as in `(() ↦ (a) ↦ a)()`.

A body can contain functions of its own, and they may take the same placeholder. A placeholder used in a body **belongs** to the nearest function around it that takes it. In `(a) ↦ (b) ↦ a`, the `a` belongs to the outer function. In `(a) ↦ (a) ↦ a`, the last `a` belongs to the inner function, and the outer `a` is not used at all.

We **carry out** an expression in **steps**. Each step happens at an application, and puts its inputs in place of the placeholders of the function being applied. A function waits to be applied: no step happens inside it. So there is nothing to carry out in a function that is not applied; it is a **value**. In this language, the values are exactly the functions.

In the rules below, `v` stands for a value and `v, …` for any number of them, even none. A `…` alone stands for any number of expressions, even none. A letter or `…` written on both sides of a rule stands for the same thing on both. And "`e` becomes `e'`" means one step.

    ((x, …) ↦ e)(v, …)     becomes     e, with each v in place of the uses of its x that belong to this function,
                                       when there are as many v as x, matched in order
    e(…)                   becomes     e'(…), when e becomes e'
    v(…, e, …)             becomes     v(…, e', …), when e becomes e' and everything before e is a value

The first rule is the step itself: the first input goes in place of the first placeholder, the second in place of the second, and so on. The other two rules say where a step can happen: first in the applied expression, then in the inputs, from left to right. These are the only steps.

The simplest step puts one input in place:

    ((a) ↦ a)((b) ↦ b)                 becomes   (b) ↦ b

With several placeholders, the inputs go in order, and a placeholder may go unused:

    ((a, b) ↦ b)((c) ↦ c, (d) ↦ d)     becomes   (d) ↦ d

A function may take no placeholders. Its body still waits until the function is applied, with no inputs, even when the body uses no placeholder from around it:

    (() ↦ ((a) ↦ a)((b) ↦ b))()        becomes   ((a) ↦ a)((b) ↦ b), which becomes (b) ↦ b

An input goes in wherever its placeholder is used, even inside an inner function, where nothing is carried out. The result below is a function whose body still contains an application, and it is a value:

    ((a) ↦ (b) ↦ a(a))((c) ↦ c)        becomes   (b) ↦ ((c) ↦ c)((c) ↦ c)

But an input does not go where the use belongs to an inner function:

    ((a) ↦ (a) ↦ a)((c) ↦ c)           becomes   (a) ↦ a

So every placeholder used must belong to some function. Only a step puts anything in place of a placeholder, and only of the uses that belong to the function being applied. A placeholder that belonged to no function would never have anything put in its place, and carrying out could reach it with no rule to apply.

The applied expression is carried out first, by the second rule, and then the inputs, by the third. The parentheses in each result follow from the grouping: the rules, and putting values in place, work on how an expression is grouped, not on how it is written, and parentheses are written wherever a result would otherwise be grouped another way:

    ((a) ↦ a)((b) ↦ b)(((c) ↦ c)((d) ↦ d))
        becomes   ((b) ↦ b)(((c) ↦ c)((d) ↦ d))
        becomes   ((b) ↦ b)((d) ↦ d)
        becomes   (d) ↦ d

Inputs are carried out from left to right, each before it goes in, even one whose placeholder is never used:

    ((a, b) ↦ b)(((c) ↦ c)((d) ↦ d), ((c) ↦ c)((b) ↦ b))
        becomes   ((a, b) ↦ b)((d) ↦ d, ((c) ↦ c)((b) ↦ b))
        becomes   ((a, b) ↦ b)((d) ↦ d, (b) ↦ b)
        becomes   (b) ↦ b

A placeholder can be applied, or be an input. Once a value is in its place, that application is carried out like any other. An input used twice is still carried out only once:

    ((a) ↦ a(a))(((b) ↦ b)((c) ↦ c))
        becomes   ((a) ↦ a(a))((c) ↦ c)
        becomes   ((c) ↦ c)((c) ↦ c)
        becomes   (c) ↦ c

Carrying out ends when the whole expression is a value, since no rule applies to a value. It can also get **stuck**. When an application's applied expression and inputs are all values but there are not as many inputs as placeholders, no rule applies, although there is still something to carry out. That happens with too few inputs or too many:

    ((a) ↦ a())((b) ↦ b)               becomes   ((b) ↦ b)(), which is stuck: one placeholder, no inputs
    ((a) ↦ a)((b) ↦ b, (c) ↦ c)        is stuck: one placeholder, two inputs

Or it can go on forever:

    ((a) ↦ a(a))((a) ↦ a(a))           becomes   ((a) ↦ a(a))((a) ↦ a(a)), the same expression again

At each point at most one rule applies, and at one application only, so there is only one way to carry out an expression, and it reaches at most one value.

The rules settle four questions, and each could have gone another way. Inputs could have gone in unfinished; carrying them out first means each is carried out once, however many times it is used, even none. Bodies could have been carried out early; leaving them alone means a function is a value as soon as it is written. Steps could have happened in any order; one fixed order means there is only ever one next step. And a function given too few inputs could have waited for the rest, and one given too many could have passed the rest on to its result; asking for exactly as many inputs as placeholders means that every step puts an input in place of each placeholder, with none left over.

Functions alone can already do a great deal, because one function can play many roles.

A function can choose. `(a, b) ↦ a` gives back the first of two inputs, and `(a, b) ↦ b` the second, so each can stand for a choice already made. Applied to two inputs, it picks one:

    ((a, b) ↦ a)((c) ↦ c, (d) ↦ d)     becomes   (c) ↦ c

Both inputs are carried out before the choice is made. To choose between two things still to be carried out, each waits in a function with no placeholders, and only the one chosen is applied. Here the second would go on forever, but it is never carried out:

    ((a, b) ↦ a)(() ↦ (c) ↦ c, () ↦ ((d) ↦ d(d))((d) ↦ d(d)))()
        becomes   (() ↦ (c) ↦ c)()
        becomes   (c) ↦ c

A function can hold. `(f) ↦ f((c) ↦ c, (d) ↦ d)` holds two values and waits for a choice. Applied to one, it hands both over, and the choice picks:

    ((f) ↦ f((c) ↦ c, (d) ↦ d))((a, b) ↦ b)
        becomes   ((a, b) ↦ b)((c) ↦ c, (d) ↦ d)
        becomes   (d) ↦ d

A function can count. A number can be a function that applies another function that many times: `(f, a) ↦ a` applies `f` no times, `(f, a) ↦ f(a)` once, and `(f, a) ↦ f(f(a))` twice. Here `(b) ↦ (c) ↦ b` wraps what it is given in a function, and the number two applies it twice:

    ((f, a) ↦ f(f(a)))((b) ↦ (c) ↦ b, (d) ↦ d)
        becomes   ((b) ↦ (c) ↦ b)(((b) ↦ (c) ↦ b)((d) ↦ d))
        becomes   ((b) ↦ (c) ↦ b)((c) ↦ (d) ↦ d)
        becomes   (c) ↦ (c) ↦ (d) ↦ d

A function can refer to itself, when it is handed itself. `(a) ↦ a(a)` hands its input to itself, so the function it is given receives itself, and can apply itself again:

    ((a) ↦ a(a))((a) ↦ (b) ↦ b)
        becomes   ((a) ↦ (b) ↦ b)((a) ↦ (b) ↦ b)
        becomes   (b) ↦ b

This one does not apply itself again. Handed itself, `(a) ↦ a(a)` does, again and again, forever.

And a function works on anything at all: `(a) ↦ a` gives back whatever it is given.

So one function can choose, hold, count, refer to itself, and work on anything. But nothing tells these roles apart. `(f, a) ↦ a`, which applies `f` no times, also gives back the second of two inputs, just as `(a, b) ↦ b` does. And anything can be applied to anything: a number given where a choice is expected carries on, and can reach a value with no sign that anything went wrong. The only mistake the rules catch is the wrong number of inputs, by getting stuck.

---

Above this line, there are functions alone. Below it, the roles are untangled one at a time, and each is given forms and rules of its own.

To choose, we used a function standing for a choice already made, `(a, b) ↦ a` or `(a, b) ↦ b`. The choice was a function like any other, and the only way to use it was to apply it.

So we want values that are nothing but themselves, and a way to ask which one we have. A **constructor** is a name that stands only for itself. We **declare** constructors together, as the alternatives of a **data type**: a name for one kind of value. Using `T` for a data type and `c` for a constructor, both names:

    T = c | …

The `|` reads "or", and `c | …` stands for one constructor or more, separated by `|`. For example,

    Bool = false | true

declares the data type `Bool`, whose values are `false` and `true`. Each data type is declared once, and so is each constructor. A constructor is an expression, and a value: there is nothing in it to carry out.

To ask which constructor a value is, a function can have several **cases**, separated by `|`. A case is what a function was until now, except that where the function took a placeholder, a case has a **pattern**: a placeholder, or a constructor. Using `k` for a case and `p` for a pattern, a function is now `k | …`, one case or more, where a case is

    (p, …) ↦ e      a case, with patterns p, … and body e

and a pattern has one of two forms:

    x               a placeholder
    c               a constructor

A name declared as a constructor is that constructor wherever it is written, in a pattern or in a body, and a placeholder is a name not declared as one. The placeholders in a case's patterns are all different. A function written as before, `(x, …) ↦ e`, is a function with one case whose patterns are all placeholders. Values now have two forms:

    c               a constructor
    k | …           a function

A value **matches** a pattern when the pattern describes it. Any value matches a placeholder, and gives it that value; a constructor matches only itself, and gives nothing:

    v matches x     giving x the value v
    c matches c     giving nothing

Several values match several patterns when there are as many values as patterns and each value matches its pattern; they give all that each gives.

A placeholder used in a body now belongs to the nearest case around it whose patterns take it. When a function with cases is applied to values, the first case they match is used. Using `w` for a value that matching gives:

    (k | …)(v, …)     becomes     e, with each w in place of the uses of its x that belong to this case,
                                  when (p, …) ↦ e is the first case that v, … match, giving each x its w

For a function with one case whose patterns are all placeholders, this is the first rule above the line: placeholders match any values, as long as there are as many. The rules above the line and this one are the only steps.

The cases are tried in order:

    ((false) ↦ true | (true) ↦ false)(true)                    becomes   false

This function turns each `Bool` into the other. With several inputs, a case has a pattern for each, and its patterns can mix constructors and placeholders. Here the first case matches, giving `false` to `a`:

    ((true, a) ↦ a | (false, a) ↦ false)(true, false)          becomes   false

Cases can overlap, and then the first that matches is used, so a last case can catch everything the cases before it leave:

    ((false, false) ↦ false | (a, b) ↦ true)(true, false)      becomes   true

Inputs are carried out before any case is matched, as before:

    ((false) ↦ true | (true) ↦ false)(((false) ↦ true | (true) ↦ false)(true))
        becomes   ((false) ↦ true | (true) ↦ false)(false)
        becomes   true

A function with cases is a value like any other, so it can be an input, and a placeholder matches it:

    ((a) ↦ a(true))((false) ↦ true | (true) ↦ false)
        becomes   ((false) ↦ true | (true) ↦ false)(true)
        becomes   false

A body reaches as far as it can, so a `|` after a body that is a function belongs to that function. For such a body to be followed by more cases of the function around it, the body is written in parentheses, which again only show grouping. This function turns a `Bool` back into a choice made of functions:

    ((true) ↦ ((a, b) ↦ a) | (false) ↦ (a, b) ↦ b)(false)      becomes   (a, b) ↦ b

Without the parentheses, the cases after the body belong to it. Then they are the cases of an inner function, and their bodies can use a placeholder of the outer case. Given a `Bool`, this function gives back a function that answers whether both `Bool`s are `true`:

    ((a) ↦ (true) ↦ a | (false) ↦ false)(false)(true)
        becomes   ((true) ↦ false | (false) ↦ false)(true)
        becomes   false

The first step puts `false` in place of `a` inside the inner function, as an input goes into an inner function above the line.

Carrying out can now get stuck in new ways: when no case matches, and when a constructor is applied, since only a function has cases to use.

    ((true) ↦ false)(false)                        is stuck: no case matches
    ((false) ↦ true | (true) ↦ false)((a) ↦ a)     is stuck: a function matches no constructor
    true(false)                                    is stuck: true is not a function

In each of these, a value is used where it cannot be: given to a function that has no case for it, or applied when it is not a function. So it is with the wrong number of inputs, and above the line, a value could even be given where another kind was expected, without getting stuck. To catch all of this before anything is carried out, we ask of each expression what kind of value it is.

A data type already names a kind of value, and its values are **data**. A function is another kind of value, described by the types of its inputs and of what it gives back: its kind is a **function type**. A **type** is a data type or a function type. Using `A` and `B` for any types:

    T               a data type
    (A, …) → B      a function type

In `(A, …) → B`, the types `A, …` are the **input types**, and `B` is the **result type**: a function of this type is given inputs of types `A, …`, in order, and gives back a `B`. Like a body, the result type reaches as far as it can, so `(Bool) → (Bool) → Bool` has the input type `Bool` and the result type `(Bool) → Bool`. A data type's name is used only in types, and a constructor's only in expressions and patterns, so a constructor may have the same name as a data type.

We write `e : A`, read "`e` has type `A`". A data type **lists** the constructors declared in it, and a pattern **fits** a type when it describes values of that type: a placeholder fits any type, and gives the placeholder that type; a constructor fits the type it has. The cases of a function **cover** its input types when every input of those types matches some case. These rules give an expression its type from its parts:

    c : T                     when T lists c
    x : A                     when the case x belongs to gives x : A
    e(e', …) : B              when e : (A, …) → B, and e', … : A, …
    (k | …) : (A, …) → B      when in each case (p, …) ↦ e, the patterns p, … fit A, …, and e : B with the
                              types they give; and the cases cover A, …

    x fits A                  giving x : A
    c fits A                  when c : A

`e', … : A, …` means as many expressions as types, each of its type, in order, and the same for patterns that fit. So a function's patterns give its placeholders their types, and its body has its type with them.

    true : Bool
    (false) ↦ true | (true) ↦ false              :   (Bool) → Bool
    ((false) ↦ true | (true) ↦ false)(true)      :   Bool

In the function, both patterns fit `Bool`, and both bodies have type `Bool`.

Whether the cases cover is decided from the patterns alone. Write a **new** placeholder, one used nowhere else, for each input type, standing for any input of that type. Such a placeholder matches only a placeholder in a case, since a constructor in a case matches only some of the inputs. If these new placeholders match some case, the cases cover. If not, pick one of them whose type is a data type, and put each constructor it lists in its place in turn: the cases cover if each of these is covered in the same way.

In the function above, `y` stands for any `Bool` and matches neither case. With `false` in its place it matches the first case, and with `true` the second, so the cases cover `Bool`. In `(true, a) ↦ a | (false, a) ↦ false`, the placeholders `y, z` match no case, but `false, z` matches the second and `true, z` the first. In `(false, false) ↦ false | (a, b) ↦ true`, `y, z` match the second case at once.

An expression can have more than one type. `(a) ↦ a` has type `(Bool) → Bool`, and also `((Bool) → Bool) → (Bool) → Bool`, since its pattern can give `a` either type.

A function type can be an input type, or a result type:

    (a) ↦ a(true)                                :   ((Bool) → Bool) → Bool
    (a) ↦ (b) ↦ a                                :   (Bool) → (Bool) → Bool

In the first, the pattern gives `a` the type `(Bool) → Bool`, so `a(true) : Bool`. A function type can have several input types, or none:

    (true, a) ↦ a | (false, a) ↦ false           :   (Bool, Bool) → Bool
    () ↦ true                                    :   () → Bool

A constructor fits only its data type, so only a placeholder fits a function type. A function given a function can apply it, but cannot ask how it was made.

None of these has a type:

    (true) ↦ false                                 the cases do not cover Bool: false matches no case
    ((false) ↦ true | (true) ↦ false)((a) ↦ a)     the input must be a Bool, and (a) ↦ a has a function type
    true(false)                                    true : Bool, which is not a function type
    ((a) ↦ a)((b) ↦ b, (c) ↦ c)                    the function type has one input type, and two inputs are given
    (a) ↦ a(a)                                     a would have a type that is its own input type

The last would have `a : A` and `a : (A) → B` at once, so `A` would contain itself, and no type does. So `((a) ↦ a(a))((a) ↦ a(a))`, which went on forever, has no type either.

What we write is **checked** when it has a type. So there are now three kinds of rule: the forms say what can be written, the types what is checked before anything is carried out, and the steps what happens while carrying out.

If `e : A`, and `e` uses no placeholder from around it, then `e` is a value, or `e` becomes some `e' : A`. A placeholder that an expression **uses from around it** is one that belongs to no case inside the expression, as the body `a(true)` of `(a) ↦ a(true)` uses `a`. Such an expression can have a type, `a(true) : Bool` when `a : (Bool) → Bool`, but it has no step until a value is put in place of `a`.

Each step keeps the type: a case's body has the function's result type, and each value put in place of a placeholder has the type its pattern gave. And while an expression that uses no placeholder from around it is not a value, there is a step: when an application's parts are all values, the applied value has a function type, so it is a function; it is given as many inputs as its type has input types, and since its cases cover them, some case matches. So a checked expression never gets stuck.

To hold two things, a function waited for a choice, as `(f) ↦ f(true, false)` does. Its type is `((Bool, Bool) → Bool) → Bool`, when the choice gives back a `Bool`. But `(f) ↦ true` has that type too, and holds nothing: the type cannot say that its values hold two `Bool`s.

So a constructor can hold values: its **contents**. Using `d` for a constructor as declared, a constructor can hold nothing, or hold contents of the types written after it:

    T = d | …

where `d` has one of two forms:

    c                         a constructor, holding nothing
    c(A, …)                   a constructor, holding contents of types A, …

In `c(A, …)` there is at least one type, since a constructor that holds nothing is the first form. The names in these types must be names of data types, the one being declared included. For example:

    Bools = bools(Bool, Bool)

A data type lists its constructors as declared: `Bools` lists `bools(Bool, Bool)`. A constructor that holds contents is applied to them, like a function, and its type is a function type:

    c : (A, …) → T            when T lists c(A, …)

So `bools : (Bool, Bool) → Bools`, and `bools(true, false) : Bools`. The rule `c : T` is only for a constructor that holds nothing, so `bools` alone does not have type `Bools`. Applied to as many values as it holds, a constructor has no step: it holds them. That is a value, and data:

    c(v, …)                   data: c holding v, …

There is no new form of expression: `c(e, …)` is an application, and its contents are carried out first, from left to right, by the rules above the line:

    bools(((false) ↦ true | (true) ↦ false)(true), true)          becomes   bools(false, true)

A pattern can look into data. Its contents are patterns too, so patterns nest:

    c(p, …)                   data built by c, with contents matching p, …

A constructor on its own is a pattern only if it holds nothing. Data matches a pattern of this form, and the pattern fits a type, part by part:

    c(v, …) matches c(p, …)   when v, … match p, …, giving what they give
    c(p, …) fits B            when c : (A, …) → B, and p, … fit A, …, giving what they give

The placeholders in the pattern receive the contents:

    ((bools(a, b)) ↦ b)(bools(true, false))                          becomes   false

To decide whether cases cover, a constructor that holds contents is put in place of a placeholder with new placeholders as its contents, one for each. Here `y` stands for any `Bools` and does not match `bools(a, b)`, but `bools(z, u)` in its place does, so the case covers `Bools`.

Patterns inside patterns look more than one layer in. This function asks whether both are `true`:

    (bools(true, true)) ↦ true | (bools(a, b)) ↦ false          :   (Bools) → Bool

    ((bools(true, true)) ↦ true | (bools(a, b)) ↦ false)(bools(true, false))
        becomes   false

`bools(true, false)` does not match the first case, since `false` does not match `true`. It matches the second, giving `true` to `a` and `false` to `b`.

To decide that these cases cover `Bools`, `bools(z, u)` in place of `y` is enough: it matches the second case at once. Putting constructors in place can also go on inside the contents. This function asks the same question another way:

    (bools(true, a)) ↦ a | (bools(false, a)) ↦ false          :   (Bools) → Bool

    ((bools(true, a)) ↦ a | (bools(false, a)) ↦ false)(bools(true, true))
        becomes   true

Here `bools(z, u)` matches neither case, since `z` matches neither `true` nor `false`. But `z` stands for any `Bool`, so `false` and `true` are put in its place in turn: `bools(false, u)` matches the second case, and `bools(true, u)` the first. So the cases cover `Bools`.

Contents can be functions. A placeholder receives such contents, and the body can apply them:

    Task = task((Bool) → Bool, Bool)

    ((task(f, a)) ↦ f(a))(task((false) ↦ true | (true) ↦ false, true))
        becomes   ((false) ↦ true | (true) ↦ false)(true)
        becomes   false

A constructor that holds contents is a value on its own, so it can be an input, and the function given it can apply it:

    ((f) ↦ f(true, false))(bools)                                  becomes   bools(true, false)

Here `(f) ↦ f(true, false) : ((Bool, Bool) → Bools) → Bools`.

A constructor given the wrong number of contents is stuck, and has no type:

    bools(true)                                                    is stuck: bools holds two

A checked expression still never gets stuck. An applied value of function type is now a function, or a constructor that holds contents, and that constructor, given as many values as it holds, is data.

To count, a number was a function that applies another function that many times. To ask even whether a number is none, we had to apply it, here to a function that gives back `false`, and to `true`, which it gives back only if it applies that function no times:

    ((f, a) ↦ f(f(a)))((b) ↦ false, true)
        becomes   ((b) ↦ false)(((b) ↦ false)(true))
        becomes   ((b) ↦ false)(false)
        becomes   false

So two is not none. But a number was only what it does, and its type does not say what it is: `(f, a) ↦ f(f(a))` has type `((Bool) → Bool, Bool) → Bool`, and so does `(f, a) ↦ true`, which is no number.

With data, a number can be built instead. A constructor's contents can be of the type being declared:

    Nat = zero | succ(Nat)

A `Nat` is `zero`, or `succ` holding a `Nat`, and is one more than it: `zero`, `succ(zero)`, `succ(succ(zero))`, and so on. Each is a value with as many layers as the number it stands for, and patterns can look into the layers. This function asks whether a number is at least two:

    ((succ(succ(a))) ↦ true | (a) ↦ false)(succ(zero))            becomes   false

`succ(zero)` does not match `succ(succ(a))`, since `zero` does not match `succ(a)`.

A function that gives back one less than a number, and `zero` for `zero`, covers `Nat`:

    (zero) ↦ zero | (succ(a)) ↦ a                                  :   (Nat) → Nat

`y` matches neither case, but with `zero` in its place it matches the first, and with `succ(z)` the second. Without the first case, `(succ(a)) ↦ a` has no type: with `zero` in place of `y` it matches no case, and `zero` has no placeholder left to put constructors in place of.

Putting constructors in place can also go on without end. In

    (zero) ↦ true | (succ(zero)) ↦ false

`zero` in place of `y` matches the first case, but `succ(z)` matches neither. With `zero` in place of `z`, `succ(zero)` matches the second; but with `succ(u)` in its place, `succ(succ(u))` matches neither, and putting constructors in place of `u` leaves the same question one layer further in, every time. So the cases do not cover `Nat`, and the function has no type: `succ(succ(zero))` matches no case.

But to work through a number of any size, a function must repeat once for each layer, and so must use itself. Above the line, a function used itself by being handed itself, as `(a) ↦ a(a)` hands its input to itself, and that has no type. So referring to itself gets its own form. A **definition** gives an expression a name, a **defined name**, which can be used anywhere, its own definition included. Using `n` for a defined name:

    n : A           declares n, of type A
    n = e           defines n as e

and an expression can now be a defined name:

    n               a defined name

Each defined name is declared once and defined once, and is not also declared as a constructor.

A case can still take a placeholder with the same name as a defined name. Inside that case, the name is the placeholder, and the defined name is hidden. A name that no case around it takes, and that is not a constructor, is a defined name, and must be declared as one. So a new definition never changes what a function written before it means, even if that function uses the same name as a placeholder.

A defined name becomes what it stands for, and has its declared type:

    n               becomes   e, when n = e
    n : A           when it is declared so

The rules before, and this one, are now the only steps.

What we write is now declarations and definitions, and then one expression to carry out.

A definition `n = e` is checked when `e : A`, for the declared `n : A`. What we write is checked when every defined name it uses is declared and defined, every definition is checked, and the expression has a type.

A definition can use any defined name, its own included, and one defined after it: since every name's type is declared, no definition has to be checked first to find it.

    not : (Bool) → Bool
    not = (false) ↦ true | (true) ↦ false

    not(true)
        becomes   ((false) ↦ true | (true) ↦ false)(true)
        becomes   false

To keep examples short, from here on "`e` comes to `e'`" means that `e` becomes `e'` in one step or more.

A value put in place of a placeholder can contain a defined name, and the placeholder can be used inside a case that takes a placeholder named like that defined name. Put there as it is, the defined name would be read as that case's placeholder. So that it stays the defined name, the case's placeholder is first renamed, with the uses that belong to it, to a new name, used nowhere else:

    ((a) ↦ (not) ↦ a)((b) ↦ not(b))(true)(false)
        becomes   ((h) ↦ (b) ↦ not(b))(true)(false)
        becomes   ((b) ↦ not(b))(false)
        becomes   not(false)
        comes to   true

Without the renaming, the first step would give `(not) ↦ (b) ↦ not(b)`, where the last `not` belongs to the case. Given `true`, it would put `true` in place of that `not`, and come to `true(false)`, which is stuck.

A definition that uses its own name repeats:

    add : (Nat, Nat) → Nat
    add = (zero, b) ↦ b | (succ(a), b) ↦ succ(add(a, b))

    add(succ(zero), succ(zero))
        comes to   succ(add(zero, succ(zero)))
        comes to   succ(succ(zero))

Each round removes a layer from the first number and puts one around the result, until the first number is `zero`. This is one and one making two.

Definitions can use each other, in any order:

    even : (Nat) → Bool
    even = (zero) ↦ true | (succ(a)) ↦ odd(a)

    odd : (Nat) → Bool
    odd = (zero) ↦ false | (succ(a)) ↦ even(a)

A defined name can stand for any expression, not only a function. Like any input, it is carried out before it goes in:

    two : Nat
    two = add(succ(zero), succ(zero))

    even(two)
        comes to   odd(succ(zero))
        comes to   even(zero)
        comes to   true

Each use of a defined name becomes its definition in a step of its own, so the definition is carried out again at each use. An input is carried out once, however many times it is used, but here `add(succ(zero), succ(zero))` is carried out twice, once for each `two`:

    bools(even(two), odd(two))
        comes to   bools(true, odd(two))
        comes to   bools(true, false)

A defined name can go wherever an expression can, as contents too:

    ((task(f, a)) ↦ f(a))(task(not, true))
        comes to   ((false) ↦ true | (true) ↦ false)(true)
        becomes    false

Nothing makes a definition use a smaller input, so types do not rule out going on forever:

    loop : (Bool) → Bool
    loop = (a) ↦ loop(a)

    loop(true)      comes to   loop(true), and so on, forever

Only the chosen case's body is carried out, so a body that would go on forever does no harm in a case that is not chosen:

    ((true) ↦ false | (false) ↦ loop(true))(true)                 becomes   false

Above the line, to choose between two things still to be carried out, each waited in a function with no placeholders. Now each waits as the body of a case.

A checked expression still never gets stuck: a defined name always has a definition to become, and that definition has the name's type.

Above the line, `(a) ↦ a` worked on anything at all. With types, a defined name has one declared type, so

    identity : (Bool) → Bool
    identity = (a) ↦ a

cannot be used on a `Nat`. To use it there, we would have to define it again, the same but for its type. Likewise `bools` holds two `Bool`s, and to hold two `Nat`s we would have to declare another data type just like `Bools`.

So a type can leave something open too. A **placeholder in a type** is a name not declared as a data type, such as `X`, standing for a type. A type can now be one:

    X               a placeholder in a type

A type with a placeholder in it can say that what has it works for every type in the placeholder's place. That is written `∀X. A`, read "for every `X`, `A`", and like a body, `A` reaches as far as it can:

    ∀X. A           for every X, A

The `∀` takes the placeholder `X`, and a use of `X` in `A` belongs to the nearest `∀` around it that takes `X`.

Two rules connect `∀X. A` with `A`. An expression of type `∀X. A` has type `A` with any type in place of `X`, the same throughout `A`. And an expression of type `A` has type `∀X. A` when it has type `A` whatever type `X` is: when `X` is used in the type of no placeholder it uses from around it.

    e : A, with B in place of X      when e : ∀X. A
    e : ∀X. A                        when e : A, and X is used in the type of no placeholder e uses from around it

In a defined name's declared type, a `∀` is left unwritten in front for each placeholder in it that no `∀` in it takes. So

    identity : (X) → X
    identity = (a) ↦ a

declares `identity : ∀X. (X) → X`. The definition is checked with `X` standing for one type, whichever it is: the pattern gives `a : X`, so `(a) ↦ a : (X) → X`. That function uses no placeholder from around it, so it also has type `∀X. (X) → X`, as declared.

Each use of `identity` can have its own type in place of `X`, and "with `B` for `X`" says which:

    identity(true) : Bool                      with Bool for X
    identity(zero) : Nat                       with Nat for X
    identity(not) : (Bool) → Bool              with (Bool) → Bool for X

Since the definition must work whichever type `X` stands for, no constructor has type `X`, and only a placeholder pattern fits it. So this is not checked:

    isZero : (X) → Bool
    isZero = (zero) ↦ true | (a) ↦ false

To have type `∀X. (X) → Bool`, the function would first have to have type `(X) → Bool`, and `zero` fits only `Nat`, not `X`.

A data type can take placeholders in types too, one or more, all different. Using `X` for a placeholder in a type:

    T(X, …) = d | …

So far, the names in the types of contents were names of data types; now they can also be the data type's own placeholders. Such a data type is **filled** with types, one for each of its placeholders, in order. What a type must be filled with is what it **needs**: a data type with placeholders needs one type for each. A type can now be a data type, filled:

    T(A, …)         a data type, filled

There are as many types in `T(A, …)` as `T` needs. For example, pairs of any two types:

    Pair(X, Y) = pair(X, Y)

A data type, filled, lists its constructors with the types filling it in place of its placeholders: `Pair(Bool, Nat)` lists `pair(Bool, Nat)`. So the rule for constructors gives `pair : (Bool, Nat) → Pair(Bool, Nat)`, and the same for any other two types:

    pair(true, zero) : Pair(Bool, Nat)         with Bool for X and Nat for Y

Each type filling a data type can be any type, so types nest, and so do values:

    pair(true, pair(zero, zero)) : Pair(Bool, Pair(Nat, Nat))
    pair(not, zero) : Pair((Bool) → Bool, Nat)

Every placeholder in a type we write must be taken: by the data type whose contents use it, or by a `∀`, written or unwritten. So this is not checked, since `Box` takes no placeholder, and no `∀` takes `X`:

    Box = box(X)

A definition can look into data whatever it holds:

    first : (Pair(X, Y)) → X
    first = (pair(a, b)) ↦ a

    first(pair(not, zero))(true)
        comes to   ((false) ↦ true | (true) ↦ false)(true)
        becomes    false

Here `(Bool) → Bool` is in place of `X`, and `Nat` of `Y`: `first` gives back the function the pair held, and it is applied.

So far, the type in place of a placeholder in a defined name's type is decided where the name is used, and within the definition, the placeholder stands for that one type. So a function given as an input has one type throughout, and this is not checked:

    onBoth : ((X) → X, Pair(Bool, Nat)) → Pair(Bool, Nat)
    onBoth = (g, pair(s, t)) ↦ pair(g(s), g(t))

Within the definition, `X` is the one type that a use of `onBoth` puts in its place, whichever it is. So `g(s)` already has no type: the input type of `g` is `X`, and `s` is a `Bool`.

What is wanted is an input that itself works for every type. A `∀` written inside the type says so:

    both : (∀X. (X) → X, Pair(Bool, Nat)) → Pair(Bool, Nat)
    both = (g, pair(s, t)) ↦ pair(g(s), g(t))

Now `g : ∀X. (X) → X`, and each use of `g` has its own type in place of `X`: `Bool` for `g(s)`, and `Nat` for `g(t)`.

`identity` has type `∀X. (X) → X`, as declared, and so does `(a) ↦ a` written in place, since `(a) ↦ a : (X) → X` uses no placeholder from around it. So either can be given to `both`:

    both(identity, pair(true, zero))
        comes to   pair(((a) ↦ a)(true), ((a) ↦ a)(zero))
        comes to   pair(true, zero)

In `onBoth`, `g` cannot be given the type `∀X. (X) → X`: the expression `g` uses the placeholder `g` from around it, and its type has `X` in it. And in return for each use of `g` having its own type, `both` can be given only what works for every type: `identity`, but not `not`.

Which name a `∀` takes makes no difference, as long as the new name is not already used inside: `∀X. (X) → X` and `∀Y. (Y) → Y` are the same type, but `∀X. (X) → Y` and `∀Y. (Y) → Y` are not.

A type put in place of `X` goes only where `X` is not taken by a `∀` inside. If it uses a placeholder that a `∀` inside takes, that `∀` is first renamed, to a name used in neither type, as a case is renamed when a value is put in place.

A `∀` can be written in a result type too:

    const : (X) → ∀Y. (Y) → X
    const = (a) ↦ (b) ↦ a

    const(true)(zero)
        comes to   ((b) ↦ true)(zero)
        becomes    true

`const(true) : ∀Y. (Y) → Bool`, so it can be given an input of any type. In a definition whose own type uses `Y`, the `∀` inside is renamed:

    keep : (Y) → (Nat) → Y
    keep = (a) ↦ const(a)

With `Y` in place of `X`, `const : (Y) → ∀Z. (Z) → Y`, so `const(a) : (Nat) → Y`, with `Nat` for `Z`. Without the renaming, the type would read `(Y) → ∀Y. (Y) → Y`, and `const(a)` would seem to give back whatever it is given.

Written as `keep = const`, the definition would not be checked. A `∀` is set aside only in front of a type, and in the type of `const`, the `∀Z.` comes after an arrow; so `const` never has type `(Y) → (Nat) → Y`, while `const(a)`, whose type has the `∀` in front, does.

An input type can also have a `∀` in front, and then the patterns must fit it. A placeholder fits it as it is, and keeps the `∀`, as `g` does in `both`. And any pattern fits `∀X. A` when it fits `A`, with `X` renamed to a new placeholder; the cases cover `∀X. A` when they cover `A` in the same way:

    p fits ∀X. A                     when p fits A, for a new X

    bit : (∀X. Bool) → Nat
    bit = (false) ↦ zero | (true) ↦ succ(zero)

`false` and `true` fit `Bool`, so they fit `∀X. Bool`, and they cover it as they cover `Bool`. And `true : ∀X. Bool`, since it uses no placeholder, so it can be given to `bit`:

    bit(true)                                  comes to   succ(zero)

`(a) ↦ a(a)`, which had no type, has one when the type of `a` works for every type:

    self : (∀X. (X) → X) → (Y) → Y
    self = (a) ↦ a(a)

The first `a` is used with `(Y) → Y` for `X`, and the second with `Y`. But `self(self)` has no type: an input of `self` must have type `∀X. (X) → X`, so that for each `X` its input type and result type are both `X`, while the input type and the result type of `self` are never the same.

So far, the names in the types of contents were names of data types and the data type's own placeholders. They can also be placeholders that a `∀` written in the contents takes, and then the contents work for every type:

    Poly = poly(∀X. (X) → X)

    poly(identity) : Poly

In the pattern `poly(g)`, `g` fits the type `Poly` declares for its contents, so `g : ∀X. (X) → X`, and `g` can be used at two types:

    ((poly(g)) ↦ pair(g(true), g(zero)))(poly(identity))
        comes to   pair(((a) ↦ a)(true), ((a) ↦ a)(zero))
        comes to   pair(true, zero)

What we write is checked only within two limits, which mean that checking never has to guess a type with a `∀` in it.

The first limit: a data type is filled only with types that have no `∀` in them, and only such types are put in place of placeholders in types. So a declaration that writes `Pair(∀X. (X) → X, Nat)` is not checked: to hold something that works for every type, contents say so, as `Poly` does.

Held in a pair, `identity` is used with one type, as in `pair(identity, zero) : Pair((Bool) → Bool, Nat)`, with `Bool` for `X`. So this, which does with a pair what was done above with `poly`, is not checked:

    ((pair(g, b)) ↦ pair(g(true), g(zero)))(pair(identity, zero))

The pattern gives `g` the first type filling `Pair`, which has no `∀`, so `g` cannot be used with both `Bool` and `Nat`.

And `identity(both)` is not checked, though `both` has a type: `identity` would be used with the type of `both` for `X`, and that type has a `∀` in it.

The second limit: a function's input types have a `∀` in them only where the function's type is **known** from where it is written. A function's type is known in three ways, and in each, a `∀` in front of the type is set aside, as `identity`'s function was checked against `(X) → X`. When the function is what a definition defines, its type is the declared one, as for `both` and `self`. When the function is an input, its type is the input type of what it is given to. And when the function is the body of a case, and the type of the function with that case is known, its type is the result type.

    withIdentity : ((∀X. (X) → X) → Bool) → Bool
    withIdentity = (h) ↦ h(identity)

The type of an input of `withIdentity` is known: `(∀X. (X) → X) → Bool`. So a function given to it can use its placeholder at two types:

    withIdentity((f) ↦ f(f)(true))
        becomes    ((h) ↦ h(identity))((f) ↦ f(f)(true))
        becomes    ((f) ↦ f(f)(true))(identity)
        comes to   true

In `f(f)(true)`, the first `f` is used with `(Bool) → Bool` for `X`, and the second with `Bool`. But a function that is applied has no known type, so the expression that the second step gives, written on its own, is not checked:

    ((f) ↦ f(f)(true))(identity)

Its input type would have no `∀`, and then for `f(f)`, `f` would have to have a type that is its own input type, as in `(a) ↦ a(a)`.

The body of a case can have a known type too:

    applyTo : (Bool, Nat) → (∀X. (X) → X) → Pair(Bool, Nat)
    applyTo = (s, t) ↦ (g) ↦ pair(g(s), g(t))

    applyTo(true, zero)(identity)
        comes to   ((g) ↦ pair(g(true), g(zero)))(identity)
        comes to   pair(true, zero)

The body `(g) ↦ pair(g(s), g(t))` has a known type, the result type of `applyTo`, so `g : ∀X. (X) → X`. But `((g) ↦ pair(g(true), g(zero)))(identity)`, written on its own, is not checked, although it does what `both` and `applyTo` do: there, the function is applied, so its type is not known.

The limits are on what we write, not on the rules for types, and a step can leave them: `withIdentity((f) ↦ f(f)(true))` is checked, and it comes to `((f) ↦ f(f)(true))(identity)`, which is not. So what each step keeps is a type by the rules: if `e : A`, and `e` uses no placeholder from around it, then `e` is a value, or `e` becomes some `e' : A`, whether `e'` is within the limits or not. The limits only accept less, so a checked expression still never gets stuck.

All of these combine. A list is empty, or an item followed by a list:

    List(X) = empty | cons(X, List(X))

    cons(true, cons(false, empty)) : List(Bool)

A defined name that is not a function can have a placeholder in its type too, and each use has its own type in its place:

    none : List(X)
    none = empty

    pair(cons(true, none), cons(zero, none))       comes to   pair(cons(true, empty), cons(zero, empty))

One `none` is a `List(Bool)`, and the other a `List(Nat)`.

`map` applies a function to every item of a list:

    map : ((X) → Y, List(X)) → List(Y)
    map = (f, empty) ↦ empty | (f, cons(a, r)) ↦ cons(f(a), map(f, r))

    map(not, cons(true, cons(true, empty)))        comes to   cons(false, cons(false, empty))
    map(even, cons(zero, empty))                   comes to   cons(true, empty)

In the first, `Bool` is in place of both `X` and `Y`; in the second, `Nat` is in place of `X`, and `Bool` of `Y`.

A function that changes the second of a pair does to a pair what `map` does to a list:

    second : ((X) → Y, Pair(Z, X)) → Pair(Z, Y)
    second = (f, pair(a, b)) ↦ pair(a, f(b))

A definition that is given such a function, and uses it to apply `not`, must say which data it works on. So it is written once for lists, and again for pairs:

    flipList : (((Bool) → Bool, List(Bool)) → List(Bool), List(Bool)) → List(Bool)
    flipList = (m, s) ↦ m(not, s)

    flipPair : (((Bool) → Bool, Pair(Nat, Bool)) → Pair(Nat, Bool), Pair(Nat, Bool)) → Pair(Nat, Bool)
    flipPair = (m, s) ↦ m(not, s)

    flipList(map, cons(true, empty))               comes to   cons(false, empty)
    flipPair(second, pair(zero, true))             comes to   pair(zero, false)

The two definitions are the same, and their types differ only where one has `List(Bool)` and the other `Pair(Nat, Bool)`. To write it once, that part must be left open. But what is left open is not the type of a value: filled with `Bool`, it must give `List(Bool)`, so it is something like `List` itself.

So "type" now widens. Until here, every type was a kind of value, the type of some expression. `List` alone is not: it still needs a type, and no value has type `List`. From here on, a type can also be something that needs types: `List` needs one type, `Pair` needs two, and `Nat`, `List(Nat)` and `(Bool) → Bool` need nothing. Only a type that needs nothing is the type of an expression.

A placeholder in a type can need types too, and then it is filled like a data type:

    X(A, …)         a placeholder in a type, filled

There is at least one type in `X(A, …)`, and as in `T(A, …)`, none of them has a `∀` in it. With it, `flipList` and `flipPair` are one definition:

    flip : (((Bool) → Bool, F(Bool)) → F(Bool), F(Bool)) → F(Bool)
    flip = (m, s) ↦ m(not, s)

What a placeholder in a type needs is what its uses show. A type written as an input type, a result type, contents, or after a `∀`, needs nothing, and so does the type of an expression; a type filling a data type or a placeholder needs what that one needs at its position. In `flip`, `F(Bool)` is an input type, so it needs nothing, and `F` is filled with one type, so `F` needs one type, which needs nothing, as `Bool` does. Where its uses show no more, a placeholder needs nothing, as `X` and `Y` do in `map`.

A type is put in place of a placeholder only when it needs the same: as many types, each needing the same in turn. `List` needs one type, which needs nothing, so it can be put in place of `F`, and then `F(Bool)` is `List(Bool)`:

    flip(map, cons(true, empty))                   comes to   cons(false, empty)

With `List` for `F`, and `Bool` for both `X` and `Y`, `map` has the type of `m`. But `Nat` cannot be put in place of `F`, since it needs nothing, and neither can `Pair`, since it needs two types.

A type can also be filled with only the first types it needs, and then it needs the rest. This widens `T(A, …)`, which until now had all the types `T` needs: `Pair(Nat)` is `Pair` filled with `Nat` alone, and needs one more type. Like `List`, it is the type of no value, but it can be put in place of `F`. Then `F(Bool)` is `Pair(Nat, Bool)`: the types filling `F` come after those already filling what is put in its place.

With `Pair(Nat)` for `F`, `Bool` for `X` and `Y`, and `Nat` for `Z`, `second` has the type of `m`, so `flip` works on pairs too:

    flip(second, pair(zero, true))
        comes to   ((f, pair(a, b)) ↦ pair(a, f(b)))(not, pair(zero, true))
        comes to   pair(zero, ((false) ↦ true | (true) ↦ false)(true))
        becomes    pair(zero, false)

The types already filling what is put in place of `F` can be placeholders too:

    notSecond : (Pair(Z, Bool)) → Pair(Z, Bool)
    notSecond = (s) ↦ flip(second, s)

Here `Pair(Z)` is in place of `F`, where `Z` is a placeholder in the type of `notSecond`.

A placeholder that needs a type can itself be put in place of another:

    flipTwice : (((Bool) → Bool, G(Bool)) → G(Bool), G(Bool)) → G(Bool)
    flipTwice = (m, s) ↦ flip(m, flip(m, s))

    flipTwice(second, pair(zero, true))            comes to   pair(zero, true)

Within `flipTwice`, `G` is put in place of `F`, since both need one type; and the use of `flipTwice` puts `Pair(Nat)` in place of `G`. A placeholder filled with only some of the types it needs can be put in place of another too:

    flipLast : (((Bool) → Bool, G(Nat, Bool)) → G(Nat, Bool), G(Nat, Bool)) → G(Nat, Bool)
    flipLast = (m, s) ↦ flip(m, s)

    flipLast(second, pair(zero, true))             comes to   pair(zero, false)

Here `G` needs two types, so `G(Nat)` needs one, as `F` does, and is put in place of `F`; the use of `flipLast` puts `Pair` in place of `G`.

A `∀` written inside a type can use a placeholder that needs types. Given a function that works for every `X` and `Y`, on one `F`, this definition uses it on an `F(Bool)` and on an `F(Nat)`:

    notAndEven : (∀X. ∀Y. ((X) → Y, F(X)) → F(Y), F(Bool), F(Nat)) → Pair(F(Bool), F(Bool))
    notAndEven = (m, s, t) ↦ pair(m(not, s), m(even, t))

    notAndEven(map, cons(true, empty), cons(zero, empty))
        comes to   pair(cons(false, empty), cons(true, empty))

The unwritten `∀` in front takes `F`, and the written ones take `X` and `Y`. With `List` for `F`, `map` has the type of `m`, and `m` is used once with `Bool` for `X`, and once with `Nat`.

The placeholders of a data type can need types too:

    Counted(F, X) = counted(F(X), Nat)

    counted(cons(true, empty), succ(zero)) : Counted(List, Bool)

`F` is used as `F(X)`, so it needs one type, which needs what `X` needs; and the uses of `X` show no more, so `X` needs nothing. So `Counted` needs two types, the first of which needs one. `Counted(List, Bool)` lists `counted(List(Bool), Nat)`.

A declaration that writes `Counted(Nat, Bool)` is not checked, since `Nat` needs nothing and `F` needs one type; neither is one that writes `Counted(Pair, Bool)`, since `Pair` needs two. `Counted(List)` needs one more type, so it can be put in place of a placeholder like `F`, but it is the type of no value.

A pattern looks into data built by `counted` as into any other. In this type, `F` fills the first position of `Counted`, so it needs one type:

    size : (Counted(F, X)) → Nat
    size = (counted(s, b)) ↦ b

    size(counted(cons(true, empty), succ(zero)))   comes to   succ(zero)

What a placeholder needs is decided for each `∀` that takes it, from the uses that belong to that `∀`, and for each placeholder of a data type, from its uses in that declaration. So renaming a `∀` never changes what is checked. Two `∀`s can take the same name, and then each of the two placeholders needs what its own uses show:

    through : (∀X. (X) → X, X(Bool)) → X(Bool)
    through = (g, s) ↦ g(s)

    through(identity, cons(true, empty))           comes to   cons(true, empty)

The `X` that the written `∀` takes needs nothing, and the `X` that the unwritten one in front takes needs one type, just as if the first were named `Y`.

The uses that show what one placeholder needs must agree. In `(F(Bool)) → F`, one use shows that `F` needs one type, and the other that it needs nothing, so a declaration that writes it is not checked. Nor is one whose needs never end:

    Deep(F) = deep(Deep(F(Bool)))

In `Deep(F(Bool))`, `F(Bool)` fills the first position of `Deep`, the position of `F` itself, so `F(Bool)` must need what `F` needs; but `F` needs one type more than `F(Bool)` does. Whatever `F` needs, it would need one type more.

So what we write is checked only when every type in it needs what its position needs, and what each placeholder in a type needs is decided in this way.

The expression at the end writes no types, so it decides no needs. The types the rules give it need nothing, and each type put in place of a placeholder needs what that placeholder needs. A placeholder that no `∀` takes in those types needs the same wherever it is used. So `(m, s) ↦ m(not, s)`, as the expression at the end, can have the type written for `flip`, with no `∀` in front, where `F` needs one type throughout; but it cannot have `(((Bool) → Bool, F(Bool)) → F(Bool), F) → F(Bool)`, where `F` would need one type in one use and nothing in another.

So each role has forms and rules of its own. Choosing is constructors and cases, holding is contents and patterns, counting is data whose contents are of its own type, referring to itself is definitions, and working on anything is placeholders in types, with `∀`. And types say what kind of value each expression is, so that a checked expression never gets stuck.
