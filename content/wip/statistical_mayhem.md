+++
title = 'Unpacked: Statistical Mayhem'
date = '2025-03-01'
[params]
  math = true
+++

<div style="text-align: justify">

The best (or worst) thing about trying to dive deeper into
sujects _after college_ is that you may realize that **passing a class is very different from actually understanding the subject** (though not incompatible).

This morning I add a _wait moment_ (kind of like _DeepSeek_) regarding statistics. Thus, I (and you alongside with me) will try to get a better understanding of some **fundamental** concepts.

<img src="/statistical_mayhem/hold_up_meme.png" style="display: block; margin: auto;" />

Let's start with one if not **the most important** concept in statistics: _Expected Value_

# Expected Value: The random variable's center of gravity

Expected value is a bit of a tricky one due to its simplicity... I mean this line on its Wikipedia article sums it up pretty well: "_Informally, the expected value is the mean of the possible values a random variable can take_".

We will start with a quick reminder of the formulas. Given a _random variable_ $X$:

- If $X$ is **discrete**, we define it's expected value to be
  $$
  \begin{equation}
  \mathbb{E}[X] = \sum_{x_i \in \Omega}{x_i\mathbb{P}[X = x_i]}
  \end{equation}
  $$
- If $X$ is **continuous**, we define it's expected value to be
  $$
  \begin{equation}
  \mathbb{E}[X] = \int_{\Omega}{x f(x)\, dx}
  \end{equation}
  $$

_These definitions are far from being rigorous but they will be sufficient for now._

Under the hood they both convey the **same idea**. To find the expected value of _something_ you need to take the **weighted average** of this _something_, that is the sum of all possible values of this _something_, each multiplied by their probability of realisation.

---

To illustrate this I won't use the _dice example_ as all of us know about it and will try to adopt a more visual approach.

Let's modify our initial formula $(1)$ a bit by taking $\bold{X} = (X_1, X_2)$ instead of just $X$:

$$
\mathbb{E}[\bold{X}] = \sum_{(x_i, y_i) \in \Omega}{(x_i, y_i)\mathbb{P}[\bold{X} = (x_i, y_i)]}
$$

Furthermore, we set $\Omega = \{(1, 1), (1, -1), (-1, 1), (-1, -1)\}$.

### Uniform distribution: The base case

Say that $\Omega$ follows a _uniform distribution_.

Computing it's expected value

$$
\mathbb{E}[\bold{X}] = \frac{1}{4} \times [(1, 1) + (1, -1) + (-1, 1) + (-1, -1)] = (0, 0)
$$

we find that the expected value of $\bold{X}$ is the origin of the plane.

We will place these points on a plane and see how their center of gravity evolves.
To better illustrate what I mean, I displayed the changing probability of each point (as we gradually add points which follow a uniform distribution) as the _weight_.

{{< youtube "I_u2xAk50gg" >}}

...

### From uniform to non-uniform:

Let's now remove the uniform distribution and say that we now have the following probability distribution:

$$
\mathbb{P}[\bold{X} = (x_i, y_i)] =
\begin{cases}
\frac{5}{8}, & \text{if } (x_i, y_i) = (-1, -1) \\
\frac{1}{8}, & \text{otherwise}
\end{cases}
$$

Computing

$$
\mathbb{E}[\bold{X}] = \frac{1}{8} \times [(1, 1) + (1, -1) + (-1, 1)] + \frac{5}{8} \times (-1, -1) = (-\frac{1}{2}, -\frac{1}{2})
$$

we find that the expected value of $\bold{X}$ is the point $(-\frac{1}{2}, -\frac{1}{2})$.

{{< youtube "VR4hqQA372w" >}}

...

---

All this is great but check the following data points.

<img src="/statistical_mayhem/same_mean.png" style="display: block; margin: auto;" />

Even though they have the same mean it is clear the yellow dots are closer to their mean than red dots. This observation leads us to an important question: how can we measure this difference in spread when the means are identical?

# Variance: Measuring the Spread from Center

At first, one may be tempted to compute the difference of each element to the mean

$$
X - \mathbb{E}[X]
$$

and then to average it

$$
\mathbb{E}[X - \mathbb{E}[X]]
$$

However, if you take a fraction of a second to think about it you will quickly realise that is **always equals zero**.

It's only logical as the differences will cancel out. One way to fix this would be to take the **absolute value** of each difference

$$
\mathbb{E}[|X - \mathbb{E}[X]|]
$$

One of the key things to remember in math is that **nobody likes absolute values** as they are a pain to work with. You know what is one of the things people like to work with and that achieves the same goal ? **Squared values** ! This gives us the following formula:

$$
\mathbb{E}[(X - \mathbb{E}[X])^2]
$$

The ~~only~~ downside with variance is that its unit is not the same as our initial one. That is, if $X$ is expressed in meters $m$, $\text{Var}(X)$ will be expressed in meters squared $m^2$ which is harder to visualize.

This is why many like to work with _standard deviation_.

The _standard deviation_ of a random variable $X$ is defined as:

$$
\begin{align*}
\sigma &= \sqrt{\mathbb{E}[(X - \mathbb{E}[X])^2]} \\
&= \sqrt{\text{Var}(X)}
\end{align*}
$$

As said above, taking the **square root** of the variance is great as it gives us the original unit of measure !

---

Awesome, we now have a way to better understand a random variable ! But hear me out: What if... what if we are working with **multiple random variables** ?

# Covariance: Measuring How Two Variables Move Together

We define the _covariance_ between two random variables $X$ and $Y$ as:

$$
\text{cov}(X, Y) = \mathbb{E}[(X - \mathbb{E}[X])(Y - \mathbb{E}[Y])]
$$

### What does this mean ?

Covariance measures the extent to which two random variables vary together. If $X$ and $Y$ tend to increase and decrease together, their covariance is **positive**. If one tends to increase when the other decreases, their covariance is **negative**. If there is no discernible pattern, their covariance is **close to zero**.

### Intuition Behind the Formula

As you can see, under the hood this formula is actually the **expected value** of a third random variable $Z$ defined as:

$$
Z = (X - \mathbb{E}[X])(Y - \mathbb{E}[Y])
$$

You might ask why can't we simply take look at $Z = XY$ and why subtracting their respective **expected values** is relevant here.

To develop intuition, let’s consider an example:

- Suppose we track the daily temperature $X$ and the number of ice creams sold $Y$ at a beach.
- We suspect that warmer temperatures might lead to higher ice cream sales.
- The question is: **Do these variables actually move together?**

#### Why Not Just Use $XY$?

If we only looked at $XY$, we'd face a problem:

- The values of $X$ and $Y$ can be large or small, but without considering their typical (mean) values, we cannot determine their relative movement.
- A large $XY$ does not necessarily mean a strong relationship; it might just mean one variable is large in absolute terms.
- If $X$ and $Y$ are always positive (e.g., temperature and ice cream sales), $XY$ will always be positive, making it misleading as a measure of co-movement.

By **subtracting their respective expected values** (i.e., taking $Z = (X - \mathbb{E}[X])(Y - \mathbb{E}[Y])$), we normalize these values, ensuring that we measure _relative movement_ rather than _absolute magnitudes_:

- If $Z > 0$, both values are either above or below their respective means.

  $\Rightarrow$ They move **together**.

- If $Z < 0$, one is above its mean while the other is below.

  $\Rightarrow$ They move **oppositely**.

- The **magnitude** of $Z$ indicates how strong the relationship is: large deviations imply a strong relationship, small deviations imply a weak one.

By taking the expectation $\mathbb{E}[Z]$, we determine whether these relationships hold _on average_.

The problem with covariance is that it is **not scale-free** which means that it is hard to interpret beside whether the variables move together.

To understand this, take a look at the following example:

```python
import numpy as np

X_1 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
Y_1 = [2*x for x in X_1] # Define Y_1 = 2*X_1

X_2 = [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
Y_2 = [2*x for x in X_2] # Define Y_2 = 2*X_2

# Print cov(X, Y) with two decimals
print(f"Cov(X_1, Y_1) = {np.cov(X_1, Y_1)[0, 1]:.2f}")
print(f"Cov(X_2, Y_2) = {np.cov(X_2, Y_2)[0, 1]:.2f}")
```

```python
Cov(X_1, Y_1) = 18.33
Cov(X_2, Y_2) = 73.33
```

See ? We didn't obtain the same results even though the relationships did not change (i.e., $Y = 2X$).

But what if... what if there was a way to make this scale free ?

# Correlation: ...

We define the _correlation_ between two random variables $X$ and $Y$ as:

$$
\text{corr}(X, Y) = \frac{\text{cov}(X, Y)}{\sigma_X\sigma_Y}
$$
