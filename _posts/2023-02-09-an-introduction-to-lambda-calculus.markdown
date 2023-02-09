---
title: "An Introduction to Lambda Calculus"
date: 2023-02-09 22:00:00 +0800
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
lambda x: x
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
\newcommand{\highlight}[1]{ { \colorbox{yellow}{$\color{black}#1$} } }
\begin{align}
& (\lambda xy.xyx)ab \\
& = (((\lambda x . \lambda y . xyx)) a ) b \\
& = (\highlight{[a/x]} (\lambda y . \highlight{x} y \highlight{x})) b \\
& = (\lambda y . \highlight{a} y \highlight{a}) b \\
& = \highlight{[b/y]} a \highlight{y} a = a \highlight{b} a
\end{align}
$$

Still confused? Try it out in Python.

```python
(lambda x: (lambda y: x + y + x))('a')('b')
# => 'aba'
```

Of course no one will write such horrible program. Let's try naming it (however few real lambda functions are given names):

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

And yes! $0$ is a function! Try it out:

$$
(\lambda sz.z) a = (\lambda s . (\lambda z.z)) a = [a/s](\lambda z.z) = \lambda z.z
$$

The input $a$ is thrown away, leaving only $\lambda z.z$ which is called a "identity function". There're also many other ways defining $0$, but there are [good reasons](https://stackoverflow.com/a/1485145/10811334) using this. Just keep going by defining $1, 2, ...$ and all the natural numbers.

### Successor

One approach to this is to define a "successor operation", which basically returns the number that is one greater than the input. It goes as follows (yes we are giving it a name since it's quote common):

$$
\mathbf{S} = \lambda wyx.y(wyx)
$$

Actually I prefer the form of:

$$
\mathbf{S} = \lambda w . (\lambda yx.y(wyx))
$$

Apply our $0$ to it:

$$
\begin{align}
& \mathbf{S} 0 \\
& = \mathbf{S} (\lambda sz.z) \\
& = (\lambda wyx.y(wyx))(\lambda sz.z) \\
& = \highlight{[\lambda sz.z / w]} (\lambda yx.y(\highlight{w} y x)) \\
& = \lambda yx.y(\highlight{(\lambda sz.z)} yx) \\
& = \lambda yx.y((\lambda z.z) x) \\
& = \lambda yx.y(x) = \lambda sz.s(z) = 1
\end{align}
$$

Another really weird function huh? Compared with $0$, $z$ is quoted by $s$ in $1$ and that's how we encode out natural numbers. Recall that this is not the only way to encode natural numbers, but we find defining calculations for it much eaiser (covered later). Rewrite it in Python if you are still confused:

```python
def zero(s):
    return lambda z: z


def S(w):
    # Define function "inner" for the sake of less nasty lambda nesting.
    def inner(y):
        # We know that w is a function, hence we're going to call it instead of join (+) it.
        return lambda x: y + w(y)(x)
    return inner
```

Since $w$ is always in the form of $\lambda sz.s(s(s( ... (z))))$, by calling $w$ using $(wyx)$ we "unwrap" the head of $w$ and "de-function" it. The $y$ at the head of $\highlight{y}(wyx)$ adds another layer of nesting.

Now you have an idea what it is doing. Now try to get $2$:

$$
\begin{align}
& \mathbf{S} 1 = (\lambda \highlight{w}yx.y(\highlight{w}yx))\highlight{(\lambda yx.y(x))} \\
& = \lambda yx.y(\highlight{(\lambda sz.s(z))}yx) \\
& = \lambda yx.y(y(x)) \\
& = \lambda sz.s(s(z)) = 2
\end{align}
$$

Note that we have renamed the variables for clarity.

Each time $\mathbf{S}$ is applied, the nesting goes deeper. We can even test it in Python!

```python
zero('s')('z')
# => 'z'
one = S(zero)
one('s')('z')
# => 'sz'
two = S(one)
two('s')('z')
# => 'ssz'
```

### Addition

Addition can also be achieved by the successor function. Write a number before $\mathbf{S}$:

$$
\begin{align}
& 2\mathbf{S} \\
& = (\lambda s . \lambda z . s (s(z))) \mathbf{S} \\
& = \lambda z.\mathbf{S}(\mathbf{S}(z))
\end{align}
$$

It's resolved into a function with $2$ successor operations! Now it's triviald calculate $2+3$ using it:

$$
2\mathbf{S}3 = \mathbf{S}\mathbf{S}3 = \mathbf{S}4 = 5
$$

Numbers defined in a recursive way make this operation a breeze.

### Multiplication

Multiplication is also made easy by the defination of numbers.

$$
\mathbf{M} = \lambda xyz.x(yz)
$$

It "unwraps" $y$ and apply the repeated sequence to x, say $2 \times 3$:

$$
\begin{align}
& (\mathbf{M}2)3 = (\lambda xyz.x(yz)2)3 \\
& = \lambda z.2(3z) \\
& = \lambda z.(\lambda uw . u(u(w)))((\lambda ij.i(i(i(j))))z) \\
& = \lambda z.(\lambda uw . u(u(w)))(\lambda j.z(z(z(j)))) \\
& = \lambda z.(\lambda w . \highlight{\lambda j.z(z(z(j)))}(\highlight{\lambda j.z(z(z(j)))}(w))) \\
& = \lambda z.(\lambda w . z(z(z(z(z(z(w))))))) \\
& = \lambda z.\lambda s . z(z(z(z(z(z(s)))))) = 6 \\
\end{align}
$$

Whoa *quotes*! But trust me it's doing the right thing.

## Further Reading

Lambda Calculus is a simple yet powerful system and there's still a lot to learn. You can:

- Try to create more arithmetic operations using the lambda system.
- Check out [Wikipedia](https://en.wikipedia.org/wiki/Lambda_calculus) for a more systematic and formal introduction.
- Try some FP languages such as [Lisp](https://lisp-lang.org/) and [Haskell](https://www.haskell.org/). (After all that's what this is all for!)

Good luck and have fun!
