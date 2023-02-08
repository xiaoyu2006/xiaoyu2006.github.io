---
title: "Lambda Calculus Explained"
date: 2023-02-15 20:00:00 +0800
tags: [programming, math]
math: true
---

## Introduction

Who doesn't like simplicity! To me simplicity is not only a preference but a belief. I always has a werid feeling that only the simple things last.

In the world of calculation, the most simple thing might be a Turing machine, a mathematical model of computation that is capable of implementing any algorithm using a tape, a head, a state and instructions. However besides the tape stuff, another extremely simple mathematical model was also invented at roughly the same time. Don't be surprised when you see its name: ***Lambda calculus*** ($\lambda$-calculus). It consists a set of notation system and reduction rules. Compared with the Turing machine, it is more "math-y" than "computer-y".

## Notation System

*I've used it!* you may think. Correct! Lambda calculus is the fundamental building blocks in Functional Programming language. It's extremely likely to meet it in most modern programming languages, for example Python, JavaScript and even OO languages such as [C++](https://stackoverflow.com/questions/48210733/link-between-lambda-calculus-and-lambda-expressions-in-c) and Java. Let's take a look at an example:

$$
\lambda x . x
$$

A little bit confused huh? Let's write it in python:

```python
lambda x : x
```

Much more intuitive now! It simply outputs whatever is inputed.

It can be applied to another expression by writing the expresstion after the function:

$$
(\lambda x.x) y
$$

And it resolves into $y$. We'll cover the details later.

This is one of the so-called *lambda expressions*. There are three kinds of lambda expressions:

- Name: just a trivial name representing anything. e.g. $x$
- Abstraction: $\lambda param . body$, where $param$ is a name and body is the substitution rule. e.g. $\lambda xy.xyx$ (which is in infact an abbreviation of $\lambda x.\lambda y.xyx$)
- Application: a list of expressions. e.g. $(\lambda xz.xxz)ij$

You can check out the formal definition at [Wikipedia](https://en.wikipedia.org/wiki/Lambda_calculus#Formal_definition). It's totally fine to treat the lambda expression notation system as a minimal programming language whose keywords are only $.(dot)$ and $\lambda$. There are also optional quotes in lambda expressions. Without quotes we phrase the expression from left to right.

In an abstraction, or function, there're two types of variables: *bounded* and *free* variables. A variable is *bounded* means it appears in the "params" section and *free* is the opposite. This is similar to local and global variables. For example in $(\lambda x.xy)$, $x$ is bounded and $y$ is free.

## Reduction Rules

The reduction rules have horrible names: $\alpha$-conversion and $\beta$-reduction.

$\alpha$-conversion is very stupid: a name can be written in another if its bounded. For exmaple $\lambda x.xy$ is equivalent to $\lambda z.zy$.

$\beta$-reduction is also a dumb operation: it means substituting using the rules defined in the body of the function. For example, resolve the application:

$$
(\lambda x.x) y = [y/x] x = y
$$

The $[a/b]$ mark simply means substituting $b$ with $a$. Now try a more complex one:

$$
\begin{align}
& (\lambda xy.xyx)ab \\
& = (((\lambda x . \lambda y . xyx)) a ) b \\
& = ({\colorbox{yellow}{$[a/x]$}} (\lambda y . {\colorbox{yellow}{$x$}} y {\colorbox{yellow}{$x$}})) b \\
& = (\lambda y . {\colorbox{yellow}{$a$}}y{\colorbox{yellow}{$a$}}) b \\
& = {\colorbox{yellow}{$[b/y]$}} a{\colorbox{yellow}{$y$}}a = a{\colorbox{yellow}{$b$}}a
\end{align}
$$

Still confused? Try it out in Python.

```python
(lambda x : (lambda y : x + y + x)) ('a') ('b')
# => 'aba'
```

Of course no one will write such horrible program. Let's try naming it (however real lambda functions are never given names):

```python
def l1(x):
    def l2(y):
        return x + y + x
    return l2

l('a')
# => <function __main__.l1.<locals>.l2(y)>
l('a')('b')
# => 'b'
```

Note that for `l2` (the inner lambda function) variable $x$ is free. However for the whole expression it's bounded.

## Arithmetic

Time to do some calculations! Let's start by defining $0$.

$$
\lambda sz.z
$$

Try it out:

$$
(\lambda sz.z) a = (\lambda s(\lambda z.z)) a = [a/s](\lambda z.z) = \lambda z.z
$$

The input $a$ is thrown away, leaving only $\lambda z.z$ which is called a "identity function". There're also many other ways defining $0$, but there are [good reasons](https://stackoverflow.com/a/1485145/10811334) using this. Just keep going by defining $1, 2, ...$ and all the natural numbers.

Now one approach to this is to define a "successor operation", which basically returns the number that is one greater than the input. It goes as follows (yes we are giving it a name since it's quote common):

$$
\mathbf{S} = \lambda wyx.y(wyx)
$$

Another really weird function huh? 

TODO

Let's apply our $0$ to it:

$$
\begin{align}
& \mathbf{S} 0 \\
& = \mathbf{S} (\lambda sz.z) \\
& = (\lambda wyx.y(wyx))(\lambda sz.z) \\
& = {\colorbox{yellow}{$[\lambda sz.z / w]$}} \lambda yx.y({\colorbox{yellow}{$w$}} yx) \\
& = \lambda yx.y({\colorbox{yellow}{$(\lambda sz.z)$}} yx) \\
& = \lambda yx.y((\lambda z.z) x) \\
& = \lambda yx.y(x) = \lambda sz.s(z) = 1
\end{align}
$$

Compared with $0$, $z$ is quoted by $s$ in $1$.

How about $2$? Try it out:

$$
\mathbf{S} 1 = (\lambda wyx.y(wyx))\lambda yx.y(x) = \lambda yx.y((\lambda sz.s(z))yx) = \lambda yx.y(y(x)) = \lambda sz.s(s(z)) = 2
$$

Every time $\mathbf{S}$ is applied, the nesting goes deeper.
