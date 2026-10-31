A language is made of different **types** of things. To refer to something of a given type, we want to mention it without choosing a particular **value** of that thing. To achieve this we use a **placeholder**. 

We use placeholders in two places: when we describe a language, and inside the language itself.

For example, take the language of arithmetic. Numbers are one type of thing in it, and `2` and `3` are particular values of that type. In arithmetic we can write:

    2 + 3

When we express something, we call what we express an **expression**. `2 + 3` is an expression of arithmetic, and so is a single number, like `2`.

When we describe arithmetic, we want to mention an expression without choosing a particular one. For that we use the placeholder:

    e

`e` is never written in arithmetic: we write it only when describing arithmetic. With it, we can describe many expressions at once:

    e + 1

Choosing an expression for `e` gives an expression of arithmetic:

    2 + 1          choosing 2 for e
    (2 + 3) + 1    choosing 2 + 3 for e

The parentheses keep the chosen expression in one piece.

A language can also let us write placeholders in it. In arithmetic, we can write `a` and `b` as placeholders for numbers:

    a + b

Choosing the value `2` for `a` and `3` for `b` gives:

    2 + 3

A placeholder written in the language itself is a **variable**. A variable is part of the expression it is in: `a + b` is an expression of arithmetic, and so is `a` on its own.

The two kinds meet when we choose for `e` an expression with a variable in it:

    a + 1          choosing a for e

`e` is gone, replaced by `a`. The variable `a` stays, and stands for any number.

So a placeholder like `e` is written about a language, and stands for an expression. A variable is written in the language, and stands for a value.
