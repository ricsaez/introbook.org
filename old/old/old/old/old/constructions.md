## Nothing

The simplest data type has one constructor, which takes no inputs:

    Unit : Type
    Unit = unit : Unit

Its one value, unit, carries no information. It is given where a value is needed but there is nothing to say.

## True and false

The simplest choice is between two things: a data type with two constructors, neither taking inputs.

    Bool : Type
    Bool = true : Bool | false : Bool

Functions on it have a case for each:

    not : (b : Bool) → Bool
    not = (true) ↦ false | (false) ↦ true

    and : (a : Bool, b : Bool) → Bool
    and = (true, b) ↦ b | (false, b) ↦ false

    or : (a : Bool, b : Bool) → Bool
    or = (true, b) ↦ true | (false, b) ↦ b

They are worked out like any call:

    not(and(true, false))
    >>  not(false)        first the input is worked out
    >>  true

A Bool can choose between two values of any type:

    if : (A : Type, b : Bool, t : A, e : A) → A
    if = (A, true, t, e) ↦ t | (A, false, t, e) ↦ e

Since every call works out its inputs first, if(A, b, t, e) works out both t and e before choosing. Cases work out only the result of the case that matches, so when t or e might never finish, the choice is made with cases instead.

## Pairs

Two values can be held together as one: a data type with one constructor that takes both.

    Pair : (A : Type, B : Type) → Type
    Pair = pair : (A : Type, B : Type, a : A, b : B) → Pair(A, B)

Functions take a pair apart with a single case:

    first : (A : Type, B : Type, p : Pair(A, B)) → A
    first = (A, B, pair(A, B, a, b)) ↦ a

    second : (A : Type, B : Type, p : Pair(A, B)) → B
    second = (A, B, pair(A, B, a, b)) ↦ b

    swap : (A : Type, B : Type, p : Pair(A, B)) → Pair(B, A)
    swap = (A, B, pair(A, B, a, b)) ↦ pair(B, A, b, a)

    swap(Bool, Unit, pair(Bool, Unit, true, unit))
    >>  pair(Unit, Bool, unit, true)

More than two values are held the same way: by a constructor that takes them all, or by pairs inside pairs.

## Numbers

A number is zero, or one more than a number:

    Nat : Type
    Nat = zero : Nat | succ : (n : Nat) → Nat

succ, short for successor, takes a number and gives back one more, so succ(succ(zero)) is two. Since succ takes a Nat, numbers are made from numbers, as many times as wanted.

Adding zero to n gives n. Adding one more than m gives one more than adding m:

    add : (m : Nat, n : Nat) → Nat
    add = (zero, n) ↦ n | (succ(m), n) ↦ succ(add(m, n))

add calls itself, each time with m one smaller, until m is zero, so it always finishes:

    add(succ(succ(zero)), succ(zero))          2 + 1
    >>  succ(add(succ(zero), succ(zero)))      1 + (1 + 1)
    >>  succ(succ(add(zero, succ(zero))))      1 + 1 + (0 + 1)
    >>  succ(succ(succ(zero)))                 3

Two numbers are the same if both are zero, or if both are one more than numbers that are the same:

    eq : (m : Nat, n : Nat) → Bool
    eq = (zero, zero) ↦ true
       | (zero, succ(n)) ↦ false
       | (succ(m), zero) ↦ false
       | (succ(m), succ(n)) ↦ eq(m, n)

    eq(succ(zero), succ(succ(zero)))           is 1 the same as 2?
    >>  eq(zero, succ(zero))                   is 0 the same as 1?
    >>  false

The first few numbers are given names, to write them shortly from here on:

    one : Nat
    one = succ(zero)

    two : Nat
    two = succ(one)

    three : Nat
    three = succ(two)

## Lists

A list is empty, or an item in front of a list:

    List : (A : Type) → Type
    List = empty : (A : Type) → List(A)
         | cons : (A : Type, a : A, rest : List(A)) → List(A)

cons, short for construct, puts a in front of rest. Like numbers, lists are made from lists, as many times as wanted.

The length of a list counts its items:

    length : (A : Type, l : List(A)) → Nat
    length = (A, empty(A)) ↦ zero | (A, cons(A, a, rest)) ↦ succ(length(A, rest))

    length(Bool, cons(Bool, true, cons(Bool, false, empty(Bool))))
    >>  succ(length(Bool, cons(Bool, false, empty(Bool))))
    >>  succ(succ(length(Bool, empty(Bool))))
    >>  succ(succ(zero))

append joins two lists, and map does f to each item:

    append : (A : Type, l : List(A), m : List(A)) → List(A)
    append = (A, empty(A), m) ↦ m
           | (A, cons(A, a, rest), m) ↦ cons(A, a, append(A, rest, m))

    map : (A : Type, B : Type, f : (a : A) → B, l : List(A)) → List(B)
    map = (A, B, f, empty(A)) ↦ empty(B)
        | (A, B, f, cons(A, a, rest)) ↦ cons(B, f(a), map(A, B, f, rest))

    map(Bool, Bool, not, cons(Bool, true, cons(Bool, false, empty(Bool))))
    >>  cons(Bool, false, cons(Bool, true, empty(Bool)))

## Optional values and errors

Where a pair holds one value and another, these hold one value or another.

A value that may be missing:

    Option : (A : Type) → Type
    Option = none : (A : Type) → Option(A) | some : (A : Type, a : A) → Option(A)

A function that may have nothing to give back gives back an Option(A): none(A) when there is nothing, some(A, a) when there is a. Zero has no number one less than it, and a list may have no item at a given place:

    pred : (n : Nat) → Option(Nat)
    pred = (zero) ↦ none(Nat) | (succ(m)) ↦ some(Nat, m)

    at : (A : Type, l : List(A), n : Nat) → Option(A)
    at = (A, empty(A), n) ↦ none(A)
       | (A, cons(A, a, rest), zero) ↦ some(A, a)
       | (A, cons(A, a, rest), succ(n)) ↦ at(A, rest, n)

pred, short for predecessor, gives back one less. at gives back the item at place n, counting from zero.

get takes an Option(A) and a fallback d. It gives back the value if there is one, and d if not:

    get : (A : Type, o : Option(A), d : A) → A
    get = (A, none(A), d) ↦ d | (A, some(A, a), d) ↦ a

    get(Nat, pred(two), zero)
    >>  get(Nat, some(Nat, one), zero)
    >>  one                   there is a value, so it is given back

    get(Nat, pred(zero), zero)
    >>  get(Nat, none(Nat), zero)
    >>  zero                  there is none, so the fallback is given back

A value, or an error saying why there is none:

    Result : (A : Type, E : Type) → Type
    Result = ok : (A : Type, E : Type, a : A) → Result(A, E)
           | err : (A : Type, E : Type, e : E) → Result(A, E)

Taking n from m fails when n is bigger, and the error says how much was missing:

    minus : (m : Nat, n : Nat) → Result(Nat, Nat)
    minus = (m, zero) ↦ ok(Nat, Nat, m)
          | (zero, succ(n)) ↦ err(Nat, Nat, succ(n))
          | (succ(m), succ(n)) ↦ minus(m, n)

    minus(one, three)
    >>  minus(zero, two)
    >>  err(Nat, Nat, two)    two short

chain carries on with f only if r is ok. An error is passed along as it is, and f is never called:

    chain : (A : Type, B : Type, E : Type, r : Result(A, E), f : (a : A) → Result(B, E)) → Result(B, E)
    chain = (A, B, E, ok(A, E, a), f) ↦ f(a)
          | (A, B, E, err(A, E, e), f) ↦ err(B, E, e)

Taking one from three, and then three from what is left:

    chain(Nat, Nat, Nat, minus(three, one), (k) ↦ minus(k, three))
    >>  chain(Nat, Nat, Nat, ok(Nat, Nat, two), (k) ↦ minus(k, three))
    >>  minus(two, three)
    >>  err(Nat, Nat, one)    the second step is one short

Taking three from one, and then one from what is left:

    chain(Nat, Nat, Nat, minus(one, three), (k) ↦ minus(k, one))
    >>  chain(Nat, Nat, Nat, err(Nat, Nat, two), (k) ↦ minus(k, one))
    >>  err(Nat, Nat, two)    the second step is never called

## Loops

repeat does f to a, n times:

    repeat : (A : Type, n : Nat, f : (a : A) → A, a : A) → A
    repeat = (A, zero, f, a) ↦ a
           | (A, succ(n), f, a) ↦ repeat(A, n, f, f(a))

    repeat(Bool, two, not, true)
    >>  repeat(Bool, one, not, false)
    >>  repeat(Bool, zero, not, true)
    >>  true

Each time, n is one smaller, so repeat always finishes.

while does f to a for as long as c holds, so it may never finish. It cannot use if, which would work out the next round before choosing, and so never stop. It chooses with cases instead, through a second function:

    while : (A : Type, c : (a : A) → Bool, f : (a : A) → A, a : A) → A
    while = (A, c, f, a) ↦ again(A, c, f, a, c(a))

    again : (A : Type, c : (a : A) → Bool, f : (a : A) → A, a : A, b : Bool) → A
    again = (A, c, f, a, true) ↦ while(A, c, f, f(a))
          | (A, c, f, a, false) ↦ a

    while(Bool, not, not, false)              while b is false, flip it
    >>  again(Bool, not, not, false, true)
    >>  while(Bool, not, not, true)
    >>  again(Bool, not, not, true, false)
    >>  true

With a c that always gives back true, while never finishes.

## Interfaces

An interface is a data type that holds functions. A function that takes one works for any type that comes with one.

Values that can be compared for sameness:

    Eq : (A : Type) → Type
    Eq = equality : (A : Type, same : (x : A, y : A) → Bool) → Eq(A)

    compare : (A : Type, e : Eq(A), x : A, y : A) → Bool
    compare = (A, equality(A, same), x, y) ↦ same(x, y)

Numbers come with one:

    natEq : Eq(Nat)
    natEq = equality(Nat, eq)

member asks whether a is in a list, for any type that comes with an Eq:

    member : (A : Type, e : Eq(A), a : A, l : List(A)) → Bool
    member = (A, e, a, empty(A)) ↦ false
           | (A, e, a, cons(A, b, rest)) ↦ or(compare(A, e, a, b), member(A, e, a, rest))

    member(Nat, natEq, two, cons(Nat, one, cons(Nat, two, empty(Nat))))
    >>  or(false, member(Nat, natEq, two, cons(Nat, two, empty(Nat))))      two is not one
    >>  or(false, or(true, false))                                          two is two, then the list ends
    >>  true

## State

State is a store of cells, each at an address and each holding a number. A store is a function from addresses to what they hold:

    Store : Type
    Store = (r : Nat) → Nat

    start : Store
    start = (r) ↦ zero

In start, every cell holds zero. Changing a cell gives back a new store, the same except at that address:

    set : (s : Store, r : Nat, v : Nat) → Store
    set = (s, r, v) ↦ (q) ↦ if(Nat, eq(q, r), v, s(q))

    set(start, zero, one)(zero)
    >>  if(Nat, eq(zero, zero), one, start(zero))
    >>  if(Nat, true, one, zero)
    >>  one

Nothing here changes: the old store is still there, and the new one is another value. Two names that hold the same address share a cell, so a change made through one is seen through the other.

A step takes a store, and gives back a value and the next store:

    Step : (A : Type) → Type
    Step = (A) ↦ (s : Store) → Pair(A, Store)

    read : (r : Nat) → Step(Nat)
    read = (r) ↦ (s) ↦ pair(Nat, Store, s(r), s)

    write : (r : Nat, v : Nat) → Step(Unit)
    write = (r, v) ↦ (s) ↦ pair(Unit, Store, unit, set(s, r, v))

write has nothing to say, so it gives back unit.

then does one step and then another, passing on the value and the store:

    then : (A : Type, B : Type, m : Step(A), f : (a : A) → Step(B)) → Step(B)
    then = (A, B, m, f) ↦ (s) ↦ pass(A, B, m(s), f)

    pass : (A : Type, B : Type, p : Pair(A, Store), f : (a : A) → Step(B)) → Pair(B, Store)
    pass = (A, B, pair(A, Store, a, s), f) ↦ f(a)(s)

Adding one to a cell is a read, then a write:

    increment : (r : Nat) → Step(Unit)
    increment = (r) ↦ then(Nat, Unit, read(r), (v) ↦ write(r, succ(v)))

    increment(zero)(start)
    >>  pass(Nat, Unit, read(zero)(start), (v) ↦ write(zero, succ(v)))
    >>  pass(Nat, Unit, pair(Nat, Store, zero, start), (v) ↦ write(zero, succ(v)))
    >>  write(zero, one)(start)
    >>  pair(Unit, Store, unit, set(start, zero, one))

## Control

A function can take, as an input, what happens next: a function to give its result to. Instead of giving back r, it gives back next(r).

With a second such input, for failure, it can choose where to go. Going to fail skips everything next would have done, and nothing has to pass the failure along:

    subtract : (R : Type, m : Nat, n : Nat, next : (k : Nat) → R, fail : (k : Nat) → R) → R
    subtract = (R, m, zero, next, fail) ↦ next(m)
             | (R, zero, succ(n), next, fail) ↦ fail(succ(n))
             | (R, succ(m), succ(n), next, fail) ↦ subtract(R, m, n, next, fail)

With outcomes that say what was left, or how much was missing:

    Outcome : Type
    Outcome = Result(Nat, Nat)

    left : (k : Nat) → Outcome
    left = (k) ↦ ok(Nat, Nat, k)

    short : (k : Nat) → Outcome
    short = (k) ↦ err(Nat, Nat, k)

Taking three from one, and then one from what is left, as with chain:

    subtract(Outcome, one, three, (k) ↦ subtract(Outcome, k, one, left, short), short)
    >>  subtract(Outcome, zero, two, (k) ↦ subtract(Outcome, k, one, left, short), short)
    >>  short(two)
    >>  err(Nat, Nat, two)        the second step is never reached

Returning early from a function, leaving a loop, and raising an error are all this: going to a different what happens next.

## Objects

An object is data holding functions that share a cell of the state. Whoever holds it can use the functions, but can reach the cell only through them.

    Counter : Type
    Counter = counter : (tick : Step(Unit), count : Step(Nat)) → Counter

    newCounter : (r : Nat) → Counter
    newCounter = (r) ↦ counter(increment(r), read(r))

tick adds one to cell r, and count reads it. Ticking twice, then counting:

    twice : (c : Counter) → Step(Nat)
    twice = (counter(t, n)) ↦ then(Unit, Nat, t, (u) ↦ then(Unit, Nat, t, (u) ↦ n))

    first(Nat, Store, twice(newCounter(zero))(start))
    >>  two

## Generators and threads

A generator gives back items one at a time. After each item, the rest of its work is paused: a function that takes unit, not worked out until it is called.

    Gen : (A : Type) → Type
    Gen = done : (A : Type) → Gen(A)
        | yield : (A : Type, a : A, rest : (u : Unit) → Gen(A)) → Gen(A)

The numbers from n upwards, without end:

    from : (n : Nat) → Gen(Nat)
    from = (n) ↦ yield(Nat, n, (u) ↦ from(succ(n)))

from(n) still finishes, since the call from(succ(n)) waits inside a function. take resumes a generator k times:

    take : (A : Type, k : Nat, g : Gen(A)) → List(A)
    take = (A, zero, g) ↦ empty(A)
         | (A, succ(k), done(A)) ↦ empty(A)
         | (A, succ(k), yield(A, a, rest)) ↦ cons(A, a, take(A, k, rest(unit)))

    take(Nat, two, from(zero))
    >>  cons(Nat, zero, take(Nat, one, from(one)))
    >>  cons(Nat, zero, cons(Nat, one, take(Nat, zero, from(two))))
    >>  cons(Nat, zero, cons(Nat, one, empty(Nat)))

Threads are generators taking turns. turns gives one item from g, then hands over to h:

    turns : (A : Type, g : Gen(A), h : Gen(A)) → Gen(A)
    turns = (A, done(A), h) ↦ h
          | (A, yield(A, a, rest), h) ↦ yield(A, a, (u) ↦ turns(A, h, rest(unit)))

    take(Nat, three, turns(Nat, from(zero), from(two)))
    >>  cons(Nat, zero, cons(Nat, two, cons(Nat, one, empty(Nat))))

zero comes from the first, two from the second, then one from the first again. Work that waits for something is paused the same way, and resumed when it can go on.

## The machine

A machine has a memory, which is a Store, and a program: a list of instructions, carried out one at a time.

    Instr : Type
    Instr = put : (r : Nat, n : Nat) → Instr
          | sum : (r : Nat, a : Nat, b : Nat) → Instr
          | dec : (r : Nat) → Instr
          | jumpz : (r : Nat, t : Nat) → Instr

    put(r, n)        put n in cell r
    sum(r, a, b)     put what cells a and b hold, added together, in cell r
    dec(r)           take one from cell r, unless it holds zero
    jumpz(r, t)      if cell r holds zero, go to instruction t

These four are enough to compute anything computable.

run carries out the instruction at place i, then the next. It also counts its steps, which is the cost of running the program:

    run : (p : List(Instr), i : Nat, s : Store, steps : Nat) → Pair(Store, Nat)
    run = (p, i, s, steps) ↦ exec(p, i, s, steps, at(Instr, p, i))

    exec : (p : List(Instr), i : Nat, s : Store, steps : Nat, o : Option(Instr)) → Pair(Store, Nat)
    exec = (p, i, s, steps, none(Instr)) ↦ pair(Store, Nat, s, steps)
         | (p, i, s, steps, some(Instr, put(r, n))) ↦ run(p, succ(i), set(s, r, n), succ(steps))
         | (p, i, s, steps, some(Instr, sum(r, a, b))) ↦ run(p, succ(i), set(s, r, add(s(a), s(b))), succ(steps))
         | (p, i, s, steps, some(Instr, dec(r))) ↦ run(p, succ(i), set(s, r, get(Nat, pred(s(r)), zero)), succ(steps))
         | (p, i, s, steps, some(Instr, jumpz(r, t))) ↦ run(p, if(Nat, eq(s(r), zero), t, succ(i)), s, succ(steps))

When there is no instruction at i, the machine stops. With jumpz, a program can go back to an earlier instruction, and may run forever.

A program that adds two and one:

    program : List(Instr)
    program = cons(Instr, put(zero, two),
              cons(Instr, put(one, one),
              cons(Instr, sum(zero, zero, one), empty(Instr))))

    first(Store, Nat, run(program, zero, start, zero))(zero)
    >>  three                 cell zero holds two and one, added

    second(Store, Nat, run(program, zero, start, zero))
    >>  three                 it took three steps

## The language

A program can itself be data. Functions and calls, the heart of the language, become a data type, with variables named by numbers:

    Expr : Type
    Expr = var : (x : Nat) → Expr
         | fun : (x : Nat, body : Expr) → Expr
         | call : (f : Expr, a : Expr) → Expr

    var(x)           the variable x
    fun(x, body)     a function with input x, giving back body
    call(f, a)       f given the input a

eval works out an Expr the way a call is worked out: first the function and the input, then the body, with the variable standing for the input. A variable that stands for nothing, or a call of something that is not a function, is left as it is.

    eval : (e : Expr) → Expr
    eval = (var(x)) ↦ var(x)
         | (fun(x, b)) ↦ fun(x, b)
         | (call(f, a)) ↦ apply(eval(f), eval(a))

    apply : (f : Expr, v : Expr) → Expr
    apply = (var(x), v) ↦ call(var(x), v)
          | (fun(x, b), v) ↦ eval(subst(x, v, b))
          | (call(g, a), v) ↦ call(call(g, a), v)

subst puts v in place of the variable x, except inside a function whose own input is also x:

    subst : (x : Nat, v : Expr, e : Expr) → Expr
    subst = (x, v, var(y)) ↦ if(Expr, eq(x, y), v, var(y))
          | (x, v, fun(y, b)) ↦ fun(y, if(Expr, eq(x, y), b, subst(x, v, b)))
          | (x, v, call(f, a)) ↦ call(subst(x, v, f), subst(x, v, a))

A function that gives back its input, given another function:

    eval(call(fun(zero, var(zero)), fun(one, var(one))))
    >>  apply(fun(zero, var(zero)), fun(one, var(one)))
    >>  eval(subst(zero, fun(one, var(one)), var(zero)))
    >>  eval(fun(one, var(one)))
    >>  fun(one, var(one))

Functions and calls alone can express anything computable, so eval can carry out any computation: the language, written in itself. Data and cases are added the same way, with a constructor in Expr and a case in eval for each.

## Compiling

A compiler turns a program into instructions for the machine. Here the programs are sums of numbers:

    Arith : Type
    Arith = num : (n : Nat) → Arith | plus : (a : Arith, b : Arith) → Arith

value works out a sum directly:

    value : (e : Arith) → Nat
    value = (num(n)) ↦ n | (plus(a, b)) ↦ add(value(a), value(b))

compile turns e into instructions that leave its value in cell t, using the cells above t to work in:

    compile : (e : Arith, t : Nat) → List(Instr)
    compile = (num(n), t) ↦ cons(Instr, put(t, n), empty(Instr))
            | (plus(a, b), t) ↦ append(Instr, compile(a, t),
                                  append(Instr, compile(b, succ(t)),
                                    cons(Instr, sum(t, t, succ(t)), empty(Instr))))

    compile(plus(num(two), num(one)), zero)
    >>  program

This is the program the machine ran above. Working the sum out directly, and running what it compiles to, give the same number:

    value(plus(num(two), num(one)))
    >>  three

    first(Store, Nat, run(compile(plus(num(two), num(one)), zero), zero, start, zero))(zero)
    >>  three

compile is right if the two agree for every e. Checking one e is a test; showing it for every e would be a proof.

## Input and output

A program that deals with the world outside is described as data: what to show, what to ask for, and what to do next.

    IO : (A : Type) → Type
    IO = finish : (A : Type, a : A) → IO(A)
       | show : (A : Type, n : Nat, next : (u : Unit) → IO(A)) → IO(A)
       | ask : (A : Type, next : (n : Nat) → IO(A)) → IO(A)

next is paused, as in a generator, until the outside is ready. Asking for a number, and showing one more:

    echo : IO(Unit)
    echo = ask(Unit, (n) ↦ show(Unit, succ(n), (u) ↦ finish(Unit, unit)))

Working out echo only gives back this data. Something outside the language reads it and carries it out. Inside the language, the outside can be stood in for by a list of answers, giving back what is shown:

    simulate : (A : Type, p : IO(A), answers : List(Nat)) → List(Nat)
    simulate = (A, finish(A, a), l) ↦ empty(Nat)
             | (A, show(A, n, next), l) ↦ cons(Nat, n, simulate(A, next(unit), l))
             | (A, ask(A, next), empty(Nat)) ↦ empty(Nat)
             | (A, ask(A, next), cons(Nat, n, rest)) ↦ simulate(A, next(n), rest)

    simulate(Unit, echo, cons(Nat, two, empty(Nat)))
    >>  simulate(Unit, show(Unit, three, (u) ↦ finish(Unit, unit)), empty(Nat))
    >>  cons(Nat, three, simulate(Unit, finish(Unit, unit), empty(Nat)))
    >>  cons(Nat, three, empty(Nat))
