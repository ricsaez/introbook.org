This is the smallest language that can express everything [base.md](base.md) can: it has no types, and every value is a function.

A term is written in one of three ways:

    x              a variable: a name standing for whichever value is given
    (x) ↦ t        a function: given a value, it gives back t, with x standing for that value
    t(u)           a call: t called with u

A function takes exactly one input. Each variable must be inside a function that brings it in.

A function is finished as it is. A call t(u) is worked out in steps: first t is worked out, then u; t is then a function (x) ↦ s, and the call stands for s, with x standing for u everywhere in s. Where an inner function in s brings in a name used in u, that inner name is changed first, so that u still means what it meant. a >> b means a works out to b. Working out can go on without end.

A name can stand for a term, as in base: n = t, and from then on writing n means the same as writing t. t cannot use n: a name here is only a shorthand.

Two terms are the same if they work out to the same thing, once the names their functions bring in are changed to match.

## From base to primitive

Every value of base is written as a term, in four steps.

Types are removed. A value of type Type, and each input of type Type, is written as (x) ↦ x: nothing ever looks inside it.

A function with several inputs takes them one at a time: (x₁, x₂, ...) ↦ r is written (x₁) ↦ ((x₂) ↦ ... r), and f(a₁, a₂, ...) is written f(a₁)(a₂)....

A value made by a constructor becomes a function that is given one function for each constructor of its type, and calls the one for its own constructor with its inputs. For T = c₁ : T₁ | ... | cₘ : Tₘ, cᵢ(a₁, ..., aₖ) is written (k₁) ↦ ... (kₘ) ↦ kᵢ(a₁)...(aₖ); a constructor with no inputs calls kᵢ((x) ↦ x). Cases then become a call: a function whose cases are cᵢ(x₁, ..., xₖ) ↦ rᵢ is written (v) ↦ v(e₁)...(eₘ), with each eᵢ = (x₁) ↦ ... (xₖ) ↦ rᵢ, or (y) ↦ rᵢ if cᵢ takes no inputs. A case with patterns inside patterns, or with several inputs matched, is first written as cases that each match one input, one constructor deep.

A name whose value uses itself is written with Z, which gives a function the function being made:

    Z = (f) ↦ ((x) ↦ f((v) ↦ x(x)(v)))((x) ↦ f((v) ↦ x(x)(v)))

n = t, where t uses n and is a function, is written n = Z((n) ↦ t). Several names that use each other are first made into one name, for a function that is given which of them to be.

## From primitive to base

Every term is written in base, using one type and one function:

    Fun : Type
    Fun = fun : (f : (v : Fun) → Fun) → Fun

    call : (g : Fun, x : Fun) → Fun
    call = (fun(f), x) ↦ f(x)

    x              is written    x
    (x) ↦ t        is written    fun((x) ↦ t), with t written in base too
    t(u)           is written    call(t, u), with t and u written in base too

Each step of working out a term is matched by steps of working out what it is written as.
