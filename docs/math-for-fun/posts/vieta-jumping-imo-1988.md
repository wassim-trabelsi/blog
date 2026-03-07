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
  - infinite-descent
comments: true
description: Vieta Jumping  the most elegant proof in the history of the International Mathematical Olympiad. IMO 1988, Problem 6.
---

# Vieta Jumping  The Most Beautiful Proof in Olympiad History (IMO 1988, Problem 6)

In 1988, at the International Mathematical Olympiad (IMO) in Canberra, Australia, **Problem 6** brought the world's best young mathematicians to their knees. Proposed by Stephan Beck (West Germany), this problem was considered so difficult that even the jury members struggled to solve it. Only **11 out of 268 participants** achieved a perfect score  among them, a 17-year-old prodigy named **Terence Tao**, future Fields Medalist.

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

???+ example "Quick example"
    For $x^2 - 5x + 6 = 0$, the roots are $x_1 = 2$ and $x_2 = 3$.

    - Sum: $2 + 3 = 5$ ✓
    - Product: $2 \times 3 = 6$ ✓

That's all we'll need. Let's move on to the proof.

---

## The Proof by Vieta Jumping

### Step 1  Setup

Let $k$ be a positive integer such that:

$$\frac{a^2 + b^2}{ab + 1} = k$$

Why is $k \in \mathbb{N}^*$? Because:

- $k$ is an **integer** (by the divisibility hypothesis)
- $k$ is **positive** (numerator and denominator are both positive)
- $k$ is **nonzero** (the numerator $a^2 + b^2 > 0$ since $a, b \geq 1$)

We introduce the set:

$$S_k = \left\{(a, b) \in (\mathbb{N}^*)^2 \;\middle|\; \frac{a^2 + b^2}{ab + 1} = k \right\}$$

???+ example "A concrete example"
    The pair $(8, 2)$ belongs to $S_4$ because:

    $$\frac{8^2 + 2^2}{8 \times 2 + 1} = \frac{64 + 4}{17} = \frac{68}{17} = 4$$

    And $4 = 2^2$ is indeed a perfect square!

    By symmetry ($a$ and $b$ play symmetric roles), $(2, 8) \in S_4$ as well.

### Step 2  Choosing a solution and proof by contradiction

Pick a pair $(a_1, b_1) \in S_k$ with $a_1 \geq b_1$ (always possible by symmetry).

**Assume for contradiction that $k$ is not a perfect square.**

Since $(a_1, b_1) \in S_k$, we have:

$$a_1^2 + b_1^2 = k(a_1 b_1 + 1)$$

Rearranging in terms of $a_1$:

$$a_1^2 - k b_1 \cdot a_1 + (b_1^2 - k) = 0$$

### Step 3  The key insight: viewing $a_1$ as a root of a polynomial

Consider the polynomial in $x$:

$$P(x) = x^2 - kb_1 \cdot x + (b_1^2 - k) = 0$$

We observe that $a_1$ is a **root** of this polynomial (by construction). Let $a_2$ be the **other root**.

By **Vieta's formulas**:

$$\begin{cases} a_1 + a_2 = kb_1 \\ a_1 \cdot a_2 = b_1^2 - k \end{cases}$$

From which we deduce:

$$a_2 = kb_1 - a_1 \qquad \text{and} \qquad a_2 = \frac{b_1^2 - k}{a_1}$$

### Step 4  Showing that $(a_2, b_1) \in S_k$

Since $a_2$ is also a root of $P(x) = 0$, retracing the algebra:

$$a_2^2 - kb_1 \cdot a_2 + b_1^2 - k = 0$$

$$\implies a_2^2 + b_1^2 = k(a_2 b_1 + 1)$$

$$\implies \frac{a_2^2 + b_1^2}{a_2 b_1 + 1} = k$$

But for $(a_2, b_1)$ to truly belong to $S_k$, we must verify that $a_2 \in \mathbb{N}^*$.

**$a_2$ is an integer:**

$a_2 = kb_1 - a_1$, and $k$, $b_1$, $a_1$ are all integers. ✓

**$a_2$ is nonzero:**

$a_2 = \dfrac{b_1^2 - k}{a_1}$. If $a_2 = 0$, then $k = b_1^2$, making $k$ a perfect square. But we assumed otherwise. So $a_2 \neq 0$. ✓

**$a_2$ is positive:**

Suppose for contradiction that $a_2 < 0$. Then $-a_2 > 0$, and since $k \geq 1$ and $b_1 \geq 1$:

$$\underbrace{a_2^2}_{> 0} + \underbrace{(-a_2) \cdot k \cdot b_1}_{> 0} + b_1^2 - k = 0$$

$$\implies a_2^2 + kb_1|a_2| + b_1^2 = k$$

But the left side is at least $a_2^2 + b_1^2 \geq 1 + 1 = 2$ (since $a_2 \neq 0$ and $b_1 \geq 1$), plus a positive term $kb_1|a_2| \geq 1$. So the left side is $\geq 3$, yet it should equal $k$.

Meanwhile, from the equation $a_2^2 + b_1^2 = k(a_2 b_1 + 1)$ with $a_2 < 0$, the right side $k(a_2 b_1 + 1)$ can become very small or even negative, leading to a **contradiction** since the left side $a_2^2 + b_1^2$ is always strictly positive.

Therefore $a_2 > 0$. ✓

### Step 5  The jump that changes everything

We've shown that $(a_2, b_1) \in S_k$. Now observe the crucial inequality:

$$a_2 = \frac{b_1^2 - k}{a_1} < \frac{b_1^2}{a_1} \leq \frac{a_1^2}{a_1} = a_1$$

Therefore:

$$\boxed{a_2 < a_1}$$

**This is the Vieta Jump!** We've "jumped" to a strictly smaller solution.

### Step 6  Infinite descent

By repeating the process:

- From $(a_1, b_1) \in S_k$, we construct $(a_2, b_1) \in S_k$ with $a_2 < a_1$
- From $(a_2, b_1) \in S_k$, we construct $(a_3, b_1) \in S_k$ with $a_3 < a_2$
- From $(a_3, b_1) \in S_k$, we construct $(a_4, b_1) \in S_k$ with $a_4 < a_3$
- And so on...

We obtain a **strictly decreasing** sequence of positive integers:

$$a_1 > a_2 > a_3 > a_4 > \cdots > 0$$

**But this is impossible!** A strictly decreasing sequence of positive integers must be **finite**. You cannot descend forever in $\mathbb{N}^*$.

!!! example "Illustration"
    Imagine the sequence $10, 8, 5, 2, 1, \ldots$  after $1$, there is no smaller positive integer. We're stuck.

This contradiction proves that our initial assumption was false.

$$\boxed{k \text{ is a perfect square.} \quad \blacksquare}$$

---

## Why Is This Proof So Beautiful?

This problem is often cited as **the most beautiful problem in Olympiad history** for several reasons:

1. **The statement is simple.** Any high school student can understand the question. No advanced knowledge is required.

2. **The difficulty is extreme.** Despite the simplicity of the statement, it defeated 95% of the world's best young mathematicians.

3. **The tools are elementary.** The proof uses only Vieta's formulas (high school curriculum!) and the principle of infinite descent.

4. **The idea is deeply creative.** The stroke of genius is to *fix $b$* and view the equation as a polynomial in $a$, then use the second root to "jump" to a smaller solution. It is an act of pure mathematical creation.

5. **Infinite descent.** This method, popularized by **Fermat**, transforms a seemingly static problem into a dynamic argument. We don't prove directly that $k$ is a square  we show that assuming otherwise creates a process that cannot terminate, which is absurd.

---

## The Historical Context

This problem has a fascinating story within the IMO:

- It was proposed by **Stephan Beck** (West Germany)
- During problem selection, **no jury member** managed to solve it within the allotted time
- It was still selected as Problem 6 (traditionally the hardest)
- Out of 268 participants, only **11** achieved the maximum score of 7 points
- Among them: **Terence Tao** (gold medal at age 13 in 1988, Fields Medal in 2006) and **Emanouil Atanassov** (Bulgaria), who reportedly proposed a near one-line solution

Vieta Jumping has since become a **classic technique** in olympiad number theory, reused in countless competition problems worldwide.

---

## Going Further

If this type of reasoning fascinates you, here are some leads:

- **Fermat's infinite descent**: the same idea applied to other number theory problems
- **The International Mathematical Olympiad**: an extraordinary playground for curious minds
- **Art and Craft of Problem Solving** by Paul Zeitz: an excellent book for developing mathematical intuition

---

*The beauty of mathematics often lies in the contrast between the simplicity of the question and the depth of the answer. IMO 1988 Problem 6 is the perfect embodiment of this.*

*Questions or thoughts? Feel free to reach out on [LinkedIn](https://linkedin.com/in/wassim-trabelsi).*
