This file builds a language that can express any computation, one idea at a time. Each idea is added because of something the ideas before it can't do.

## 1. Values

To compute, there must first be something to compute with. These things are called values.

Every value has a type, and a type is made by listing its values, separated by |, which means "or":

    Bool : Type
    Bool = true : Bool | false : Bool

The colon reads "is a": Bool : Type says Bool is a type, and true : Bool says true is a Bool. So the second line lists Bool's values: true, a Bool, or false, a Bool.

The values listed are the only values the type has: a Bool is true or false, and nothing else is a Bool.

A type can list as many values as it needs:

    Color : Type
    Color = red : Color | green : Color | blue : Color

Now there are values, but they only sit there: nothing turns one into another.

## 2. Functions

A function turns a value into another. It is made by listing cases, one for each value it can be given:

    not : Bool → Bool
    not = (true) ↦ false | (false) ↦ true

Bool → Bool is the type of functions that take a Bool and give back a Bool. ↦ reads "gives back", and | again means "or". So not is: given true, give back false, or given false, give back true.

These two lines are a definition: they make not a name that stands for this function. The first line says not's type, and the second which function not stands for.

A function is given an input by writing it after the function, in brackets. This is a call:

    not(true)

A call is a value: the one the function gives back. So not(true) is a Bool, not(true) : Bool.

To find which value a call is, it is worked out: the case for its input is found, and the call becomes what that case gives back. a >> b means a works out to b:

    not(true)
    >>  false        the case for true is (true) ↦ false

>> is only how this file shows working out; it is not part of the language.

Each value a function can be given must have exactly one case, so that the function always has exactly one value to give back. A type has no values other than the ones it lists, so a case for each of them covers every input: not has two cases, because Bool has two values.

What a function gives back can be of another type than what it takes:

    warm : Color → Bool
    warm = (red) ↦ true | (green) ↦ false | (blue) ↦ false

    warm(blue)
    >>  false        the case for blue is (blue) ↦ false

This is the first building block, choosing: a function gives back something different for each value it is given.

Now a value can be turned into another. But each function does only one small thing: not flips a Bool once, and warm says whether one color is warm.

## 3. Calls inside calls

A call is a value, so it can be written anywhere a value of its type can. In particular, it can be the input of another call:

    not(warm(red))        not warm, for red

not's case can't be found yet, because its input, warm(red), is not one of the listed values. But warm's input, red, is, so warm(red) is worked out first, right where it is, and the not around it stays:

    not(warm(red))
    >>  not(true)         the case for red is (red) ↦ true
    >>  false             the case for true is (true) ↦ false

Calls can go inside calls as deep as needed. Working out is always the same step: a call whose input is one of the listed values becomes what its case gives back, wherever that call is. The innermost call goes first, because it is the only one whose input is already a listed value:

    not(not(warm(red)))
    >>  not(not(true))
    >>  not(false)
    >>  true

A call can also be what a case gives back. This builds a new function out of ones that already exist:

    cool : Color → Bool
    cool = (red) ↦ not(warm(red)) | (green) ↦ not(warm(green)) | (blue) ↦ not(warm(blue))

When a call becomes what its case gives back, and that is itself a call, it is worked out the same way:

    cool(green)
    >>  not(warm(green))     the case for green
    >>  not(false)           the input: the case for green is (green) ↦ false
    >>  true                 the case for false is (false) ↦ true

Working out stops once what is left is one of the listed values, like true.

This is the second building block, combining: calls go inside calls, so small functions are built into bigger ones.

Now functions can be built from other functions. But cool says the same thing three times, once for each color: not warm, for that color. There is no way yet to say it once, for whichever color is given.

## 4. Variables

A case can be for whichever value is given, instead of for one value. In place of the value it has a new name, called a variable, which stands for the value given:

    cool : Color → Bool
    cool = (x) ↦ not(warm(x))

x is a variable. The case (x) is for every Color, and what it gives back can use x wherever a Color could go.

A call to cool becomes what the case gives back, with x replaced by the input:

    cool(green)
    >>  not(warm(green))     x is replaced by green
    >>  not(false)
    >>  true

The input is worked out first, so x is always replaced by one of the listed values.

A variable can also be all that a case gives back:

    same : Bool → Bool
    same = (x) ↦ x

    same(false)
    >>  false                x is replaced by false

A case with a variable is for every value, so it is the function's only case: otherwise some value would have two cases.

A variable is a name, but unlike not or warm, it doesn't stand for one value given in advance. It stands for whichever value a call is given, and only in its own case.

Now a case can be written once, for every value. But a function still takes only one value, so there is no way yet to ask something about two values at once, like whether two Bools are both true.

## 5. Several inputs

A function can take several values at once. Its type lists them, separated by commas, and each case has one value for each:

    and : (Bool, Bool) → Bool
    and = (true, true) ↦ true | (true, false) ↦ false | (false, true) ↦ false | (false, false) ↦ false

(Bool, Bool) → Bool is the type of functions that take two Bools and give back a Bool. A call gives the inputs in the same order, separated by commas:

    and(true, false)
    >>  false            the case for true and false is (true, false) ↦ false

Each way the inputs can be given must have exactly one case. Two Bools can be given in four ways, so and has four cases.

A variable can go in any place, and then the case is for every value in that place. and gives back the second input when the first is true, and false when the first is false, so it can also be written with two cases:

    and = (true, y) ↦ y | (false, y) ↦ false

(true, y) is for true with any Bool, and (false, y) is for false with any Bool. Together they cover all four ways, each exactly once. So a case with a variable can now share a function with other cases, as long as each way the inputs can be given still has exactly one case.

All the inputs are worked out first, just like a single input:

    and(not(false), cool(red))
    >>  and(true, cool(red))           the case for false is (false) ↦ true
    >>  and(true, not(warm(red)))      x is replaced by red
    >>  and(true, not(true))           the case for red is (red) ↦ true
    >>  and(true, false)               the case for true is (true) ↦ false
    >>  false                          y is replaced by false

When more than one call can be worked out, it doesn't matter which goes first: the result is the same. This file goes from left to right.

The inputs can be of different types:

    choose : (Bool, Color, Color) → Color
    choose = (true, x, y) ↦ x | (false, x, y) ↦ y

Given true it gives back the first color, and given false the second. The variables in one case need different names, so that each stands for its own input.

Now a function can take several values. But it can still give back only one, so there is no way yet to give back two values, like a color together with whether it is warm.

## 6. Values that hold values

A listed value can take inputs, like a function. Then it holds them, and together they are one value:

    Pair : Type
    Pair = pair : (Color, Bool) → Pair

pair takes a Color and a Bool, and gives back a Pair that holds them:

    pair(red, true) : Pair

A call to pair doesn't become anything else: pair(red, true) is itself the value, holding red and true. Values listed this way are called constructors, since each one constructs values of its type. true, false, red, green and blue are constructors too, ones that take no inputs.

Now a function can give back two values, held in one:

    tag : Color → Pair
    tag = (x) ↦ pair(x, warm(x))

    tag(red)
    >>  pair(red, warm(red))      x is replaced by red
    >>  pair(red, true)           the case for red is (red) ↦ true

A constructor's inputs are worked out first, like a function's. A value made only of constructors, like pair(red, true), is finished: working out stops there. From now on, a call's case is found once each of its inputs is finished.

To get the values back out, a case can be for a constructor with variables in place of its inputs:

    color : Pair → Color
    color = (pair(x, y)) ↦ x

    color(pair(blue, false))
    >>  blue                      x is replaced by blue, and y by false

The case (pair(x, y)) is for every Pair, because every Pair is made by pair.

What a case is for is called a pattern: a value with variables in place of some of its parts. A value matches a pattern if it was made the same way, except where the pattern has a variable: there it can be anything, and the variable stands for what is there:

    pair(red, y)          matches a Pair that holds red, with any Bool
    pair(x, true)         matches a Pair that holds true, with any Color
    pair(x, y)            matches every Pair
    x                     matches every value

This is the third building block, making: a constructor puts values together into one value.

Now values can hold other values. But each type still has only so many values: Pair has six, one for each Color with each Bool. Counting needs a value for every number, with no end.

## 7. Values that hold their own type

A constructor's input can be of the very type being made. Then a value can hold another value of its own type, which can hold another, with no end:

    Nat : Type
    Nat = zero : Nat | succ : Nat → Nat

Nat is short for natural number. succ, short for successor, takes a number and gives back the one after it:

    zero                  0
    succ(zero)            1
    succ(succ(zero))      2
    ...                   and so on, one for each number

So two constructors make a value for every number.

A name can stand for any value, not just a function, so numbers can be given names:

    one : Nat
    one = succ(zero)

    two : Nat
    two = succ(one)

    three : Nat
    three = succ(two)

A name means the same as the value it stands for, so two is succ(succ(zero)).

Patterns can look inside a number:

    before : Nat → Nat
    before = (zero) ↦ zero | (succ(n)) ↦ n

    before(three)
    >>  two                   three is succ(two), so n is replaced by two

Every Nat is made by zero or by succ, so these two cases cover every number.

A list is made the same way. A list of numbers is either empty, or a number in front of another list:

    NatList : Type
    NatList = empty : NatList | cons : (Nat, NatList) → NatList

    cons(one, cons(two, empty))      the list 1, 2

cons(n, rest) is the list with n in front of rest.

Now a type can have values with no end. But a function's cases only look a fixed depth inside: before looks one succ deep. Adding two numbers needs to go as deep as one of them is, however deep that is.

## 8. Functions that call themselves

A definition can use its own name, so a case can give back a call to the function being defined. Adding works this way: adding zero to n gives n, and adding one more than m to n gives one more than adding m to n:

    add : (Nat, Nat) → Nat
    add = (zero, n) ↦ n | (succ(m), n) ↦ succ(add(m, n))

    add(two, one)
    >>  succ(add(one, one))           m is replaced by one, and n by one
    >>  succ(succ(add(zero, one)))    m is replaced by zero, and n by one
    >>  succ(succ(one))               n is replaced by one

which is three. Each time add calls itself, m is one smaller, so it reaches zero, and the calls stop.

Lists work the same way. length counts the numbers in a list:

    length : NatList → Nat
    length = (empty) ↦ zero | (cons(n, rest)) ↦ succ(length(rest))

    length(cons(one, cons(two, empty)))
    >>  succ(length(cons(two, empty)))
    >>  succ(succ(length(empty)))
    >>  succ(succ(zero))

which is two.

A definition can also use any other name, even one defined after it, so functions can call each other too.

This is the fourth building block, repeating: a function calls itself, through its name.

Now a function can go as deep as it needs. But nothing makes it call itself on something smaller.

## 9. Running forever

A function can call itself without anything getting smaller:

    loop : Bool → Bool
    loop = (x) ↦ loop(x)

    loop(true)
    >>  loop(true)
    >>  loop(true)
    >>  ...

This never finishes. It is still something the language can write; it just never gives back a value.

Since inputs are worked out first, a call never finishes if one of its inputs doesn't, even when its case doesn't use it:

    and(false, loop(true))        never finishes, though (false, y) ↦ false doesn't use y

Only the case that is found is worked out, so a call that might never finish can be kept from being worked out by putting it in a case:

    safe : Bool → Bool
    safe = (true) ↦ true | (false) ↦ loop(false)

    safe(true)
    >>  true                  loop(false) is never reached

Running forever can't be left out. A language that can compute anything can also run forever, and Alan Turing showed that there is no general way to tell, without working it out, whether a call will finish.

Each step so far added something the language couldn't do. What is left is to see whether anything is still missing.

## 10. Nothing is missing

To see that nothing is missing, the language can carry out a machine that is known to compute anything any computer can.

The machine has two counters, A and B, each holding a number, and a program: a list of instructions, numbered from zero.

    Instr : Type
    Instr = incA : Nat → Instr
          | incB : Nat → Instr
          | decA : (Nat, Nat) → Instr
          | decB : (Nat, Nat) → Instr
          | stop : Instr

    incA(i)        add one to A, then go to instruction i
    incB(i)        add one to B, then go to instruction i
    decA(i, j)     if A is zero, go to instruction j; otherwise take one from A, then go to instruction i
    decB(i, j)     the same, for B
    stop           stop, and give back what A holds

A type's constructors can be listed over several lines, as Instr's are.

    Program : Type
    Program = end : Program | then : (Instr, Program) → Program

at finds the instruction at place i, and gives back stop if there is none:

    at : (Program, Nat) → Instr
    at = (end, i) ↦ stop
       | (then(s, rest), zero) ↦ s
       | (then(s, rest), succ(i)) ↦ at(rest, i)

run carries out program p from instruction i, with x in A and y in B. exec carries out one instruction, then goes back to run for the next:

    run : (Program, Nat, Nat, Nat) → Nat
    run = (p, i, x, y) ↦ exec(p, at(p, i), x, y)

    exec : (Program, Instr, Nat, Nat) → Nat
    exec = (p, incA(i), x, y) ↦ run(p, i, succ(x), y)
         | (p, incB(i), x, y) ↦ run(p, i, x, succ(y))
         | (p, decA(i, j), zero, y) ↦ run(p, j, zero, y)
         | (p, decA(i, j), succ(x), y) ↦ run(p, i, x, y)
         | (p, decB(i, j), x, zero) ↦ run(p, j, x, zero)
         | (p, decB(i, j), x, succ(y)) ↦ run(p, i, x, y)
         | (p, stop, x, y) ↦ x

A program that adds B to A:

    addB : Program
    addB = then(decB(one, two), then(incA(zero), then(stop, end)))

    0    decB(one, two)    if B is zero, go to 2; otherwise take one from B, then go to 1
    1    incA(zero)        add one to A, then go to 0
    2    stop              give back what A holds

Here each >> skips the steps of at and exec in between, and numbers are written by their names:

    run(addB, zero, two, one)
    >>  run(addB, one, two, zero)        decB(one, two): B is one, so take one, then go to 1
    >>  run(addB, zero, three, zero)     incA(zero): add one to A, then go to 0
    >>  run(addB, two, three, zero)      decB(one, two): B is zero, so go to 2
    >>  three                            stop: give back what A holds

Marvin Minsky showed that a machine with just these instructions and two counters can carry out any computation any computer can, once its input is written as a number in the right way. run carries out any such program, given to it as a value. So everything computable can be computed with what steps 1 to 8 built. Nothing is missing.

Everything the language computes is built from four things, and the machine uses all four:

    making       constructors put values together into one value       then(stop, end), succ(x)
    choosing     a function gives back something different per case    exec has a case for each instruction
    combining    calls go inside calls                                 exec(p, at(p, i), x, y)
    repeating    a function calls itself, through its name             run and exec call each other

The rest of this file computes nothing new. It adds ways to write a program once, instead of many times.

## 11. Functions as inputs

Two functions can be the same except for one step:

    double : Nat → Nat
    double = (n) ↦ add(n, n)

    doubleAll : NatList → NatList
    doubleAll = (empty) ↦ empty | (cons(x, rest)) ↦ cons(double(x), doubleAll(rest))

    succAll : NatList → NatList
    succAll = (empty) ↦ empty | (cons(x, rest)) ↦ cons(succ(x), succAll(rest))

They differ only in double and succ. Each new step, like tripling every number, would need the whole function written again.

A function's type is a type too, so it can be the type of an input. Then the step that differs can be given as an input:

    map : (Nat → Nat, NatList) → NatList
    map = (f, empty) ↦ empty | (f, cons(x, rest)) ↦ cons(f(x), map(f, rest))

map takes a Nat → Nat, a function from numbers to numbers, and a list, and gives each number in the list to the function. Its input f is matched by a variable: a function isn't made by constructors, so there is nothing inside it to look at. But f can be called, like any function. A function given as an input is finished as it is.

    map(succ, cons(one, cons(two, empty)))
    >>  cons(succ(one), map(succ, cons(two, empty)))         f is replaced by succ, x by one, and rest by cons(two, empty)
    >>  cons(succ(one), cons(succ(two), map(succ, empty)))
    >>  cons(succ(one), cons(succ(two), empty))

which is cons(two, cons(three, empty)). succ is a constructor, but it takes a Nat and gives back a Nat, so it can be given as a Nat → Nat.

Now doubleAll(l) is map(double, l), and succAll(l) is map(succ, l). Each new step needs one call, not a new function.

This doesn't let the language compute anything new: doubleAll and succAll could already be written. It only saves writing them out again.

But the function given has to be one already written, with a name. To add a number n to every number in a list, map needs a function that adds n, and that function can't be written until n is known.

## 12. Functions made while working out

A function written with ↦ is a value, so it can be written anywhere a value can, without a name. Written inside a case, it can use that case's variables:

    addToAll : (Nat, NatList) → NatList
    addToAll = (n, l) ↦ map((m) ↦ add(n, m), l)

(m) ↦ add(n, m) is a function with no name: given m, it gives back add(n, m). It uses n, so when n is replaced, the new function holds what n was replaced by:

    addToAll(two, cons(one, empty))
    >>  map((m) ↦ add(two, m), cons(one, empty))                        n is replaced by two, and l by cons(one, empty)
    >>  cons(((m) ↦ add(two, m))(one), map((m) ↦ add(two, m), empty))
    >>  cons(add(two, one), map((m) ↦ add(two, m), empty))              m is replaced by one
    >>  cons(three, map((m) ↦ add(two, m), empty))                      the steps of add, as before
    >>  cons(three, empty)

A function can also give back a function:

    adder : Nat → (Nat → Nat)
    adder = (n) ↦ ((m) ↦ add(n, m))

    adder(two)
    >>  (m) ↦ add(two, m)          n is replaced by two

A function is finished as it is: what it gives back is only worked out once it is called. So addToAll could also be written (n, l) ↦ map(adder(n), l).

A function made this way holds only its cases and the values its variables were replaced by. So it can be written as data instead:

    Adder : Type
    Adder = adding : Nat → Adder

    apply : (Adder, Nat) → Nat
    apply = (adding(n), m) ↦ add(n, m)

adding(two) holds two, as (m) ↦ add(two, m) does, and apply(adding(two), one) >> add(two, one), as calling it would. Any program that makes functions while working out can be rewritten this way, with steps 1 to 8 alone. That is why this computes nothing new.

Now a function can be made wherever it is needed. But every list so far holds numbers. A list of colors would need a type of its own, like NatList with Color in place of Nat, and a map of its own, with the same cases written out again.

## 13. Types that take types

A type can take a type as an input, as a function takes a value. Then it gives back a type for each type it is given:

    List : (A : Type) → Type
    List = empty : List(A) | cons : (A, List(A)) → List(A)

(A : Type) → Type is the type of things that take a type and give back a type. The input is named A, so that what comes after can use it. List(A) is the type of lists that hold A's: each is either empty, or an A in front of another list of A's.

    List(Nat)        lists of numbers, like NatList
    List(Color)      lists of colors

The same constructors make both:

    cons(one, cons(two, empty)) : List(Nat)
    cons(red, cons(blue, empty)) : List(Color)

A function's type can also use a name for a type it isn't given as an input. Then the name stands for whichever type the inputs have, and the function is written once, for every type:

    map : (A → B, List(A)) → List(B)
    map = (f, empty) ↦ empty | (f, cons(x, rest)) ↦ cons(f(x), map(f, rest))

A and B are not inputs: a call gives map only a function and a list. They stand for the types those have. Given warm, a Color → Bool, A is Color and B is Bool, so a list of colors becomes a list of Bools:

    map(warm, cons(red, cons(blue, empty)))
    >>  cons(warm(red), map(warm, cons(blue, empty)))     f is replaced by warm, x by red, and rest by cons(blue, empty)
    >>  cons(true, map(warm, cons(blue, empty)))          the case for red is (red) ↦ true
    >>  cons(true, cons(warm(blue), map(warm, empty)))    f is replaced by warm, x by blue, and rest by empty
    >>  cons(true, cons(false, map(warm, empty)))         the case for blue is (blue) ↦ false
    >>  cons(true, cons(false, empty))                    the case for empty

The cases are the same as map's in step 11; only its type changed. Types say which values can go where, and take no part in working out.

This computes nothing new either: List(Color) and a map for it could be written out by hand, as NatList and its map were. It only saves writing them again for each type.

That is the whole language. Steps 1 to 8 can compute anything computable, and steps 11 to 13 let a program be written once instead of many times.
