The constructions programming languages are built from, each written using only the rules of [base.md](base.md). Nothing here adds a rule: each construction is types made with | and functions made of cases.

Python and Rust stand for all programming languages. Each section says what it explains in each, then builds it. The types and functions used are the ones in [example-types.md](example-types.md) and [example-functions.md](example-functions.md). Each section uses only what comes before it.

## 1. Data

Records, choices, lists and maps. Python: tuples, `dict`, `list`. Rust: `struct`, `enum`, `Vec`.

A record is a type with one constructor; a choice is a type with several.

    Point : Type
    Point = point : (x : Nat, y : Nat) → Point                                   Rust: struct Point { x, y }    Python: (x, y)

    Shape : Type
    Shape = circle : (r : Nat) → Shape | rect : (w : Nat, h : Nat) → Shape        Rust: enum Shape { Circle(r), Rect(w, h) }

Lists are List, from example-types.md. Rust's `Option` is Maybe.

A map is a list of keys, each with its value. Keys are compared with a function, so they need one.

    eqNat : (a : Nat, b : Nat) → Bool
    eqNat = (zero, zero) ↦ true | (zero, succ(b)) ↦ false | (succ(a), zero) ↦ false | (succ(a), succ(b)) ↦ eqNat(a, b)

    Map : Type
    Map = empty : Map | entry : (key : Nat, val : Nat, rest : Map) → Map         Python: {key: val, ...}

    lookup : (m : Map, k : Nat) → Maybe(Nat)
    lookupAt : (found : Bool, v : Nat, rest : Map, k : Nat) → Maybe(Nat)
    lookup = (empty, k) ↦ nothing(Nat) | (entry(j, v, rest), k) ↦ lookupAt(eqNat(j, k), v, rest, k)
    lookupAt = (true, v, rest, k) ↦ just(Nat, v) | (false, v, rest, k) ↦ lookup(rest, k)

    lookup(entry(zero, two, empty), zero)  >>  just(Nat, two)                    Python: {0: 2}[0]

lookupAt is how `if` is written: a function made of cases on Bool. Its branches are given as what they need, not as values worked out already: inputs are worked out first, so lookup(rest, k) as an input would be worked out even when the key is found.

## 2. State passing

A variable that changes. Python: `total = total + 1`. Rust: `let mut`.

Nothing in base changes. Instead, each step takes the current value as an input, and passes the new one to the next step.

In Python:

    total = 0
    for i in range(1, n + 1):
        total = total + i

In base, the loop is a function that calls itself, with total as an input:

    sumTo : (i : Nat, total : Nat) → Nat
    sumTo = (zero, total) ↦ total | (succ(i), total) ↦ sumTo(i, plus(total, succ(i)))

    sumTo(two, zero)  >>  sumTo(succ(zero), two)  >>  sumTo(zero, succ(two))  >>  succ(two)

A function that changes something outside itself takes the current state, and gives back its result together with the new state. A step is such a function:

    Step : (S : Type, A : Type) → Type
    Step = (S, A) ↦ (s : S) → Pair(A, S)                                         given the state, gives back a result and the new state

Four functions do all the passing, so it is written in one place only:

    done : (S : Type, A : Type, a : A) → Step(S, A)
    done = (S, A, a) ↦ ((s) ↦ pair(A, S, a, s))                                  gives back a, leaves the state as it is

    then : (S : Type, A : Type, B : Type, m : Step(S, A), k : (a : A) → Step(S, B)) → Step(S, B)
    next : (S : Type, A : Type, B : Type, p : Pair(A, S), k : (a : A) → Step(S, B)) → Pair(B, S)
    then = (S, A, B, m, k) ↦ ((s) ↦ next(S, A, B, m(s), k))                     do m, then k with m's result, in the state m left
    next = (S, A, B, pair(C, D, a, t), k) ↦ k(a)(t)

    get : (S : Type) → Step(S, S)
    get = (S) ↦ ((s) ↦ pair(S, S, s, s))                                         gives back the state

    put : (S : Type, t : S) → Step(S, Unit)
    put = (S, t) ↦ ((s) ↦ pair(Unit, S, unit, t))                                replaces the state with t

In Python:

    count = 0

    def tick():
        global count
        count = count + 1
        return count

In base:

    tick : Step(Nat, Nat)
    tick = then(Nat, Nat, Nat, get(Nat), (c) ↦ then(Nat, Unit, Nat, put(Nat, succ(c)), (u) ↦ done(Nat, Nat, succ(c))))

    tickTwice : Step(Nat, Nat)
    tickTwice = then(Nat, Nat, Nat, tick, (a) ↦ tick)

    tick(zero)  >>  ...  >>  pair(Nat, Nat, succ(zero), succ(zero))
    tickTwice(zero)  >>  ...  >>  pair(Nat, Nat, two, two)

Python keeps the state for you, hidden. Here it is an input and a result of every step.

## 3. Store

Places that hold values, and references to them. Python: two names for one object, `is`. Rust: `&`, `&mut`, `Box`.

A store is state, as in section 2, that maps addresses to values. An address is a Nat. A reference is an address.

    Mem : Type
    Mem = mem : (next : Nat, cells : Map) → Mem                                  the next free address, and what each address holds

    fetch : (m : Mem, a : Nat) → Nat
    fetch = (mem(n, cells), a) ↦ withDefault(Nat, zero, lookup(cells, a))        an address that holds nothing gives 0

    store : (m : Mem, a : Nat, v : Nat) → Mem
    store = (mem(n, cells), a, v) ↦ mem(n, entry(a, v, cells))                   the newest entry hides the older ones

As steps:

    new : (v : Nat) → Step(Mem, Nat)
    new = (v) ↦ ((mem(n, cells)) ↦ pair(Nat, Mem, n, mem(succ(n), entry(n, v, cells))))      a new place holding v; gives back its address

    read : (a : Nat) → Step(Mem, Nat)
    read = (a) ↦ ((m) ↦ pair(Nat, Mem, fetch(m, a), m))

    write : (a : Nat, v : Nat) → Step(Mem, Unit)
    write = (a, v) ↦ ((m) ↦ pair(Unit, Mem, unit, store(m, a, v)))

In Python, two names for one list:

    a = [1]
    b = a
    b[0] = 2
    a[0]        # 2

In base, b = a gives the same address another name: aliasWith is given it twice.

    aliasWith : (a : Nat, b : Nat) → Step(Mem, Nat)
    aliasWith = (a, b) ↦ then(Mem, Unit, Nat, write(b, two), (u) ↦ read(a))

    alias : Step(Mem, Nat)
    alias = then(Mem, Nat, Nat, new(succ(zero)), (a) ↦ aliasWith(a, a))

    alias(mem(zero, empty))  >>  ...  >>  pair(Nat, Mem, two, mem(succ(zero), entry(zero, two, entry(zero, succ(zero), empty))))

Python's `a is b` compares addresses, with eqNat. `a == b` compares what they hold, with fetch. Rust's `&x` is x's address, and `*r = v` is write(r, v).

## 4. Environments

Names, looked up while the program runs, and functions that keep names from where they were made. Python: scopes, closures. Rust: closures.

A function given back in base keeps the values it uses: adder in example-functions.md. That is a Rust `move` closure:

    byValue : (i : Nat) → ((u : Unit) → Nat)
    byValue = (i) ↦ ((u) ↦ i)                                                    Rust: move || i

A Python closure keeps the variable, not its value: the variable is a place in the store, and the closure reads it when called.

    byPlace : (a : Nat) → ((u : Unit) → Step(Mem, Nat))
    byPlace = (a) ↦ ((u) ↦ read(a))                                              Python: lambda: i, where i is at address a

In Python:

    i = 0
    f = lambda: i
    i = 2
    f()         # 2, not 0

In base:

    lateWith : (a : Nat, f : (u : Unit) → Step(Mem, Nat)) → Step(Mem, Nat)
    lateWith = (a, f) ↦ then(Mem, Unit, Nat, write(a, two), (u) ↦ f(unit))

    late : Step(Mem, Nat)
    late = then(Mem, Nat, Nat, new(zero), (a) ↦ lateWith(a, byPlace(a)))

    late(mem(zero, empty))  >>  ...  >>  pair(Nat, Mem, two, ...)                 byValue would have given 0

Python looks a name up in the local scope, then the enclosing ones, then the global one, then the built-in one. An environment is a list of maps, searched in order:

    find : (e : List(Map), k : Nat) → Maybe(Nat)
    findIn : (r : Maybe(Nat), rest : List(Map), k : Nat) → Maybe(Nat)
    find = (nil(M), k) ↦ nothing(Nat) | (cons(M, m, rest), k) ↦ findIn(lookup(m, k), rest, k)
    findIn = (just(N, v), rest, k) ↦ just(Nat, v) | (nothing(N), rest, k) ↦ find(rest, k)

    find(cons(Map, empty, cons(Map, entry(zero, two, empty), nil(Map))), zero)  >>  just(Nat, two)      not local, found in the next scope

## 5. Effects and handlers

A program that stops partway with a request, and some code around it that decides what to do. Python: `raise`, `try`, `yield`, `async`, `await`, `print`, `input`. Rust: `panic`, `?`, iterators, `async`, `.await`.

Each kind below is a type of programs that either finish, or stop with a request and the rest of the program. A handler is a function made of cases on that type. What the handler does with the rest of the program is what makes each kind different.

### Exceptions: the rest is thrown away

    Result : (A : Type) → Type
    Result = ok : (A : Type, a : A) → Result(A) | fail : (A : Type, e : Nat) → Result(A)      an answer, or an error with a code

    attempt : (A : Type, B : Type, m : Result(A), k : (a : A) → Result(B)) → Result(B)
    attempt = (A, B, ok(C, a), k) ↦ k(a) | (A, B, fail(C, e), k) ↦ fail(B, e)  a failure skips k, the rest

    recover : (A : Type, m : Result(A), h : (e : Nat) → A) → A
    recover = (A, ok(C, a), h) ↦ a | (A, fail(C, e), h) ↦ h(e)                   the handler

In Python:

    def pred1(n):
        if n == 0: raise ValueError
        return n - 1

    def minus2(n):
        return pred1(pred1(n))

    try:
        minus2(1)
    except ValueError:
        0

In base:

    pred1 : (n : Nat) → Result(Nat)
    pred1 = (zero) ↦ fail(Nat, zero) | (succ(k)) ↦ ok(Nat, k)

    minus2 : (n : Nat) → Result(Nat)
    minus2 = (n) ↦ attempt(Nat, Nat, pred1(n), (k) ↦ pred1(k))                  Rust: pred1(pred1(n)?)

    minus2(succ(zero))  >>  attempt(Nat, Nat, ok(Nat, zero), (k) ↦ pred1(k))  >>  pred1(zero)  >>  fail(Nat, zero)
    recover(Nat, minus2(succ(zero)), (e) ↦ zero)  >>  zero

Rust's `Result` is this type, and `?` is attempt.

### Generators: the rest is kept, and resumed once per value

    Gen : Type
    Gen = finish : Gen | yield : (n : Nat, rest : (u : Unit) → Gen) → Gen      gave n; the rest waits until it is asked for

rest is a function so that it is not worked out yet: that is the pause.

In Python:

    def count_from(n):
        while True:
            yield n
            n += 1

In base:

    countFrom : (n : Nat) → Gen
    countFrom = (n) ↦ yield(n, (u) ↦ countFrom(succ(n)))

    take : (k : Nat, g : Gen) → List(Nat)
    take = (zero, g) ↦ nil(Nat) | (succ(k), finish) ↦ nil(Nat) | (succ(k), yield(n, rest)) ↦ cons(Nat, n, take(k, rest(unit)))

    take(two, countFrom(zero))  >>  ...  >>  cons(Nat, zero, cons(Nat, succ(zero), nil(Nat)))

take is the handler: a Python `for` loop, or calls of `next`. Rust's `Iterator::next` gives back `Some(n)` with the rest kept inside, or `None`.

### Async: the rest is kept, and a scheduler takes turns

    Task : Type
    Task = end : (n : Nat) → Task | pause : (rest : (u : Unit) → Task) → Task  finished with n, or paused at an await

    slow : (n : Nat, k : Nat) → Task
    slow = (n, zero) ↦ end(n) | (n, succ(k)) ↦ pause((u) ↦ slow(n, k))          pauses k times, then finishes with n

    both : (a : Task, b : Task) → Pair(Nat, Nat)
    both = (end(x), end(y)) ↦ pair(Nat, Nat, x, y)
         | (end(x), pause(s)) ↦ both(end(x), s(unit))
         | (pause(r), end(y)) ↦ both(r(unit), end(y))
         | (pause(r), pause(s)) ↦ both(r(unit), s(unit))                         each goes on one step, in turn

In Python:

    async def slow(n, k):
        for _ in range(k):
            await asyncio.sleep(0)
        return n

    await asyncio.gather(slow(0, 2), slow(2, 0))       # (0, 2)

In base:

    both(slow(zero, two), slow(two, zero))  >>  ...  >>  pair(Nat, Nat, zero, two)

both is the handler: Python's event loop, or a Rust executor. A Rust `Future` is a Task: polling it gives back `Ready(n)`, which is end, or `Pending`, which is pause. In a real program, each pause also says what it waits for, such as a timer or a network reply.

### The shape all of them share

All three are one type, given what can be requested, and what answer each request gets back:

    Prog : (Req : Type, Ans : (r : Req) → Type, A : Type) → Type
    Prog = ret : (Req : Type, Ans : (r : Req) → Type, A : Type, a : A) → Prog(Req, Ans, A)
         | ask : (Req : Type, Ans : (r : Req) → Type, A : Type, r : Req, k : (x : Ans(r)) → Prog(Req, Ans, A)) → Prog(Req, Ans, A)

ret finishes with a. ask makes request r; k is the rest of the program, waiting for the answer.

    exceptions          a request is an error; its answer type is Empty, so k can never be called
    generators          a request is a value given; its answer is unit
    async               a request is what to wait for; its answer is unit, given once it is ready
    state               a request is get or put(n); get's answer is a Nat, put's is unit

Input and output are this too:

    IO : Type
    IO = output : (n : Nat) → IO | input : IO

    IOAns : (r : IO) → Type
    IOAns = (output(n)) ↦ Unit | (input) ↦ Nat

    echo : Prog(IO, IOAns, Unit)                                                 Python: print(input())
    echo = ask(IO, IOAns, Unit, input, (n) ↦ ask(IO, IOAns, Unit, output(n), (u) ↦ ret(IO, IOAns, Unit, unit)))

A handler given the inputs as a list, which gives back what was printed:

    runIO : (p : Prog(IO, IOAns, Unit), ins : List(Nat)) → List(Nat)
    runIO = (ret(R, N, A, a), ins) ↦ nil(Nat)
          | (ask(R, N, A, output(n), k), ins) ↦ cons(Nat, n, runIO(k(unit), ins))
          | (ask(R, N, A, input, k), nil(M)) ↦ nil(Nat)
          | (ask(R, N, A, input, k), cons(M, x, rest)) ↦ runIO(k(x), rest)

    runIO(echo, cons(Nat, two, nil(Nat)))  >>  ...  >>  cons(Nat, two, nil(Nat))

When a real program runs, the computer is the handler: it answers input with what was typed, and output by showing it.

## 6. Dispatch

Choosing a function by the kind of value. Python: methods, inheritance, `len`, `+` calling `__add__`. Rust: traits.

### Rust traits: the implementation is an input

A trait is a type of records of functions. An `impl` is a value of it.

    Len : (A : Type) → Type
    Len = withLen : (A : Type, len : (a : A) → Nat) → Len(A)                     Rust: trait Len { fn len(&self) -> usize; }

    listLen : (A : Type) → Len(List(A))
    listLen = (A) ↦ withLen(List(A), (l) ↦ length(A, l))                         Rust: impl<A> Len for Vec<A>

    lenOf : (A : Type, impl : Len(A), a : A) → Nat
    lenOf = (A, withLen(B, f), a) ↦ f(a)                                         Rust: fn len_of<T: Len>(x: &T)

    lenOf(List(Nat), listLen(Nat), cons(Nat, zero, nil(Nat)))  >>  succ(zero)

In Rust, `T: Len` is the input impl, and Rust finds its value for you from the type. Writing it in by hand gives the same program.

### Python objects: the methods travel with the value

An object holds its class and its fields. A class holds its methods, by name, and maybe a parent class.

    Obj : Type
    Class : Type

    Method : Type
    Method = (self : Obj) → Nat

    Obj = obj : (of : Class, fields : Map) → Obj
    Class = class : (methods : (name : Nat) → Maybe(Method), parent : Maybe(Class)) → Class

Calling a method looks for its name in the object's class, then in the parent, and so on:

    lookupMethod : (c : Class, name : Nat) → Maybe(Method)
    orParent : (found : Maybe(Method), p : Maybe(Class), name : Nat) → Maybe(Method)
    lookupMethod = (class(m, p), name) ↦ orParent(m(name), p, name)
    orParent = (just(F, f), p, name) ↦ just(Method, f)
             | (nothing(F), nothing(C), name) ↦ nothing(Method)
             | (nothing(F), just(C, c), name) ↦ lookupMethod(c, name)

    runMethod : (m : Maybe(Method), o : Obj) → Nat
    runMethod = (just(F, f), o) ↦ f(o) | (nothing(F), o) ↦ zero                  Python raises AttributeError; here 0

    send : (o : Obj, name : Nat) → Nat                                           Python: o.name()
    send = (obj(c, fs), name) ↦ runMethod(lookupMethod(c, name), obj(c, fs))

In Python:

    class Animal:
        def legs(self): return 4
        def heads(self): return 1

    class Bird(Animal):
        def legs(self): return 2

    Bird().legs()       # 2
    Bird().heads()      # 1, found in Animal

In base, with method 0 for legs and method 1 for heads:

    Animal : Class
    Animal = class((zero) ↦ just(Method, (o) ↦ four) | (succ(zero)) ↦ just(Method, (o) ↦ succ(zero)) | (succ(succ(k))) ↦ nothing(Method), nothing(Class))

    Bird : Class
    Bird = class((zero) ↦ just(Method, (o) ↦ two) | (succ(k)) ↦ nothing(Method), just(Class, Animal))

    send(obj(Bird, empty), zero)  >>  ...  >>  two
    send(obj(Bird, empty), succ(zero))  >>  ...  >>  succ(zero)

A class with several parents is searched the same way, once Python has put the parents in one order: the MRO. `a + b` is a method call too: `__add__`, looked up in a's class. `len(x)` calls `__len__`.

Obj takes itself inside the input of a function, Method. Base allows this, and so objects can be made. [extension.md](extension.md) does not count such types as evidence.

## 7. Dynamic types

One type for every value, with each value's kind checked while the program runs. Python: every value. Rust: rarely, `dyn Any`.

    Py : Type
    Py = pyint : (n : Nat) → Py | pynone : Py | pyfunc : (f : (x : Py) → Py) → Py | typeError : Py

Every operation has a case for each kind of value, and gives typeError where the kinds do not fit:

    pyAdd : (a : Py, b : Py) → Py
    addInt : (m : Nat, b : Py) → Py
    pyAdd = (pyint(m), b) ↦ addInt(m, b) | (pynone, b) ↦ typeError | (pyfunc(f), b) ↦ typeError | (typeError, b) ↦ typeError
    addInt = (m, pyint(n)) ↦ pyint(plus(m, n)) | (m, pynone) ↦ typeError | (m, pyfunc(f)) ↦ typeError | (m, typeError) ↦ typeError

    pyCall : (f : Py, x : Py) → Py
    pyCall = (pyfunc(f), x) ↦ f(x) | (pyint(n), x) ↦ typeError | (pynone, x) ↦ typeError | (typeError, x) ↦ typeError

    pyAdd(pyint(two), pyint(two))  >>  pyint(four)                              Python: 2 + 2
    pyAdd(pyint(two), pynone)  >>  typeError                                    Python: 2 + None
    pyCall(pyint(two), pynone)  >>  typeError                                   Python: 2(None)

In Python, a type error is raised, as in section 5; here it is a value. Python's `type(x)` and `isinstance` are cases on the constructor.

Py is the Value of example-types.md, and the Fun of [primitive.md](primitive.md), with a few more constructors. Python is close to primitive.md: one type, holding everything.

## 8. Code as data

Programs that make, change or run other programs. Python: `eval`. Rust: macros.

Code is a type made with |. Running it is a function made of cases on that type:

    Expr : Type
    Expr = lit : (n : Nat) → Expr | var : (k : Nat) → Expr | add : (a : Expr, b : Expr) → Expr | letE : (k : Nat, v : Expr, body : Expr) → Expr

    eval : (e : Expr, env : Map) → Nat
    eval = (lit(n), env) ↦ n
         | (var(k), env) ↦ withDefault(Nat, zero, lookup(env, k))
         | (add(a, b), env) ↦ plus(eval(a, env), eval(b, env))
         | (letE(k, v, body), env) ↦ eval(body, entry(k, eval(v, env), env))

    eval(add(var(zero), lit(succ(zero))), entry(zero, two, empty))  >>  ...  >>  succ(two)      Python: eval("x + 1", {"x": 2})

A macro is a function from code to code, used before the program runs:

    twiceE : (e : Expr) → Expr
    twiceE = (e) ↦ add(e, e)                                                     Rust: macro_rules! twice { ($e:expr) => { $e + $e } }

A Python decorator needs nothing new: it is a function that takes a function, like twice in example-functions.md.

## 9. Checks over code

Rules a program must pass before it runs. Rust: types, ownership, borrowing, lifetimes. Python: type hints, checked by tools outside the language.

A check is a function from code, as in section 8, to Bool. Rust's ownership rule says a value is moved when it is used, so a variable can be used at most once:

    oneIf : (b : Bool) → Nat
    oneIf = (true) ↦ succ(zero) | (false) ↦ zero

    uses : (k : Nat, e : Expr) → Nat
    usesUnder : (hidden : Bool, k : Nat, e : Expr) → Nat
    uses = (k, lit(n)) ↦ zero
         | (k, var(j)) ↦ oneIf(eqNat(k, j))
         | (k, add(a, b)) ↦ plus(uses(k, a), uses(k, b))
         | (k, letE(j, v, body)) ↦ plus(uses(k, v), usesUnder(eqNat(k, j), k, body))
    usesUnder = (true, k, e) ↦ zero | (false, k, e) ↦ uses(k, e)                a let of the same name hides k

    owned : (e : Expr) → Bool
    owned = (lit(n)) ↦ true
          | (var(k)) ↦ true
          | (add(a, b)) ↦ and(owned(a), owned(b))
          | (letE(k, v, body)) ↦ and(small(uses(k, body)), and(owned(v), owned(body)))      small: 0 or 1 uses

In Rust:

    let s = String::new();
    f(s, s);            // ✗ use of moved value: `s`

In base:

    owned(letE(zero, lit(two), add(var(zero), var(zero))))  >>  ...  >>  false
    owned(letE(zero, lit(two), add(var(zero), lit(two))))  >>  ...  >>  true

Rust's full rules are larger, but of the same kind. Numbers and other `Copy` values do not count as moved. A borrow `&s` is a use that does not move s; while it lasts, s cannot be changed. A borrow `&mut s` is the only use of s while it lasts. A lifetime is the part of the program a borrow lasts for. Rust's type checker is a function from code to its type, or to an error.

## 10. Concurrency

Several programs running at once, sharing a store. Python: threads. Rust: threads, `Send`, `Sync`, `Mutex`.

A thread is like a Task in section 5, but each step also uses the store:

    Thread : Type
    Thread = stop : Thread | act : (step : (m : Mem) → Pair(Thread, Mem)) → Thread      one step on the store, giving back the rest of the thread

x = x + 1 is two steps, a read and then a write:

    incr : (a : Nat) → Thread
    writeBack : (a : Nat, v : Nat) → Thread
    incr = (a) ↦ act((m) ↦ pair(Thread, Mem, writeBack(a, fetch(m, a)), m))
    writeBack = (a, v) ↦ act((m) ↦ pair(Thread, Mem, stop, store(m, a, succ(v))))

Base works out the same way every time. The order the threads take turns in comes from outside, as an input: a list saying which thread goes next.

    stepT : (t : Thread, m : Mem) → Pair(Thread, Mem)
    stepT = (stop, m) ↦ pair(Thread, Mem, stop, m) | (act(s), m) ↦ s(m)

    run2 : (order : List(Bool), t : Thread, u : Thread, m : Mem) → Mem
    afterFirst : (order : List(Bool), p : Pair(Thread, Mem), u : Thread) → Mem
    afterSecond : (order : List(Bool), t : Thread, p : Pair(Thread, Mem)) → Mem
    run2 = (nil(B), t, u, m) ↦ m
         | (cons(B, true, rest), t, u, m) ↦ afterFirst(rest, stepT(t, m), u)
         | (cons(B, false, rest), t, u, m) ↦ afterSecond(rest, t, stepT(u, m))
    afterFirst = (rest, pair(T, M, t, m), u) ↦ run2(rest, t, u, m)
    afterSecond = (rest, t, pair(T, M, u, m)) ↦ run2(rest, t, u, m)

    start : Mem
    start = mem(succ(zero), entry(zero, zero, empty))                            address 0 holds 0

    oneThenOther : List(Bool)
    oneThenOther = cons(Bool, true, cons(Bool, true, cons(Bool, false, cons(Bool, false, nil(Bool)))))

    interleaved : List(Bool)
    interleaved = cons(Bool, true, cons(Bool, false, cons(Bool, true, cons(Bool, false, nil(Bool)))))

In Python, two threads each running:

    x = x + 1

In base:

    fetch(run2(oneThenOther, incr(zero), incr(zero), start), zero)  >>  ...  >>  two
    fetch(run2(interleaved, incr(zero), incr(zero), start), zero)  >>  ...  >>  succ(zero)      both read 0: one update is lost

That is a race: the result depends on the order. A lock is a place in the store saying which thread holds it; a thread that finds it held pauses until it is free. Python's GIL runs one thread at a time, but switches between small steps, so x = x + 1 can still be split as above. Rust's `Send`, `Sync` and `Mutex` are a check, as in section 9: memory two threads share can only be reached through a lock, so no order loses an update.

## 11. Resources

How many steps a program takes, and how memory is given out and given back. Python: reference counting, garbage collection. Rust: `Drop`, the stack and the heap, `unsafe`.

Cost is the number of steps in working out. Two functions can give back the same value in very different numbers of steps:

    append : (A : Type, l : List(A), r : List(A)) → List(A)
    append = (A, nil(B), r) ↦ r | (A, cons(B, x, rest), r) ↦ cons(A, x, append(A, rest, r))

    slowRev : (A : Type, l : List(A)) → List(A)
    slowRev = (A, nil(B)) ↦ nil(A) | (A, cons(B, x, rest)) ↦ append(A, slowRev(A, rest), cons(A, x, nil(A)))

    fastRev : (A : Type, l : List(A), acc : List(A)) → List(A)
    fastRev = (A, nil(B), acc) ↦ acc | (A, cons(B, x, rest), acc) ↦ fastRev(A, rest, cons(A, x, acc))

For a list of n values, slowRev takes about n × n / 2 steps, and fastRev about n. slowRev(A, l) and fastRev(A, l, nil(A)) are the same value.

Memory is given back by removing a place from the store:

    remove : (m : Map, k : Nat) → Map
    keepUnless : (same : Bool, j : Nat, v : Nat, rest : Map) → Map
    remove = (empty, k) ↦ empty | (entry(j, v, rest), k) ↦ keepUnless(eqNat(j, k), j, v, remove(rest, k))
    keepUnless = (true, j, v, rest) ↦ rest | (false, j, v, rest) ↦ entry(j, v, rest)

    free : (a : Nat) → Step(Mem, Unit)
    free = (a) ↦ ((mem(n, cells)) ↦ pair(Unit, Mem, unit, mem(n, remove(cells, a))))

When to free is the hard part. Rust frees a value at the end of its owner's scope: section 9 makes sure nothing uses it after that, and `Drop` is a function run just before. Python keeps, for each place, a count of the references to it, and frees it when the count reaches 0. A garbage collector also finds places that only refer to each other.

Reading a place after it is freed gives 0 here, because base must say what every program does. A real machine says nothing: whatever is now at that address is read. That is what undefined behaviour means. On a real machine a place holds a byte, a value takes several places in a row, and an address is a number that can be added to. Safe Rust's checks make sure undefined behaviour never happens. Inside `unsafe`, making sure is left to the programmer.

## Reading a language through these

    Python                                   Rust
    tuple, dict, list            1           struct, enum, match, Vec       1
    x = x + 1                    2           let mut                        2
    two names for one object     3           &, &mut, Box                   3
    closures, scopes             4           closures, move                 4
    raise, try, yield, async     5           panic, ?, Iterator, async      5
    classes, methods, __add__    6           traits, impl                   6
    every value                  7           dyn Any                        7
    eval                         8           macros                         8
    type hints                   9           types, ownership, lifetimes    9
    threads, GIL                 10          threads, Send, Sync, Mutex     10
    reference counting           11          Drop, unsafe                   11

A feature is one of these, or several together. A Python class is data (1), in the store (3), with dispatch (6). A Rust `async fn` is an effect (5) that passes Rust's checks (9).

What is not here is not a construction but a fact: the exact rules of each feature, the libraries, the tools. Each one fits into a section above.
