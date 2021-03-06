---
title: Numerically verifying properties of random permutations
date: 2019-05-28
---

According to some generating function magic, the proportion of permutations on
$n$ letters which have cycles only of lengths divisible by a fixed integer $k$
is $$\frac{(1/k)^{\overline{n/k}}}{(n/k)!},$$ where the overline means ["rising
factorial"](https://en.wikipedia.org/wiki/Falling_and_rising_factorials). I
will prove this, but then I will *actually* demonstrate that it is true, using
computational methods. Empirical verification carries a certainty that a "mere
proof" cannot!

# Background

In [*generatingfunctionology*](https://www.math.upenn.edu/~wilf/DownldGF.html),
Wilf gives a very clear discussion of the exponential formula. This formula
tells us how to count structures built from smaller, "connected" structures.
Permutations fit this mold exactly. Every permutation is the product of
disjoint cycles, which we can think of as being "connected." There is a lot to
say here, but we can just quote two special cases of the result:

Let $d_n = (n - 1)!$ be the number of cycles which permute exactly $n \geq 1$
letters, and

$$D(x) = \sum_{n \geq 1} \frac{d_n}{n!} x^n = -\log(1 - x)$$

be the [exponential generating
function](https://en.wikipedia.org/wiki/Generating_function) of this sequence.
Let $h_n = n!$ be the number of permutations on $n$ letters (which may touch
fewer than $n$ elements), and

$$H(x) = \sum_{n \geq 0} \frac{n!}{n!} x^n = \frac{1}{1 - x}$$

be the exponential generating function of this sequence. The exponential
formula tells us that

$$H(x) = e^{D(x)},$$

which is easy to check in this case. This does not seem so impressive, but it
is far more useful when we do not know one of the two sides.

# A proof

It turns out that the exponential formula applies in broader situations than
what we have described so far. In particular, we can use it to prove the result
stated at the beginning of this post. For a fixed integer $k$, we will apply
the exponential formula in a subtler way.

Let $d_n$ be the number of cycles which permute exactly $n$ letters, *but have
length divisible by $k$*. That is,

$$d_n = (n - 1)! [k \backslash n],$$

where the brackets are [Iverson
brackets](https://en.wikipedia.org/wiki/Iverson_bracket). The exponential
generating function $D(x)$ is now

$$
\begin{align*}
    D(x) &= \sum_{n \geq 1} \frac{(n - 1)!}{n!} x^n [k \backslash n] \\
         &= \sum_{m \geq 1} \frac{x^{mk}}{mk} \\
         &= -\frac{1}{k} \log(1 - x^k).
\end{align*}
$$

The sequence $h_n$ now counts the number of permutations on $n$ letters which
only have cycles with lengths divisible by $k$. The exponential formula gives
us the same result as before, namely

$$H(x) = (1 - x^k)^{-1/k}.$$

If we knew what generating function the right-hand side was, then we could
equate coefficients and be done. In accordance with the [binomial
theorem](https://en.wikipedia.org/wiki/Binomial_series), the sequence $a_m =
{\alpha \choose m}$ is generated by $(1 + x)^\alpha$. We can use this to write
$(1 - x^k)^{-1/k}$ as a generating function.

If $(-1)^{1/k}$ is any $k$th root of $(-1)$, then

$$
\begin{align*}
    (1 - x^k)^{-1/k} &= (1 + ((-1)^{1/k} x)^k)^{-1/k} \\
                     &= \sum_m {-1/k \choose m} (-1)^m x^{mk}.
\end{align*}
$$

So we have

$$
    H(x) = \sum_m {-1/k \choose m} (-1)^m x^{mk}.
$$

The coefficient on $x^n / n!$ on the left-hand side is $h_n$, the number that
we want to find. The coefficient on $x^n / n!$ on the right-hand side is

$$n! (-1)^m {-1/k \choose m} [mk = n] = n! (-1)^{n / k} {-1/k \choose n / k}.$$

Since there are $n!$ permutations on $n$ letters total, this tells us that the
proportion of them containing only cycles of length divisible by $k$ is

$$\frac{d_n}{n!} = (-1)^{n / k} {-1/k \choose n / k}.$$

The right-side looks daunting, but it simplifies a lot. First, let's let $n / k
= m$ again, to make things look nicer. Using the falling factorial definition
of the binomial coefficient we get

$$
(-1)^m {-1/k \choose m} = (-1)^m \frac{(-1/k)^{\underline{m}}}{m!}.
$$

It isn't hard to check that $(-1)^m(-x)^{\underline{m}} = x^{\overline{m}}$ for
all $x$ and $m$, therefore our expression reduces to

$$\frac{(1/k)^{\overline{m}}}{m!} = \frac{(1/k)^{\overline{n/k}}}{(n/k)!}.$$

Phew! That was a lot of work. We should at least try our result out before
continuing. With $n = 4$ and $k = 2$, this says that exactly $9$ permutations
of the $24$ on $4$ letters have only even cycles. These are:

$$
\begin{align*}
    &(0, 1, 2, 3) \\
    &(0, 1, 3, 2) \\
    &(0, 1)(2, 3) \\
    &(0, 2, 1, 3) \\
    &(0, 2, 3, 1) \\
    &(0, 2)(1, 3) \\
    &(0, 3, 1, 2) \\
    &(0, 3, 2, 1) \\
    &(0, 3)(1, 2).
\end{align*}
$$

With $n = 10$ and $k = 2$, this says that roughly 24.6% of all
$10!$ permutations on ten letters have only even cycles.

# A *real* proof

I just can't believe this result without some computational evidence.
Fortunately, this is easy to provide. Here is some Python code to do exactly
that:

```python
from sympy.combinatorics.permutations import Permutation
from sympy import rf, factorial, S
import matplotlib.pyplot as plt


def cycles_divisible(perm, k):
    """Check if every cycle in perm has length divisible by k."""
    cycles = perm.cycle_structure
    return all(length % k == 0 for length in cycles.keys())


def expected_proportion(n, k):
    m = n // k
    return rf(S(1) / k, m) / factorial(m)


n = 10
sample_size = 2000
k = 2

samples = [cycles_divisible(Permutation.random(n), k) for _ in range(sample_size)]
proportions = [sum(samples[:m]) / m for m in range(1, len(samples))]

plt.style.use("ggplot")
plt.axhline(expected_proportion(n, k), ls="--", color="black", label="Expected proportion")
plt.plot(proportions)

plt.xlim(-1, sample_size)
plt.legend()

plt.title("Proportion of permutations with even cycles")
plt.xlabel("Sample size")
plt.ylabel("Proportion")

plt.show()
```

This snippet, ran three times, generated the following plot:

![Graph showing three "random" lines converging to our expected
value.](/files/cycles.svg)

Amazing! The proportions are converging to what we expect, which is a wonderful
thing to see. In some ways explaining this picture is the best part of the
above proof. Why slog through such computations if you aren't explaining
something surprising?

Anyway, none of this is original. It's all from exercise 2 in Chapter 3 of
Wilf's *generatingfunctionology*, which I highly recommend.
