---
title: "Kerbalhopper: PID explained with KSP"
date: 2023-04-30 17:00:00 +0800
tags: [programming, physics]
math: true
---

## Introduction

The Starship test flight by SpaceX is incredibly thrilling! True to your old habits, you've resolved to recreate it in Kerbal Space Program. Perhaps it would be wise to begin by constructing a Starhopper replica. After all, you just need to take off and maintain your altitude for a few seconds, right?

The rocket design is remarkably straightforward. All you need to do is affix a Dart engine beneath a Rockomax Fuel Tank, integrate landing legs, a probe core and battery together, and you're ready to launch.

![craft](/files/20230430/craft.png)

After fiddling aroud with it, you decide to automate this go-to-an-altitude-and-hold-it process. Being a programmer, automation comes naturally to you.

Thanks to the community, a mod called [krpc](https://github.com/krpc/krpc) exposes the underlying API of KSP. You can use it to write a script that controls your craft.

## The math way

You want to figure this problem out by turing it into an optimization problem. In short, you need to figure out a function $f: \mathbb{R} \mapsto [0,1], \text{time} \mapsto \text{throttle}$. According to Newton's law:

$$
F = ma
$$

while the corresponding $F$, $m$ and $a$ are:

$$
\begin{align}
F = f(t) + \text{gravity}(m) + \text{drag}(\frac{dx}{dt}) \\
m = \text{wet mass} - \text{fuel consumption}(k\int_{0}^{t} f(s) ds) \\
a = \frac{d^2 x}{d t^2}
\end{align}
$$

And you want at some time $t_0$:

$$
\begin{align}
x(t_0) = x_0 \\
\frac{dx}{dt} = 0
\end{align}
$$

Ok it seems that things are getting out of hand. Too many variabled are affecting each other. You wander if there is a better way to do this. And it turns out that this is an engineering question: **You don't have to find an optimal solution, you just need to find a good enough one.**

## The engineering way

Time to be creative! $f$ can be found by trial and error. A pretty straightforward way is to set your 

## Conclusion
