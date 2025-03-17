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

<img src="/zero_knowledge_proofs/introduction_meme.png" style="display: block; margin: auto;" />

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

---

Note that we will only consider [NP-Complete](https://en.wikipedia.org/wiki/NP-completeness) problems here. I won't elaborate on them, but just think of them as all problems for which a solution is **easy to verify** but **hard to find** (e.g., a _sudoku_). If you're interested, [here](https://www.claymath.org/millennium/p-vs-np/)'s an easy way to make some _pocket money_.

<img src="/zero_knowledge_proofs/p_equals_np_meme.png" style="display: block; margin: auto;" />
