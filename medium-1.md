# The medium

This is a small language for describing computations. We will build it around one game, rock, paper, scissors: choosing hands, playing rounds, keeping score, and asking a person to play.

Each idea begins with a small example in the core notation. When a longer spelling would get in the way, we introduce a readable abbreviation and show exactly what it means. We use that spelling from then on, returning to the expanded form when it helps explain an idea.

Short shapes collect the possibilities, with any restrictions beside them. Those shapes describe the expanded core. The last section collects both the core and the abbreviations, so we can always get from what we write to the few forms underneath it.

## 1. Things

First, we need the game's three hands:

    Hand = rock | paper | scissors

This reads: a hand is rock, paper or scissors. `Hand` is a **type**: it tells us what kind of thing we mean. The names on the right are **constructors**. For now, each names a different thing.

A round also has an outcome:

    Outcome = win | lose | draw

A hand is one of three choices. An outcome is one of three results. The names change; the shape stays the same:

    T = c | …

`T` stands for a type's name, and `c` for a constructor. `| …` means we can list more constructors. These letters describe the shape; they aren't names in the game.

Each type is declared once, and each constructor belongs to one declaration. Types start with a capital letter; constructors don't.

A constructor can be written on its own, like `rock` or `win`. That is an **expression**, and it is already a **value**: nothing needs working out. With `e` for an expression and `v` for a value:

    e ::= c
    v ::= c

`::=` reads “has one of these shapes”. At this point, an expression can only be a constructor.

## 2. Functions

Paper beats rock, scissors beat paper, and rock beats scissors. We can put those three answers into one rule:

    better : (Hand) → Hand
    better = (rock) ↦ paper
           | (paper) ↦ scissors
           | (scissors) ↦ rock

`better` is a **function** with three **cases**, separated by `|`. Each case is what it is given, followed by what it gives back. `↦` reads “gives”.

The line above the definition gives its **function type**. It reads “better goes from a hand to a hand”. `→` describes the kinds of inputs and results; `↦` gives the answer in a particular case.

### Writing cases as a block

We will usually lay out a named function like this:

    better : (Hand) → Hand
    def better:
        case (rock): paper
        case (paper): scissors
        case (scissors): rock

This is the same definition. `def better:` replaces `better =`; each `case (pattern): body` replaces `(pattern) ↦ body`. Indentation keeps the cases together, and their order stays the same. The two displays are two spellings of one definition.

The type line still describes the function. It doesn't change its behaviour. We will keep types separate from definitions so that we can read either on its own.

The input and result can have different types. This function gives the outcome of playing a hand against rock:

    againstRock : (Hand) → Outcome
    def againstRock:
        case (rock): draw
        case (paper): win
        case (scissors): lose

`better` takes a hand and gives a hand. `againstRock` takes a hand and gives an outcome.

To use a function, put an input after its name. We write `>>` for one step:

    better(rock)
        >> paper

This is **applying** the function. We find the first case that matches the input, and replace the application with that case's answer. A constructor matches itself: rock matches rock.

`better(rock)` and `paper` are both expressions. Only `paper` is already a value: `better(rock)` still needs a step.

The input can itself be something to work out:

    better(better(rock))
        >> better(paper)
        >> scissors

The inner application goes first. Once it gives us `paper`, the outer one can use it. This is how we combine functions: the result of one becomes the input of another. The type helps us see why this combination works: `better` gives back the same kind of thing it takes.

The two functions can combine too:

    againstRock(better(rock))
        >> againstRock(paper)
        >> win

`better` gives back a hand, which is what `againstRock` takes. The result of the whole expression is an outcome.

We have added functions and application. `n` stands for a defined function's name, and `A` and `B` for declared types:

    n : (A) → B               a function's type
    n = (c) ↦ c | …           its cases

    e ::= c                   a constructor
        | n(e)                an application

`A` and `B` may be the same or different types. Each `c` is a separate place: `(rock) ↦ paper` uses different constructors. A function has one or more cases, each taking one input. Each function name has one definition and is distinct from constructor names.

The `e` inside `n(e)` can be either form again: a constructor or another application. This allows nesting as deep as we need. For now, a function name appears only in an application, and each case gives a constructor directly. Values are still just constructors.

The nested steps have a shape too. If the selected cases give `n₂(c₁) >> c₂` and `n₁(c₂) >> c₃`, then:

    n₁(n₂(c₁)) >> n₁(c₂) >> c₃

The subscripts distinguish places, not necessarily different functions or constructors. `>>` describes a step; it isn't part of a program.

### Naming an input and working out an answer

Suppose we want the hand that rock beats. That is scissors: the result of using `better` twice. The same works for every hand, so let's give it a name:

    worse : (Hand) → Hand
    worse = (a) ↦ better(better(a))

`a` is a **variable**: it wasn't declared as a constructor, so in a pattern it matches any value and names that value inside its case. Given `paper`, we put `paper` wherever this `a` occurs:

    worse(paper)
        >> better(better(paper))
        >> better(scissors)
        >> rock

The part before `↦` is the **pattern**: it matches the input. The expression after `↦` is the **body**. The body waits until we apply the function, and only the case we use is worked out.

A function with one case has a shorter block spelling:

    def worse(a):
        better(better(a))

`def name(pattern): body` means `name = (pattern) ↦ body`. The body can sit on the same line when it is short, as in `def worse(a): better(better(a))`. We will normally use this spelling for a single case and a `case` block for several.

Naming an input doesn't make us use it. This function accepts a hand and always gives rock:

    alwaysRock : (Hand) → Hand
    def alwaysRock(a): rock

    alwaysRock(paper)
        >> rock

We can also give the input back unchanged. This function changes rock to the hand that beats it, and keeps any other hand:

    adjust : (Hand) → Hand
    def adjust:
        case (rock): better(rock)
        case (a): a

    adjust(rock)
        >> better(rock)
        >> paper
    adjust(scissors)
        >> scissors

The first case has a constructor as its pattern and an application as its body. In the second, `a` names the input and the body gives it back. Both cases could match rock; trying them in order makes the second mean “otherwise”.

These examples cover the available kinds of case. Here they are in the block spelling:

| Pattern | Constructor as body | Variable as body | Application as body |
|---|---|---|---|
| A constructor | `case (rock): paper` | No variable is named by this pattern | `case (rock): better(rock)` |
| A variable | `case (a): rock` | `case (a): a` | `case (a): better(better(a))` |

An application's input can now be a constructor, a variable, or another application. We have seen all three: `better(rock)`, `better(a)` inside `worse`, and `better(better(a))`.

A case can now name its input and work out its answer. The definition, pattern and expression shapes change:

    n = (p) ↦ e | …

    p ::= c | x
    e ::= c
        | x                   if this case's pattern is x
        | n(e)

`p` stands for a pattern and `x` for a variable. Every `e` place, whether a body or an input, can use any of the three forms. A variable must come from this case's pattern, even inside nested applications. A constructor pattern names no variable; outside a case there is none to use.

For now, a body can use function names already defined. A variable's name must differ from constructor names and defined function names. The rule for application is the same: work out the input, then use the first matching case. Its body may now need further steps, as `worse` and `adjust` show.

Matching can fail. `better(win)` gets **stuck**: `win` is a value, but none of the cases accepts it. The type line helps us see the mismatch. Writing a type doesn't change how the cases work.

### Several inputs

To find who wins a round, we need two hands:

    result : (Hand, Hand) → Outcome
    def result:
        case (rock, scissors): win
        case (scissors, paper): win
        case (paper, rock): win
        case (rock, rock): draw
        case (paper, paper): draw
        case (scissors, scissors): draw
        case (a, b): lose

Each input matches its own pattern. The first three cases are wins for the first player; the next three are draws. The last case accepts anything left over, so it means “otherwise”.

    result(paper, rock)       >>   win
    result(rock, paper)       >>   lose

Several inputs are worked out from left to right:

    result(better(rock), better(paper))
        >> result(paper, better(paper))
        >> result(paper, scissors)
        >> lose

A function can take any number of inputs, even none:

    later : () → Hand
    def later(): better(rock)

    later()
        >> better(rock)
        >> paper

With no inputs, we still have a body waiting to be used. Its application is what starts it.

Each pattern place can independently hold a constructor or a variable. A practice rule can use all four combinations for two inputs:

    practice : (Hand, Hand) → Hand
    def practice:
        case (rock, rock): paper
        case (rock, b): b
        case (a, rock): a
        case (a, b): better(a)

With two rocks, it chooses paper. With just one rock, it keeps the other hand. Otherwise it chooses the hand that beats the first. The cases are still tried in order.

Only the number of inputs changes:

    n : (A, …) → B
    n = (p, …) ↦ e | …

    e ::= c
        | x                   if named by this case's patterns
        | n(e, …)

A comma followed by `…` means any number of these places, including none. Each input is an expression, so a call can mix constructors, available variables and other applications. Every case of one function has the same number of patterns, and the function takes that many inputs.

The variables in one case must have different names. In `(a, a)`, the same name would be given two inputs. To match two rocks, write `(rock, rock)`, as above.

## 3. Data and patterns

A round needs two hands. We can keep them together:

    Round = round(Hand, Hand)

A `Round` is made with `round`, holding two hands:

    round(paper, rock)

The first player chose paper, and the second chose rock. This is already a value. The constructor holds the two hands together. Values built with constructors are **data**.

To use those hands, write the same shape as a pattern:

    judge : (Round) → Outcome
    def judge(round(a, b)): result(a, b)

    judge(round(paper, rock))
        >> result(paper, rock)
        >> win

`round(a, b)` takes the round apart: `a` names its first hand, and `b` its second. A body can put data together again:

    flip : (Round) → Round
    def flip(round(a, b)): round(b, a)

    flip(round(paper, rock))
        >> round(rock, paper)

A constructor has one place for each thing it holds. A constructor that holds nothing, like `rock`, is written without parentheses.

Each content can itself be something to work out. We work out the contents from left to right before the data is a value:

    round(better(rock), rock)
        >> round(paper, rock)

Each place in a pattern can be fixed or left open too. `round(rock, a)` fixes the first hand and leaves the second open. It matches `round(rock, paper)`, with `paper` as `a`, but doesn't match `round(paper, rock)`. `round(a, b)` leaves both hands open.

Data can hold data. A record can say that nothing was played, hold a round, or hold a round with its outcome:

    Record = unplayed | played(Round) | scored(Round, Outcome)

    unplayed
    played(round(rock, paper))
    scored(round(better(rock), rock), win)
        >> scored(round(paper, rock), win)

The constructors hold zero, one and two values. The pattern `scored(round(paper, a), outcome)` looks inside both layers of the last value: `a` names rock and `outcome` names win. A variable can also name a whole layer: `scored(thisRound, outcome)` keeps the round together.

Constructors can now hold contents. That changes declarations, expressions, values and patterns:

    T = c(A, …) | …

    e ::= c(e, …)
        | x                   if named by this case's patterns
        | n(e, …)
    v ::= c(v, …)
    p ::= c(p, …) | x

`A` and `B` still stand for declared types. For now, a declaration uses types declared before it. A constructor takes as many contents as its declaration lists; with none, write its name alone.

An `e` place accepts any expression; a `v` place needs a value. A `p` place accepts a constructor pattern or a variable, so patterns can nest as data does. All variables in one case's patterns must have different names; each is available throughout its body.

A type describes which contents fit. `round(win, lose)` can still be written: it is data, but it isn't a `Round`.

## 4. Functions as values

We used `better` twice to define `worse`. We can do the same with any rule from hands to hands:

    twice : ((Hand) → Hand, Hand) → Hand
    def twice(f, a): f(f(a))

Read its type from the outside: two inputs, then one result. The first input is a function from hands to hands. The second is a hand. The result is a hand.

    twice(better, rock)
        >> better(better(rock))
        >> better(paper)
        >> scissors

`better` on its own is the function. `better(rock)` applies it. A function is a value, so we can pass it around before using it.

We can also write a function directly, without giving it a name:

    (a) ↦ rock

This rule accepts an input and always gives back rock. An unnamed function can also have several cases, such as `(rock) ↦ paper | (a) ↦ a`. Named or unnamed, the same pattern and body rules apply.

We can pass the rule directly:

    twice((a) ↦ rock, paper)
        >> ((a) ↦ rock)(((a) ↦ rock)(paper))
        >> ((a) ↦ rock)(rock)
        >> rock

Parentheses keep each function together. A body extends as far to the right as it can, so we group a function when something after it belongs outside its body.

### Writing an unnamed function

The block spelling for an unnamed function uses `fn`:

    fn(a): rock

This means `(a) ↦ rock`. For several cases, use the same layout as a named function:

    fn:
        case (rock): paper
        case (a): a

That means `(rock) ↦ paper | (a) ↦ a`. `def` gives a global name to a function; `fn` writes the function value itself. Its body can be a single expression after the colon or an indented body on the following lines.

The earlier application can now be written:

    twice(fn(a): rock, paper)
        >> … >> rock

The comma ends the function's body here: `paper` is the second input to `twice`. As before, parentheses can group a function when we want to apply it directly: `(fn(a): rock)(paper)`.

### Giving back a function

Suppose we want to choose the hand that a rule always gives back:

    always : (Hand) → (Hand) → Hand
    def always(a):
        fn(b): a

Expanded, this is `always = (a) ↦ (b) ↦ a`: applying the outer function gives back the inner one.

Given a hand, `always` makes a rule that ignores its own input and gives back that hand:

    always(rock)
        >> fn(b): rock

The outer input went into the inner function's body, where `a` was. That body still waits until someone applies the new function:

    always(rock)(paper)
        >> (fn(b): rock)(paper)
        >> rock

The type makes the two stages visible. `(Hand) → (Hand) → Hand` means “from a hand to a function from hands to hands”. What follows `→` stays together, just as a body does. Two stages, `f(a)(b)`, are different from giving one function two inputs, `f(a, b)`.

A function can take a rule and make another rule from it:

    twofold : ((Hand) → Hand) → (Hand) → Hand
    def twofold(f):
        fn(a): f(f(a))

    twofold(better)
        >> fn(a): better(better(a))

That is the rule used by `worse`. And the rule made by `always` can be passed to `twice`:

    twice(always(rock), paper)
        >> … >> rock

`>>` shows one step. `>> … >>` abbreviates a sequence of steps to show where they lead.

### Keeping names clear

A variable belongs to its case. Functions inside the body can use it, as `always` uses `a`. If an inner case introduces the same name, that inner name takes over there:

    (fn(a): fn(a): a)(rock)(paper)
        >> (fn(a): a)(paper)
        >> paper

The last `a` belongs to the inner case, so rock never replaces it. We replace a variable only where its name means that variable.

Variables cannot reuse a constructor's or a defined function's name. Each global name has one meaning, and each function is defined once. For now, a definition uses only functions defined before it; repetition will widen that rule.

### Naming an intermediate result

Applying a function also gives us a way to name a result before using it. For example:

    ((hand) ↦ result(hand, rock))(better(rock))

First work out `better(rock)`, then use its answer as `hand`. We will write this in the order we read it:

    let hand = better(rock)
    result(hand, rock)

These two lines are one expression. The first names an intermediate result; the last gives the result of the whole block. The expansion is:

    let p = e
    body

    means ((p) ↦ body)(e)

`p` can be any pattern. `e` is worked out once, before the body, and the names in `p` belong to the body. They are not available in `e`. Matching follows the usual rules, including getting stuck when the pattern doesn't match.

Several bindings nest in the same order:

    worseInSteps : (Hand) → Hand
    def worseInSteps(a):
        let once = better(a)
        let again = better(once)
        again

This computes the same answer as `worse`, naming both intermediate results. A block ends with an expression; each preceding `let` binds names in the rest of that block. Reusing a variable name introduces an inner binding, as it did with nested functions. It doesn't change an earlier value.

We can name a local function in the same way: `let rule = fn(a): better(a)`. Global definitions still use `def`; local names come from patterns and `let`.

### Holding a function

A player can hold its rule:

    Player = player((Hand) → Hand)

    player(better)
    player(always(rock))

The first player chooses the hand that beats the one it is given. The second always chooses rock. A pattern gets the rule out, and application uses it:

    choose : (Player, Hand) → Hand
    def choose(player(f), a): f(a)

    choose(player(better), rock)
        >> better(rock)
        >> paper

A pattern can name a function, as `f` does here, but can't inspect its cases. We use a function by applying it.

An application has this shape:

    e(e, …)

First work out the expression on the left to find the function. Then work out its inputs, from left to right, and apply it. In `always(rock)(paper)`, finding the function means working out `always(rock)` first.

If the value found isn't a function, or takes a different number of inputs, the application gets stuck:

    twice(rock, paper)        gets stuck: rock isn't a function
    twice(result, rock)       gets stuck: result takes two inputs

Each gets stuck when `f` is applied. The same rule applies to a name used directly: `better()` gets stuck because its input is missing.

### What changed

Every value is now data or a function. We have seen both as inputs and results:

| Input | Result | Example |
|---|---|---|
| Data | Data | `better(rock)` |
| Data | A function | `always(rock)` |
| A function, with data | Data | `twice(better, rock)` |
| A function | A function | `twofold(better)` |

Data can hold either kind too: `round(rock, paper)` holds data, and `player(better)` holds a function. Three shapes change:

    A ::= T
        | (A, …) → B

    e ::= c(e, …)
        | x                   if named by a surrounding case
        | n
        | (p, …) ↦ e | …
        | e(e, …)

    v ::= c(v, …)
        | n
        | (p, …) ↦ e | …

`A` and `B` now stand for either kind of type. The `e` to the left of an application's parentheses can be any expression too: it must produce a function when worked out. Every other `e` place has the same choices.

A variable can come from this case or a surrounding case; the nearest one with that name wins. Patterns haven't changed: they take data apart or name a whole value, including a function.

## 5. Repeating

To keep score, we need numbers. We can make them from two constructors:

    Nat = zero | succ(Nat)

`succ` means “one more than”. Zero is a number; one more than a number is another number. So `zero`, `succ(zero)` and `succ(succ(zero))` are zero, one and two.

The declaration uses its own name. A function can do that too:

    add : (Nat, Nat) → Nat
    def add:
        case (zero, b): b
        case (succ(a), b): succ(add(a, b))

Zero and `b` make `b`. One more than `a`, and `b`, make one more than `a` and `b`:

    add(succ(succ(zero)), succ(zero))
        >> succ(add(succ(zero), succ(zero)))
        >> succ(succ(add(zero, succ(zero))))
        >> succ(succ(succ(zero)))

Each step takes a layer off the first input and puts it around the result. The same two cases work however many layers there are. This is **recursion**: using a name inside its own definition.

### Writing numbers

Now that we know what these numbers are, we can write them in the usual way:

    0     means zero
    1     means succ(zero)
    2     means succ(succ(zero))

Any nonnegative whole-number literal is that many `succ` constructors around `zero`. The abbreviation works in patterns too. `case (0, b): b` is the first case of `add`; `succ(a)` still names the number inside a successor.

We will also write `a + b` for `add(a, b)`. Application binds more tightly than `+`; additions group from the left unless parentheses say otherwise. This is the `add` just defined, on `Nat`, not a new arithmetic operation.

The earlier calculation now reads:

    2 + 1
        >> … >> 3

A win earns one point:

    points : (Outcome) → Nat
    def points:
        case (win): 1
        case (other): 0

    points(draw)       >>   0

Here the last case overlaps the first: it could match win too. Trying cases in order is what makes win worth a point and everything else worth zero.

Recursion needn't make progress:

    loop : (Hand) → Hand
    def loop(a): loop(a)

    loop(rock)
        >> loop(rock), and so on forever

A function type doesn't promise to finish. `(Hand) → Hand` says that on hand inputs it doesn't get stuck, and any result is a hand. `loop` never finishes. The function `fn(rock): paper` doesn't fit: it gets stuck on paper or scissors.

Two functions can also refer to each other. These alternate between rock and paper as the number grows:

    rockTurn : (Nat) → Hand
    def rockTurn:
        case (0): rock
        case (succ(a)): paperTurn(a)

    paperTurn : (Nat) → Hand
    def paperTurn:
        case (0): paper
        case (succ(a)): rockTurn(a)

    rockTurn(2)
        >> paperTurn(1)
        >> rockTurn(0)
        >> rock

Types can refer to each other too:

    Turn = turn(Hand, Reply) | finished
    Reply = reply(Hand, Turn)

    turn(rock, reply(paper, finished))

A turn holds a hand and a reply; the reply holds a hand and the next turn. Here the next turn is `finished`.

No expression, value or pattern shape changes. The name rule does: functions and types can now refer to themselves or to ones defined later. For now, a recursive type reference must go through data contents. Section 12 will widen this to functions and requests.

Working out an expression can finish with a value, get stuck, or go on forever.

## 6. Leaving a type open

A match is a list of rounds. The hands a player has shown are a list too. We can describe both with one declaration:

    List(Item) = empty | cons(Item, List(Item))

`Item` leaves the type of each item open. A `List(Hand)` is empty, or a hand followed by a list of hands. A `List(Round)` has the same shape with rounds in those places.

    cons(rock, cons(paper, empty))

This is a list of two hands. `cons` holds the first item and the remaining list. A function can follow that structure:

    size : (List(Item)) → Nat
    def size:
        case (empty): 0
        case (cons(a, rest)): 1 + size(rest)

    size(cons(rock, cons(paper, empty)))
        >> … >> 2

In a signature, an undeclared capitalised name such as `Item`, written where a type goes, stands for an arbitrary type. Use the same choice everywhere that name occurs in that signature. The signature describes the function for every choice: `size` works for lists of hands, rounds, or any other kind of value. Types guide our reading; they aren't extra inputs to the function.

### Writing lists

Square brackets abbreviate the constructors we just used:

    []              means empty
    [a, b]          means cons(a, cons(b, empty))
    [a, *rest]      means cons(a, rest)

We can list any finite number of items. A final `*rest` supplies the remaining list instead of `empty`; it is a tail, not another item. The items and tail are worked out from left to right. These are the same constructor values as before; the brackets introduce no changeable storage. These spellings work in patterns too:

    def size:
        case ([]): 0
        case ([a, *rest]): 1 + size(rest)

This is the same `size`, with its list patterns abbreviated. `[a, *rest]` takes a nonempty list apart, naming its first item and remaining list. We will use brackets from here on, except when explaining their expansion.

    size([rock, paper])
        >> … >> 2

We can transform every item in a list:

    each : ((From) → To, List(From)) → List(To)
    def each:
        case (f, []): []
        case (f, [a, *rest]): [f(a), *each(f, rest)]

    each(better, [rock, paper])
        >> … >> [paper, scissors]
    each(points, [win, draw])
        >> … >> [1, 0]

The first example changes hands into hands. The second changes outcomes into numbers. The signature shows the connection: the function takes the kind of item in the input list and gives the kind in the output list.

Two independent types can be left open too:

    Pair(First, Second) = pair(First, Second)

`pair(win, 0)` is a `Pair(Outcome, Nat)`. We will write `(win, 0)` for that value: `(a, b)` abbreviates `pair(a, b)` in expressions and patterns. A comma distinguishes a pair from ordinary grouping. One pair as an input, `f((a, b))`, is different from two inputs, `f(a, b)`. Function input lists keep their existing meaning; matching one pair there takes an extra pair of parentheses, as in `def firstOfPair((a, b)): a`.

The declaration's type variables belong only to that declaration. Their names must be different, and must not reuse a declared type's name. Types can nest: `List(List(Hand))` is a list of lists, and `List((Hand) → Hand)` is a list of rules. Type notation stays as `Pair(First, Second)`; the comma abbreviation is for expressions and patterns.

Declarations gain type variables; types gain places to fill them:

    T(X, …) = c(A, …) | …

    A ::= T(A, …)
        | X                   if available in this declaration or signature
        | (A, …) → B

`X` stands for a type variable. In a declaration it must be listed on the left; in a signature it stands for an arbitrary type. Each type takes as many type arguments as its declaration lists. With none, write `Hand`, without parentheses.

### Playing a match

We can now let two rules play. Each rule sees the other player's previous hand. `play` asks both for their next hands; `meet` puts those hands into a round and plays the remaining rounds:

    play : (Nat, (Hand) → Hand, (Hand) → Hand, Hand, Hand) → List(Round)
    def play:
        case (0, f, g, a, b): []
        case (succ(left), f, g, a, b):
            meet(left, f, g, f(b), g(a))

    meet : (Nat, (Hand) → Hand, (Hand) → Hand, Hand, Hand) → List(Round)
    def meet(left, f, g, a, b):
        [round(a, b), *play(left, f, g, a, b)]

The two definitions use each other. Two rounds of `better` against “always rock”, starting with rock as both previous hands:

    play(2, better, always(rock), rock, rock)
        >> … >> [round(paper, rock), round(paper, rock)]

And we can count the wins:

    wins : (List(Round)) → Nat
    def wins:
        case ([]): 0
        case ([this, *rest]): points(judge(this)) + wins(rest)

    wins(play(2, better, always(rock), rock, rock))
        >> … >> 2

## 7. The core together

Every value we have made is data or a function:

| A value | How we make it | How we use it |
|---|---|---|
| Data | A constructor with its contents | A pattern that takes it apart |
| A function | Cases with patterns and bodies | Application to inputs |

Contents, inputs and results can themselves be data or functions. A type describes which kind belongs in each place.

Here are the expanded shapes underneath everything introduced so far:

    T(X, …) = c(A, …) | …
    n : (A, …) → B
    n = (p, …) ↦ e | …

The complete expression shape is:

    e ::= c(e, …)               data
        | x                     if named by a surrounding case
        | n                     a defined name
        | (p, …) ↦ e | …        a function
        | e(e, …)               an application

    v ::= c(v, …)
        | n
        | (p, …) ↦ e | …

Each `e` place can hold any of the five forms. This gives a function returning a function, data holding an application, or an application finding its function through another application.

Patterns also fit inside patterns:

    p ::= x                     name the whole value
        | c(p, …)               match its constructor and contents

For example, `[round(rock, a), *rest]` expands to `cons(round(rock, a), rest)`. It looks inside both the list and its first round. It matches `[round(rock, paper)]`, with paper as `a` and `[]` as `rest`.

Types have three shapes:

    A ::= T(A, …)               a declared type, filled with types
        | X                     a type variable available here
        | (A, …) → B            a function type

Each type place can hold any of these forms, as in `List((Hand) → Hand)`. In a declaration, `X` must be listed among its type variables. In a signature, it stands for an arbitrary type, chosen consistently.

The shapes say where pieces fit; matching and evaluation say what happens. A program gives declarations and definitions, then an expression to work out.

Our usual spelling adds no behaviour to these shapes. `def` and `fn` lay out functions; `let` names a result by applying a function; numbers, lists and pairs abbreviate constructors; `+` calls `add`. We can expand a passage when we need to see its parts, and keep the readable spelling when we use them.

These pieces are enough to describe any computable transformation of data. Next we will let a computation ask for something while it runs: a hand someone chooses, for example.

## 8. Requests

Everything so far has been **saying**. Each step rewrites part of an expression into something that means the same: `worse(paper)` and `rock` are two ways of writing one hand. Computing can also be pictured another way, as steps that change a memory kept beside the program. That is **doing**. The two pictures compute the same things, but some ideas are natural only as doing: waiting for a person, keeping a score that changes, giving up partway.

The medium keeps saying underneath and brings doing back on top. It adds a new form only when an idea can't be said **locally** with the forms already there: either not at all, or only by changing parts of the program far from where the idea is used. Waiting for a person is the first such idea.

So far, we have chosen every hand in the program. To let a person choose, we need a way to ask:

    Unit = unit

    effect Console = read : () → Hand
                   | show : (Outcome) → Unit

`Console` is an **effect**, declared by listing its **requests**. `read` takes no input and asks to be answered with a hand. `show` takes an outcome to display. Its answer is `unit`, the only value of `Unit`: there is nothing useful to give back, only something done.

An effect declaration has this shape:

    effect F = r : (A, …) → B | …

`F` stands for an effect's name, and `r` for a request's name. Each request gives the types of its inputs and, after `→`, the type of its answer.

We can lay out the same declaration as a block:

    effect Console:
        read : () → Hand
        show : (Outcome) → Unit

The colon and indented request lines replace `=` and the alternatives separated by `|`. We will use this layout for effect declarations from here on.

    show(result(read(), rock))

This asks for a hand, finds its outcome against rock, and shows that outcome. The first thing it needs is `read()`. A request has no answer of its own, so the program waits there.

Suppose the person chooses paper. The answer goes where the request was:

    show(result(paper, rock))
        >> show(win)

Now it waits at `show(win)`. Whatever runs the program displays win and answers `unit`. The whole expression then becomes that answer, and is finished.

A request can go inside data as well as inside an application:

    round(read(), rock)

The round waits for the requested hand before it is a value.

Requests can take or return functions too. We can ask for a playing rule, or ask to save one:

    effect Rules:
        askRule : () → ((Hand) → Hand)
        saveRule : ((Hand) → Hand) → Unit

    saveRule(better)
    askRule()(rock)
    saveRule(askRule())

The first gives a function to a request. The second asks for a function, then applies it to rock. The third uses the answer to one request as the input of another. The inputs and answer have the types in the declaration; they can be data or functions.

Expressions gain one alternative:

    e ::= c(e, …)
        | x                   if named by a surrounding case
        | n
        | (p, …) ↦ e | …
        | e(e, …)
        | r(e, …)

A request can fill any `e` place. Its inputs are worked out from left to right, just like a function's. Each request belongs to one effect and takes its declared number of inputs. Values and patterns haven't changed: a request waits for an answer; it isn't a value.

### Doing one thing, then another

Sometimes we need to finish one expression before starting the next, without keeping its result. Function application can say that too:

    ((ignored) ↦ show(win))(show(draw))

First ask to show draw. Once that request is answered, ask to show win. The first answer is unused. We will write these in order:

    show(draw)
    show(win)

This extends our blocks. An expression before the last line is worked out, its result is discarded, and the rest follows. The whole block gives the last expression's result. Precisely:

    e
    body

    means ((ignored) ↦ body)(e)

The expansion chooses a fresh name for `ignored`, so it cannot capture a name in the body. A `let` line keeps the result; an ordinary line discards it. Both wait for that expression to finish before continuing. If it waits for a request or runs forever, the later lines wait too.

Line breaks inside parentheses, brackets or an indented subexpression belong to that expression. Only separate expressions at the block's own indentation are sequenced this way.

### Saying what a function may ask

We can give the game a name and keep it for later:

    game : () → Unit ! {Console}
    def game():
        let hand = read()
        let outcome = result(hand, rock)
        show(outcome)

`! {Console}` reads “may ask Console”. Calling `game()` starts the requests. Until then, the body waits like any other function's body.

A person can supply a playing rule too:

    human : (Hand) → Hand ! {Console}
    def human(previous): read()

The rule ignores the previous hand and asks the person for the next one. Its result is a hand, provided the request is answered with a hand.

With no effects, leave out `! {…}`.

“May ask” includes not asking. So `better` also fits `(Hand) → Hand ! {Console}`. We can keep rules and people in one list:

    [better, human]

Its type is `List((Hand) → Hand ! {Console})`. Several effects can be listed too: `fn(): askRule()(read())` fits `() → Hand ! {Rules, Console}`. It asks for a rule, then a hand, and applies the rule to that hand.

The list describes requests left for something else to answer. If each receives an answer of its declared type, the function must avoid getting stuck and give the stated kind of result if it finishes.

### Asking now or later

We can ask a person for a hand, then make a rule that always gives that hand:

    pick : () → ((Hand) → Hand) ! {Console}
    def pick():
        let hand = read()
        always(hand)

Calling `pick()` asks once. If the person answers paper, it gives back `fn(b): paper`. Applying that rule asks nothing: the hand is already chosen.

Parentheses group a type just as they group an expression. Here they keep the returned rule's type, `((Hand) → Hand)`, together. The `! {Console}` outside those parentheses belongs to the call that makes the rule.

To return a rule that asks each time it is used:

    askLater : () → ((Hand) → Hand ! {Console})
    def askLater(): human

Calling `askLater()` gives back `human` without asking. Calling the returned rule asks for a hand. The effect is inside the parentheses because it belongs to that rule.

Both calls may ask too: `() → ((Hand) → Hand ! {Console}) ! {Console}`. Each suffix belongs to the nearest preceding arrow in the same group. Without parentheses, arrows extend to the right.

### Leaving the requests open

`each` applies a function to every item in a list. That function can be `human` too:

    each(human, [rock, paper])

This asks for two hands, from left to right, and collects the answers. If the person answers scissors and then rock, the result is `[scissors, rock]`.

The earlier signature described only functions that ask nothing. Replace it with this wider one; the definition stays the same:

    each : ((From) → To ! {Asks}, List(From)) → List(To) ! {Asks}

`Asks` leaves a list of effects open, just as `From` and `To` leave types open. Choose the same list everywhere it occurs in this signature. For `human`, it is `Console`; for `better`, it can be empty. `each` may ask whatever the function it uses may ask.

An undeclared capitalised name in an effect list stands for a list, possibly empty. Known effects can go alongside it, as in `{Console, Asks}`. Order doesn't matter, and repeated effects count once. These are descriptions, not extra inputs to the function.

The type shape now includes possible effects. An entry in their list is a named effect or a list variable:

    A ::= T(A, …)
        | X                   if available in this declaration or signature
        | (A, …) → B ! {E, …}

    E ::= F                   a declared effect
        | S                   a list variable, only in a signature

`S` follows the rule just shown with `Asks`: one choice throughout its signature. One name cannot stand for both a type and an effect list.

## 9. Handlers

The program can answer a request itself. A **handler** goes around an expression and says how to answer:

    handle better(read()) with
          (answer) ↦ answer
        | (read(), next) ↦ next(rock)

The first case receives the final value and gives it back. The second receives the request. It answers rock, then continues.

`next` is what remains to be done, written as a function. Here, what remains is to use `better` on the answer. It also keeps the same handler around that work. Written out in the core notation, it is:

    (hand) ↦ handle better(hand) with
                   (answer) ↦ answer
                 | (read(), next) ↦ next(rock)

So `next(rock)` continues with rock, `better(rock)` becomes paper, and the handler's first case gives back paper. The whole handled expression comes to `paper`.

With a different answer, the same expression behaves differently. A handler using `next(paper)` would make `better(read())` come to scissors. The expression asks; the handler chooses how to answer.

### Writing a handler as a block

The readable spelling keeps the two kinds of case visible:

    handle better(read()) with:
        case answer: answer
        case read() as next: next(rock)

`case p: body` means the value case `(p) ↦ body`. `case r(patterns) as next: body` means the request case `(r(patterns), next) ↦ body`. Indentation replaces the separators between cases. As with functions, a case body can continue on indented lines.

When the expression being handled needs several lines, give it a block too:

    handle:
        let hand = read()
        better(hand)
    with:
        case answer: answer
        case read() as next: next(rock)

The block between `handle:` and its matching `with:` is the handled expression. Expand its `let` as usual, then place that expression inside the same core handler. The `with:` aligns with its `handle:`; it ends the handled block and starts the cases. Both layouts use the same rules.

### Which handler answers?

A request goes to the nearest surrounding handler with a case for its name. A handler with no such case lets it pass:

    handle:
        handle better(read()) with:
            case hand: worse(hand)
    with:
        case answer: answer
        case read() as next: next(rock)

        >> … >> rock

The inner handler has no case for `read`, so the outer one answers rock. Continuing still includes the inner handler: `better(rock)` gives paper, and the inner handler applies `worse` to paper, giving rock.

If no surrounding handler answers, the request reaches the outside, as it did when a person chose a hand. The outside answers once and the program continues.

A handler's case runs outside the handler it just used. Calling `next` puts that handler back around the remaining work. This matters if the case makes another request:

    handle show(win) with:
        case answer: answer
        case show(outcome) as next: next(show(outcome))

`outcome` names the request's input. The case sends that outcome to `show` outside this handler. Once the outside answers `unit`, `next` carries on and the handler returns `unit`. It doesn't repeatedly handle its own request.

Having a case for a request's name is different from matching its inputs:

    handle show(win) with:
        case answer: answer
        case show(lose) as next: next(unit)

This gets stuck. The handler takes responsibility for `show`, but its only pattern doesn't match win. It doesn't pass the request on. Likewise, a handler whose only value case accepts rock gets stuck if its expression finishes with paper.

### The shape with handlers

A handler is an expression too. Its result can fill an input, a data content or the place where an application finds its function. Here it returns a rule, which is then applied to paper:

    let rule = handle read() with:
        case hand: always(hand)
        case read() as next: next(rock)
    rule(paper)
        >> … >> rock

The handler supplies rock and makes the rule `always(rock)`. The binding names that result `rule`; the final application uses it. Expanding the `let` puts the whole handler in the input of a function, just as for any other expression.

Handlers may have value cases, request cases, or both. A value-only handler appeared in the nested example above. A request-only handler can stop at the request:

    handle read() with:
        case read() as next: rock
        >> rock

If a handled expression reaches a value and there is no matching value case, it gets stuck. As with functions, several cases of the same kind are tried in order.

Expressions gain a handler. Its cases have two forms, written `h`:

    e ::= c(e, …)
        | x                   if named by a surrounding case
        | n
        | (p, …) ↦ e | …
        | e(e, …)
        | r(e, …)
        | handle e with h | …

    h ::= (p) ↦ e              for a final value
        | (r(p, …), x) ↦ e     for a request; x names the rest

Each `e` place can hold any expression, including another handler. A request case has one pattern per input and one variable for the rest. All names introduced by a case are distinct and belong to its body; the nearest binding still wins. No other shape changes.

## 10. Choosing how to continue

Since `next` is a function, a case can use it once, several times, later, or never. We will use these choices for search, state and generators. Section 11 will also give a request a way to choose a particular handler.

### Stopping

A player can run out of prepared hands:

    effect Forfeit:
        forfeit : () → Hand

    firstOf : (List(Hand)) → Hand ! {Forfeit}
    def firstOf:
        case ([a, *rest]): a
        case ([]): forfeit()

The handler can make that a loss:

    handle result(firstOf([]), rock) with:
        case outcome: outcome
        case forfeit() as next: lose

        >> … >> lose

The case never applies `next`, so the round is never compared. The handler's answer becomes the result of the whole handled expression. An error that abandons the current computation works this way.

### Trying several answers

Which hands would beat rock? We can answer `read()` three times and count the wins:

    handle result(read(), rock) with:
        case outcome: points(outcome)
        case read() as next:
            let fromRock = next(rock)
            let fromPaper = next(paper)
            let fromScissors = next(scissors)
            fromRock + (fromPaper + fromScissors)

        >> … >> 1

Each call to `next` starts from the same waiting point, with a different hand. Each result passes through the handler's first case, so each becomes a number: zero for rock, one for paper, zero for scissors.

Continuing again repeats the saved work. It doesn't undo work already done outside that continuation. If that work asks the outside to display something, trying another answer doesn't erase the earlier display.

Here `next` has type `(Hand) → Nat`. The request still needs a hand as its answer, but the handler turns the final outcome into a number. The type makes both ends visible.

This is search: try possible answers and combine what they lead to. We used ordinary functions and data to say what to do with the alternatives.

## 11. Keeping a score

A score changes as rounds are played. We can describe each change with a score before and a score after:

    recordWith : (Outcome, Nat) → Pair(Outcome, Nat)
    def recordWith(outcome, total):
        (outcome, points(outcome) + total)

    recordWith(win, 0)
        >> … >> (win, 1)
    recordWith(draw, 1)
        >> … >> (draw, 1)

To record two rounds, we take the score from the first result and pass it into the second call:

    recordBothWith : (Round, Round, Nat) → Pair(List(Outcome), Nat)
    def recordBothWith(first, second, total):
        let (a, afterFirst) = recordWith(judge(first), total)
        let (b, afterSecond) = recordWith(judge(second), afterFirst)
        ([a, b], afterSecond)

The first binding names the first outcome and the score after it. The second uses that score and names the next outcome and score. The last line gives back both outcomes with the final score. Expanding the two bindings would nest two function applications; their readable spelling keeps the work in order.

    recordBothWith(round(paper, rock), round(rock, rock), 0)
        >> … >> ([win, draw], 1)

`recordBothWith` only passes the score along. A function that uses it would have to pass the score too. Every function between the place the score starts and the places it changes would need to carry it in and out. The core can say a changing score, but not locally: one small idea changes every function on the way. That is the kind of idea requests are for.

Requests let us mention the score where it is actually used:

    effect State(Stored):
        get : () → Stored
        put : (Stored) → Unit

An effect can leave types open, as a data type can. `State(Nat)` keeps a number; `State(Hand)` keeps a hand. `get()` asks for the current value. `put(a)` asks to replace it with `a` and is answered with `unit`.

Only effect declarations and effect-list entries change:

    effect F(X, …) = r : (A, …) → B | …

    E ::= F(A, …)
        | S                   a list variable, only in a signature

An effect's type variables follow the same rules as a data type's. Fill them with as many types as the declaration lists. With none, write `Console`, without parentheses.

For requests selected only by name, each effect in one list has one choice of type arguments. Combining `State(Nat)` and `State(Hand)` this way isn't allowed: both use the same `get` and `put` names, so their requests couldn't be distinguished. Later in this section, explicit targets will distinguish them.

We can record a win by getting the score, adding one, and putting it back. Our block notation already gives these requests their order:

    record : (Outcome) → Outcome ! {State(Nat)}
    def record:
        case (win):
            let total = get()
            put(1 + total)
            win
        case (outcome): outcome

And record two rounds:

    recordBoth : (Round, Round) → List(Outcome) ! {State(Nat)}
    def recordBoth(first, second):
        let a = record(judge(first))
        let b = record(judge(second))
        [a, b]

`recordBoth` passes no score. Its type says it may ask for state because `record` does. The bindings order the two calls; the score itself is requested only where it is used.

### Answering the memory requests

A handler supplies the score. It passes the current score from one request to the next, but writes that passing in one place:

    keep : (() → Result ! {State(Stored), Asks}, Stored) → Pair(Result, Stored) ! {Asks}
    def keep(task, start):
        let run = handle task() with:
            case answer:
                fn(current): (answer, current)
            case get() as next:
                fn(current):
                    let runNext = next(current)
                    runNext(current)
            case put(value) as next:
                fn(current):
                    let runNext = next(unit)
                    runNext(value)
        run(start)

The signature says: give `keep` a task and an initial stored value; it gives back the task's result together with the final stored value. It answers the state requests itself. Other requests, described by `Asks`, pass through it.

The task is a function with no inputs. That keeps its work waiting until `task()` runs inside the handler. Given the work directly, we would run it before the handler was there.

Every case returns a function waiting for the current score:

- At a final value: pair that value with the score.
- At `get()`: answer with the score and continue with the same score.
- At `put(value)`: answer `unit` and continue with `value` as the score.

The handler gives a function, which we name `run`. `run(start)` supplies the first score. In the `get` case, `next(current)` answers the request and gives back another function waiting for a score. We name it `runNext`, then supply the same score. The `put` case supplies the new value instead.

    keep(fn(): recordBoth(round(paper, rock), round(rock, rock)), 0)
        >> … >> ([win, draw], 1)

The first round wins and increases the score. The second draws and leaves it alone. The task gives back the outcomes; `keep` adds the final score.

### Giving a block to a function

Passing a waiting task is common enough to give it a block spelling:

    with keep(0):
        recordBoth(round(paper, rock), round(rock, rock))

This means the preceding call: `keep(fn(): recordBoth(round(paper, rock), round(rock, rock)), 0)`. In general:

    with f(inputs):
        body

    means f(() ↦ body, inputs)

The block becomes a function with no inputs, passed as the first input to `f`. The written inputs follow it. With no written inputs, only the task is passed. Expand the body by the usual block rules.

`with` itself neither runs the block nor handles requests. The function decides what to do with the task. Here `keep` runs it inside a state handler. This spelling lets us read that use from top to bottom.

Order now matters in a visible way:

    with keep(0):
        put(1)
        get()

        >> … >> (1, 1)

    with keep(0):
        let saved = get()
        put(1)
        saved

        >> … >> (0, 1)

In the first, we put one and then get one. In the second, we get zero first, call it `saved`, then put one. The final score is the same, but the task returns the value it read earlier.

The handler never inspects the stored value. The same definition keeps a hand:

    with keep(rock):
        put(paper)
        get()

        >> … >> (paper, paper)

The task can also ask to show its outcome:

    with keep(0):
        show(record(win))
        get()

`keep` answers the score requests. `show(win)` passes through it to the outside. Once that is answered with `unit`, the expression comes to `(1, 1)`. Here `Stored` is `Nat`, `Result` is `Nat`, and `Asks` is `Console`.

Different effects can appear in the same expression. Each request still finds the nearest handler with a case for its name:

    handle:
        handle:
            show(win)
            get()
        with:
            case answer: answer
            case show(outcome) as next: next(unit)
    with:
        case answer: answer
        case get() as next: next(0)

        >> … >> 0

The inner handler answers `show`. The request for `get` passes through it to the outer handler. The result is the number supplied there.

### Choosing a handler

One `keep` supplies one store. Two nested handlers for the same `get` and `put` names don't give us two stores we can choose between: the nearer handler answers both names. The same rule can catch a request from a function passed in, even when that request was meant for an outer handler.

We need to say which handler a request is for. Give the handler a **target**:

    handle get@mine() as mine with
          (answer) ↦ answer
        | (get(), next) ↦ next(zero)

        >> … >> zero

`as mine` makes a fresh target and names it in the handled expression. `get@mine()` sends a request to that target. The cases still say how to answer `get`; the target says which handler should answer it.

The handler block keeps the same `as mine`:

    handle get@mine() as mine with:
        case answer: answer
        case get() as next: next(0)

For a longer handled body, write `handle as mine:`, followed by that body and its matching `with:` block. These are the same abbreviations as before, with the target binding kept on the handler.

A named handler answers only requests sent to its target. An unnamed handler answers only requests without a target. A request for another target passes through, even when both handlers have cases for `get`.

Each time we enter a named handler, it makes a different target. Reusing the written name `mine` doesn't reuse an earlier target. A continuation keeps the target of the handler it saved: applying `next` reinstalls that same target, rather than making a new one.

A target is a value. We can pass it to a function or hold it in data. A variable can name it, but a constructor pattern cannot take it apart, and it cannot be applied as a function.

Two expression shapes are added:

    e ::= …
        | r@e(e, …)                      a request to a target
        | handle e as x with h | …       a handler with a fresh target

    v ::= … | a                          a target made during evaluation

`a` stands for a target value made by a handler. It has no literal spelling in a program; we reach it through the variable bound by `as`.

The target of a request is worked out first, then its inputs from left to right. Group a target more complicated than a name, as in `get@(chooseStore(a, b))()`. If it isn't a target value, the request gets stuck.

`x` is bound in the handled expression, including functions made there. It isn't bound in the handler's cases. Those cases still run outside the handler; `next` puts it back.

### Describing a target

The type of a target must distinguish it from other targets. Write `Target(Store)` for the type containing just the target identified by `Store`. In a signature, `Store` leaves that identity open, as `Stored` leaves a type open.

The effect list names the target too:

    bumpAt : (Target(Store)) → Unit ! {State(Nat)@Store}
    def bumpAt(place):
        let total = get@place()
        put@place(1 + total)

The input chooses a store. Both requests go to that store. `State(Nat)@Store` says which requests, with which stored type, may be sent there.

Two distinct targets can use different instances of the same effect. For example, `{State(Nat)@Score, State(Hand)@Choice}` describes requests to a number store and a hand store. The target distinguishes them. At one target, one effect still has one choice of type arguments.

The type shapes gain targets and addressed effects:

    A ::= … | Target(Q)
    E ::= F(A, …) | F(A, …)@Q | S

`Q` stands for a target identity. An undeclared capitalised name used in `Target(Q)` or after `@` stands for an arbitrary identity in a signature. One name cannot also stand for a type or an effect list. These identities describe target values; they aren't extra inputs to a function.

### Keeping either score

We can make one state handler and use it for either player. This version gives its task the fresh target and returns only the task's answer:

    def keepAt(task, start):
        let run = handle task(place) as place with:
            case answer:
                fn(current): answer
            case get() as next:
                fn(current):
                    let runNext = next(current)
                    runNext(current)
            case put(value) as next:
                fn(current):
                    let runNext = next(unit)
                    runNext(value)
        run(start)

The state is passed between requests just as in `keep`. The task takes one input now: the target to use. Our `with` spelling can name that input:

    with mine = keepAt(0):
        get@mine()

This means `keepAt((mine) ↦ get@mine(), 0)`. In general, `with p = f(inputs): body` passes `(p) ↦ body` as the first input to `f`. The pattern receives one value when the function calls the task. Here `keepAt` supplies the fresh target; `with` simply names it in the block.

To keep two scores, use the same function twice:

    with mine = keepAt(0):
        with yours = keepAt(0):
            bumpAt(mine)
            (get@mine(), get@yours())

        >> … >> (1, 0)

The inner handler answers requests sent to `yours`. Requests sent to `mine` pass through it to the outer handler. No new effect declaration or second state handler is needed.

Two variables can name the same target:

    with mine = keepAt(0):
        let samePlace = mine
        bumpAt(samePlace)
        get@mine()

        >> … >> 1

Adding through `samePlace` changes what reading through `mine` finds: both requests reach the same store. The target chooses the store; the changing value lives in the handler.

The same handler can keep different kinds of values:

    with score = keepAt(0):
        with hand = keepAt(rock):
            put@hand(paper)
            (get@score(), get@hand())

        >> … >> (0, paper)

To give `keepAt` its type, we need to describe a task that accepts whichever fresh target it is given. Write `for Store.` in front of that task's type:

    keepAt : ((for Store. (Target(Store)) → Result ! {State(Stored)@Store, Asks}), Stored)
             → Result ! {Asks}

Read this as before: a task and an initial value give a result. The task must work for any choice of `Store`. `keepAt` chooses a fresh one and answers its state requests. Other requests pass through.

`for Store.` binds the identity only in the type that follows it. `Result`, `Stored` and `Asks` are chosen outside it, so they cannot depend on that private identity. This matters if the task tries to return a function that still needs the private store: such a function would have `State(Stored)@Store` in its type, and would not fit the independent `Result`.

This adds one type form, used in signatures:

    A ::= … | for Q. A

The value needs no new kind of application. It is the same function, described for every choice of the bound identity. Parentheses show where that description ends. Other free type, effect-list and target variables in a signature are still chosen for the whole signature.

### Keeping requests separate

A function can use a private score while running work supplied by its caller:

    localScore : (() → Result ! {Asks}) → Result ! {Asks}
    def localScore(task):
        with local = keepAt(0):
            bumpAt(local)
            task()

    with keep(1):
        localScore(fn(): get())

        >> … >> (1, 1)

`bumpAt(local)` changes the private score. The caller's `get()` has no target, so the named handler leaves it alone. It reaches `keep` and reads the caller's score. A caller's request to another named target would pass through in the same way.

Choosing a target doesn't keep its handler running. Consider a handler that returns a function before that function makes its request:

    let readLater = handle (fn(): get@store()) as store with:
        case answer: answer
        case get() as next: next(0)
    readLater()

        gets stuck: the target's handler is no longer surrounding the request

The function remembers the target, but no active handler has that target. The request does not fall back to an unnamed handler or to the outside. Saving `next` is different: it saves the handler around the remaining work too, as the next section will show.

A named request requires an active handler with that target and a case for its request name. With no such handler, or with no matching case, it gets stuck. If saved continuations put several copies of the same target around a request, the nearest copy answers it.

## 12. Continuing later

A handler can return the rest of the work instead of carrying it out immediately. Then whoever receives it decides when to continue.

Let a player give its hands one at a time:

    effect Give:
        give : (Hand) → Unit

    cycle : (Hand) → Unit ! {Give}
    def cycle(a):
        give(a)
        cycle(better(a))

Starting with rock, it gives rock, paper, scissors, then rock again. It keeps asking forever.

A handler can stop at each hand and return that hand with the rest:

    Moves = done | gave(Hand, (Unit) → Moves)

    moves : (() → Unit ! {Give}) → (Unit) → Moves
    def moves(task):
        fn(u):
            handle task() with:
                case answer: done
                case give(a) as next: gave(a, next)

`moves` gives back a function waiting for `unit`. When applied, it starts the task. At `give(a)`, it returns `a` together with `next`, without applying `next`.

The returned `next` also waits for `unit`, the answer to `give`. Applying it reaches the next hand. In these lines, `next`, `after` and `again` name the functions returned alongside the hands:

    moves(fn(): cycle(rock))(unit)      >> … >> gave(rock, next)
    next(unit)                         >> … >> gave(paper, after)
    after(unit)                        >> … >> gave(scissors, again)

The player is infinite, but each request for a move reaches one hand. This is a generator: its caller asks for each next item when it needs it.

A named handler can save its work in the same way:

    handle as output:
        give@output(rock)
        give@output(paper)
    with:
        case answer: done
        case give(a) as next: gave(a, next)

        >> … >> gave(rock, next)

    next(unit)       >> … >> gave(paper, after)
    after(unit)      >> … >> done

Here `next` and `after` again name the returned functions. They keep the handler with its original target. The later `give@output(paper)` therefore reaches that handler when we continue, even though the first call has already returned.

`Moves` also shows a recursive type containing a function. A `gave` holds a hand and a function that can produce more `Moves`. Such a sequence can keep unfolding indefinitely; it needn't eventually reach `done`.

The type forms haven't changed, but the rule for recursive references widens. In section 5, a type referred back to itself through data contents. It may also do so through functions and requests, including their inputs and results.

### Taking a player as input

A playing rule can take the opposing player itself, rather than just its previous hand:

    Rival = rival((Rival) → Hand)

    chooseAgainst : (Rival, Rival) → Hand
    def chooseAgainst(rival(rule), other): rule(other)

    steady : (Rival) → Hand
    def steady(other): rock

    respond : (Rival) → Hand
    def respond(other):
        let hand = chooseAgainst(other, rival(steady))
        better(hand)

    chooseAgainst(rival(respond), rival(steady))
        >> … >> paper

`steady` always chooses rock. `respond` asks the opponent what it would choose against a steady player, then chooses the hand that beats that answer. The `Rival` inside the function's input makes this a recursive type too.

To read such a type, we cannot finish checking the input before beginning to check the function: each refers back to the other. Instead, we examine finite observations. Looking inside data, applying a function and answering a request each leave less to examine. A value fits when it passes every finite bound. Section 13 states this reading precisely.

This permits recursive references through inputs, including request answers, without making termination part of a type's promise. Rules that keep consulting each other may still run forever.

### Taking turns

Two generators can play each other. `turns` asks each for a hand, and `joinMoves` builds a round if both supplied one:

    turns : (Nat, (Unit) → Moves, (Unit) → Moves) → List(Round)
    def turns:
        case (0, first, second): []
        case (succ(left), first, second):
            let a = first(unit)
            let b = second(unit)
            joinMoves(left, a, b)

    joinMoves : (Nat, Moves, Moves) → List(Round)
    def joinMoves:
        case (left, gave(a, first), gave(b, second)):
            [round(a, b), *turns(left, first, second)]
        case (left, first, second): []

The new functions use no new forms. `first` and `second` hold what remains of each player, and `turns` chooses when to continue each one.

    rocks : () → Unit ! {Give}
    def rocks():
        give(rock)
        rocks()

    turns(3, moves(fn(): cycle(rock)), moves(rocks))
        >> … >> [round(rock, rock), round(paper, rock), round(scissors, rock)]

Each player describes its own moves. `turns` controls their order. This gives a small example of cooperative scheduling: tasks give control back at requests, and a scheduler decides which task continues.

## 13. The whole medium

Here are the pieces together. We normally write the readable forms from the examples. Their expansions below lead back to the core grammar and evaluation rules. The letters stand for parts of the language:

| Letter | What it stands for |
|---|---|
| `e`, `v` | An expression, a value |
| `b` | A readable body, expanded to one expression |
| `p`, `h` | A pattern, a handler case |
| `x`, `n` | A variable, a defined function's name |
| `c`, `r` | A constructor, a request |
| `a`, `Q` | A target value made during evaluation, an identity used to describe it |
| `T`, `F` | A declared type's name, an effect's name |
| `A`, `B`, `X` | Types, and a variable for a type |
| `E`, `S` | An entry in an effect list, and a variable for a list of effects |

`e, …` means any number of expressions, including none; likewise for the other letters. An item followed by `| …` allows one or more alternatives. Omit parentheses for a type or effect with no type arguments, and for a constructor with no contents. `>>` shows one evaluation step; `>> … >>` abbreviates several. Neither is part of a program.

### Readable forms and their expansions

Expand abbreviations wherever they occur, including inside another abbreviation. The remaining expression uses only the core forms that follow this section.

| Readable spelling | Expanded spelling |
|---|---|
| `def n(p, …): b` | `n = (p, …) ↦ b` |
| `fn(p, …): b` | `(p, …) ↦ b` |
| `0`, `1`, `2`, … | `zero`, `succ(zero)`, `succ(succ(zero))`, … |
| `e₁ + e₂` | `add(e₁, e₂)` |
| `[]` | `empty` |
| `[e₁, e₂]` | `cons(e₁, cons(e₂, empty))` |
| `[e, *rest]` | `cons(e, rest)` |
| `(e₁, e₂)` | `pair(e₁, e₂)` |

Application binds more tightly than `+`; repeated additions group from the left. Parentheses can choose another grouping. The addition spelling always uses the `add` defined for `Nat`.

Numbers, lists and pairs have the same expansions in patterns. A list may have any finite number of items; if it has a tail, that tail is last. Wrap one `cons` around the tail for each preceding item; with no preceding items, `[*rest]` is simply `rest`. Without a tail, use `empty` at the end. A pair has exactly two contents. Parentheses without a comma still group one expression. Function and request input parentheses still separate inputs: `f(a, b)` takes two, and `f((a, b))` takes one pair. In a function's pattern list, `case ((a, b))` likewise matches one pair.

For several function cases, `def n:` or `fn:` introduces an indented list of `case (p, …): b` clauses. Expand those to `(p, …) ↦ b | …`, keeping their order; a named definition also keeps `n =`. Every case has the same number of input patterns. `def` remains a global definition, while `let f = fn…` can name a local function. An inline `fn` body extends to the end of its surrounding input, case or group; group the function when following text, such as an application, belongs outside it.

An effect block `effect F(X, …):` lists indented request signatures. Join those signatures with `|` after `effect F(X, …) =`. Omit type-argument parentheses when there are none, as before.

A body is one expression after a colon, or an indented block whose last item is an expression. Before that last item, it may have bindings and expressions to sequence. Starting from the last expression, expand the preceding items from right to left:

    let p = e
    b
        means ((p) ↦ b)(e)

    e
    b
        means ((fresh) ↦ b)(e)

Choose `fresh` so it occurs nowhere in `b` and conflicts with no declared name. A binding's pattern is in scope in the remaining body, not in its own input. This expansion preserves the left-to-right order, evaluates each input once, and gives the final expression's result. A body cannot end with a `let` that has no following expression.

Handler blocks expand their cases as follows:

| Readable case | Core case |
|---|---|
| `case p: b` | `(p) ↦ b` |
| `case r(p, …) as x: b` | `(r(p, …), x) ↦ b` |

`handle e with:` keeps `handle e with` and joins the expanded cases with `|`. `handle e as x with:` keeps the named handler in the same way. For `handle:` or `handle as x:`, first expand the handled body to `e`; the aligned `with:` begins its cases. Handler case bodies and the handled body each follow the same block rules.

Passing a body to a function has these expansions:

    with f(e, …):
        b
        means f((() ↦ b), e, …)

    with p = f(e, …):
        b
        means f(((p) ↦ b), e, …)

The supplied function is the first input; the written inputs follow it. With none, omit the comma after that function. The bound version receives one input, matched by `p`; the unbound version receives none. `f` may be a function expression, grouped when needed. It is worked out before its inputs, just as in the expanded application. The body waits until the supplied function is applied. This notation adds no handling, cleanup or other behaviour of its own.

Indentation determines which case or body a line belongs to. A short body may sit after its colon; a multiline body is indented beneath it. Lines inside parentheses or brackets continue that expression. A block-form expression used on the right of `let` ends when indentation returns to that binding's level. `with:` aligns with the handler it belongs to. In examples, evaluation lines beginning with `>>` describe the whole preceding expression and are never part of a body.

All the names, arities, scopes and matching restrictions below apply after expansion. The readable forms are spellings of these computations, not extra primitives. Adding a binding can add an application step; abbreviated data and function spellings themselves add no evaluation steps. Count `>>` steps in the expanded program.

### Declarations and definitions

    T(X, …) = c(A, …) | …                 a type and its constructors
    effect F(X, …) = r : (A, …) → B | …   an effect and its requests

    n : A                                 a function's type, when written
    n = (p, …) ↦ e | …                    its definition

A program gives declarations and definitions, then an expression to work out. A signature can describe a particular use of a more general function. It doesn't change evaluation or enforce its description: checking whether a function fits is outside the medium.

### Expressions, values and patterns

    e ::= c(e, …)                         data
        | x                               if bound by a surrounding case or handler
        | n                               a defined function
        | (p, …) ↦ e | …                  a function written out
        | e(e, …)                         an application
        | r(e, …)                         a request
        | r@e(e, …)                       a request to a target
        | handle e with h | …             a handled expression
        | handle e as x with h | …        a handler with a fresh target

    v ::= c(v, …)                         data whose contents are values
        | n                               a defined function
        | (p, …) ↦ e | …                  a function written out
        | a                               a target made during evaluation

    p ::= x                               match any value and name it
        | c(p, …)                         match a constructor and its contents

    h ::= (p) ↦ e                         for a final value
        | (r(p, …), x) ↦ e                for a request, naming the rest as x

Every `e` place accepts any expression form, subject to the rules below. The shapes can nest without a fixed limit.

Target values can also occur in running expressions. There is no program spelling for a particular target: a named handler makes one, and variables can carry it. `a` is how we refer to such a value when describing evaluation, not a constructor or a new global name.

### Types

    A ::= T(A, …)                         a declared type, filled with types
        | X                               a type variable available here
        | (A, …) → B ! {E, …}             a function type
        | Target(Q)                       one particular target
        | for Q. A                        every choice of an identity, in a signature

    E ::= F(A, …)                         a declared effect, filled with types
        | F(A, …)@Q                       that effect at a particular target
        | S                               a list variable, only in a signature

Parentheses can group any type: `(A)` means the same type as `A`. Leave out `! {…}` when there are no effects. Each effect suffix belongs to the nearest preceding arrow in the same group. Without grouping, the result after an arrow extends to the right.

A declaration can use declared types and effects, and its own type variables. Those variables have distinct names and cannot reuse declared type or effect names. A type or effect takes as many type arguments as its declaration lists.

In a signature, an undeclared capitalised name stands for an arbitrary type, an arbitrary list of effects, or an arbitrary target identity, according to where it is used. One name cannot do more than one of these jobs. Free variables are chosen for the whole signature. `for Q. A` binds its own identity variable only in `A`; it cannot reuse a declared type or effect name. Rename bound variables when necessary to keep their meanings distinct. A variable chosen outside `for Q.` cannot be filled with a type or effect list mentioning that bound `Q`.

An effect-list variable can stand for no effects, one, or several. Combine the entries; order doesn't matter and repetitions count once. For a given effect, all unaddressed entries must agree on its type arguments. Entries addressed to the same target must also agree with each other. Different targets, and the unaddressed form, are separate. Only choices satisfying these rules are allowed. Effect-list variables and identity quantifiers occur only in signatures.

`Target(Q)` describes exactly the target with identity `Q`. A named effect entry describes a requirement on the surrounding handlers: that target must be available and answer the listed operations at the stated types. An unaddressed entry can be supplied by a surrounding handler or by the outside. A handler removes the requests it answers, not requests to other targets. Requests in its cases still need their own answers.

### Reading recursive types

The descriptions are read with a finite bound on what we observe. This keeps a recursive description from depending on its own completed answer. Zero observations make no demand. At any larger bound:

- For a declared data type, check the outer constructor. Check each content at a bound one smaller, using the declared content type with its type arguments filled in.
- For a function type, check that the value is a function with the stated number of inputs. For every smaller bound, give it any inputs that fit their types at that bound. Examine the matching of its cases and the selected body with that bound. The initial application is the observation already used in passing to the smaller bound.
- For `Target(Q)`, check that the value is the target identified by `Q`.
- For `for Q. A`, use the same bound for `A` under every allowed choice of `Q`. This opens a binder; following a recursive data declaration still decreases the bound.

To examine a computation, follow its evaluation with the observations remaining. A function application, each constructor inspection while matching a pattern, entering or dispatching a handler, and answering an unanswered request each use one observation. Matching a nested pattern inspects each constructor separately; a displayed `>>` step can therefore contain several observations. Looking for a matching case follows the stated case order.

At a final value, check the result type with the remaining bound. A stuck operation or an unlisted request rejects the description if there is enough bound left to observe it. Reaching the bound without a failure makes no claim about what happens next.

For a request left to the surroundings, first check its name and target against the effect list, using one observation. With the remaining bound, check its inputs against the request's declared input types. For every answer fitting its declared answer type at that bound, examine the remaining computation at that bound too. Do the same for every smaller remaining bound. A listed named request is examined assuming its required handler is supplied; this does not make a missing target valid in a running program.

A value belongs to a type when it meets these descriptions at every finite bound. A function fits a signature when this holds for every allowed choice of its free variables. Quantified identities are chosen where their `for` occurs, including inside an input type.

The bounds describe how much of a value's behaviour we examine. They aren't inputs to programs, limits on their running time, or a cost model. A computation may run forever. Any finite failure must eventually be visible at a large enough bound.

Every recursive return to a data declaration passes through a constructor, and examining a function or a request's answer also uses a smaller bound. Recursive references can therefore occur through function inputs as well as outputs, through effect declarations, and through type arguments. `Moves` can keep unfolding through functions; `Rival` can take another `Rival` as input.

### Names and grouping

Each global name has one declaration or definition, and a defined function has at most one signature. Each constructor belongs to one type declaration, and each request to one effect declaration. Types, effects, and variables in type descriptions start with capitals; names used in expressions start without capitals. `Target` and `for` are reserved type notation. The words `def`, `fn`, `case`, `let`, `effect`, `handle`, `with` and `as` belong to the notation and cannot be used as names.

A definition can use any defined name, including itself or a name defined later. A type or effect declaration can refer to itself or other declared types and effects. Recursive types are read by the finite observations above.

In a pattern, a declared constructor means that constructor. Any other name is a variable. The variables of one case are distinct, including the variable for the rest in a request case. They cannot reuse a constructor, request or defined function's name.

A case's variables are in scope in its body, including functions inside it. In `handle e as x with h | …`, `x` is in scope in `e`, not in the cases `h`; it follows the same restrictions on variable names. The nearest binding determines what a name means. A name in an expression with no such binding must be declared or defined.

A constructor or request uses its declared number of contents or inputs, also in patterns. All cases of a function have the same number of patterns. Applying it to a different number of inputs gets stuck.

Parentheses group an expression or a type. Function and handler bodies extend to the right within their surrounding input, case or group. An ungrouped `|` continues the nearest function or handler; group an inner body when a following case belongs to an outer one. In `r@e(e, …)`, group the target if it is more complicated than a name, so that the request's input parentheses remain distinct.

### Working out an expression

In an application, first work out what is being applied, then its inputs from left to right. In data or an unaddressed request, work out the contents or inputs from left to right. In an addressed request, first work out the target, then its inputs from left to right; a target expression that gives anything other than a target gets stuck. Nothing happens inside a function until it is applied.

Entering `handle e as x with h | …` takes one step. Make a target distinct from every target already made, replace the bound occurrences of `x` in `e` with it, and start the handler around that expression. The running handler keeps that target. An unnamed handler needs no fresh target; work directly on its expression.

Applying a function to values takes one step. Find the first case whose patterns all match. The application becomes that case's body, with each matched value put wherever its variable means it. A defined function uses the cases of its definition in the same way. All replacements preserve bindings: rename inner bound variables if needed so that they cannot capture a variable in a value being inserted. This includes bindings introduced by named handlers.

Matching follows the shape of data. A constructor pattern needs the same constructor, with each content matching its pattern. A variable matches the whole value at that place, including a function or target. If no case matches, applying gets stuck. Applying data or a target also gets stuck.

When a handled expression becomes a value, the handler takes one step to the body of its first matching value case. Put the matched values in place in that body. With no matching value case, it gets stuck.

When the next work is an unaddressed request with value inputs, find the nearest surrounding unnamed handler with a case for that request's name. For an addressed request, find the nearest active handler with that target. Other handlers are passed through. A handler with that target must have a case for the request's name; otherwise the request gets stuck.

The selected handler takes one step to the body of its first matching request case. Put the inputs' matched values in place, and give the extra variable the rest of the work. If it has cases for the name but none of their patterns matches, it gets stuck. It doesn't try an outer handler instead.

The rest is a function taking one answer. Its body is the interrupted expression with that answer where the request was, surrounded by the same handler. Intervening handlers that let the request pass are kept too. Keep all their existing targets: resuming doesn't repeat their fresh-target creation. Use a fresh name for the answer so it cannot interfere with any existing variable. The request case itself runs outside the handler just used and outside the intervening handlers saved in the continuation.

With no handler for an unaddressed request, evaluation waits for the outside. Its answer replaces the request, and evaluation continues. An addressed request with no active handler for its target gets stuck. Keeping a target value alone doesn't keep a handler active; keeping a continuation can, because applying it reinstalls its saved handlers. A whole computation therefore has four possibilities: it returns a value, gets stuck, waits for an answer, or continues forever.

### The shape to remember

| Piece | How it is made | How it is used |
|---|---|---|
| Data | A constructor holds values | A pattern takes them apart |
| A function | Cases connect patterns to bodies | Application supplies inputs |
| A request | An operation asks with inputs | A handler supplies a response and controls continuation |
| A target | Entering a named handler makes a fresh identity | A request uses it to select that handler |

Names let functions refer to themselves and to one another. Types make contents, inputs, results and required handlers visible. The larger examples combine these pieces: state passes a value between requests; a generator keeps the rest for later.

### Why these pieces

Functions alone could already compute anything; even hands and numbers can be written as functions. The core gives data its own form anyway, so that it can be seen.

Requests and handlers let us ask for something where it is needed, without passing the changing state or the remaining work through every function on the way. Targets let separately supplied handlers be chosen without changing their request names. A target can be passed directly or kept inside a function, so intervening code need not know which handler it uses. Errors, search, state, generators and taking turns combine these forms.

Types describe what kinds of things we mean. They don't change what a program does.

The readable forms let us use these constructions without expanding them at every use. When an idea needs a closer look, expand the relevant part and follow its core rules; the rest can stay in the shorter spelling.

So when you meet something new, whether a feature of another language or a precise idea of your own, ask what data, what functions and what requests it involves, what kinds of things they are, and how they are combined.
