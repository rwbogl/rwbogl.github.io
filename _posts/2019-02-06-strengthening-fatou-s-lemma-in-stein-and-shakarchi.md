---
title: Strengthening Fatou's Lemma in Stein and Shakarchi
---

My real analysis course at Emory is using Stein and Shakarchi's *Real
Analysis*. Concurrently I'm chewing on Rudin's *Real and Complex Analysis*
since I really enjoyed *Principles of Mathematical Analysis*. So far, I feel
that Stein and Shakarchi complicate matters a lot for a text that puts off
general measure theory for several chapters. As an example, I want to look at
Stein and Shakarchi's statement of Fatou's lemma.

First, let's compare the differences in presentation of the Lebesgue integral
for nonnegative functions:

**Rudin:**

- Define simple functions (generalization of step functions)
- Define integral for simple functions
- Define integral for nonnegative functions

**Stein and Shakarchi:**

- Define simple functions (generalization of step functions)
- Define integral for simple functions
- Define integral for bounded functions supported on a set of finite measure
- Define integral for nonnegative functions

The Stein and Shakarchi step of "bounded functions supported on a set of finite
measure" seems like a *really* complicated detour to make. Why do that? It
doesn't get us any stronger results, and in fact, it makes some results even
harder to get!

Take, for example, Fatou's Lemma. The general statement is this:

> If $f_n$ is a sequence of nonnegative measurable functions, then
>
$$\int \liminf f_n \leq \liminf \int f_n.$$

In Stein and Shakarchi, the statement is this:

> If $f_n$ is a sequence of nonnegative measurable functions on $\mathbb{R}^n$
that converge to the function $f$, then
>
$$\int f \leq \liminf \int f_n.$$

To prove this, Stein and Shakarchi go out of their way to prove a "bounded
convergence theorem" for bounded functions with finite support. After all the
time spent introducing supports and proving the bounded convergence theorem,
they produce a proof about the same length as Rudin's, but not in a general
measure space, plus an extra hypothesis and a weaker conclusion. Sad!

We can improve Stein and Shakarchi's result by applying their result to
$\liminf f_n$. That is, set $g_n = \inf_{k \geq n} f_k$ and $g = \lim g_n
= \liminf f_n$. Applying their result to $g$ and $g_n$, we obtain

$$
\int \liminf f_n \leq \liminf \int g_n \leq \liminf \int f_n,
$$

where the last inequality comes from $g_n \leq f_n$.

This *still* doesn't get us what we want in an arbitrary measure space. Stein
and Shakarchi do, however, move to general measure spaces several chapters
after their development of the Lebesgue integral. There is likely some
pedagogical wisdom in this. It's all build up to the dominated convergence
theorem and company anyway, I suppose.
