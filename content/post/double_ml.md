+++
title = 'Unpacked: Double Machine Learning'
date = '2025-03-14'
[params]
  math = true
+++

<div style="text-align: justify">
<style>
    table {
        width: 100%;
    }
    table :is(td, th) {
      padding: 0.3em;
    }
tr:nth-child(even) {
  background-color: lightgray;
}
</style>

You've all heard about Machine Learning. Some say it's amazing; I say it's meh. I'd rather _double it_ and give it to the next person (See what I did there?). This next person happens to be you. You guessed it today we will talk about **Double Machine Learning (Double ML)**

For now, we’ll focus on how Double ML is used to compute the **Average Treatment Effect (ATE)**. Conditional ATE (CATE) might come later.

# Introduction: Causation vs. Correlation

Before diving into Double ML, let’s talk about **causation** and **correlation**.

Imagine we have a population, and for each individual $i$ we observe an outcome $Y_i$ . Now, suppose we’re interested in understanding the effect of some treatment $T$ on an this outcome.

Each $Y_i$ has **two potential outcomes**:

- $Y_i(0)$ → The outcome if the individual **did not** receive the treatment.
- $Y_i(1)$ → The outcome if the individual **did** receive the treatment.

However, here’s the catch: **We can only observe one of these outcomes per individual**. If someone receives the treatment, we’ll see $Y_i(1)$, but we’ll never know what $Y_i(0)$ would have been (and vice versa).

The goal of **causal inference** is to estimate the effect of the treatment:

$$
Y_i(1) - Y_i(0)
$$

Since we can’t measure this for each individual, we instead try to estimate the **population-level effect**:

$$
E[Y(1) - Y(0)]
$$

where the expectation is taken over the entire population.

### Why You Can’t Just Compare Treated vs. Untreated Groups

A common mistake is to estimate the treatment effect using:

$$
\begin{equation}
E[Y | T = 1] - E[Y | T = 0]
\end{equation}
$$

which seems reasonable at first: it compares the average outcome of treated vs. untreated individuals. But **this is wrong** because it captures **association, not causation**.

Let’s expand the terms:

$$
E[Y | T = 1] - E[Y | T = 0] = E[Y(1) | T = 1] - E[Y(0) | T = 0]
$$

By adding and subtracting $E[Y(0) | T = 1]$, we get:

$$
\underbrace{E[Y(1) | T = 1] - E[Y(0) | T = 1]}_{\text{ATT (Average Treatment Effect on the Treated)}} + \underbrace{E[Y(0) | T = 1] - E[Y(0) | T = 0]}_{\text{Bias}}
$$

The second term (Bias) represents the difference between treated and untreated individuals **before** treatment. It captures pre-existing differences (_confounders_) rather than the causal effect.

### What does it look like with a basic example ?

Let's say we want to understand the impact of a taking a specific _food supplement_ on someone's height but we don't want to perform any **randomized controlled trials** or any experiment that would grant us new data. We only want to work with **already available data**.

What we have is a population $Y$ inside which some people took this food supplement and others didn't.

**NB:** In our experiment, $Y_i$ represents the **height** of the $i^{\text{th}}$ individual.

Here is our data:

| $\text{Individual}$ |         $Y(0)$          |         $Y(1)$          | $T$ |
| :------------------ | :---------------------: | :---------------------: | :-: |
| $1$                 | $\color{red}\textbf{?}$ |          $186$          | $1$ |
| $2$                 |          $171$          | $\color{red}\textbf{?}$ | $0$ |
| $3$                 | $\color{red}\textbf{?}$ |          $190$          | $1$ |
| $4$                 |          $174$          | $\color{red}\textbf{?}$ | $0$ |
| $5$                 |          $169$          | $\color{red}\textbf{?}$ | $0$ |
| $6$                 | $\color{red}\textbf{?}$ |          $185$          | $1$ |
| $7$                 | $\color{red}\textbf{?}$ |          $195$          | $1$ |
| $8$                 |          $170$          | $\color{red}\textbf{?}$ | $0$ |

Let's try with $(1)$ :

- We first compute the average height of **people who did not took the food supplement**

  $\Rightarrow E[Y | T = 0] = \frac{171 + 174 + 169 + 170}{4} = 171$

- We then compute the average height of **people who took the food supplement**

  $\Rightarrow E[Y | T = 1] = \frac{186 + 190 + 185 + 195}{4} = 189$

Applying the above formula we find that taking the food supplement results in a height increase of $189 - 171 = 18\text{cm}$.

Well that's awesome ! Where can I find this amazing supplement ?

For the sake of this experiment we were granted a time machine. Let's go back in time just to make sure our results are correct.

After our little trip we are back with this updated table

| $\text{Individual}$ | $Y(0)$ |         $Y(1)$          | $T$ |
| :------------------ | :----: | :---------------------: | :-: |
| $1$                 | $186$  |          $186$          | $1$ |
| $2$                 | $171$  | $\color{red}\textbf{?}$ | $0$ |
| $3$                 | $190$  |          $190$          | $1$ |
| $4$                 | $174$  | $\color{red}\textbf{?}$ | $0$ |
| $5$                 | $169$  | $\color{red}\textbf{?}$ | $0$ |
| $6$                 | $185$  |          $185$          | $1$ |
| $7$                 | $195$  |          $195$          | $1$ |
| $8$                 | $170$  | $\color{red}\textbf{?}$ | $0$ |

Remember how $(1)$ included a $\text{Bias}$ term when we expanded it ? Let's see what it looks like with our new data.

- We compute once again the average height of **people who took the food supplement** before they actually took it

  $\Rightarrow E[Y(0) | T = 0] = \frac{171 + 174 + 169 + 170}{4} = 171$

- Now, let's compute the average height of these same individuals **before** they took the supplement

  $\Rightarrow E[Y(0) | T = 1] = \frac{186 + 190 + 185 + 195}{4} = 189$

We find that the _bias_, that is the initial difference between the two sub-populations, is of $18 \text{cm}$.

_Wait... What ???_

This means that the $\text{ATT}$, the average treatment effect on the treated, is actually $\text{0}$. In other words, the supplement had no effect at all: the difference we observed was purely due to **pre-existing height differences between the two groups**.

We were lucky to have our time machine to reveal the truth but as you may have noticed, we don't have that luxury in real life. Since we can never observe both outcomes $Y(0)$ and $Y(1)$ for the same individual, we must be extra cautious. Without a properly designed experiment, we risk mistaking correlation for causation, leading to completely false conclusions.

This is why randomized controlled trials are so valuable: they **help us separate real effects from misleading associations**.

But what if we cannot perform RCTs ?

# Computing the ATE

Let's recap a bit. We have our outcome $Y_i$ for each individual. It can be expressed as

$$
Y_i(T) = T\tau_i + f(\bold{X}_i)\boldsymbol{\beta}_i + \epsilon_i
$$

where $T$ represents whether we applied the treatment or not, $\tau_i$ the impact of the treatment on our outcome $i$, $f(\bold{X}_i)$ all elements that have an impact on our outcome, $\boldsymbol{\beta}_i$ their impacts, and $\epsilon_i$ the error term.

As you can see, subtracting $Y_i(1) - Y_i(0)$ does give us $\tau_i$, the impact of the treatment.
