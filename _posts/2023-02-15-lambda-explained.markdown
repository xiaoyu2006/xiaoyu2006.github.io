---
title: "Lambda Explained"
date: 2023-02-15 20:00:00 +0800
tags: [programming, math]
math: true
---

## Introduction

Who doesn't like simplicity! To me simplicity is not only a preference but a belief. I always has a werid feeling that only the simple things last.

In the world of calculation, the most simple thing might be a Turing machine, a mathematical model of computation that is capable of implementing any algorithm using a tape, a head, a state and instructions. However besides the tape stuff, another extremely simple mathematical model was also invented at roughly the same time. Don't be surprised when you see its name: ***Lambda calculus*** ($\lambda$-calculus). It consists a set of notation system and reduction rules. Compared with the Turing machine, it is more "math-y" than "computer-y".

## Notation System

*I've used it!* you may think. Correct! Lambda calculus is the fundamental building blocks in Functional Programming language. It's extremely likely to meet it in most modern programming languages, for example Python, JavaScript and even OO languages such as [C++](https://stackoverflow.com/questions/48210733/link-between-lambda-calculus-and-lambda-expressions-in-c) and Java. Let's take a look at a simple lambda expression:

$$
\lambda x . x
$$

A little bit confused huh? Let's write it in python:

```python
lambda x : x
```

Much more intuitive! This lambda expression simply outputs whatever is inputed. This is one of the so-called *lambda expression*. There are three kinds of lambda expressions:

- Name: just a trivial name representing anything. e.g. $x$
- Abstraction: $\lambda params . body$, where $params$ is a list of names and body is the substitution rule. e.g. $\lambda xy.xyx$
- Application: a list of expressions. e.g. $(\lambda x.x)y$

You can check out the formal definition at [Wikipedia](https://en.wikipedia.org/wiki/Lambda_calculus#Formal_definition). It's totally fine to treat the lambda expression notation system as a minimal programming language whose keywords are only $.(dot)$ and $\lambda$.

In an abstraction, or function, there're two types of variables: *bounded* and *free* variables. A variable is *bounded* means it appears in the "params" section and *free* is the opposite. This is similar to local and global variables. For example in $(\lambda x.xy)$, $x$ is bounded and $y$ is free.

## Reduction Rules

The reduction rules have horrible names: $\alpha$-conversion and $\beta$-reduction.

$\alpha$-conversion is very stupid: a name can be written in another if its bounded. For exmaple $\lambda x.xy$ is equivalent to $\lambda z.zy$.

$\beta$-reduction is also a dumb operation: it means substituting using the rules defined in the body of the function. For example, the application $(\lambda xy.xyx)(ab)$ can be transformed to $aba$. You can even see it in action using python:

```python
(lambda x, y : x + y + x) ('a', 'b')
# => 'aba'
```

## Arithmetic

You might think the lambda calculus is more than simple, but it can acturally do all the arithmetic calculations.

<!-- TODO -->
