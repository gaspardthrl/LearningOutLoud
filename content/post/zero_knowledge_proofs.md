+++
title = 'Unpacked: Zero-Knowledge Proofs'
date = '2025-03-14'
[params]
  math = true
+++

<div style="text-align: justify">

Before anything, don't worry if you don't understand anything. As the title suggests this post will provide you with **zero knowledge**.

---

Ever said _'On God'_ to someone, hoping they'd believe you even without any actual evidence? Well, zero-knowledge proofs are a bit similar. They let you prove that you know something **without revealing it**.

This topic is fairly complex. Before diving into what zero-knowledge proofs are, let's make sure that our foundations are rock solid.

# Wait a minute... what is a proof?

A proof is a **logical argument** that **demonstrates the truth of a statement**. For example, what proves that I am French ? Most would say that it is my pretty face coupled with my love for bread, wine, and cheese. While I can't disagree, the strongest proof that I am French is my parentage. Indeed, In France, we have what is called _jus sanguinis_ (blood right), meaning that if one of your parents is French you are automatically granted the French nationality. My parents being both French and the _blood right_ being in effect, I am French.

To **formally describe** proofs, especially in computational complexity and cryptography, we break them down into four key components:

- **Language** $(L)$ - It consists of _all valid inputs_ that satisfy a given property.

  _Example_: Consider the set of all composite numbers (i.e., not prime). The language here is

  $$L = \{x | x \text{ is not a prime number}\}$$

- **Verifier** $(V)$ - A function (which can be thought of as an _algorithm_) that checks whether a given statement belongs to the language. It takes an _input_ and a _proof_ and determines whether the proof is valid.

  _Example_: A verifier for composite numbers might check whether a given number has any divisors other than $1$ and itself.

- **Witness** $(w)$ - A piece of evidence that helps convince the verifier that the statement is true. It is sometimes referred to as the **certificate** $(c)$.

  _Example_: If someone claims that $91$ is not prime, a witness could be a divisor like $7$ (since $91 = 7 \times 13$)

- **Prover** $(P)$ - The entity that constructs and presents a proof to convince the verifier

  _Example_: The prover knows $w = 7$ and want to convince the verifier that $91$ belongs to $L$ (i.e. that $91$ is _composite_).

Note that we will only consider [NP-Complete](https://en.wikipedia.org/wiki/NP-completeness) problems here. I won't elaborate on them, but just think of them as all problems for which a solution is **easy to verify** but **hard to find** (e.g., a _sudoku_). If you're interested, [here](https://www.claymath.org/millennium/p-vs-np/)'s an easy way to make some _pocket money_.

<img src="/zero_knowledge_proofs/p_equals_np_meme.png" style="display: block; margin: auto;" />

---

# What does it mean for a proof to be _zero-knowledge_?
_We refer to people following the protocol properly as honest_.

For the proof a given statement to be _zero-knowledge_ it must satisfy **three properties**:
- **Completeness** - If a statement is **true**, then an _honest verifier_ will be convinced of this fact by an _honest prover_ .

  In other words, if what you claim to be true is true, the verifier will always validate your claim.

- **Soundness** - If the statement is false, then no _cheating prover_ can convince an _honest verifier_ thet it is true, **except with some small probability**.

  In other words, if what you claim to be true is false, the verifier will almost always reject your claim. While probabilistic, the error probability is so small that we are okay with this not being true exactly 100% of the time.

- **Zero-knowledge** - If the statement is true, then no verifier learns anything other than the fact that the statement is true.

  In other words, the verifier knows that I know but does not know what I know.

<img src="/zero_knowledge_proofs/introduction_meme.png" style="display: block; margin: auto;" />


Zero-knowledge proofs can be subdivided into **two categories**:
- **Interactive** - This means that the _prover_ and the _verifier_ exchange **multiple** messages.

- **Non-interactive** - This means that the _verifier_ is convinced by a **single** message.

# Interactive zero-knowledge proofs

We will start by picturing what an interactive proof looks like and how can it reveal no information using the [Ali Baba Cave](https://en.wikipedia.org/wiki/Zero-knowledge_proof#The_Ali_Baba_cave) example.

## Ali Baba and his cave

Picture this: You are in front of a cave with a single entrance that branches into two paths, $A$ and $B$. These paths are separated by a **password-protected door**. Without the password, one must **exit the cave using the same path they entered**.

<img src="/zero_knowledge_proofs/ali_baba_cave_example.png" style="display: block; margin: auto;" />

<br>

You, the _prover_, claim to know the password. I, the _verifier_, want to confirm this without you revealing the password to me.

How can I be sure that you indeed know the password without you telling me the password and trying out myself ?

Here's how we can achieve this:
- You enter the cave without me seeing which path you take.
- I then ask you to exit the cave using a specific path, say $A$.

There are four possible outcomes:

- You don't know the password but luckily chose path $A$ initially.
- You don't know the password and chose path $B$, so you exit through $B$.
- You know the password and happened to choose path $A$ initially.
- You know the password, initially chose $B$, but can use the password to exit through $A$.

If you exit through $B$ when asked to use $A$, I'll reject your claim because you either don't have the password, or you have the password but are _too dumb_ for me to accept you. If you exit through $A$, I won't immediately accept your claim, as you might have been lucky.

If we perform this experience only once, and you exit the cave taking path $A$, there is a $50%$ chance that you don't have the password and were just lucky.

That's where the **iterative** aspect of these proofs comes into play: Each time we replay this cave adventure, with me randomly picking your exit path, your odds of consistently lucking out plummet. It's like trying to win a coin toss not just once, but over and over again.

$$
\lim_{n \to \infty}{\frac{1}{2^n}} = 0
$$

Obvisouly we don't repeat this experience an infinite amount of time but we do so **until this probability becomes too small for us to care**.

---

