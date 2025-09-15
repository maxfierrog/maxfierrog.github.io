---
title: "Perspectives into Tensors, Signals, and Kernel Methods"
category: technical
date: 2025-09-08
math: true
draft: true 
---

{{< toc >}}

## Abstract

Linear algebra, signal processing, and machine learning methods are (one of many groups of) topics that enjoy beautiful relationships enclosed in a dense shell of mathematics. Within the shell, one finds a kernel of surprisingly diverse perspectives which are simply a pleasure to entertain. This article hopes to give the reader a romantic and thematic glimpse of the truths in this particular group of topics.

---

## Overview 

I follow an essay-like structure including an introduction, body, and conclusion. The introduction sets the stage building from "class-style" knowledge, assuming a first course in linear algebra, signal processing, and machine learning. For readers lacking this background, I leave pointers to free resources.

The introduction arrives at the [linear operator](https://en.wikipedia.org/wiki/Linear_map) perspective of [systems of many dimensions](https://en.wikipedia.org/wiki/Multidimensional_system), defining system properties like [time-invariance](https://en.wikipedia.org/wiki/Time-invariant_system) and [causality](https://en.wikipedia.org/wiki/Causal_system) in terms of [tensor](https://en.wikipedia.org/wiki/Tensor) representations. This results in the marriage of many linear algebra and signal processing concepts. These perspectives then help the body, where I present a cross-cutting perspective of the [convolution kernel](https://en.wikipedia.org/wiki/Convolution) and the [reproducing kernel](https://en.wikipedia.org/wiki/Reproducing_kernel_Hilbert_space#:~:text=then%20called%20the-,reproducing%20kernel,-%2C%20and%20it%20reproduces).

Finally, the conclusion provides some subjective thematization to the concepts emphasized in the rest of the piece, summarizing mathematical details and indulging in a little bit of sensationalism. An attempt is made of providing pointers to further reading on adjacent concepts.


{{% hint title="Clarification" %}}

The word "kernel" is criminally polysemous in mathematics, and it will be used a lot in this piece. I mostly use the word under two semantics: The convolution kernel from signal processing and the reproducing kernel from machine learning. All other instances of the word should simply refer to its english meaning.

However, a primary objective of this piece is to reach a perspective of the convolution and reproducing kernels that allows them to be seen through a common lense. So an acute reader may interpret this as an explanation of why they are both called "kernel," assuming that they were both named that way because they represent the same kind of mathematical object in some profound way.

What is interesting is that they both received their names for a superficial reason -- because the symbols that represent them show up inside other symbols. One could imagine that people started calling them "kernel" independently just to avoid saying the phrase "that term in in the middle" while pointing at a blackboard.

As such, the connecting view of the convolution and reproducing kernels that we will work towards only applies to these two kernels (convolution and reproducing). If these were the only two kernels in mathematics, maybe one could now say that they are named the same for a profound reason. But there are numerous other kinds of kernels for which the constructions in this piece simply do not apply.

{{% /hint %}}

---

## Introduction 

This section provides not much more than a definition-based refresher on select topics from first courses in linear algebra, signal processing, and machine learning. For readers in need of comprehensive review or first-time coverage, I leave these free resources on said topics:

* **Kernel methods.** [_Foundations of Machine Learning_](https://cs.nyu.edu/~mohri/mlbook/) by Mohri, Rostamizadeh, and Talwalkar.
* **Signals and Systems.** [_Signals & Systems: Theory and Applications_](https://ss2-2e.eecs.umich.edu/) by Ulaby and Yagle.
* **Linear algebra.** [_Linear Algebra Done Right_](https://linear.axler.net/) by Axler.

### Bases of Vector Spaces 

In what is nowadays close to being a canon of linear algebra education, Sheldon Axler opens with the statement below to set the stage for the rest of _Linear Algebra Done Right_:

> Linear algebra is the study of linear maps on finite-dimensional vector spaces.

Here, the restriction of vector spaces to the finite-dimensional case was one the most mathematically respectful ways to negotiate generality with practical pedagogy. However, the spirit of linear algebra is alive way beyond the finite-dimensional case.

#### Hamel Bases

Most engineers are familiar with the concept of a (Hamel) basis of a vector space. If we have a vector space $V$ over a field $\mathbb{F}$ and a Hamel basis $\mathcal{B}$, then "$\mathcal{B}$ spans $V$" translates to

$$
\begin{equation}
    \forall v \in V, \; v = \sum_{i \, = \, 0}^{k} c_i b_i \;\; \text{s.t.} \;\; c_i \in \mathbb{F}, \, b_i \in \mathcal{B}, \, k \in \mathbb{N}.
\end{equation}
$$

Importantly, for $B$ to be a Hamel basis, the sum in $(1)$ must have finite terms. Note that this is allowed even in cases where $\mathcal{B}$ is infinite (or in other words, where $V$ is infinite-dimensional), as one does not necessarily assign nonzero coefficients $c_i$ to each element of $\mathcal{B}$.

{{% hint title="Example" %}}

The vector space of polynomials (of finite terms) with coefficients in a field $\mathbb{F}$,

$$
\mathbb{F}[x] = \left\{ \sum_{i=0}^n a_i x^i \;\Big|\; n \in \mathbb{N},\ a_i \in \mathbb{F} \right\},
$$

has the infinite basis $\mathcal{B}_{\mathbb{F}[x]} = \\{1, x, x^2, x^3, \dots \\}$. Each of its elements, however, is the linear combination of a finite number of basis elements. For example, the polynomial 

$$ 
p(x) = 3 + 4x^2 + x^3
$$

can be expressed as a (finite) linear combination of basis elements,

$$
p(x) = 3
\begin{bmatrix}
 1 \\
 0 \\
 0 \\
 0 \\
 \vdots
\end{bmatrix} + 4  
\begin{bmatrix}
 0 \\
 0 \\
 1 \\
 0 \\
 \vdots
\end{bmatrix} + 1 
\begin{bmatrix}
 0 \\
 0 \\
 0 \\
 1 \\
 \vdots
\end{bmatrix}.
$$

Here we imposed a (canonical) representation such that, for example, $x^2 = \left[ 0, \\, 0, \\, 1, \\, 0, \\, {\dots} \right]^\top$. We see that, despite each basis vector being infinite-dimensional, all polynomials are determined by a finite number of them. 

{{% /hint %}}

#### Schauder Bases

Interpreting Axler strictly, $\mathbb{F}[x]$ is already beyond linear algebra because it is of [semi-infinite](https://en.wikipedia.org/wiki/Semi-infinite) dimension. But definitionally, it is a perfectly valid vector space. Just as finite dimensionality is not necessary in order to access the theorems of linear algebra, having a countable Hamel basis is also not necessary; all vector spaces do have a Hamel basis[^axiom-choice], but not all of them have a countable one.

{{% hint title="Note" %}}

Countable bases are desireable not for being countable per se, but rather that, in most cases where a vector space does not have a countable Hamel basis, the uncountable Hamel basis is unconstructive and unutterable. Put another way, the most useful fact about an uncountable Hamel basis, in many cases, is that it exists.

{{% /hint %}}

For some vector spaces that do not have a countable Hamel basis, one can relax the definition of a basis itself to obtain one that is countable. Specifically, we redefine the phrase "$\mathcal{B}$ spans $V$" to 

$$
\begin{equation}
    \forall v \in V, \; v = \sum_{i \, = \, 0}^{\infty} c_i b_i \;\; \text{s.t.} \;\; c_i \in \mathbb{F}, \, b_i \in \mathcal{B}.
\end{equation}
$$

If the above is true for a vector space $V$ over $\mathbb{F}$, then $\mathcal{B}$ is a Schauder basis of said space. The critical difference to a Hamel basis is of course the generality afforded by the possiblity of infinite terms for the sum in $(2)$, giving us a new countably infinite flavor of linear combination.

{{% hint title="Example" %}}

The vector space of square-summable sequences,

$$
\ell^2 = \left\{ (x_1, x_2, x_3, \dots) \;:\; \sum_{n=1}^\infty |x_n|^2 < \infty \right\},
$$

has no Hamel basis because, no matter how you define one, you can come up with an element of $\ell^2$ which requires a decomposition into an infinite number of basis elements (which is not allowed). However, it does have the countably infinite Schauder basis

$$
\mathcal{B}_{\ell^2} = \left\{ 
\left(
 1, \,
 0, \,
 0, \,
 \dots
\right), \,
\left(
 0, \,
 1, \,
 0, \,
 \dots
\right), \,
\left(
 0, \,
 0, \,
 1, \,
 \dots
\right), \,
{\dots}
\right\}.
$$

This is quite remarkable, as the uncountably infinite $\ell^2$ is spanned by the countably infinite $\mathcal{B}_{\ell^2}$. Allowing infinite linear combinations bridges this countability barrier via convergence.

{{% /hint %}}

### Signals and Systems 

### Kernel Methods


[^axiom-choice]: When considering infinite-dimensional vector spaces, this statement is true if and only if one admits the axiom of choice. Perhaps this was another motivation of Axler's restriction to finite-dimensional vector spaces.
