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

Before anything I want to thank **Matheus Facure Alves** for his material <a href="https://matheusfacure.github.io/python-causality-handbook/landing-page.html#acknowledgments">_Causal Inference for The Brave and True_</a> which was of great help to understand this topic.

---

You've all heard about Machine Learning. Some say it's amazing; I say it's meh. I'd rather _double it_ and give it to the next person (See what I did there?). This next person happens to be you. You guessed it today we will talk about **Double Machine Learning**

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

# Individual-Level vs Population-Level Outcome Model

Let's recap a bit. We have our outcome $Y_i$ for each individual. It can be expressed as

$$
Y_i(T) = T\tau_i + \bold{f}(\vec{X}_i)\vec{\beta}
$$

where:

- $T \in \{0,1\}$ is the treatment indicator.
- $\tau_i$ is the individual-level treatment effect.
- $\vec{X}_i$ is a row vector of individual covariates.
- $\bold{f}(\vec{X}_i)$ is a function of covariates which we will talk about later.
- $\vec{\beta}$ is a column vector of individual-specific coefficients capturing how covariates impact the outcome.

As you can see, subtracting $Y_i(1) - Y_i(0)$ does give us $\tau_i$, the impact of the treatment.

Let's now take all individuals into account:

$$
\vec{Y}(\vec{T}) = \vec{T} \boldsymbol{\circ} \vec{\tau} + \bold{f}(\bold{X})\vec{\beta}
$$

- $\vec{Y}, \vec{T}, \vec{\tau} \in \R^{N\times 1}$
- $\bold{X} \in \R^{N \times M}$
- $\bold{f}: \R^{N \times M} \rightarrow \R^{N \times M}$
- $\vec{\beta} \in \R^{M \times 1}$

As you can see, subtracting $\vec{Y}(\vec{1}) - \vec{Y}(\vec{0})$ does give us $\vec{\tau}$, the impact of the treatment for each individual.

Remember that we are interested in computing the **ATE** which is defined as
$E[\vec{Y}(1) - \vec{Y}(0)]$.

Expanding the expression

$$
\begin{align*}
E[\vec{Y}(1) - \vec{Y}(0)] &= E[\vec{\tau} + \bold{f}(\bold{X})\vec{\beta} - \bold{f}(\bold{X})\vec{\beta}]
\\
&= E[\vec{\tau}]
\end{align*}
$$

We see that it is indeed what it claims to be: _the average effect of a treatment_ which we will denote $\tau$.

---

A great way to estimate $\tau$ is through **regression**:

$$
\vec{Y} = \tau \vec{T} + \bold{f}(\bold{X})\vec{\beta} + \vec{\epsilon}
$$

It's all fun and games until you find out that the previously introduced function $\bold{f}$ was not _just_ a pretty face but also a gigantic spoilsport. This function represents the relationship between covariates and the outcome. It can be highly flexible and **may introduce non-linearity**, making it difficult to isolate the treatment effect using simple regression.

However, we would like some sort of **linear regression** technique so that we can easily isolate $\tau$ in the final result.

We are stuck with the following probematic: "We want to use a linear model for regression to gain interpretability over a non-linear relation."

Since our primary goal is estimating $\tau$, we might be tempted to ignore $\bold{f}(\bold{X})$ and fit a simple regression $\vec{Y} \sim \tau \vec{T}$. However, if $T$ is correlated with $\bold{X}$, omitting confounders introduces bias. Ideally, we'd like to remove their effect before running the regression.

# The Frisch-Waugh-Lovell Theorem

This <a href="https://en.wikipedia.org/wiki/Frisch%E2%80%93Waugh%E2%80%93Lovell_theorem">beautiful theorem</a> states that in a linear regression model, the coefficients of a subset of regressors can be obtained by first partialling out the effects of the other regressors from both the dependent variable and the subset of interest, and then regressing the residuals of the dependent variable on the residuals of the subset. This simplifies the estimation of coefficients in the presence of multiple regressors.

Essentially, we first remove the effects of $\bold{X}_2$ from $\vec{Y}$ and $\bold{X}_1$, then regress the residualized $\vec{Y}$ on the residualized $\bold{X}_1$ to estimate $\vec{\beta_1}$.

Take the following expression:

$$
\vec{Y} = \bold{X_1} \vec{\beta_1} + \bold{X_2} \vec{\beta_2} + \vec{\epsilon}
$$

This theorem states that the estimate of $\vec{\beta_1}$ will be the same as the estimate of it from a modified regression of the form

$$
\bold{M_{X_2}}\vec{Y} = \bold{M_{X_2}X_1}\vec{\beta_1} + \bold{M_{X_2}}\vec{\epsilon}
$$

where the vector $\bold{M_{X_2}}\vec{Y}$ is the vector of residuals from the regression of $\vec{Y}$ on the columns of $\bold{X_2}$.

The Frisch-Waugh-Lovell theorem gives us a clever way to estimate treatment effects in a linear regression setting, even in the presence of confounders. By first removing the influence of confounders and then regressing the residualized outcome on the residualized treatment, we could isolate the effect of treatment.

But remember $\bold{f}$ ? In practice, it could be **highly non-linear** and **high-dimensional**. This breaks the assumptions required for FWL to work directly.

So, what can we do? **We borrow the core idea of FWL**, that is removing confounders before estimation, and replace the linear regressions with **Machine Learning**.

# Extending FWL with Machine Learning

Rather than assuming a fixed, linear form for $\bold{f}(\bold{X})$, we let **Machine Learning estimate it for us**. The key insight is that if we can accurately capture the influence of confounders on both the outcome $Y$ and the treatment $T$, we can remove their effects before estimating the treatment effect.

This leads to **Double Machine Learning**, which follows a two-step process:

1. Learn the nuisance functions: Use flexible ML models to estimate the relationships $Y \sim \bold{X}$ and $T \sim \bold{X}$.

2. Partial out the confounders: Remove the estimated confounder effects and estimate $\tau$ from the residualized outcome and treatment.

# Conclusion

There we have it, a way of computing the **average treatment effect**! Even if our initial regression equation, $\vec{Y} = \tau \vec{T} + \bold{f}(\bold{X})\vec{\beta} + \vec{\epsilon}$ is non-linear and complex, Double-ML allows us to isolate the computation of $\tau$ by first using ML to account for $\bold{f}(\bold{X})$. This way, we retain the core intuition of FWL while overcoming its limitations.
