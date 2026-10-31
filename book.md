# The Book

This book builds a small language, one piece at a time. Each piece is added because of something the pieces before it cannot say, and each comes with examples that mean something. Most are about the game rock, paper, scissors.

The language has two parts. The **core** is for saying things: things, and functions that turn things into other things. **Effects** are for doing things: keeping a score that changes, giving up, trying every possibility, talking to the world outside.

On top of a language can sit **claims** about what is written in it: that a function is only ever given things it has a case for, say. Types are the simplest claims. A claim adds nothing to what can be said, only to what we know about it, so this book leaves claims out. It is about the language itself.

As we go, we keep a **map** of the language: a few lines that list every form it has. Each section adds to it. By the end, the map is the whole language on one page, and every way its pieces combine can be read off it.

## 1. Things

The simplest thing to say is a thing that is just itself. We bring things in by listing them:

    Hand = rock | paper | scissors

The `|` reads "or". This **declares** three **constructors**, `rock`, `paper` and `scissors`: names that stand only for themselves. `Hand` is a name for the three together, so that we can talk about them: a hand is rock, paper or scissors. That name is for reading. The language itself uses only the constructors.

Here is another declaration:

    Bool = false | true

Each constructor is declared once.

Anything we write that says something is an **expression**. A constructor is the simplest expression: it says itself, and there is nothing in it to work out.

So far, things only sit there. Nothing turns one into another.

## 2. Functions

A **function** turns things into other things. To say what it does, we say what it gives back for each thing it can be given. Each of these is a **case**:

    (rock) ↦ paper | (paper) ↦ scissors | (scissors) ↦ rock

`↦` reads "gives", and `|` again reads "or": rock gives paper, or paper gives scissors, or scissors gives rock. In each case, what is in parentheses is what the case is for, and what comes after `↦` is its **body**: what the case gives back. This function gives back the hand that beats the one it is given.

To use a function more than once, we give it a name:

    answer = (rock) ↦ paper | (paper) ↦ scissors | (scissors) ↦ rock

This **defines** the name `answer`: from now on, `answer` stands for this function. A name given this way is a **defined name**, and each is defined once.

To use a function, we **apply** it to an **input**, written after it in parentheses:

    answer(rock)

This is a question: what does `answer` give back for `rock`? To find out, we **carry it out**, in **steps**:

    answer(rock)
        becomes   ((rock) ↦ paper | (paper) ↦ scissors | (scissors) ↦ rock)(rock)
        becomes   paper

In the first step, the name becomes the function it stands for. In the second, the function is applied: its cases are tried in order, and the application becomes the body of the first case that is for `rock`. Nothing is left to work out in `paper`, so `paper` is the answer.

The parentheses around the function only keep it in one piece. Without them, the input `(rock)` would seem to belong to the last case.

From here on, "comes to" means "becomes, in one step or more". We also skip the step where a name becomes what it stands for, and keep writing the name in its place:

    answer(rock)      comes to   paper

One more function, which turns each `Bool` into the other:

    not = (false) ↦ true | (true) ↦ false

    not(true)         comes to   false

## 3. The map

We have now written four kinds of expression:

    rock                                 a constructor
    not                                  a defined name
    (false) ↦ true | (true) ↦ false      a function
    not(true)                            an application

To describe them all at once, and not just these examples, we use letters that stand for parts of the language. `e` stands for any expression, `c` for any constructor, and `n` for any defined name:

    e ::= c                  a constructor
          n                  a defined name
          (c) ↦ e | …        a function
          e(e)               an application

`::=` reads "is one of", and each line is one **form**. So this says: an expression is a constructor, or a defined name, or a function, or an application. In the form of a function, `| …` means "and more cases like the first, as many as needed": a function has one case or more.

This is the first piece of the map. Its letters are not expressions, constructors or names. Each marks a place where any expression, constructor or name can go.

The map shows something the examples don't. Look at the forms with an `e` inside them. A case's body is an `e`, so a body can be any expression, not just a constructor. An application is `e(e)`, so the function applied can be any expression, and so can its input, including another application:

    answer(answer(rock))

A form with `e` inside it can hold any form, itself included, so forms go inside forms as deep as we like. Four forms combine without end.

Parentheses can go around any expression to keep it in one piece. They only group: they are not a form of their own. A case's body reaches as far as it can, so a function that is written out and then applied goes in parentheses, as in section 2.

What we write is declarations and definitions, then one expression to carry out. Using `T` for a name given to constructors together:

    T = c | …                declares the constructors c, …, under the name T
    n = e                    defines n as e

`T` is for reading: no other form on the map uses it.

Every section from here on adds to the map.

## 4. Carrying out

In `answer(answer(rock))` there is an application inside an application. Which is carried out first?

The rule is: an input is worked out before it goes in. So first `answer(rock)`, then `answer` of what it gave:

    answer(answer(rock))
        comes to   answer(paper)
        comes to   scissors

The answer to the answer to rock: paper, then scissors.

An expression with nothing left to work out is a **value**. Using `v` for any value:

    v ::= c                  a constructor
          (c) ↦ e | …        a function

A function is a value even though its bodies are expressions: nothing happens inside a function until it is applied. Only then is a body carried out, and only the body of the case that is used.

Here is all of carrying out so far. "`e` becomes `e'`" means one step, and `e'` is what `e` becomes:

    n                    becomes   what n is defined as
    ((c) ↦ e | …)(v)     becomes   the body of the first case for v

Where a step happens: in an application, first in the function being applied, until it is a value; then in its input, until that is a value; then the application takes its own step. So at each point there is one next step at most, and carrying out never has a choice to make.

But writing a case for every input soon says the same thing more than once.

## 5. Placeholders

This function gives back the hand that a given hand beats, which is the answer to its answer:

    beaten = (rock) ↦ answer(answer(rock)) | (paper) ↦ answer(answer(paper)) | (scissors) ↦ answer(answer(scissors))

It says the same thing three times. To say it once, for whatever hand is given, a case can have a **placeholder** where it had a constructor: a new name, which stands for whatever the function is given.

    beaten = (a) ↦ answer(answer(a))

The case is for any hand, and its body uses `a` where the hand given goes. When the function is applied, the input is put in place of the placeholder:

    beaten(rock)
        comes to   answer(answer(rock))
        comes to   scissors

A placeholder is like the letters on the map, but written in the language. On the map, `e` marks a place for any expression; in a body, `a` marks a place for whatever the function is given. A placeholder means something only in the body of its own case, functions written inside that body included. Its name is new: not a constructor, not a defined name, and not a placeholder of a case it is inside. So a name in a case's parentheses is a constructor if it was declared as one, and a placeholder otherwise; and in a body, every name stands for one thing.

What a case is for is now a **pattern**, one of two things. Using `p` for any pattern and `x` for any placeholder:

    p ::= c                  a constructor: matches only itself
          x                  a placeholder: matches anything

A value **matches** a pattern when the pattern describes it. Cases are tried in order, and the first that matches is used, so a last case with a placeholder catches everything the cases before it leave. That is how a function says "otherwise":

    isRock = (rock) ↦ true | (a) ↦ false

    isRock(paper)     comes to   false

On the map, a placeholder is a new form of expression, and a case's pattern can now be any pattern, in a function and so in a value:

    e ::= x                  a placeholder                                 new
          c
          n
          (p) ↦ e | …        a function                                    (c) widened to (p)
          e(e)
    v ::= (p) ↦ e | …        a function                                    widened

And applying puts the input in place:

    ((p) ↦ e | …)(v)     becomes   the body of the first case that v matches,
                                   with v in place of its placeholder, if the pattern is one

## 6. Several inputs

Some questions are about two things at once: does one hand beat another? A function can take several inputs, separated by commas, and then each case has one pattern for each:

    beats = (rock, scissors) ↦ true | (scissors, paper) ↦ true | (paper, rock) ↦ true | (a, b) ↦ false

Rock beats scissors, scissors beat paper, paper beats rock; otherwise, the first hand doesn't win. Several values match a case when each matches its own pattern, in order:

    beats(paper, rock)      comes to   true
    beats(rock, paper)      comes to   false

In the second, only the last case matches. Without placeholders, `beats` would need nine cases, one for each pair of hands. The placeholders of one case are all different: in `(a, a) ↦ a`, we couldn't tell which input `a` means.

Another function of two inputs, which asks whether both are `true`:

    and = (true, b) ↦ b | (false, b) ↦ false

Inputs are worked out one at a time, from left to right, each to a value, before the function takes its step:

    and(not(false), beats(rock, paper))
        comes to   and(true, beats(rock, paper))
        comes to   and(true, false)
        comes to   false

A function can also take no inputs at all, and is then applied to none. Its body waits until then, so it is a question kept for later:

    later = () ↦ answer(rock)

    later()           comes to   paper

On the map, functions and applications now take any number of inputs. In `p, …`, the `…` means "any number of them, even none", and likewise in `e, …` and `v, …`:

    e ::= (p, …) ↦ e | …     a function                                    widened
          e(e, …)            an application                                widened
    v ::= (p, …) ↦ e | …     a function                                    widened

The cases of one function all have the same number of patterns: that is how many inputs it takes. Applying it puts each input in place:

    ((p, …) ↦ e | …)(v, …)     becomes   the body of the first case whose patterns v, … match,
                                         with each value in place of its placeholder

## 7. Getting stuck

So far we have only applied functions to what they were made for. What if a function is given something it has no case for?

    answer(true)
        becomes   ((rock) ↦ paper | (paper) ↦ scissors | (scissors) ↦ rock)(true)

No case matches `true`, so no step applies, yet something is left to work out. Carrying out is **stuck**. It gets stuck in the same way when what is applied is not a function, or a function is given the wrong number of inputs:

    rock(paper)         stuck: rock is not a function
    beats(rock)         stuck once beats becomes its function: it takes two inputs, and is given one

In each, a value is somewhere it doesn't fit, and there is no way on. That is what stuck means in general: something is left to work out, but no step applies.

A function needn't have a case for everything. `(rock) ↦ paper` is a function, and it gets stuck only if it is given something other than `rock`.

So carrying out can end in two ways: in a value, which is an answer, or stuck, which is not. Showing ahead of time that something will never get stuck is a claim about it, of the kind this book leaves out. Here, getting stuck is simply one of the ways carrying out can end.

## 8. Holding

So far a value is one thing: one hand, one `Bool`. But a round of the game is two hands, one for each player. A constructor can hold things, its **contents**:

    Round = round(first, second)

`round` holds two things. The names in its parentheses, `first` and `second`, are for reading, like `Round`: they say what each content is for, and stand for nothing. The language uses only how many things `round` holds.

A constructor that holds contents is applied to them, like a function:

    round(rock, paper)

But it takes no step: it holds them. `round(rock, paper)` is a value: a round in which the first player showed rock and the second paper. Values built from constructors are **data**. Contents are worked out before they go in, like any input:

    round(rock, answer(rock))      comes to   round(rock, paper)

Written on its own, without its contents, `round` is a value too, and works like a function: given two things, it makes a round. So wherever a function can go, it can go. Like a function, it must be given as many things as it holds: `round(rock)` is stuck. A constructor that holds nothing is not a function at all, as `rock(paper)` showed.

That a round holds two hands is what we mean by it. The language knows only that `round` holds two things, so `round(true, false)` is data too.

To use data, we take it apart with a pattern: a constructor, with patterns for its contents.

    won = (round(a, b)) ↦ beats(a, b)

    won(round(paper, rock))        comes to   true

The pattern `round(a, b)` matches any round, and gives `a` the first hand and `b` the second. The contents of a pattern are patterns, so a pattern can look more than one layer in:

    draw = (round(rock, rock)) ↦ true | (round(paper, paper)) ↦ true | (round(scissors, scissors)) ↦ true | (other) ↦ false

    draw(round(paper, rock))       comes to   false

`round(paper, rock)` doesn't match `round(paper, paper)`, since `rock` doesn't match `paper`; only the last case matches it.

On the map, a constructor can now be declared holding contents. Using `d` for a constructor as it is declared:

    T = d | …                declares constructors, under the name T         widened from T = c | …
    d ::= c                  a constructor holding nothing
          c(x, …)            a constructor holding contents, one or more

In `c(x, …)`, the names are for reading, as in `round(first, second)`: they mark the places of the contents, and stand for nothing.

There is no new form of expression: `round(rock, paper)` is already an application, `e(e, …)`. But there is a new value, and a new pattern:

    v ::= c(v, …)            data: c holding values, as many as it holds    new
    p ::= c(p, …)            data built by c, with contents matching p, …   new

`v` is inside its own new form, so data can hold data. `p` is inside its own new form, so patterns nest. And since a pattern can now hold placeholders deep inside it, applying puts in place of each placeholder what it matched:

    ((p, …) ↦ e | …)(v, …)     becomes   the body of the first case whose patterns v, … match,
                                         with what each placeholder matched in its place

## 9. Repeating

So far there are only so many hands, and only so many rounds made of them: nine. Some kinds of thing have no end. A constructor can hold data built by itself:

    Nat = zero | succ(previous)

A number is `zero`, or `succ` holding the number before it; `succ` reads "one more than". So `zero`, `succ(zero)`, `succ(succ(zero))` are zero, one, two, and so on, with no end. A list is built the same way:

    List = empty | cons(item, rest)

A list is `empty`, or `cons` holding an item and the rest of the list; `cons` reads "followed by". The rounds of a game are a list:

    cons(round(paper, rock), cons(round(rock, paper), empty))

A pattern sees only as many layers as it is written with. To work through a list of any length, a function must go once around for each layer. First, a small function that adds one to a total when a round was won:

    more = (true, total) ↦ succ(total) | (false, total) ↦ total

To go around once for each layer, a function uses itself. A definition can use any defined name, its own included, even one defined after it. `wins` counts the rounds the first player won:

    wins = (empty) ↦ zero | (cons(this, rest)) ↦ more(won(this), wins(rest))

In no rounds, none. In this round followed by the rest: as many as in the rest, and one more if this round was won.

    wins(cons(round(paper, rock), cons(round(rock, paper), empty)))
        comes to   more(won(round(paper, rock)), wins(cons(round(rock, paper), empty)))
        comes to   more(true, wins(cons(round(rock, paper), empty)))
        comes to   more(true, more(won(round(rock, paper)), wins(empty)))
        comes to   more(true, more(false, wins(empty)))
        comes to   more(true, more(false, zero))
        comes to   more(true, zero)
        comes to   succ(zero)

Each time around takes one layer off the list, until it is `empty` and the first case ends it. One round was won.

Adding works the same way. Zero and `b` make `b`; one more than `a`, and `b`, make one more than `a` and `b`:

    add = (zero, b) ↦ b | (succ(a), b) ↦ succ(add(a, b))

    add(succ(zero), succ(zero))      comes to   succ(succ(zero))

Nothing makes a definition use itself on something smaller, so repeating need not stop:

    loop = (a) ↦ loop(a)

    loop(true)      comes to   loop(true), and so on, forever

So carrying out has a third way to end, which is not to end at all: it can go on forever.

Repeating added nothing to the map. It is two uses of forms already there: data inside data built by the same constructor, and a name inside its own definition. The map already allows both. The `v`s in `c(v, …)` can be any values, data built by `c` included, and the `e` in `n = e` can be any expression, `n` included.

## 10. Functions as values

A function is a value, like a hand. So whatever can be done with a value can be done with a function: it can be an input, be given back, or be held in data.

A function given a function:

    twice = (f, a) ↦ f(f(a))

    twice(answer, rock)       comes to   scissors

`twice` takes a function and a hand, and applies the function twice. In the body, the placeholder `f` stands for a function, so it can be applied. As with any input, `answer` is worked out before it goes in: what goes in place of `f` is the function `answer` stands for.

A function that gives back a function:

    against = (a) ↦ (b) ↦ beats(b, a)

    against(rock)             comes to   (b) ↦ beats(b, rock)
    against(rock)(paper)      comes to   true

`against(rock)` is a test: does this hand beat rock? Since a body reaches as far as it can, the body of the outer function is the whole inner function. And `rock` went in place of `a` inside the inner function: an input goes wherever its placeholder is used, even where nothing is carried out yet.

Data holding a function. A player plays by a rule: given the hand the other player showed last, it gives the hand to show next.

    Player = player(rule)

    next = (player(f), a) ↦ f(a)

    next(player(answer), rock)      comes to   paper

This player always shows what would have beaten the last hand.

A function given a function and a list:

    each = (f, empty) ↦ empty | (f, cons(a, rest)) ↦ cons(f(a), each(f, rest))

    each(answer, cons(rock, cons(paper, empty)))      comes to   cons(paper, cons(scissors, empty))

`each` gives back the list of what `f` gives for each item. Nothing says what a list holds, so the same `each` works on a list of anything: `each(not, cons(true, empty))` comes to `cons(false, empty)`.

A pattern can take data apart, but nothing takes a function apart: no pattern looks inside one. A function given a function can apply it, and nothing else.

Again the map needed nothing new. Every combination here was already on it. An input can be any expression, and so can a body, so either can be a function; the `v`s in `c(v, …)` can be functions; and the applied part of `e(e, …)` can itself be an application, as in `against(rock)(paper)`. Every value is data or a function, counting a constructor written without its contents as a function. So every place a value can go holds one or the other:

    isRock                     takes data, gives data
    against                    takes data, gives a function
    twice                      takes a function, gives data
    (f) ↦ (a) ↦ f(f(a))        takes a function, gives a function
    round(rock, paper)         data holding data
    player(answer)             data holding a function

### The core

That is the whole core. Every value is data or a function, and each is made one way and used one way:

|          | made by                           | used by                          |
|----------|-----------------------------------|----------------------------------|
| data     | a constructor, holding values     | a pattern, which takes it apart  |
| function | cases, each a pattern and a body  | applying it to inputs            |

Two kinds of name refer to values: a placeholder stands for what a case was given, and a defined name stands for its definition, which may use the name itself.

Everything in the core says something, and nothing changes: an expression and what it comes to say the same thing, the second more directly. The rest of the book is about doing.

## 11. State, written out

As they play, the players keep score: after each round that the first player wins, their score goes up by one. A score changes, and in the core nothing changes. But a change can be said with values: the score before, and the score after.

    score = (this, total) ↦ more(won(this), total)

Given a round and the score before it, `score` gives back the score after it. Two rounds, the first won and the second lost, starting from zero:

    score(round(rock, paper), score(round(paper, rock), zero))
        comes to   score(round(rock, paper), succ(zero))
        comes to   succ(zero)

The score is a **memory**: something kept, and changed as we go. Here it is written out as a value, handed from one step to the next. The inner `score` is an input, so it is carried out first, and the first round is counted first: the order of the rounds is the order in which the memory is handed on.

This works, but it has a cost. This function asks whether the first player won both of two rounds:

    both = (first, second) ↦ and(won(first), won(second))

To keep the score as well, playing a round must give back two things, whether it was won and the new score. A pair holds two things:

    Pair = pair(first, second)

    play = (this, total) ↦ pair(won(this), more(won(this), total))

And `both` must be rewritten to take the score, hand it through each round in turn, and give it back with its answer:

    both = (first, second, total) ↦ ((pair(a, m)) ↦ ((pair(b, t)) ↦ pair(and(a, b), t))(play(second, m)))(play(first, total))

This reads: play the first round with the score `total`, and call what it gives back `a` and `m`; play the second with `m`, and call what that gives back `b` and `t`; give back `and(a, b)`, with the score `t`.

`both` doesn't care about the score at all: it only hands it along. And every function that uses `both` must now do the same, and every function that uses those. The memory goes in and out of every function between the place it starts and the places that use it, and each of their definitions changes. One small idea, a score, touches everything on the way.

What we want is to mention the score only where it is used.

## 12. Requests and handlers

Take a simpler case of the same problem first. To know whether a hand wins, a function needs the hand the opponent plays. Written out, that hand would be one more input, handed through every function on the way, just like the score. Instead, the function can ask for it:

    effect Opponent = opponent()

This declares a **request**, `opponent`, which takes no inputs. Requests declared together, under one name, are an **effect**, here `Opponent`. As with constructors, the name is for reading: the language uses only the requests, and how many inputs each takes. A request is written like an application, always with as many inputs as it is declared with:

    beatsOpponent = (a) ↦ beats(a, opponent())

`opponent()` asks for the opponent's hand, and `beatsOpponent` says whether `a` beats it.

Carrying out `beatsOpponent(rock)` goes as far as this:

    beatsOpponent(rock)
        comes to   beats(rock, opponent())

The next thing to work out is the input `opponent()`, and no rule carries it out. A request is not a value, and takes no step of its own: it waits to be answered. It is not stuck, though: it is waiting for an answer from outside the expression.

A **handler** answers it. It goes around the expression that asks:

    handle beatsOpponent(rock) with
          (outcome) ↦ outcome
        | (opponent(), k) ↦ k(scissors)

A handler has cases, like a function. The first is for the value the expression comes to in the end: here it gives that value back as it is. The second is for the request `opponent()`, and its body answers `scissors`. In a handler's case, a request's name stands for that request, as a constructor's name does in a pattern. To see how the answer is given, we need to know what `k` is.

When the request is made, the expression around it is waiting for an answer: `beats(rock, opponent())` is waiting for a hand, to put where `opponent()` is. That waiting expression is **the rest** of the computation, and it can be written as a function. Given an answer, it puts it where the request was, and goes on, with the handler still around it:

    (y) ↦ handle beats(rock, y) with …

Here, and from now on, `with …` stands for the same handler's cases, written out again. The request's case receives the rest as its placeholder `k`. So when `opponent()` is reached, the handler takes a step: it becomes the body of its case for the request, with the rest in place of `k`. Applying the rest to `scissors` goes on with `scissors` where the request was:

    handle beatsOpponent(rock) with …
        comes to   handle beats(rock, opponent()) with …
        becomes    ((y) ↦ handle beats(rock, y) with …)(scissors)
        becomes    handle beats(rock, scissors) with …
        comes to   handle true with …
        becomes    true

In the last step, the expression has come to a value, `true`, so the handler becomes the body of its first case, which gives `true` back.

`beatsOpponent` never says where the opponent's hand comes from: the handler does. A handler that answered `paper` would make the same `beatsOpponent(rock)` come to `false`, without changing `beatsOpponent` at all.

On the map, using `F` for the name of an effect, `r` for a request, and `h` for a handler's case:

    effect F = r(x, …) | …           declares requests, under the name F              new

    e ::= r(e, …)                    a request                                         new
          handle e with h | …        a handler around e                                new

    h ::= (p) ↦ e                    for the value e comes to
          (r(p, …), x) ↦ e           for a request: patterns for its inputs, and a placeholder for the rest

In a declaration, as in a constructor's, the names in `r(x, …)` are for reading. In a handler's case, `x` is a placeholder.

The steps:

    handle v with h | …     becomes   the body of the first case for values that v matches,
                                      with what its placeholder matched in its place
    handle e with h | …     becomes   the body of the first case for r whose patterns v, … match,
                                      with what they matched in place, and the rest in place of x,
                                      when the next thing to work out in e is a request r(v, …),
                                      and no handler in e around that request has a case for r

Otherwise, the expression inside a handler takes its steps as usual. A request that a handler has no case for passes it by, to a handler further out.

Requests and handlers are made one way and used one way, like data and functions:

|        | made by   | used by                      |
|--------|-----------|------------------------------|
| effect | a request | a handler, which answers it  |

## 13. Four ways to go on

The handler above went on once, with one answer. But it holds the rest as a value, `k`, and can do with it whatever a function can do with a function.

It can never go on:

    handle beatsOpponent(rock) with
          (outcome) ↦ outcome
        | (opponent(), k) ↦ false

        comes to   false

The case never applies `k`, so the rest never happens: the handler's answer stands in for the whole computation. This is how errors work. Something asks for what can't be given, and the handler decides what happens instead.

It can go on several times. This handler asks, for each hand the opponent could play, whether `rock` wins, and counts the wins:

    count = (true) ↦ succ(zero) | (false) ↦ zero

    handle beatsOpponent(rock) with
          (outcome) ↦ count(outcome)
        | (opponent(), k) ↦ add(k(rock), add(k(paper), k(scissors)))

        comes to   succ(zero)

Each `k(…)` goes on from the request with a different hand, and comes to a value through the handler's first case:

    k(rock)          comes to   count(false), which comes to zero
    k(paper)         comes to   count(false), which comes to zero
    k(scissors)      comes to   count(true), which comes to succ(zero)

and `add(zero, add(zero, succ(zero)))` comes to `succ(zero)`. Against the three hands, rock wins once. This is search: one question, every answer tried.

It can also keep `k` in data, and go on later (not shown here). That is how a generator works, giving back items one at a time, each when it is asked for, and how two computations can take turns.

So a handler receives a request and the rest, and chooses how to go on: once, never, several times, or later. The same two forms, request and handler, say all four.

## 14. State, said once

Now the score:

    Unit = unit
    effect Score = get() | put(new)

`get()` asks for the score, and `put(new)` sets it to `new`. `put` has nothing to give back, so it is answered with `unit`, which says nothing.

To do one thing and then another:

    then = (u, a) ↦ a

Since inputs are worked out from left to right, `then(e, e')` works out `e` first, then `e'`, and gives back what `e'` gave. In the core, the order in which inputs were worked out never changed what they came to. With requests it does: asking for the score before setting it, or after, gives different answers. Order has become part of what is said.

Recording a round:

    record = (true) ↦ then(put(succ(get())), true) | (false) ↦ false

Given that a round was won, `record` adds one to the score and gives back `true`; given that it was lost, it changes nothing. And `both`, rewritten once more:

    both = (first, second) ↦ and(record(won(first)), record(won(second)))

Compare it with the first `both`, `(first, second) ↦ and(won(first), won(second))`. Only `record`, the one place that changes the score, mentions it. No score is handed in or out, and a function that uses `both` doesn't change at all.

A handler holds the memory:

    keep = (task, start) ↦ (handle task() with
                                (outcome) ↦ ((m) ↦ pair(outcome, m))
                              | (get(), k) ↦ ((m) ↦ k(m)(m))
                              | (put(new), k) ↦ ((m) ↦ k(unit)(new)))(start)

`keep` takes a computation kept for later, `task`, and a starting score, `start`. Its handler doesn't give back an answer directly: it gives back a function waiting for the score so far, `m`. The `(start)` at the end gives it the starting score; the parentheses before it keep the handler in one piece, so it can be applied. Its cases read:

- when the computation comes to a value: given the score `m`, give back that value together with `m`;
- when asked for the score: given `m`, answer `m`, and go on with the score still `m`;
- when told to set the score to `new`: given `m`, answer `unit`, and go on with the score `new` instead.

The rest, `k`, has the handler around it, so it too gives back a function waiting for a score. That is why what `k(m)` gives back is given a score in turn: `k(m)(m)`.

Two rounds, the first won and the second lost, starting from zero. Below, `k` is the rest after the first request, `get()`, and `k'` the rest after the second, `put(succ(zero))`:

    keep(() ↦ both(round(paper, rock), round(rock, paper)), zero)
        comes to   ((m) ↦ k(m)(m))(zero)                    the first request is get()
        becomes    k(zero)(zero)
        comes to   ((m) ↦ k'(unit)(succ(zero)))(zero)        the next request is put(succ(zero))
        becomes    k'(unit)(succ(zero))
        comes to   ((m) ↦ pair(false, m))(succ(zero))        the rest comes to false
        becomes    pair(false, succ(zero))

Not both rounds were won, and the score is one.

The memory is handed along exactly as in section 11, in and out of every step. But now that is written once, in the handler, instead of in every function on the way.

## 15. Input and output

A request that no handler in the program answers goes outside it. Showing something on a screen, or reading what someone types, is a request answered by whatever runs the program:

    effect Console = show(what) | read()

    game = () ↦ then(show(rock), beats(rock, read()))

`game` shows `rock`, reads the other player's hand, and says whether rock won. Carried out, it stops at `show(rock)` and waits; whatever runs it shows rock on the screen and answers `unit`. At `read()` it waits again, for a hand. Inside the language, a handler can stand in for the outside, which is how such a program is tested:

    handle game() with
          (outcome) ↦ outcome
        | (show(a), k) ↦ k(unit)
        | (read(), k) ↦ k(scissors)

        comes to   true

Requests of different effects can be made together. A task that shows rock while the score is kept:

    keep(() ↦ then(show(rock), both(round(paper, rock), round(rock, paper))), zero)

`keep`'s handler has no case for `show`, so `show(rock)` passes it by and goes outside: whatever runs the program shows rock and answers `unit`. Then the task goes on as in section 14, and the whole comes to `pair(false, succ(zero))`.

And a player can be a person. This player's rule doesn't look at the last hand; it reads the next one from the keyboard:

    next(player((a) ↦ read()), rock)

It comes to `read()`, and waits there for the person to choose.

## 16. The whole language

These letters describe the language: `e` an expression, `v` a value, `x` a placeholder, `c` a constructor, `n` a defined name, `p` a pattern, `d` a constructor as declared, `T` a name for constructors declared together, `F` a name for requests declared together, `r` a request, `h` a handler's case. In a declaration, the `x`s are names for reading, which stand for nothing. `e, …` means any number of expressions, even none, and likewise for the other letters. `| …` after a case means more cases like it, as many as needed. Parentheses only group, and a body reaches as far as it can.

**What we write**: declarations and definitions, then one expression to carry out.

    T = d | …                    constructors, under a name for reading
    d ::= c                      a constructor holding nothing
          c(x, …)                a constructor holding contents, one or more
    effect F = r(x, …) | …       requests, under a name for reading
    n = e                        a definition

**Names**: each constructor and each request is declared once, and each defined name is defined once. A definition can use any of them, even one declared or defined after it. In a pattern, a name is a constructor if it was declared as one, and otherwise a placeholder. A placeholder's name is new: not a constructor, a request or a defined name, and not a placeholder of a case it is inside; and the placeholders of one case are all different. So in a body, every name stands for one thing: a placeholder of a case it is in, a constructor, a request, or a defined name. The cases of one function all have the same number of patterns, and a request is written with as many inputs as it is declared with.

**Expressions**

    e ::= x                          a placeholder
          c                          a constructor
          n                          a defined name
          (p, …) ↦ e | …             a function: its cases, tried in order
          e(e, …)                    an application
          r(e, …)                    a request
          handle e with h | …        a handler

    p ::= x                          matches anything
          c                          matches only itself
          c(p, …)                    matches data built by c, with contents matching p, …

    h ::= (p) ↦ e                    for the value
          (r(p, …), x) ↦ e           for a request, and the rest

**Values**

    v ::= c                          a constructor
          c(v, …)                    data: c holding values, as many as it holds
          (p, …) ↦ e | …             a function

**Steps**

    n                          becomes   what n is defined as
    ((p, …) ↦ e | …)(v, …)     becomes   the body of the first case whose patterns v, … match,
                                         with what each placeholder matched in its place
    handle v with h | …        becomes   the body of the first case for values that v matches,
                                         with what its placeholder matched in its place
    handle e with h | …        becomes   the body of the first case for r whose patterns v, … match,
                                         with what they matched in place, and the rest in place of x,
                                         when the next thing to work out in e is a request r(v, …),
                                         and no handler in e around that request has a case for r

The rest is `e` with a new placeholder `y` where the request was, inside the same handler, as a function of `y`: `(y) ↦ handle … with h | …`. Steps happen in an application, first in what is applied, then in its inputs from left to right; in a request, in its inputs from left to right; in a handler, in the expression inside it. Nothing happens inside a function until it is applied.

**How carrying out ends**: in a value; or waiting, when the next thing to work out is a request that no handler around it has a case for, which only the outside can answer; or stuck, when something is left to work out, no step applies, and it is not waiting; or never, going on forever.

**The shape**: every value is data or a function, and every effect is requests and a handler. Each is made one way and used one way:

|          | made by                           | used by                          |
|----------|-----------------------------------|----------------------------------|
| data     | a constructor, holding values     | a pattern, which takes it apart  |
| function | cases, each a pattern and a body  | applying it to inputs            |
| effect   | a request                         | a handler, which answers it      |

**Reading another language through the map.** Many languages can be read as this one, with some of its forms chosen, some hidden, and their own way of writing them. Python, for example:

| Python                               | on the map                                         |
|--------------------------------------|----------------------------------------------------|
| an `Enum` class                      | constructors, under a name                         |
| `True`, `False`, an `Enum`'s members | constructors                                       |
| `def`, `lambda`                      | functions                                          |
| `if`, `match`                        | cases                                              |
| tuples, `dataclass`                  | constructors holding contents                      |
| lists                                | `empty` and `cons`                                 |
| `for`, `while`                       | a definition that uses itself, with a memory       |
| `x = x + 1`                          | a memory: `put(succ(get()))`, its handler hidden   |
| `raise`, `try`                       | a request whose handler never goes on              |
| `yield`                              | a request whose handler goes on later              |
| `print`, `input`                     | requests the outside answers                       |
| `TypeError`                          | getting stuck, turned into a request that a handler can answer |
| type hints                           | claims, checked by separate tools: on top of the language, not part of it |

Five forms of expression for saying, two more for doing, and a few rules for each. Everything else is a combination of them.
