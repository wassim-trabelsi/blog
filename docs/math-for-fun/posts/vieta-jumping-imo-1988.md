---
date: 2026-03-07
authors:
  - wassim-trabelsi
categories:
  - Number Theory
  - Olympiads
tags:
  - vieta-jumping
  - imo
  - minimal-counterexample
comments: true
description: Vieta Jumping  the most elegant proof in the history of the International Mathematical Olympiad. IMO 1988, Problem 6.
---

# Vieta Jumping  The Most Beautiful Proof in Olympiad History (IMO 1988, Problem 6)

In 1988, at the International Mathematical Olympiad (IMO) in Canberra, Australia, **Problem 6** brought the world's best young mathematicians to their knees. Proposed by Stephan Beck (West Germany), this problem was considered so difficult that even the jury members struggled to solve it. Only **11 out of 268 participants** achieved a perfect score  among them, **Nicușor Dan** (Romania), now President of Romania.

The method behind the solution? **Vieta Jumping**, a technique as surprising as it is elegant.

<!-- more -->

## The Problem Statement

!!! quote "IMO 1988  Problem 6"
    Let $a$ and $b$ be positive integers such that $ab + 1$ divides $a^2 + b^2$.

    Show that $\dfrac{a^2 + b^2}{ab + 1}$ is a perfect square.

At first glance, this problem seems harmless. In reality, it is devastating. We must prove a **universal** property: for **every** pair $(a, b)$ satisfying the divisibility condition, the quotient is a perfect square.

---

## The Prerequisite: Vieta's Formulas

Before diving into the proof, let's recall a fundamental result about **quadratic equations**.

Consider the equation:

$$x^2 - Sx + P = 0$$

If $x_1$ and $x_2$ are the two roots (when they exist), then **Vieta's formulas** tell us:

$$\begin{cases} x_1 + x_2 = S \quad \text{(sum of roots)} \\ x_1 \cdot x_2 = P \quad \text{(product of roots)} \end{cases}$$

In other words, the sum of the roots equals the negated coefficient of $x$ (divided by the leading coefficient), and the product of the roots equals the constant term.

Indeed if we rewrite 

$$x^2 - Sx + P = (x -x_1)(x-x_2) = 0$$

Expanding the product gives:

$$(x - x_1)(x - x_2) = x^2 - (x_1 + x_2)x + x_1x_2$$

Comparing coefficients we got :

$x_1 + x_2 = S$ and $x_1 x_2 = P$


???+ example "Quick example"
    For $x^2 - 5x + 6 = 0$, the roots are $x_1 = 2$ and $x_2 = 3$.

    - Sum: $2 + 3 = 5$ ✓
    - Product: $2 \times 3 = 6$ ✓

That's all we'll need. Let's move on to the proof.

---

## The Proof by Vieta Jumping

### Step 1  Setup

Suppose for contradiction that $k$ is not a perfect square. Among all pairs $(a, b)$ of positive integers with $\dfrac{a^2 + b^2}{ab + 1} = k$, choose $(a_1, b_1)$ such that $a_1 + b_1$ is **minimal** (with $a_1 \geq b_1$ by symmetry).

We have:

$$\frac{a_1^2 + b_1^2}{a_1b_1 + 1} = k$$

Rearranging in terms of $a_1$:

$$a_1^2 - k b_1 \cdot a_1 + (b_1^2 - k) = 0$$

### Step 2 The key insight: viewing $a_1$ as a root of a polynomial

Consider the polynomial in $x$:

$$P(x) = x^2 - kb_1 \cdot x + (b_1^2 - k) = 0$$

We observe that $a_1$ is a **root** of this polynomial (by construction). Let $a_2$ be the **other root**.

By **Vieta's formulas**:

$$\begin{cases} a_1 + a_2 = kb_1 \\ a_1 \cdot a_2 = b_1^2 - k \end{cases}$$

From which we deduce:

$$a_2 = kb_1 - a_1 \qquad \text{and} \qquad a_2 = \frac{b_1^2 - k}{a_1}$$

### Step 3 Showing that $(a_2, b_1)$ is also a solution

Since $a_2$ is also a root of $P(x) = 0$, retracing the algebra:

$$a_2^2 - kb_1 \cdot a_2 + b_1^2 - k = 0$$

$$\implies a_2^2 + b_1^2 = k(a_2 b_1 + 1)$$

$$\implies \frac{a_2^2 + b_1^2}{a_2 b_1 + 1} = k$$

But for $(a_2, b_1)$ to truly belong to $S_k$, we must verify that $a_2 \in \mathbb{N}^*$.

**$a_2$ is an integer:**

$a_2 = kb_1 - a_1$, and $k$, $b_1$, $a_1$ are all integers. ✓

**$a_2$ is nonzero:**

$a_2 = \dfrac{b_1^2 - k}{a_1}$. If $a_2 = 0$, then $k = b_1^2$, making $k$ a perfect square, contradicting our assumption. So $a_2 \neq 0$. ✓

**$a_2$ is positive:**

Since $a_2$ is a root, we have $\dfrac{a_2^2 + b_1^2}{a_2 b_1 + 1} = k$. The numerator $a_2^2 + b_1^2 > 0$, and $k > 0$, so the denominator $a_2 b_1 + 1 > 0$, which requires $a_2 > 0$ (since $b_1 \geq 1$). ✓

### Step 5  The contradiction

We've shown that $(a_2, b_1)$ is also a solution with $k = \dfrac{a_2^2 + b_1^2}{a_2 b_1 + 1}$ and $a_2 \in \mathbb{N}^*$. Now observe:

$$a_2 = \frac{b_1^2 - k}{a_1} < \frac{b_1^2}{a_1} \leq \frac{a_1^2}{a_1} = a_1$$

Therefore $a_2 < a_1$, which means:

$$a_2 + b_1 < a_1 + b_1$$

But $(a_2, b_1)$ is a solution where $k$ is still not a perfect square, with a **strictly smaller** sum. This contradicts the **minimality** of $a_1 + b_1$.

$$\boxed{k \text{ is a perfect square.} \quad \blacksquare}$$

---

## Why Is This Proof So Beautiful?

This problem is often cited as **the most beautiful problem in Olympiad history** for several reasons:

1. **The statement is simple.** Any high school student can understand the question. No advanced knowledge is required.

2. **The difficulty is extreme.** Despite the simplicity of the statement, it defeated 95% of the world's best young mathematicians.

3. **The tools are elementary.** The proof uses only Vieta's formulas (high school curriculum!) and a minimality argument.

4. **The idea is deeply creative.** The stroke of genius is to *fix $b$* and view the equation as a polynomial in $a$, then use the second root to "jump" to a smaller solution. It is an act of pure mathematical creation.

5. **Minimality argument.** We don't prove directly that $k$ is a square  we assume a minimal counterexample exists, then construct a strictly smaller one, which is a contradiction.

---

## The Historical Context

This problem has a fascinating story within the IMO:

- It was proposed by **Stephan Beck** (West Germany)
- During problem selection, **no jury member** managed to solve it within the allotted time
- It was still selected as Problem 6 (traditionally the hardest)
- Out of 268 participants, only **11** achieved the maximum score of 7 points
- Among them: **Nicușor Dan** (Romania, now President of Romania) and **Emanouil Atanassov** (Bulgaria), who reportedly proposed a near one-line solution
- **Terence Tao** (Australia), then 13 years old, won a gold medal at this IMO but did not fully solve Problem 6

Vieta Jumping has since become a **classic technique** in olympiad number theory, reused in countless competition problems worldwide.

---

## Going Further

If this type of reasoning fascinates you:

- **Harvey Cohn, [Advanced Number Theory](https://cdn.preterhuman.net/texts/math/Advanced%20Number%20Theory.pdf)** (read the introduction): explores how symmetric quadratic equations can be generated by a few solutions and elegant jumps  a generalisation of the idea behind Vieta Jumping.

---

*The beauty of mathematics often lies in the contrast between the simplicity of the question and the depth of the answer. IMO 1988 Problem 6 is the perfect embodiment of this.*

*Questions or thoughts? Feel free to reach out on [LinkedIn](https://linkedin.com/in/wassim-trabelsi).*
