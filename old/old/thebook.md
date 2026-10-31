# The Book

## What this is for

Some things we say are questions, and some are answers. "The opposite of true" is a question, and "false" is its answer. "The 1000th prime" is a question, and "7919" is its answer. A question and its answer are about the same thing. The difference is that the answer shows it directly, while the question still needs work.

This book builds a language for saying such things exactly: questions, answers, and the work that gets from one to the other. The work is done in small **steps**, each one saying the same thing a little more directly, until only the answer is left. The steps follow a handful of rules, simple enough that anyone, or any machine, can carry them out and always get the same answer.

The language is small, but anything that can be described as things and steps can be said in it: the rules of a game, arithmetic, sorting, anything a computer can work out. And what we write can be checked before anything is worked out, so that a thing put where it doesn't fit is caught before it does harm.

Here is how the book goes. Sections 1 to 9 build the language one piece at a time, and each piece starts from what it is for. Section 10 looks back and finds something surprising: functions alone could do the work of almost every piece, which is why so small a language can say so much. Section 11 takes on the two hardest ideas. Section 12 puts the whole language on one page.

## 1. Things

The simplest thing to say is a thing that is just itself. We say what kind of thing it is by listing every thing of that kind:

    Bool = false | true

The `|` reads "or". This **declares** a **type**, `Bool`: a kind of thing. A `Bool` is `false` or `true`, and nothing else. `false` and `true` are **constructors**: names that stand only for themselves.

A type can list any number of constructors, one or more. Here is a second type, the hands of a game:

    Hand = rock | paper | scissors

Each type is declared once, and each constructor belongs to one type only.

Everything we write in the language, question or answer, is an **expression**. An expression with nothing left to work out is a **value**: an answer. A constructor is a value.

## 2. Functions

A **function** turns things into other things. To say what it does, we say what it gives back for each thing it can be given. Each of these is a **case**:

    not : (Bool) → Bool
    not = (false) ↦ true | (true) ↦ false

The second line reads "given `false`, `true`; or given `true`, `false`". Each case has a **pattern**, in parentheses, then `↦`, then its **body**: what the case gives back. The first line reads "`not` takes a `Bool` and gives back a `Bool`". That is the **type** of `not`, a **function type**.

The two lines also give the function a name. The first **declares** the name `not`, with its type, and the second **defines** it: `not` now stands for the function. A name given this way is a **defined name**, and it can be used anywhere an expression can.

To use a function, we **apply** it to an **input**, written after it in parentheses:

    not(true)

This is a question: what does `not` give back for `true`? We **carry it out**, one step at a time:

    not(true)
        becomes   ((false) ↦ true | (true) ↦ false)(true)
        becomes   false

In the first step, the name `not` becomes the function it stands for. In the second, the function is applied. Its cases are tried in order, and the first whose pattern **matches** the input is used: `false` doesn't match `true`, and `true` does, so the application becomes that case's body, `false`. Nothing is left to work out, so `false` is the answer.

The parentheses around the function only keep it in one piece. Without them, `(true)` would be read as part of the last body, `false(true)`.

A defined name can stand for any expression, not only a function:

    favourite : Hand
    favourite = rock

and `favourite` becomes `rock` in one step.

## 3. Placeholders

A case for every possible input works for `not`, but soon becomes too much: a function of two `Hand`s would need nine cases. And often a case doesn't care what it is given, or only passes it on. For this, a pattern can be a **placeholder**: a name that matches anything, and stands, in the body, for whatever it matched.

    and : (Bool, Bool) → Bool
    and = (true, b) ↦ b | (false, b) ↦ false

A function can take several inputs, separated by commas, and then each case has one pattern for each input, matched in order. Here: given `true` and anything, give back that thing; given `false` and anything, give back `false`.

    and(true, false)
        becomes   ((true, b) ↦ b | (false, b) ↦ false)(true, false)
        becomes   false

The first case matches: `true` matches `true`, and `false` matches the placeholder `b`, which **gives** `b` the value `false`. The application becomes the body, `b`, with `false` in its place.

So a pattern is one of two things:

    a constructor      matches only itself
    a placeholder      matches anything, and gives the placeholder that value

A name declared as a constructor is a constructor; any other name in a pattern is a placeholder. The placeholders of one case are all different: in `(a, a) ↦ a`, we could not tell which input the body means.

Because cases are tried in order, a last case of placeholders catches everything the cases before it left. That is how a function says "otherwise":

    beats : (Hand, Hand) → Bool
    beats = (rock, scissors) ↦ true | (scissors, paper) ↦ true | (paper, rock) ↦ true | (a, b) ↦ false

Rock beats scissors, scissors beat paper, paper beats rock; otherwise, the first hand doesn't win. From here on, "comes to" means "becomes, in one step or more":

    beats(paper, rock)      comes to   true
    beats(rock, paper)      comes to   false

A case can be all placeholders. This function gives back the first of two inputs, whatever they are:

    ((a, b) ↦ a)(rock, paper)      becomes   rock

## 4. Carrying out, exactly

So far each question held one application. Usually questions sit inside questions:

    not(and(true, false))

Which part is worked out first? The rule is: an input is worked out to a value before it goes in. So first `and(true, false)`, then `not` of its answer. Each line below is one step, in one place:

    not(and(true, false))
        becomes   ((false) ↦ true | (true) ↦ false)(and(true, false))
        becomes   ((false) ↦ true | (true) ↦ false)(((true, b) ↦ b | (false, b) ↦ false)(true, false))
        becomes   ((false) ↦ true | (true) ↦ false)(false)
        becomes   true

First the function being applied, `not`, becomes what it stands for. Then its input: `and` becomes its function, and that application takes its step. Only when the function and its input are both values does the outer application take its step.

That is all there is to carrying out. Here it is in full. To describe the language we use letters that are never written in it: `e` for any expression, `v` for any value, `n` for a defined name, `k` for a case, and `e, …` for any number of expressions, even none, and likewise `v, …`; `k | …` is one case or more. A `…` alone stands for any number of expressions. "`e` becomes `e'`" means one step.

    n                  becomes   e, when n = e
    (k | …)(v, …)      becomes   the body of the first case that v, … match,
                                 with each placeholder replaced by the value it was given
    e(…)               becomes   e'(…), when e becomes e'
    v(…, e, …)         becomes   v(…, e', …), when e becomes e' and everything before e is a value

Several values match several patterns when there are as many values as patterns, and each value matches its own. The first two rules are the steps. The other two say where a step happens: in the function being applied first, then in the inputs, from left to right. There are no other steps.

At each point at most one rule applies, in one place. So carrying out never has a choice to make, and a question has at most one answer.

**Bodies wait.** No step happens inside a function. Its body is worked out only once the function is applied, with the inputs in place. So a function is a value as soon as it's written, even when its body holds questions. A function can take no inputs at all, and then it is a question kept for later:

    later : () → Bool
    later = () ↦ not(true)

`later` becomes `() ↦ not(true)`, a value: the question inside is not touched. Applying it, to no inputs, opens it:

    later()
        comes to   not(true)
        comes to   false

**How it ends.** Carrying out ends when the whole expression is a value, since no rule applies to a value. But it can also get **stuck**: something is left to work out, and no rule applies. That happens when a value is somewhere it doesn't fit:

    not(rock)           stuck once not becomes its function: no case matches rock
    true(false)         stuck: true is not a function, and has no cases
    not(true, false)    stuck once not becomes its function: its cases have one pattern, and it is given two inputs

**Grouping.** A body reaches as far as it can. So `(a) ↦ a(true)` is a function whose body is `a(true)`, and a function written out and applied goes in parentheses, as in `((a, b) ↦ a)(rock, paper)`. Such parentheses only show how the expression is grouped; the rules work on the grouping, not on the writing.

**Four choices.** The rules make four choices, and each could have gone another way.

- Inputs are worked out before they go in. So each is worked out once, however many times it's used, even if it's never used: in `((a, b) ↦ a)(rock, not(true))`, `not(true)` is worked out before the case drops it.
- Bodies wait. So a function is a value as soon as it's written, and a question can be kept for later.
- The order is fixed. So carrying out never has to choose.
- A function is given exactly as many inputs as its cases have patterns. So every step uses every input and fills every placeholder. It could have been otherwise: a function given too few could wait for the rest, and one given too many could pass the rest on.

## 5. Checking

The stuck expressions have something in common: in each, a value is somewhere it doesn't fit. `rock` was given to a function whose cases are for `Bool`s; `true` was applied, though it is not a function. And we had already written what fits, in `not : (Bool) → Bool`. Nothing yet makes sure that what we write agrees with it.

That is what **checking** does. Before anything is carried out, we find the type of each expression from the types of its parts. An expression that has a type is **checked**, and a checked expression never gets stuck.

We write `e : A`, read "`e` has type `A`", using `A` and `B` for any types and `T` for the name of a type. A type is one of two kinds:

    T              a data type: a type declared with its constructors, like Bool
    (A, …) → B     a function type: takes A, …, gives back B

There is one rule for each kind of expression. Here `c` stands for a constructor and `x` for a placeholder:

    c : T                    when T lists c                           true : Bool
    n : A                    when n is declared with A                not : (Bool) → Bool
    x : A                    when its pattern gives it A
    e(e', …) : B             when e : (A, …) → B, and e', … : A, …    not(true) : Bool
    (k | …) : (A, …) → B     when in every case, the patterns fit A, …, and the body : B
                             with the types they give; and any values of types A, … match some case

`e', … : A, …` means as many expressions as types, each of its own type, in order. A pattern **fits** a type like this:

    x fits A                 giving x : A
    c fits A                 when c : A

So checking a function goes case by case. The patterns give the placeholders their types, and with those types the body is checked. For `and`: in `(true, b) ↦ b`, `true` fits `Bool`, and `b` fits `Bool` and gets the type `Bool`, so the body `b : Bool`. The second case is checked the same way. And any two `Bool`s match some case. So `and : (Bool, Bool) → Bool`.

The last condition, that any values match some case, means a function always has something to give back for what it's given:

    (true) ↦ false         has no type: false is a Bool, and matches no case

And none of the stuck expressions has a type:

    not(rock)              not takes a Bool, and rock : Hand
    true(false)            true : Bool, which is not a function type
    not(true, false)       not takes one input, and is given two

**What we write** is declarations and definitions, then one expression to carry out. Each name is declared once and defined once, and each definition is checked: `n = e` is checked when `e` has the type `n` is declared with. Since every name's type is declared, it is known before any definition is checked, so a definition can use any name, in any order.

**Why a checked expression never gets stuck.** Two things are true of it at every point. Each step keeps its type: a name becomes a definition of its declared type, and a case's body has the type the function gives back, with each placeholder replaced by a value of the type its pattern gave it. And while it is not a value, there is a step to take: when an application's parts are all values, the applied value has a function type, so it is a function; it is given as many inputs as it takes; and since they have the types it takes, they match some case.

## 6. Holding

Often a thing is made of other things: a round of the game is two hands, one for each player. A constructor can hold things, its **contents**, of the types written after it:

    Round = round(Hand, Hand)

A constructor that holds contents is applied to them, like a function, and its type is a function type:

    round : (Hand, Hand) → Round

But applied to values, it takes no step: it holds them. `round(rock, paper)` is a value: a round where the first player showed rock and the second paper. Values built from constructors are **data**.

Contents are inputs like any others, worked out before they go in:

    answer : (Hand) → Hand
    answer = (rock) ↦ paper | (paper) ↦ scissors | (scissors) ↦ rock

    round(rock, answer(rock))       comes to   round(rock, paper)

`answer` gives back the hand that beats the one it's given.

To use data, we take it apart with a pattern: a constructor, with patterns for its contents.

    won : (Round) → Bool
    won = (round(a, b)) ↦ beats(a, b)

    won(round(paper, rock))         comes to   true

The pattern `round(a, b)` matches any round, giving `a` the first hand and `b` the second. Since the contents of a pattern are patterns too, patterns nest, and can look more than one layer in:

    same : (Round) → Bool
    same = (round(rock, rock)) ↦ true | (round(paper, paper)) ↦ true | (round(scissors, scissors)) ↦ true | (r) ↦ false

    same(round(paper, paper))       comes to   true
    same(round(paper, rock))        comes to   false

`round(paper, rock)` doesn't match `round(paper, paper)`, since `rock` doesn't match `paper`; it matches only the last case. Exactly, using `p` for any pattern:

    c(v, …) matches c(p, …)     when v, … match p, …, giving what they give
    c(p, …) fits B              when c : (A, …) → B, and p, … fit A, …, giving what they give

A type can mix constructors that hold things with constructors that don't. A move is passing, or playing a hand:

    Move = pass | play(Hand)

A constructor that holds contents holds at least one thing, and its contents can be of any type. As a pattern on its own, without contents, a constructor must be one that holds nothing. A constructor given the wrong number of contents, as in `round(rock)`, is stuck, and has no type.

None of this changes the steps: `c(v, …)` is simply one more kind of value. And a checked expression still never gets stuck: a value of function type is now a function, or a constructor that holds contents, and that constructor, given as many values as it holds, is data.

## 7. Repeating

How many numbers are there? No end of them, so a type of numbers can't be declared by listing them. But every number can be built: a number is zero, or one more than a number.

    Nat = zero | succ(Nat)

A constructor's contents can be of the very type being declared. `succ` reads "one more than": `zero`; `succ(zero)`, one; `succ(succ(zero))`, two; and so on. Each number is a value with as many layers as it counts.

Patterns can look into the layers. This function asks whether a number is at least two:

    ((succ(succ(a))) ↦ true | (a) ↦ false)(succ(zero))       becomes   false

`succ(zero)` doesn't match `succ(succ(a))`, since `zero` doesn't match `succ(a)`.

But a pattern sees only as many layers as it is written with. To work through a number of any size, say to add two numbers, a function must repeat once for each layer. It does that by using itself: a definition can use its own name.

    add : (Nat, Nat) → Nat
    add = (zero, b) ↦ b | (succ(a), b) ↦ succ(add(a, b))

Zero and `b` make `b`; one more than `a`, and `b`, make one more than `a` and `b`.

    add(succ(zero), succ(zero))
        comes to   succ(add(zero, succ(zero)))
        comes to   succ(succ(zero))

One and one make two. Each round takes a layer off the first number and puts one around the result, until the first number is `zero` and the first case ends it. This is checked because the type of `add` is declared: while its definition is checked, the use `add(a, b)` already has a known type.

Definitions can also use each other:

    even : (Nat) → Bool
    even = (zero) ↦ true | (succ(a)) ↦ odd(a)

    odd : (Nat) → Bool
    odd = (zero) ↦ false | (succ(a)) ↦ even(a)

    even(succ(succ(zero)))      comes to   true

Nothing makes a definition use itself on something smaller, so repeating need not stop:

    loop : (Bool) → Bool
    loop = (a) ↦ loop(a)

    loop(true)      comes to   loop(true), and so on, forever

`loop` is checked. A type says what kind of answer an expression will have, if it has one; not that it has one. So carrying out a checked expression ends in one of two ways: it comes to a value of its type, or it goes on forever. It never gets stuck.

## 8. Functions as values

A function is a value like any other. So it can be an input, be held as contents, or be given back. That lets us say a way of doing something once, and use it on many things.

A function that takes a function:

    twice : ((Hand) → Hand, Hand) → Hand
    twice = (f, a) ↦ f(f(a))

    twice(answer, rock)       comes to   scissors

The answer to the answer to rock: paper, then scissors. The type of `twice` reads: takes a function from `Hand` to `Hand`, and a `Hand`, and gives back a `Hand`. As with any input, `answer` is worked out before it goes in, so what takes the place of `f` is the function `answer` stands for.

A function that gives back a function:

    against : (Hand) → (Hand) → Bool
    against = (a) ↦ (b) ↦ beats(b, a)

    against(rock)             comes to   (b) ↦ beats(b, rock)
    against(rock)(paper)      comes to   true

`against(rock)` is a test for hands: does this one beat rock? Like a body, the `B` in `(A, …) → B` reaches as far as it can: `(Hand) → (Hand) → Bool` takes a `Hand` and gives back a `(Hand) → Bool`.

Notice that `rock` went in place of `a` although that `a` is inside the inner function, where nothing is carried out. A step puts each value wherever its placeholder is used in the body, inner functions included.

**Belonging.** Functions inside functions can use the same placeholder names. A placeholder used in a body **belongs** to the nearest case around it that has that placeholder in its patterns, and a step replaces only the uses that belong to the case being applied:

    ((a) ↦ (a) ↦ a)(rock)          becomes   (a) ↦ a

The last `a` belongs to the inner function, so `rock` does not go there. Every placeholder used must belong to some case, since otherwise nothing would ever replace it. A name used that belongs to no case is a constructor or a defined name.

**Grouping, again.** A body reaches as far as it can, so a `|` after a body that is a function belongs to that function. To give more cases to the function around it, the body goes in parentheses:

    pick : (Bool) → (Hand, Hand) → Hand
    pick = (true) ↦ ((a, b) ↦ a) | (false) ↦ (a, b) ↦ b

    pick(false)(rock, paper)       comes to   paper

**Held as contents.** Contents can be functions, so data can hold a way of doing something:

    Task = task((Hand) → Hand, Hand)

    ((task(f, a)) ↦ f(a))(task(answer, rock))      comes to   paper

A constructor that holds contents is also a value on its own, so it can be an input:

    ((f) ↦ f(rock, paper))(round)      becomes   round(rock, paper)

Here `(f) ↦ f(rock, paper) : ((Hand, Hand) → Round) → Round`.

**What a function can't do.** Only a placeholder fits a function type, since there is no constructor to match a function against. So a function given a function can apply it, but can't look inside to see how it was made.

## 9. Working on anything

Checking has a cost. Written out on its own, `(a) ↦ a` gives back whatever it is given, and it has many types: `(Bool) → Bool`, `(Hand) → Hand`, and more. But a defined name has one declared type, so

    identity : (Bool) → Bool
    identity = (a) ↦ a

works on `Bool`s only. To use it on `Hand`s, we'd have to define it again, the same except for its type. Likewise `Round` holds two `Hand`s, and to hold two numbers we'd need another type just like it. The idea is the same each time; only the types change.

So a type can leave something open too. In a type, a **placeholder** is a name that is not a data type, like `X`, and stands for any type. In a declared type, it means that the definition works for every type in its place, and each use of the name chooses its own:

    identity : (X) → X
    identity = (a) ↦ a

    identity(true) : Bool              with Bool for X
    identity(rock) : Hand              with Hand for X
    identity(not) : (Bool) → Bool      with (Bool) → Bool for X

Within one use, `X` is the same type throughout, so `identity(true)` can't have type `Hand`.

The definition itself is checked without knowing which type `X` is. So it can't look at what it's given: only a placeholder pattern fits `X`, and any values must match some case, whatever types its placeholders stand for.

A data type can take placeholders too, all different, and is then filled with one type for each wherever it is used:

    Pair(X, Y) = pair(X, Y)
    List(X) = empty | cons(X, List(X))

So a type can now also be a placeholder, or a data type with its placeholders filled, like `Pair(Bool, Hand)`, `List(Nat)` or `List(List(Bool))`. A data type's constructors have the types its declaration gives them, for every type in place of its placeholders:

    pair : (X, Y) → Pair(X, Y)
    empty : List(X)
    cons : (X, List(X)) → List(X)

A list is empty, or an item followed by a list:

    cons(rock, cons(paper, empty)) : List(Hand)

Definitions on lists work whatever the items are:

    length : (List(X)) → Nat
    length = (empty) ↦ zero | (cons(a, r)) ↦ succ(length(r))

    map : ((X) → Y, List(X)) → List(Y)
    map = (f, empty) ↦ empty | (f, cons(a, r)) ↦ cons(f(a), map(f, r))

    length(cons(rock, cons(paper, empty)))             comes to   succ(succ(zero))
    map(answer, cons(rock, cons(paper, empty)))        comes to   cons(paper, cons(scissors, empty))
    map(even, cons(zero, cons(succ(zero), empty)))     comes to   cons(true, cons(false, empty))

`map` gives back the list of what `f` gives for each item. In the first use of `map`, `X` and `Y` are both `Hand`; in the second, `X` is `Nat` and `Y` is `Bool`.

With these, the game can be said whole. Given a list of rounds, how many did the first player win?

    more : (Bool, Nat) → Nat
    more = (true, total) ↦ succ(total) | (false, total) ↦ total

    wins : (List(Round)) → Nat
    wins = (empty) ↦ zero | (cons(r, others)) ↦ more(won(r), wins(others))

    wins(cons(round(paper, rock), cons(round(rock, paper), cons(round(scissors, paper), empty))))
        comes to   succ(succ(zero))

Every piece is here: types that list things, functions by cases, data that holds things, definitions that repeat, and types left open.

## 10. How far it reaches

Look back at what has been built: things that are only themselves, functions, data that holds things, definitions that repeat, checking, types left open. It looks like many parts. Here is the surprise: functions alone can do the work of nearly all of them. Take away constructors, data, definitions and types, keep only functions whose patterns are placeholders, and applying them, and every job can still be done. Seeing how is the clearest way to see why so small a language can say anything.

In this section we set checking aside, since part of the point is something that has no type. And we still use constructors and names, but only to see results: `rock` and `paper` could be anything.

**Choosing.** A `Bool` is used by cases: one body for `true`, another for `false`. Instead, a function can stand for a choice already made:

    (a, b) ↦ a        true: of two, the first
    (a, b) ↦ b        false: of two, the second

To use it, we apply it to the two things to choose between:

    ((a, b) ↦ a)(rock, paper)       becomes   rock
    ((a, b) ↦ b)(rock, paper)       becomes   paper

Both inputs are worked out before the choice is made. To choose between two questions and work out only the one chosen, each is kept for later in a function with no inputs, and only the chosen one is applied. Here the second would go on forever, but it is never worked out:

    ((a, b) ↦ a)(() ↦ rock, () ↦ loop(true))()
        becomes   (() ↦ rock)()
        becomes   rock

**Holding.** A pair can be a function that holds two things and waits for a choice:

    (f) ↦ f(rock, paper)          holds rock and paper

To take one out, we hand it a choice:

    ((f) ↦ f(rock, paper))((a, b) ↦ b)
        becomes   ((a, b) ↦ b)(rock, paper)
        becomes   paper

**Counting.** A number can be a function that does something that many times:

    (f, a) ↦ a              zero: f no times
    (f, a) ↦ f(a)           one: f once
    (f, a) ↦ f(f(a))        two: f twice

Two, applied to `answer`, starting from `rock`:

    ((f, a) ↦ f(f(a)))(answer, rock)        comes to   scissors

A number is what it does, so to ask anything of it, even whether it is zero, we apply it. Adding `m` and `n` is doing something `n` times, then `m` times more. Here one and one are added, and the result is applied to `answer` from `rock`:

    ((m, n) ↦ (f, a) ↦ m(f, n(f, a)))((f, a) ↦ f(a), (f, a) ↦ f(a))(answer, rock)
        comes to   scissors

It answers twice: one and one make two.

**Repeating.** Without definitions, a function has no name to use itself by. But it can be handed itself. `(a) ↦ a(a)` hands its input to itself, so the function it is given receives itself, and can run itself again. Here it receives itself as `me`, and doesn't use it:

    ((a) ↦ a(a))((me) ↦ rock)
        becomes   ((me) ↦ rock)((me) ↦ rock)
        becomes   rock

One that runs itself again every time goes on forever:

    ((a) ↦ a(a))((me) ↦ me(me))
        becomes   ((me) ↦ me(me))((me) ↦ me(me)), the same again, forever

And one can choose, each round, whether to run again. This one answers `rock` as many times as a number says, with no definition using its own name. To keep it short, it chooses with cases; a choice made of functions would do the same.

    ((a) ↦ a(a))((me) ↦ (zero) ↦ rock | (succ(m)) ↦ answer(me(me)(m)))(succ(succ(zero)))
        comes to   scissors

Handed itself as `me`, it gives back a function on numbers. At `zero` it stops, with `rock`. At one more than `m`, it hands itself to itself again, `me(me)`, works on `m`, and answers the result. Two layers, two answers: scissors.

**Working on anything.** And without types, `(a) ↦ a` gives back anything at all.

**Enough for anything.** Choosing, holding, counting and repeating, all by functions alone, are enough to compute anything that can be computed. In the 1930s, Alonzo Church described computing with functions like these, and Alan Turing with machines that read and write symbols on a tape. The two turned out to compute exactly the same things, and so has every other way of describing computing found since. So the language's power was all there in functions alone.

**Why the rest, then.** If functions alone are enough, what are the other pieces for? Two things go wrong.

One function plays many roles, and nothing tells them apart. `(f, a) ↦ a` is zero, and it is also "of two, the second", which is false. So a number given where a choice is expected doesn't get stuck. It carries on, and gives an answer, with no sign that anything went wrong:

    ((f, a) ↦ a)(rock, paper)       becomes   paper

And handing a function itself can't be checked. `(a) ↦ a(a)` has no type: `a` would need some type `A`, and also a type `(A) → B`, so `A` would contain itself, and no type does.

So the rest of the language is structure. Each role gets a form of its own, which says what it is and can be checked:

    choosing               constructors and cases
    holding                contents and patterns
    counting               data whose contents are of its own type
    repeating              definitions that use their own name
    working on anything    placeholders in types

The last two give back what checking took away. Once things are checked, a function can't be handed itself, so a definition can use its own name instead. And a defined name has one declared type, so placeholders in types let it work on anything again.

This is also what "anything" means here. The language can say anything that can be described as things and steps, and say it exactly. Whether a description fits the world, whether `Round` is the right way to think of a round or `beats` the right rule, is a different question, settled outside the language. What the language does is make each such choice explicit, so that it can be seen and argued about.

## 11. The two hardest ideas

Everything so far is the main part of the language. Two ideas remain, both about types, both harder, and nothing before depends on them.

### For every, written inside a type

A placeholder in a declared type means "for every type in its place". This can be written: `∀X. A` reads "for every `X`, `A`", and like a body, `A` reaches as far as it can. A declared type has a `∀` left unwritten in front for each of its placeholders, so `identity : (X) → X` means `identity : ∀X. (X) → X`. Two rules say what `∀` means:

    e : A, with B in place of X      when e : ∀X. A
    e : ∀X. A                        when e : A, and X is in the type of no placeholder e uses from around it

The first: what works for every type works for any one we choose. The second: an expression works for every `X` when it has type `A` without depending on what `X` is, that is, when nothing it uses from around it fixes `X`. In `(a) ↦ (b) ↦ a`, with `a : X`, the inner function `(b) ↦ a` has type `(Y) → X` for every `Y`, but not for every `X`: it uses `a`, whose type is `X`, fixed by the case around it.

Writing the `∀` matters when it is not in front. So far, a placeholder in a definition's type is chosen once, where the name is used, and a function given as an input has that one type throughout. So this has no type:

    onBoth : ((List(X)) → List(X), List(Bool), List(Hand)) → Pair(List(Bool), List(Hand))
    onBoth = (g, s, t) ↦ pair(g(s), g(t))

`g(s)` needs `X` to be `Bool`, and `g(t)` needs it to be `Hand`. With the `∀` written inside, the input itself must work for every type, and each use of it chooses:

    onBoth : (∀X. (List(X)) → List(X), List(Bool), List(Hand)) → Pair(List(Bool), List(Hand))
    onBoth = (g, s, t) ↦ pair(g(s), g(t))

    rest : (List(X)) → List(X)
    rest = (empty) ↦ empty | (cons(a, r)) ↦ r

    onBoth(rest, cons(true, cons(false, empty)), cons(rock, empty))
        comes to   pair(cons(false, empty), empty)

`rest` drops a list's first item, whatever the items are. A function written in place works too, when nothing it uses fixes `X`, as in `onBoth((l) ↦ l, …)`. In return, `onBoth` can be given only what works on every kind of list: not `(l) ↦ cons(true, l)`, which works on lists of `Bool`s only.

Contents can work for every type too:

    Edit = edit(∀X. (List(X)) → List(X))

    edit(rest) : Edit

Two limits keep checking always able to decide whether something has a type:

- A placeholder is filled only with a type that has no `∀` in it. `List(∀X. (List(X)) → List(X))` is not a type. To keep a list of such functions, contents say so: `List(Edit)`.
- A `∀` is never guessed. A placeholder's type has a `∀` only where a declared type gives it one, as the type of `onBoth` gives one to `g`.

One detail: which name a `∀` uses makes no difference, so `∀X. (X) → X` and `∀Y. (Y) → Y` are the same type. A type put in place of `X` doesn't go inside an inner `∀X`, which has taken the name for itself; and if it uses a name that an inner `∀` takes, that `∀` changes its name first, so the two aren't confused.

### Types that take types

`map` works for every `X` and `Y`, but only on lists. Another container needs a map of its own:

    Twin(X) = twin(X, X)

    mapTwin : ((X) → Y, Twin(X)) → Twin(Y)
    mapTwin = (f, twin(a, b)) ↦ twin(f(a), f(b))

To write something once for every container that has a map, the container must be left open. `List` takes a type, and a placeholder in a type can take types just as well:

    X(A, …)        a placeholder, filled with the types A, …

    negate : (((Bool) → Bool, F(Bool)) → F(Bool), F(Bool)) → F(Bool)
    negate = (m, s) ↦ m(not, s)

    negate(map, cons(true, cons(false, empty)))       comes to   cons(false, cons(true, empty))
    negate(mapTwin, twin(true, true))                 comes to   twin(false, false)

Given a way to apply a function inside some container `F`, and a container of `Bool`s, `negate` turns over every `Bool` in it. `F` is `List` the first time and `Twin` the second. Putting `List` in place of `F` makes `F(Bool)` into `List(Bool)`, and then `map`, with `Bool` for both `X` and `Y`, has the type `m` needs.

What a type **takes** is what it must be filled with. `List` takes one type and `Pair` takes two; `Bool`, `List(Nat)` and `(Bool) → Bool` take none. A placeholder takes what its uses show, the same throughout: `F` is used as `F(Bool)`, so it takes one type, which itself takes none. A placeholder is filled only with a type that takes the same, so `List` and `Twin` can fill `F`, and `Bool` and `Pair` can't.

A data type's placeholders can take types too:

    Counted(F, X) = counted(F(X), Nat)

    counted(cons(true, empty), succ(zero)) : Counted(List, Bool)

A `Counted` is a container of some kind, with how many items it holds. `Counted` takes two types, the first of which takes one. A type that takes types is written on its own only where a type that takes the same can go, as `List` is in `Counted(List, Bool)`. Everywhere else a type is written, as an input, an output, contents, under a `∀`, or as the type of an expression, it takes nothing. And just as a function is given all its inputs at once, a type is filled with all its types at once: `Pair(Bool)` is not a type.

## 12. The whole language on one page

These letters describe the language and are never written in it: `e` an expression, `v` a value, `x` a placeholder, `c` a constructor, `n` a defined name, `k` a case, `p` a pattern, `d` a constructor as declared, `T` the name of a data type, `X` a placeholder in a type, `A` and `B` types. `e, …` is any number of them, even none, and likewise for the other letters; `…` alone is any number of expressions; `e'` is what `e` becomes.

**What we write**: declarations and definitions, then one expression to carry out.

    T(X, …) = d | …        a data type, and its constructors; T alone if it takes no types
    d ::= c                a constructor holding nothing
          c(A, …)          a constructor holding contents of types A, …
    n : A                  declares n, of type A
    n = e                  defines n as e

**Expressions**

    e ::= x                a placeholder
          c                a constructor
          n                a defined name
          k | …            a function: one case or more
          e(e, …)          an application
    k ::= (p, …) ↦ e       a case: its patterns, and its body
    p ::= x                a placeholder: matches anything
          c                a constructor holding nothing: matches itself
          c(p, …)          data built by c, with contents matching p, …

**Values**

    v ::= k | …            a function
          c                a constructor
          c(v, …)          data

**Steps**

    n                  becomes   e, when n = e
    (k | …)(v, …)      becomes   the body of the first case that v, … match, with each value it gives
                                 in place of the uses of its placeholder that belong to that case
    e(…)               becomes   e'(…), when e becomes e'
    v(…, e, …)         becomes   v(…, e', …), when e becomes e', and everything before e is a value

    v matches x                  giving x the value v
    c matches c                  giving nothing
    c(v, …) matches c(p, …)      when v, … match p, …, giving what they give
    v, … match p, …              when there are as many v as p, each matching its own

Carrying out ends in a value, gets stuck, or goes on forever.

**Types**

    A ::= T(A, …)          a data type, filled; T alone if it takes no types
          (A, …) → B       a function type
          X(A, …)          a placeholder in a type, filled; X alone if it takes no types
          ∀X. A            for every X

    c : T(X, …)                  when T(X, …) lists c
    c : (A, …) → T(X, …)         when T(X, …) lists c(A, …)
    n : A                        when n is declared with A
    x : A                        when its pattern gives it A
    e(e', …) : B                 when e : (A, …) → B, and e', … : A, …
    (k | …) : (A, …) → B         when in every case the patterns fit A, …, and the body : B with the
                                 types they give; and any values of types A, … match some case
    e : A, with B in place of X  when e : ∀X. A
    e : ∀X. A                    when e : A, and X is in the type of no placeholder e uses from around it

    x fits A                     giving x : A
    c fits A                     when c : A
    c(p, …) fits B               when c : (A, …) → B, and p, … fit A, …, giving what they give

**Conditions**

- Each type is declared once, and each constructor is in one type only. Each defined name is declared once and defined once, and `n = e` is checked when `e` has the declared type of `n`.
- The placeholders of a case are all different, and every placeholder used belongs to the nearest case around it that has it in its patterns. A name that belongs to no case is a constructor or a defined name.
- A body, the `B` of a function type, and the `A` of `∀X. A` each reach as far as they can. Parentheses only group.
- A declared type has an unwritten `∀` in front for each placeholder no `∀` in it takes.
- A placeholder in a type is filled only with a type that has no `∀` in it, and that takes the same types it takes. A type that takes types is written alone only where such a type can go. A `∀` is never guessed: a placeholder's type has one only where a declared type gives it one.

**The guarantee**: a checked expression never gets stuck. It comes to a value of its type, or goes on forever.
