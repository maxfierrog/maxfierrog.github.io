---
title: N-Gram Model of Optimal Policy on Interpretable Abstractions
category: technical
date: 2025-02-21
math: true
---

{{< toc >}}

## Abstract

Generally, the choice of functional form of a policy model and of the domain that it operates on forms the basis of interpretability. Domains that are the image of class-valued abstractions of the observable state space are desireable because humans excel at visual classification tasks that map onto (largely) discrete characteristics. Hence, we provide an interpretable functional form that is valid over multiclass spaces in the form of an $n$-gram model approximation of dynamics under optimal policy.

---

## Background

### $N$-Gram Modeling

$n$-gram models were developed as a rudimentary statistical model of language. Assuming an $n^{th}$-order [Markov property](https://en.wikipedia.org/wiki/Markov_property) on the probability of a word $w_{t + 1}$ at discrete time $t + 1$ given a history $\langle w_i \rangle_{i \in [1, \\, t]}$,

$$
\begin{equation}
  P(w_1, \, \ldots, w_{t + 1}) =
  P(w_1, \dots, w_{t - n - 1}) \prod_{i = 0}^{n - 1} P(w_{t+1} \mid w_{t-n}, \dots, w_t),
\end{equation}
$$

straightforward [maximum likelihood estimation](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) shows that this probability is the proportion of times that the sequence $\langle w_{t-n}, \\, \ldots, w_t \rangle$ appears before $w_{t + 1}$ in observations. This is can be seen as frequentist inference, making the probability measure intuitive.

When applied to a set of symbols (words) $S$, such a model implies a Markov chain over the product $S^n = S \times \cdots \times S$. It follows that the chain's [stochastic matrix](https://en.wikipedia.org/wiki/Stochastic_matrix) $\Pi$ is an element of $\mathbb{R}^{k \times k^n}$ with $k = |S|$, so the number of learnable parameters grows exponentially with the order of the model for a fixed $S$.

As a result of upholding the Markov property, $n$-gram models are stationary[^stationary]. This flaw makes them incompatible with natural language to any useful extent, and is directly addressed by modern language models through mechanisms like [attention](https://en.wikipedia.org/wiki/Attention_(machine_learning)).

### Rules of Thumb

Many heuristics taught in strategic decision-making can be described to be conditionals on the result of classification exercises. For example, there is a rule of thumb in Chess which calls for protecting one's own king if it is open.

When implementing this heuristic, a player performs classification via a mapping $\phi : S \to \\{\text{Yes}, \\, \text{No}\\}$ from the set of board states to an answer to the heuristic's condition, where experience insists that if a player's $\phi$ is sufficiently close to ground truth, they obtain a performance improvement in expectation.

Naturally, the complexity involved in evaluating a classification $\phi_h(s)$ for some state $s \in S$ should be minimal so that its heuristic $h$ can be implemented without computer assistance. In many cases, their simplicity to humans (i.e., how intuitive they are) directly translates to the simplicity of implementing them in other models of computation. Put simply, it is generally easy to program such functions.

However, humans can obtain an _unexplainable_ intuitive understanding of a game. In such cases, the classification exercises they carry out for their expert heuristics are mappings onto a set of abstract characteristics (e.g., area 'crowdedness' in Chess). This can be seen as [representation learning](https://en.wikipedia.org/wiki/Feature_learning).

But even in these cases, it is relatively simple to train a model which replicates a human's capacity to perform classification for their own expert-level heuristics by having them label training datasets by hand. Hence, one can generally assume access to efficient classifiers for human-interpretable features.

### Markov Abstractions

Given an abstraction $\phi : S \to Z$ over a state set $S$, the lack of an injectivity constraint could produce a situation where, for a policy $\pi_S : S \to S$ with $\pi(s) = a$ and $\pi(s^\prime) = b$ on distinct $a, \\, b, \\, s, \\, s^\prime \in S$,

$$
\begin{equation}
  \phi(s) = \phi(s^\prime) \;\; \text{and} \;\; \phi(a) \neq \phi(b).
\end{equation}
$$

Hence, learning a counterpart $\pi_Z : Z \to Z$ which preserves the information in $\pi_S$ could be impossible, as $\pi_Z(\alpha(s)) = \pi_Z(\alpha(s^\prime))$ would have to 'retain' the information of both $\pi_S(s) = a$ and $\pi_S(s^\prime) = b$. Such an abstraction $\phi$ is said to not be Markov, as its image contains insufficient information to induce a dynamics that corresponds to the behavior specified by $\pi_S$ in $S$.

In the context of interpretable rules of thumb, reducing the state space $S$ to significantly smaller abstract spaces (e.g., taking decision in $\\{\text{Yes}, \\, \text{No}\\}$ while implementing a heuristic) nearly guarantees that the abstraction which mediated the reduction is not Markov[^markov-heuristics].

---

## Model

Let $\langle \phi^{(\alpha)} : S \to Z^{(\alpha)} \rangle_{\alpha \in \Alpha}$ be a collection of abstractions enumerated in $\Alpha$, and $\pi_S : S \to S$ a policy over $S$. We propose modeling class-conditional transition probability distributions,

$$
\begin{equation}
  P^{(\alpha)}_{t+1}(k) = P[\phi^{(\alpha)}(\pi^{t + 1}(s)) = k \; | \; \phi^{(\alpha)}(\pi^t(s)) = k_t, \, \ldots, \, \phi^{(\alpha)}(\pi^0(s)) = k_0],
\end{equation}
$$

of the elements $k_i \in Z^{(\alpha)}$ via an $n$-gram model. This effectively establishes sequences in $\phi^{(\alpha)}(S)$ via repeated aplication of $\pi$ within $S$ (following the dynamics of $\pi$), so that in the above equation, we allow $\pi^t(s) = \pi_t(\pi_{t-1}(\ldots\pi_1(s)))$.
This yields a collection of stochastic matrices $\langle  \Pi^{(\alpha)} \rangle_{\alpha \in \Alpha}$ with

$$
\Pi^{(\alpha)}_{i, j} = P[\, i \text{ is observed at time } t \; | \; j \text{ is observed immediately before}\,],
$$

where $i \in Z^{(\alpha)}$ and $j \in (Z^{(\alpha)})^n$. The amount of learnable parameters (i.e., the size) of such a model $M = \langle  \Pi^{(a)} \rangle_{\alpha \in \Alpha}$ is therefore

$$
\begin{equation}
  |M| = \sum_{\alpha \in \Alpha} |Z^{(\alpha)}|^n \, (|Z^{(\alpha)}| - 1).
\end{equation}
$$

The finalized abstract policy $\pi_Z$ would use this model to operate on $Z = \large{\times_{\alpha \in \Alpha}} Z^{(\alpha)}$ (see the [inference section](#inference) for a high-level overview of evaluation). By operating on the cross-product of multiple sufficiently independent heuristics, $\pi_Z$ could closely approximate $\pi_S$ while remaining interpretable.

---

## Training

The parameter space for a model $M$ of order $n$ is precisely

$$
\begin{equation}
  \Theta =
  \large{\times_{\alpha \in \Alpha}}
  \large{\times_{k \in Z^{(\alpha)}}}
  \bold{S}^{|Z^{(\alpha)}|^n},
\end{equation}
$$

(where $\bold{S}^d$ denotes the $d$-dimensional unit sphere). Finding optimal parameters $\theta^* \in \Theta$ follows standard procedure as in any $n$-gram model. Hence, we simply provide the generic closed-form solution written in terms of the objects at hand,

$$
\begin{equation}
 \Pi^{(\alpha)}_{i, j} = \frac{1}{N}
 \sum_{s \in S}
  I^{(\alpha)}_{i,j}(\pi^n(s), \langle \pi^i(s) \rangle_{i \in [0, \, n)}),
\end{equation}
$$

where

$$
\begin{equation*}
 I^{(\alpha)}_{i,j}(a, \langle b_i \rangle_{i \in [0, \, n)}) =
 \begin{cases}
       1 & \text{if } \; \phi^{(\alpha)}(a) = i \; \text{ and } \; \phi^{(\alpha)}(b) = j, \\
       0 & \text{otherwise},
  \end{cases}
\end{equation*}
$$

and $N$ is the number of length-$(n + 1)$ contiguous subsequences in the dynamics of $\pi$, which can be easily sketched while computing the sum in $(5)$.

### Sources
The nature of the policy operator $\pi$ is such that there exists some $s \in S$ wihtout an $s^\prime$ with $\pi(s^\prime) = s$. Here, $s$ is called a source within the dynamics of $\pi$. This constitutes a problem, as the start $s_0$ of the game for which $S$ is a state space is necessarily a source (which may not be unique); therefore, an attempt to find an $n$-length sequence of moves leading up to a state less than $n$ applications of $\pi$ away from a source in its dynamics may fail.

This is important because it is a step necessary to compute the $\Pi^{(\alpha)}_{i, j}$$^{\text{th}}$ parameter of the model, where $i$ is the parameter that is too close to a source to have a valid $n$-gram history. A solution which does not significantly alter transition distributions of $\Pi^{(\alpha)}$ is to sample missing elements of $n$-gram histories from a uniform distribution while computing its entries. If this measure is taken, $N$ can be set to $|S|$ in $(5)$, avoiding the need for sketching proportions.

### Sinks

In many traditional definitions of a policy $\pi$, there may exist elements $s^\prime_i$ of $S$ over which $\pi$ is not defined, as they are terminal in the game under representation. These are sinks in the dynamics of $\pi$, and should never be considered as part of a history while computing model parameters.

---

## Inference

When at a state $s \in S$, a human player can consider the set of next possible states $t(s)$ (where the transition function $t : S \to \mathcal{P}(S)$ is set-valued). Optimally, combinatorial optimization would be done across all elements $s^\prime \in t(s)$ under the MLE objective of maximizing the probability that their action is observed across all abstract state space transitions.

While this is possible to an extent due to the simplicity of the abstractions in consideration (which map onto small sets of classes, reducing maximization objectives during MLE), the true value of the model is in the subjective analysis of each $\Pi^{(\alpha)}$. Additionally, quantitative techniques (such as finding the static distribution and convergence rate of these matrices) may illustrate interpretable patterns in the dynamics of $\pi$, depending on $\langle \phi^{(\alpha)} \rangle$.

---

## Remarks

Establishing an approximation of optimal policy in the form of a Markov process provides an interpretable functional representation that is able to work with intuitive abstractions. Thus, it is a valid representation of a praxis, and the above methods effectively 'translate' from policies of arbitrary form.

### Explorations

The following are left as potential avenues of analysis relating to the model family.

* Smoothing techniques, and an analysis of their benefit in the context of optimal policy.
* Non-interpretability of $n$-gram model successors; in particular transformer attention.
* Skip-gram models as an extension of this family.

---

## Credits

Thank you to my good friend Humberto Gutierrez for spending late nights discussing the concept of policy abstraction with me, and helping me organize many ideas about policies over continuous abstractions.


[^praxis-learning]: Automated synthesis of artifacts that improve unassisted human performance from machine representations of perfect or near-perfect game-theoretic strategy.

[^stationary]: A stationary model's probability assignments are invariant with respect to shifts in the time index.

[^markov-heuristics]: Which is a way of saying that rules of thumb are not globally applicable.
