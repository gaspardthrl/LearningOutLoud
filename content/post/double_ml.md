+++
title = 'Unpacked: Double Machine Learning'
date = '2025-03-04'
[params]
  math = true
+++

You've all heard about Machine Learning. Some say it's amaying, I say it's meh. I would rather _double it_ and give it to the next person which, happend to be you (see the pun I just did ?). You guessed it today we will talk about Double Machine Learning.

**NB:** For now we will focus on how Double ML is used to compute ATE. CATE may come later.

# Introduction

Before diving into this subject we need to talk about **causation** and **causal inference**.

We will start with a very basic example:

Say we have two populations $P_D$ and $P_C$ where people in $P_D$ were given a drug to increase their heights and people in $P_C$ were not. A few weeks lates, we measure the average height ($H_D$ resp. $H_C$) of each population and find out that $H_D$ > $H_C$. Wait what ? Does this mean that the drug works ? Let's rewind a bit.

We travel back in time a few weeks before people in $P_D$ were given the drug, measure their initial height $H_D'$, and compare it to $H_C$. We make a shocking discovery: $H_D'$ > $H_C$.

Our initial thought was equivalent to the following computation:

$$
E[Y | T = 1] - E[Y | T = 0]
$$

If we expand this calcul we get

$$
\begin{align*}
&= E[P_D | T = 1] - E[P_C | T = 0] \\
&= E[P_D | T = 1] - E[P_C | T = 0] + E[P_C | T = 1] - E[P_C | T = 1] \\
&= \underbrace{E[P_D - P_C | T = 1]}_\text{ATT} + \underbrace{E[P_C | T = 1] - E[P_C | T = 0]}_\text{Bias}
\end{align*}
$$
