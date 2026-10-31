    e ::= x                     placeholder
          (x, …) ↦ e            function, with body e
          e(e, …)               application

    v ::= (x, …) ↦ e            value

    ((x, …) ↦ e)(v, …)   becomes   e[x := v, …]       if as many v as x
    e(…)                 becomes   e'(…)              if e becomes e'
    v(…, e, …)           becomes   v(…, e', …)        if e becomes e', and all before e are values

    where
      ::=              the forms, one per line
      e, e', x, v      any expression, placeholder (a name, such as a), value; not part of the language
      x, …             any number of x, even none; likewise for every letter
      … alone          any number of e, even none
      letters, …       independent within a form; the same on both sides of a rule
      (x, …) ↦ e       the x, … all different; the body reaches as far as it can
      ( )              around an applied function: grouping only
      grouping         the rules, and [x := v, …], work on how what we write is grouped, not on how it is
                       written, and add ( ) wherever a result would otherwise be grouped another way
      x used in e      belongs to the nearest function around it that takes x; in what we write, there must be one
      e[x := v, …]     each v, in order, in place of the uses of its x that belong to this function
      becomes          one step; the three rules above are the only ones

---

    T = c | …                   data type, with constructors

    e ::= c                     constructor
          k | …                 function, with cases
    k ::= (p, …) ↦ e            case, with patterns and body e
    p ::= x                     placeholder
          c                     constructor

    v ::= c                     value
          k | …                 value

    (k | …)(v, …)        becomes   e[x := w, …]       if (p, …) ↦ e is the first case that v, … match, giving x := w, …

    v matches x          giving x := v
    c matches c          giving nothing

    where
      T, c, k, p, w    a data type (a name), constructor (a name), case, pattern, value; not part of the language
      c | …, k | …     one or more, separated by |
      T = c | …        each T declared once, and each c declared once
      x                a name not declared as a constructor
      (x, …) ↦ e       the function with one case, its patterns all placeholders; the first rule above the line is this rule for it
      (p, …) ↦ e       the placeholders in p, … all different; e reaches as far as it can, so a | after it belongs to e if e is a function
      ( )              also around a function that is a body with a | after it
      x used in e      belongs to the nearest case around it whose patterns take x
      v, … match p, …  as many v as p, each v matching its p; giving all that each gives
      e[x := w, …]     each w in place of the uses of its x that belong to this case
      becomes          one step; the rules above the line and this one are the only ones

    A ::= T                     data type
          (A, …) → B            function type

    c : T                       if T lists c
    x : A                       if the case x belongs to gives x : A
    e(e', …) : B                if e : (A, …) → B, and e', … : A, …
    (k | …) : (A, …) → B        if each case (p, …) ↦ e has p, … fit A, …, and e : B with what they give;
                                and the cases cover A, …

    x fits A                    giving x : A
    c fits A                    if c : A

    q, … covered                if q, … match some case
    q, … covered                if some x in q, … has x : T, and for each c that T lists, q, … with c in place of x are covered

    where
      A, B             any type; not part of the language
      q                a pattern, standing for the values that match it; not part of the language
      T and c          T names a type and c an expression, so a c may share a T's name
      e : A            e has type A
      T lists c        c is declared in T
      e, … : A, …      as many e as A, each e of its A, in order; likewise v, … : A, … and p, … fit A, …
      (A, …) → B       A, … its input types, B its result type; B reaches as far as it can
      q, … match       as v, … match p, …, so an x in q matches only an x
      new              used nowhere else
      cover A, …       new x, … : A, … are covered
      from around it   an x used in e is from around it if it belongs to no case in e
      if e : A         for an e that uses no x from around it: e is a value, or e becomes some e' : A
      checked          what we write, if it has a type

    T = d | …                   data type, with constructors
    d ::= c                     constructor, holding nothing
          c(A, …)               constructor, holding contents of types A, …

    v ::= c(v, …)               data
    p ::= c(p, …)               data built by c

    c : (A, …) → T              if T lists c(A, …)
    c(p, …) fits B              if c : (A, …) → B, and p, … fit A, …, giving what they give
    c(v, …) matches c(p, …)     if v, … match p, …, giving what they give

    where
      d                a constructor as declared; T = c | … above is T = d | … with every d holding nothing
      T lists d        d is declared in T; so T lists c only for a c holding nothing
      c(…)             at least one inside; in c(v, …), as many v as c holds
      c(e, …)          an application, carried out by the rules above the line; with values, data
      A in c(A, …)     its names are data types, T included
      p ::= c          only for a c that holds nothing
      data             c holding nothing, and c(v, …)
      y                a placeholder, as x
      covered          the rule above for x : T, extended: for a c(A, …) that T lists, c(y, …) in place of x,
                       for new y, … : A, …

    n : A                       declares n, of type A
    n = e                       defines n as e

    e ::= n                     defined name

    n                    becomes   e                  if n = e

    n : A                       if declared so

    where
      n                a defined name (a name); not part of the language
      n : A, n = e     each n declared once and defined once, and not declared as a constructor
      a name in e      if no c: an x, if a case around it takes it, belonging to the nearest; otherwise an n
      e[x := w, …]     a case in e that takes a name some w uses as an n is first renamed, in that x and the
                       uses that belong to it, to a new x
      n = e            checked if e : A, for the declared n : A; e may use any defined name, n itself included
      what we write    declarations and definitions, then one expression
      checked          what we write, if each n in it is declared and defined, every definition is checked,
                       and the expression has a type
      if e : A         as above, when each n = e has e : A, for the declared n : A
      becomes          the rules above, this one included, are the only ones

    T(X, …) = d | …             data type, with placeholders X, …

    A ::= X                     placeholder in a type
          T(A, …)               data type, filled
          ∀X. A                 for every X

    e : A[X := B]               if e : ∀X. A
    e : ∀X. A                   if e : A, and X is used in the type of no placeholder e uses from around it
    p fits ∀X. A                if p fits A, for a new X
    q, … covered                if some x in q, … has x : ∀X. A, and q, … with x : A, for a new X, are covered

    where
      X, Y             a placeholder in a type: a name not declared as a data type
      X used in A      belongs to the nearest ∀ around it that takes X, or to T in the contents of T(X, …);
                       in what we write, there must be one
      n : A            an unwritten ∀X. in front of A for each X in A that no ∀ in A takes
      T(X, …)          T takes X, …, at least one, all different
      T(A, …)          T filled: given A, …, in order, as the first types it needs
      needs            what a type must be filled with: X nothing; T(X, …) one type for each X; T(A, …) what
                       T needs after A, …
      uses             each A in T(A, …) needs what T needs at its position; a type anywhere else, as an input
                       type, a result type, contents, under ∀X., or in e : A, needs nothing
      A in c(A, …)     may also use T's placeholders, and those a ∀ in A takes
      T(B, …) lists    d[X := B, …] for each d that T(X, …) lists; in the rules above, it is a data type like T
      ∀X. A            A reaches as far as it can; the same type as ∀Y. A[X := Y], for a Y not in A
      A[X := B, …]     each B in place of the uses of its X that no ∀ in A takes; a ∀ in A that takes
                       a placeholder used in B is first renamed to one in neither A nor B
      known            a function's type, past any ∀ in front: as the e of n = e, n's declared type; as an
                       input, the input type of what it is given to; as the body of a case of a function whose
                       type is known, the result type
      limits           on what we write: no ∀ in any A in T(A, …), or B in [X := B]; and no ∀ in the input
                       types of a function whose type is not known
      checked          also within the limits, and with every X in what we write taken
      if e : A         also outside the limits: a step may leave them, so this is said of e : A, not of checked

    A ::= X(A, …)               placeholder in a type, filled

    X(A, …)[X := B, …]   is   B(A[X := B, …], …)

    where
      is               names the same type
      C                a type, as A
      X(A, …), T(A, …) at least one A: the first types X or T needs, all of them or fewer
      needs            now T(X, …) one type for each X, needing what that X needs; X(A, …) what X needs after A, …
      X needs          decided for each ∀ or T(X, …) that takes X: what the uses that belong to it show, the
                       same for all of them, and nothing where they show no more
      uses             also each A in X(A, …) needs what X needs at its position
      B(A, …)          for B a T or a Y, alone or filled with C, …: T(C, …, A, …) or Y(C, …, A, …)
      X := B           B needs what X needs, so B is a T or a Y, alone or filled, unless X needs nothing
      needs the same   as many types, each needing the same in turn
      limits           also no ∀ in any A in X(A, …)
      checked          also when every type in it needs what its position needs, and what each X needs is
                       decided as above: with no uses that contradict each other, and no needs that never end,
                       as for X in T(X) = c(T(X(A)))
      expression       what we write ends with one; it writes no type, so it decides no needs: an X that
                       nothing takes, in the types the rules give it, needs the same throughout
