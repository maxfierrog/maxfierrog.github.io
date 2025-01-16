---
title: "The Elo Rating System via Likelihood Gradient Ascent"
date: 2025-01-09
math: true
draft: true
---

# Introduction

TODO

### Abstract

TODO

---

# Background

TODO

### The Elo rating system

TODO

### Likelihood maximization

TODO

---

# Derivation

Let $\bold{r} = [ r_1, \\, \ldots, \\, r_n ]^\top$ be a vector encoding a player rating profile. Now, allow $\bold{X} \sim \mathcal{N}(\bold{r}, \sigma^2I)$ to be a multivariate gaussian of the players' performance on a given match.

TODO: Note how to generalize to $n > 2$ players.

We obtain the likelihood function of a match outcome $O$, a bernoulli random variable on the event of $X_1 > X_2$ (indicating that the performance of player 1 was dominant),

$$
\mathcal{L}(O; \\, \bold{r}) = \mathbb{P}[X_1 - X_2 > 0 \\; | \\; \bold{r}].
$$

Given that $\bold{X}$ is gaussian,

TODO: Derivation
