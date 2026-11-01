This is what learning needs, before any of it is built: what must come first, what does not matter, what does, and what is left open. Nothing here is new; it gathers results that already exist.

## Expression comes first

A medium is a way of expressing: base, the lambda calculus, a neural network.

An expression is one thing written in a medium.

Learning is picking among expressions. Nothing can be learnt that its medium cannot express, so the medium comes first.

The smallest expression is a function whose output depends on its input. There are four functions from a type of two values to another. Two of them give back the same value whatever the input, so they say nothing about it. The other two carry one distinction through:

    Touch : Type
    Touch = touched : Touch | untouched : Touch

    Move : Type
    Move = forward : Move | backward : Move

    react : (t : Touch) → Move
    react = (touched) ↦ backward | (untouched) ↦ forward

Learning is also a computation, so it is an expression too. It can be written in the same medium as what it learns. Base can do this: a type whose values are expressions of base, and functions that make and work them out.

## What does not matter

Three results say that, in principle, a choice does not matter:

    Turing equivalence         every universal medium can express what any other can
    multiple realizability     the same computation can run on neurons, chips, or anything else universal
    No Free Lunch              averaged over every possible world, every way of learning does equally well

Being universal takes little: [primitive.md](primitive.md) has three rules. Nothing is above it.

The limits are shared too. What one universal medium cannot do, none can. For example, no medium can decide, for every expression, whether its working out finishes.

The length of a thing's shortest expression changes with the medium, but by at most a constant: the length of a translator from one medium to the other. This is the invariance theorem. For large things the constant hardly matters; for small things it is most of the length.

## What does matter

These results say what is possible. They say nothing about what is cheap or likely.

Every choice makes some things easier than others:

- a medium, picked from at random, makes some expressions likely and others rare
- a way of learning tries some expressions first
- a substrate runs some computations faster

Each of these is a bias. A bias is good only when it fits the world it is in.

The smallest case shows this. Pick functions Touch → Move at random until one is rewarded:

    in base, each case's output picked at random:     each of the four functions 1 in 4
    one neuron, backward if w·t + b > 0, with
    w and b drawn at random, touched as 1:            each constant 3 in 8; react 1 in 8

In a world that rewards react, base finds it first. In a world that rewards always forward, the neuron does. Neither is better in every world.

Some bias is always needed. Without one, nothing seen says anything about what is not yet seen. This is Hume's problem of induction; No Free Lunch is its exact form.

The usual bias is for short expressions. An expression that fits everything seen can still predict badly, because it has learnt the noise along with the law. This is overfitting. Preferring short expressions guards against it: the best theory is the one that makes the theory plus what it leaves unexplained shortest (minimum description length).

## Knowledge

A behavior is an expression that acts: Touch → Move.

A theory is an expression that predicts: Experiment → Result. A theory of the world is also called a model. A learner can learn behaviors directly, or learn a model and work out behaviors from it.

Science is finding statements that come ever closer to the world, by acting in it:

- an experiment is an action chosen to test theories; a good one rules out the most
- a tool, such as a telescope, is an action that changes what can be observed

So science is learning in which the learner shapes what it sees.

Acting matters for another reason. Watching shows only what goes with what. To learn what causes what, the learner must set something and see what changes (Pearl).

Two theories that predict the same result for every experiment are, for a learner, the same theory.

Evidence for a claim of mathematics lies inside the medium, and can be checked completely: [extension.md](extension.md). Evidence for a theory of the world comes from outside, one experiment at a time. A theory can be refuted, but never proven. A learner can even reach the right theory and never know that it has (Gold).

## Two mediums

Base is the medium used to write all this down. The medium a learner thinks in is a separate choice. A learner described in base need not think in base, just as a neural network described in mathematics does not do mathematics.

## The layers

Each layer is written in base, using only the ones below it. The first three are standard and settled:

    numbers        natural numbers, integers, fractions, reals
    information    the bit, and two ways of measuring: Shannon's, for uncertainty, and Kolmogorov's, the length of the shortest expression
    probability    a distribution is an expression given random bits

A real number is a rule that gives as many digits as are asked for. It is never complete, but nothing that can be calculated is lost, since a calculation only ever uses finitely many digits. Floats have a fixed number of digits, so they can be built too.

Shannon's and Kolmogorov's measures agree: for a distribution that can be computed, Shannon's measure is the average length of the shortest expressions for what it gives, up to a constant.

After those comes one layer for learning, experience and science. They are one thing:

    the loop       a learner gives actions to a world and gets observations back
    Bayes          how belief changes after an observation

The loop is a definition. Bayes is the only consistent way to change belief: any other way reduces to it (Cox).

Learning is the loop without actions. Experience adds actions. Science aims them at knowledge.

The loop has standard parts:

    state          what of the past is needed to predict the future
    hidden state   when the world does not show all of it, the learner keeps a belief about the rest
    value          how good a situation is, counting what comes after; worked out one step at a time (Bellman)
    exploration    acting on what seems best now, or acting to learn more and do better later
    credit         when a result follows many actions, which ones earned it

## What is left open

Three pieces have no settled answer:

    the prior        which worlds are expected before anything is seen; it includes the medium the learner thinks in
    the objective    what the learner acts for
    approximation    how a learner with limited time and memory comes close to what Bayes would do

### The prior

The best prior known is Solomonoff's. Give a universal medium random bits as an expression, and see what it gives back. Short expressions come up more often: each is as likely as one half to the power of its length.

This is the same as picking expressions at random, as in the Touch → Move example. A learner with this prior does as well as possible in every world that can be computed, up to the invariance constant.

It has two limits. It cannot itself be computed. And the constant depends on the medium chosen, and for a learner with a finite life it is not small (Leike and Hutter). So the choice of medium returns here, as the learner's bias.

### The objective

Proposed, none settled:

    reward                  a number given by the world, made as large as possible (Sutton, Silver)
    information gain        acting to learn the most about the world (Orseau, Hutter)
    compression progress    seeking what most shortens the learner's theories (Schmidhuber)
    empowerment             seeking where the learner's actions have the most effect
    free energy             keeping observations unsurprising (Friston)

A reward that can be made large in a way not intended will be made large that way.

### Approximation

Standard ways, each suited to some mediums:

    search         try expressions, shortest first (Levin)
    selection      vary expressions and keep those that do better: evolution
    gradient       in a medium of numbers, change each number a little in the direction that does better
    sampling       draw theories at random, and keep them in proportion to how well they fit

Standard ways to explore: act as if a theory drawn from belief were true (Thompson), or prefer what is least known.

### Open problems

    variables    finding which quantities a theory should be about; every system that has found a law was given them
    libraries    making each discovery a part to build the next from, so the medium grows with what is learnt
    embedding    a learner that is part of the world it learns, not outside it
    continuing   learning without end, without losing what was learnt before
