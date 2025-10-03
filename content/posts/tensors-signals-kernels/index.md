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


{{% hint title="2.1. Clarification" %}}

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

### Vector Spaces 

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

{{% hint title="3.1. Example" %}}

The vector space of polynomials (of finite terms) with coefficients in a field $\mathbb{F}$,

$$
\mathbb{F}[x] = \left\{ \sum_{i \, = \, 0}^n a_i x^i \;\Big|\; n \in \mathbb{N},\ a_i \in \mathbb{F} \right\},
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

{{% hint title="3.2. Note" %}}

Countable bases are desireable not for being countable per se, but rather that, in most cases where a vector space does not have a countable Hamel basis, the uncountable Hamel basis is unconstructive and unutterable. Put another way, the most useful fact about an uncountable Hamel basis, in many cases, is that it exists.

{{% /hint %}}

For some vector spaces that do not have a countable Hamel basis, one can relax the definition of a basis itself to obtain one that is countable. Specifically, we redefine the phrase "$\mathcal{B}$ spans $V$" to 

$$
\begin{equation}
    \forall v \in V, \; v = \lim_{ n \to \infty } \, \sum_{i \, = \, 0}^{n} c_i b_i \;\; \text{s.t.} \;\; c_i \in \mathbb{F}, \, b_i \in \mathcal{B}.
\end{equation}
$$

If the above is true for a vector space $V$ over $\mathbb{F}$, then $\mathcal{B}$ is a Schauder basis of said space. The critical difference to a Hamel basis is of course the generality afforded by the possiblity of infinite terms for the sum in $(2)$, giving us a new countably-infinite flavor of linear combination.

{{% hint title="3.3. Example" %}}

The vector space of square-summable sequences of real numbers,

$$
\ell^2 = \left\{ (x_1, x_2, x_3, \dots) \;:\; \sum_{n \, = \, 1}^\infty |x_n|^2 < \infty \right\},
$$

has no Hamel basis because, no matter how you define one, you can come up with an element of $\ell^2$ which requires a decomposition into an infinite number of basis elements (which is not allowed). However, it does have the countably-infinite Schauder basis

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

{{% /hint %}}

#### Taxonomy of Spaces

Hidden in $(2)$ is the requirement that all such sums over basis elements converge. But the definition of a vector space does not include any operation that computes the "closeness" of two vectors, so additional concepts are needed to make sense of convergence. Abstractly, one needs to equip the vector space of interest with a [topology](https://en.wikipedia.org/wiki/Topological_space#topology). The way of doing so that we will consider is by assuming a [norm](https://en.wikipedia.org/wiki/Norm_(mathematics)) over the space, such that we can concretely declare the definition of an infinite series

$$
\begin{equation}
    \lim_{n \to \infty} \left\| x - \sum_{k \, = \, 1}^n a_k \right\| = 0 \iff x = \sum_{k \, = \, 1}^{\infty} a_k.
\end{equation}
$$

Vector spaces that have a norm are called normed vector spaces. If a normed vector space is [complete](https://en.wikipedia.org/wiki/Complete_metric_space) under the norm-induced [metric](https://en.wikipedia.org/wiki/Metric_space) $d : (x_1, \\, x_2) \mapsto ||x_1 - x_2||$, then it is also called a [Banach space](https://en.wikipedia.org/wiki/Banach_space). 

{{% hint title="3.4. Note" %}}

Banach spaces do not necessarily have a Schauder basis. The reason for this is technical and out of scope. Additionally, not all normed vector spaces that have a Schauder basis are Banach spaces, because they may not be complete. But for the remainder of this piece, completeness can be comfortably assumed. Indeed, most of the time anyone talks about a Schauder basis in a practical context, it spans a complete space (such as $\ell^2$).

{{% /hint %}}

If a Banach space is also equipped with an [inner product](https://en.wikipedia.org/wiki/Inner_product_space) in such a way that $\langle x, \\, x \rangle = ||x||^2$, then it is also called a [Hilbert space](https://en.wikipedia.org/wiki/Inner_product_space). With the understanding that a vector space with an inner product defined is called an inner-product space, we arrive at the following:

$$
\begin{equation}
\begin{aligned}
\text{Vector} & \text{ spaces} \\[0.2em]
&\supset
\Bigg\{
  \begin{aligned}
  & \text{Normed vector spaces} \;\supset\; \text{Banach spaces} \\
  & \text{Inner-product vector spaces}
  \end{aligned}\\[1.2em]
&\supset \text{Hilbert spaces} = (\text{Banach spaces} \, \cap \, \text{Inner-product spaces}).
\end{aligned}
\end{equation}
$$

#### Continuous Bases

The last thing we will consider are vector spaces of uncountably-infinite dimension. So far, we have been comfortable in using syntax such as "$x = [ 1, \\, 2, \\, \ldots ]^\top$" to refer to vectors of countably-infinite dimension. This will no longer be possible with uncountably-infinite dimensions, so we must revisit our notation.

{{% hint title="3.5. Reminder" %}}

A vector $v$ is an abstract object, and is independent of a choice of basis. To write $v$ as a tuple $[c_1, \dots, c_n]$, one must choose a basis $\{b_1, \\, \dots, \\, b_n\}$ and expand (in the finite-dimensional case) 

$$
v = \sum_{i \, = \, 1}^n c_i b_i.
$$

Hence, $[1, \\, 2, \\, 3]^\top$ means “the coefficients of $v$ in this basis.” 
Changing the basis changes the coefficients, but not the vector itself. This also applies to infinite-dimensional cases.

{{% /hint %}}

Observing that, once a basis is chosen, a vector in a vector space $V$ over a field $\mathbb{F}$ is determined by the coefficients in its representation as a linear combination of basis vectors, we can introduce a new type of linear combination to evolve the translation of "$\mathcal{B}$ spans $V$" to involve a [Lebesgue integral](https://en.wikipedia.org/wiki/Lebesgue_integral),

$$
\begin{equation}
    \forall v \in V, \; v = \int_{\mathcal{\Omega}} c(\omega) b(\omega) \, d\mu(\omega) \;\; \text{s.t.} \;\; c : \Omega \to \mathbb{F}, \, b : \Omega \to \mathcal{B},
\end{equation}
$$

where a measure ${\mu}$ over $\Omega$ is provided. Here, the index $\omega$ intuitively replaces the index $i$ from $(2)$, where there is a map $c$ "choosing" a coefficient $c(\omega)$ and a map $b$ "choosing" a basis element $b(\omega)$ per index $\omega$. This way, once a map $b : \Omega \to \mathcal{B}$ and a measure $\mu$ over $\Omega$ have been agreed upon, a vector can still be represented by "its coefficients," which is just the map $c$.

{{% hint title="3.6. Example" %}}

We can consider the Hilbert space of all square-integrable functions

$$
L^2(\mathbb{R}) = 
\Bigl\{\, f:\mathbb{R} \to \mathbb{C} \; \big| \; \int_{-\infty}^{\infty} |f(t)|^2 \, dt < \infty \,\Bigr\},
$$

with the inner product $\langle f_1(t), \\, f_2(t) \rangle = \int_{\mathbb{R}} f_1(t) \overline{f_2(t)} \\, dt$. A natural choice of "continuous basis" for $L^2(\mathbb{R})$ is the family of complex exponentials indexed by frequency, $b_\omega(t) = e^{2 \pi i \omega t}$ with $\omega \in \mathbb{R}$. For all $f \in L^2(\mathbb{R})$,

$$
f(t) = \int_{\mathbb{R}} c(\omega)b(\omega) \, d\mu(\omega) = \int_{-\infty}^{\infty} c(\omega)\, b_\omega(t)\, d\omega.
$$

The measure $d\omega$ is the typical Lebesgue measure on $\mathbb{R}$. Here, we see that any $f \in L^2(\mathbb{R})$ can be expressed as a continuous linear combination of basis elements in the form $e^{2 \pi i \omega t}$ which, to reiterate, are other functions parameterized by $t$ and indexed by $\omega \in \mathbb{R}$. In this case, the [Fourier transform](https://en.wikipedia.org/wiki/Fourier_transform) of $f(t)$ provides $c(\omega)$:

$$
c(\omega) = \int_{-\infty}^{\infty} f(t) \, \overline{b_\omega(t)} \, dt
= \int_{-\infty}^{\infty} f(t)\, e^{-2 \pi i \omega t} \, dt.
$$

In a finite-dimensional case, we would compute $\langle v, \\, e_n \rangle$ to observe the "contribution" of the basis element $e_n$ in the vector $v$, obtaining the coefficient it would be assigned in its decomposition as a linear combination of basis elements. The Fourier transform does exactly the same thing per $\omega$, where $v = f(t)$ and $e_n = b_\omega(t)$:

$$
c(\omega) = \langle f(t), \, b_\omega(t) \rangle.
$$

It is not difficult to show algebraically that the Fourier transform $\mathcal{F} : L^2(\mathbb{R}) \to L^2(\mathbb{R})$ is a linear operator over this Hilbert space. (So if it were countably-infinite dimensional, it would have a matrix representation.)

{{% /hint %}}

#### Overview

We have expanded a finite-dimensional view of vector spaces to potentially allow those with countably- or uncountably-infinite dimensions. To do so, we had to slowly relax our concept of a linear combination from $(1)$ (which already spanned certain infinite-dimensional spaces like $\mathbb{F}[x]$), to $(2)$ (which was able to span a more countably-infinite-dimensional spaces like $\ell^2$), and finally $(5)$ (which can span uncountably-infinite dimensional spaces like $L^2(\mathbb{R})$). We have also encountered the conepts of Banach and Hilbert spaces.

{{< hcenter >}}
{{< figure src="david-hilbert.jpg" width="256" caption="David Hilbert (January 23, 1862 – February 14, 1943)" >}}
{{< /hcenter >}}

{{% hint title="3.7. Note" %}}

In fact, $(1)$ is a special case of $(2)$, which is a special case of $(5)$. Therefore, it is truly a relaxation of the linear combination; at no point did we lock ourselves out of any vector spaces we could already span. Namely, in the case of $(5)$ and $(2)$ for a given vector space $V$,

$$
\Omega = \mathbb{N} \iff \forall v \in V, \; v = \int_{\Omega} c(\omega)b(\omega) \, d{\mu}(\omega) = \sum_{i \, = \, 0}^{\infty} c_i b_i.
$$

In the case of $(2)$ and $(5)$, when all vectors in $V$ are a linear combination of a finite number of basis elements (as is the case for $\mathbb{F}[x]$),

$$
\forall v \in V, \; v = \sum_{i \, = \, 0}^{\infty} c_i b_i = \sum_{i \, = \, 0}^{k} c_i b_i \;\; \text{s.t.} \;\; k \in \mathbb{N}.
$$

{{% /hint %}}

### Tensor Spaces

Let us revisit the opening scene of _Linear Algebra Done Right_:

> Linear algebra is the study of linear maps on finite-dimensional vector spaces.

Another thing to note (apart from the restriction to finite dimensions) is the lack of matrices and vectors in this description. This section will live up to this witholding; we will first look for a linear-map-centric view of the objects in linear algebra and, afterwards, gain an understanding of tensor spaces.

#### Vectors and Matrices

A regrettable aspect of a typical introduction to linear algebra is the marriage of syntax to abstract objects. Most of us were told in a first impression that a vector $v$ and a matrix $M$ may look something like this:

$$
\begin{equation}
v = 
\begin{bmatrix}
1 \\
2
\end{bmatrix}, \quad 
M = 
\begin{bmatrix}
1 & 2 \\
3 & 4
\end{bmatrix}.
\end{equation}
$$

I assert that $v$ and $M$ are _both_ matrices, each of which simultaneously identifies a vector and linear map. For richer support, I will establish three resources below and explain their relationship afterward.

{{% hint title="3.8. Note" %}}

The set of linear maps from a vector space $V$ over the field $\mathbb{F}$ to another vector space $W$ (over the same field) forms a vector space over $\mathbb{F}$. That is,

$$
\mathcal{L}(V, W) = \left\{ \, T : V \to W \; | \; T \text{ is linear} \right\}
$$

is a vector space over $\mathbb{F}$. We denote the case of linear operators on $V$, which is simply $\mathcal{L}(V, V)$, as $\mathcal{L}(V)$.

{{% /hint %}}

{{% hint title="3.9. Note" %}}

There is a bijection between $\mathcal{L}(V, W)$ and $\mathbb{F}^{(\dim V) \times (\dim W)}$, where $V$ and $W$ are vector spaces on the same field $\mathbb{F}$ and are finite-dimensional. In other words, for each linear map $T$ from a vector space of dimension $n$ to another of dimension $m$, there is exactly one $m$-by-$n$ matrix with entries in $\mathbb{F}$.

{{% /hint %}}

{{% hint title="3.10. Note" %}}

A vector $v$ in a space $V$ over $\mathbb{F}$ can be regarded as a linear map from the space $\mathbb{F}^1$ into $V$, via 

$$
\psi_v : \mathbb{F}^1 \to V, \;\; \psi_v(\lambda) = v \lambda.
$$

When a basis for $V$ is fixed, the map $\psi_v$ is represented by an $n \times 1$ matrix (as an instance of theorem 3.9). This matrix is the familiar column of "coordinates" of $v$. In particular, observe that scalar multiplication can be seen as matrix multiplication with a single-dimensional vector,

$$
\psi_v(\lambda) = 
\begin{bmatrix}
v_1 \\
v_2 \\
\vdots \\
v_{(\dim V)}
\end{bmatrix}
\begin{bmatrix}
\lambda_1
\end{bmatrix} =
\begin{bmatrix}
\lambda_1 v_1 \\
\lambda_1 v_2 \\
\vdots \\
\lambda_1 v_{(\dim V)}
\end{bmatrix} = 
\lambda_1 v.
$$

{{% /hint %}}

This sets up a clear organization of vectors, matrices, and linear maps. With fixed bases, we see that the set of linear maps from $V$ to $W$ is already a vector space (via 3.8) and also that all vectors in a space $U$ over a field $\mathbb{F}$ are canonically a linear map from $\mathbb{F}^1$ into $U$ (via 3.10), hence 

$$
\text{Linear maps} \cong \text{Vectors}.
$$

Also, we see that there is a one-to-one correspondence between linear maps and matrices (via 3.9) by the bijection $\mathcal{L}(V, W) \leftrightarrow \mathbb{F}^{(\dim W) \times (\dim V)}$, so in finite dimensions ($\cong^\!$)

$$
\text{Matrices} \cong^! \text{Linear maps}.
$$

Indeed, a vector can be turned into a matrix solely through its identifiability as a linear map, turning the linear map into the central object of our understanding of linear algebra. In summary,

$$
\begin{equation}
    \text{Matrices} \cong^! \text{Linear maps} \cong \text{Vectors}.
\end{equation}
$$

#### Vector Translation

The statement of 3.10 is neuanced. Consider the case of a linear map $T \in \mathcal{L}(V, W)$ represented by a matrix $M$ (under fixed bases). According to 3.8 that map is a vector, but according to 3.10 it is identified by some _other_ linear map $\psi_v$, which is identified by some _other_ column matrix $M^\prime$,

$$
M 
\xrightarrow{\displaystyle\Phi_\mathcal{B}^{-1}} T 
\xrightarrow{\displaystyle\Psi_{\mathcal{L}(V, W)}} \psi_v 
\xrightarrow{\displaystyle\Phi_\mathcal{B}} M^\prime.
$$

In this diagram, the basis-conscious bijection $\Phi_\mathcal{B} : \mathcal{L}(V, W) \to \mathbb{F}^{(\dim W) \times (\dim V)}$ lives up to 3.9, but we silently adopted the canonical translation $\Psi_U$ of arbitrary vectors into linear maps in 3.10,

$$
\Psi_U : U \to \mathcal{L}(\mathbb{F}^1, U) \;\; \text{s.t.} \;\; \Psi_U(u) = \psi_u \; \forall \, u \in U.
$$

{{% hint title="3.11. Note" %}}

This makes sense when the vector space $U$ is finite-dimensional, as is the case whenever $U = \mathcal{L}(V, W)$ for finite-dimensional $V$ and $W$; the fact that we always interpret finite-dimensional vectors as column matrices is what makes this case of $\Psi_U$ "canonical." In other cases where matrix representations make no sense (e.g. the linear map of the Fourier transform $\mathcal{F}$ from 3.6), the choice of $\Psi_U$ will have to be more conscientious.

{{% /hint %}}

#### Linear Forms 

In a way that is "dual" to the statements of 3.10 and 3.11, one could look at another representation which, while not as obvious as mapping onto linear maps of column-matrix form (and hence non-canonical), can be seen as equally valid. Namely, one could map vectors to linear maps of row-matrix form,

$$
\Psi_U^* : U \to \mathcal{L}(U, \mathbb{F}^1) \;\; \text{s.t.} \;\; \Psi_U^*(u) = \varphi_u \; \forall \, u \in U.
$$

That is, for each vector $u \in U$, we can choose to represent it as a linear map $\varphi_u : U \to \mathbb{F}^1$. Back to the line of thought in 3.10, we see that when a basis is fixed, any such $\varphi_u$ can be identified by a $1 \times n$ matrix (again by 3.8). This can be illustrated quite simply for finite dimensions:

$$
\varphi_u(v) = 
\begin{bmatrix}
u_1 \quad
u_2 \quad
\dots \quad
u_{(\dim U)}
\end{bmatrix}
\begin{bmatrix}
v_1 \\
v_2 \\
\vdots \\
v_{(\dim U)}
\end{bmatrix} =
\begin{bmatrix}
\sum_{i \, = \, 1}^{\dim U} u_i v_i
\end{bmatrix}.
$$

{{% hint title="3.12. Note" %}}

Just as stated in 3.11 for $\Psi_U$, motivating the form $U \to \mathbb{F}^1$ from row matrices in the case of vector spaces $U$ of infinite dimension does not always work, as matrix representations sometimes make no sense there. Hence, the concrete choice of $\Psi_U^\*$ may require deeper consideration (e.g. in uncountably-infinite dimensions).

{{% /hint %}}

#### Dual Spaces 

All linear maps $\varphi_u : U \to \mathbb{F}^1$ receive the name of [linear forms](https://en.wikipedia.org/wiki/Linear_form) on the space $U$. After 3.8, this is no more than the vector space 

$$
 U^* = \{ \, \varphi : U \to \mathbb{F}^1 \; | \; \varphi \text{ is linear} \}.
$$

This is called the [dual vector space](https://en.wikipedia.org/wiki/Dual_space) of $U$, which receives the special notation $U^\*$ due to how naturally it arises. Much like elements of $U$ can be represented by column matrices, the elements of $U^\*$ (which are called covectors) can be represented by row matrices (wherever matrices make sense).

{{% hint title="3.13. Note" %}}

In any Hilbert space $H$, for every continuous linear form $\varphi \in H^*$, there is a unique vector $u \in H$ with

$$
 \forall \, v \in H, \; \varphi(v) = \langle u, v \rangle.
$$

Furthermore, $||u|| = ||\varphi||$. This gives a natural identification between elements of $H$ and $H^*$ by the canonical bijection $J : u \mapsto \varphi$. Here, we say "canonical" because it is provided uniquely by the inner product. This is the statement of the [Riesz representation theorem](https://en.wikipedia.org/wiki/Riesz_representation_theorem).

{{% /hint %}}

It is not difficult to see that for any vector space $V$ over a field $\mathbb{F}$, in fact, $\Psi_V$ and $\Psi_V^\*$ are both bijections. Since they are canonical (in the sense that they are naturally defined), it is only right to assert that 

$$
V \cong \mathcal{L}(\mathbb{F}^1, V) \;\; \text{and} \;\; V^* \cong \mathcal{L}(V, \mathbb{F}^1).
$$

#### Multilinearity

We can continue talking about linearity in maps even when they have multiple arguments. For a map $T$ from multiple vector spaces $V_i$ into another $W$ (all over some field $\mathbb{F}$) where 

$$
T : V_1 \times V_2 \times \dots \times V_n \to W \;\; \text{s.t.} \;\; T(v_1, \, v_2, \, \ldots, \, v_n) = w,
$$

we say that $T$ is linear in an argument $v_i$ if, for all other arguments $v_j \neq v_i$, fixing $v_j$ makes the newly altered $T^\prime : V_i \to W$ a linear map. If such a map $T$ is linear in all of its $n$ arguments it is called $n$-linear, and all maps like this are called [multilinear maps](https://en.wikipedia.org/wiki/Multilinear_map). 

{{% hint title="3.14. Example" %}}

Matrix-vector multiplication can be seen as a bilinear ($2$-linear) map,

$$
B : M_{m \times n}(\mathbb{F}) \times \mathbb{F}^n \to \mathbb{F}^m.
$$

This is with the understanding that $m$-by-$n$ matrices with entries in $\mathbb{F}$, in other words $M_{m \times n}(\mathbb{F})$, indeed form a vector space over the same field $\mathbb{F}$. One can verify that fixing either argument (the vector or the matrix) forms a linear map with the other argument.

{{% /hint %}}

We may also extend linear forms with the same treatment that led us to multilinear maps. In particular, if an $n$-linear map over a field $\mathbb{F}$ has the codomain of $\mathbb{F}$ itself, it is called an $n$-linear form (where all maps like this are called [multilinear forms](https://en.wikipedia.org/wiki/Multilinear_form)).

#### Tensor Product

If one has two vector spaces $V$ and $W$ over the same field, one can naturally talk about their cartesian product $V \times W$ (as we have been doing in the case of multilinear maps). But instead of doing that, one can talk about a third vector space $V \otimes W$ (called the [tensor product](https://en.wikipedia.org/wiki/Tensor_product) of $V$ and $W$) which, while having just as much expressiveness as $V \times W$, of course has the added benefit of being a vector space itself.

{{% hint title="3.15. Note" %}}

Let $\varphi : V \times W \to V \otimes W$ be a bilinear map. Then, for each bilinear map $h : V \times W \to Z$ (into another vector space $Z$), there is a unique linear map $\tilde h : V \otimes W \to Z$ such that $h = \tilde h \circ \varphi$. This is referred to as the [universal property](https://en.wikipedia.org/wiki/Universal_property) of the tensor product, which justifies the phrase "just as much expressiveness."

{{% /hint %}}

Every tensor product $V \otimes W$ is equipped with such a bilinear map $\varphi : V \times W \to V \otimes W$ that allows the construction of vectors in $V \otimes W$. The [outer product](https://en.wikipedia.org/wiki/Outer_product) is an example of this in finite dimensions, but in other cases one must be more creative. Confusingly, this map $\varphi$ is also called a tensor product, and $\otimes$ is predominantly used instead of $\varphi$ in notation. Summarizing,

$$
\forall (v, w) \in V \times W, \; \varphi(v, w) = v \otimes w \quad \text{where} \quad v \otimes w \in V \otimes W.
$$

{{% hint title="3.16. Note" %}}

Even if the tensor product among vectors is not strictly commutative, there are canonical isomorphisms among permutations of tensor products of vector spaces. That is, for any permutation $\sigma$,

$$
V_1 \otimes \cdots \otimes V_n \;\cong\; V_{\sigma(1)} \otimes \cdots \otimes V_{\sigma(n)}.
$$

Due to this symmetry, we often write tensor products as if they were commutative without loss of generality. But when one talks about actual computation or representations, order will probably matter.

{{% /hint %}}

Totally, through 3.15 and 3.16, the tensor product is precisely designed to "linearize" multilinear maps. To elaborate, for any multilinear map $h : V_1 \times V_2 \times \dots \times V_n \to W$, there exists a unique linear map

$$
\tilde h : \bigotimes_i V_i \to W \;\; \text{s.t.} \;\; \tilde h(v_1 \otimes \cdots \otimes v_n) = h(v_1, \ldots, v_n).
$$

#### More Matrices

Being now able to identify every multilinear map with a unique linear map over a tensor product space, it is possible to assert that 3.8, 3.9, and 3.10 also apply to tensor product spaces. I will reiterate the notes, dressing them up specifically for the case of tensor product spaces.

{{% hint title="3.17. Specialization of 3.8" %}}

The set of linear maps from a tensor product space $\bigotimes_i V_i$ over the field $\mathbb{F}$ to another vector space $W$ over $\mathbb{F}$ forms a vector space over $\mathbb{F}$. Symbolically,

$$
\mathcal{L}({\textstyle\bigotimes}_i V_i, W) = \left\{ \, T : \bigotimes_i V_i \to W \;\; \bigg| \;\; T \text{ is linear}  \right\}
$$

is a vector space over $\mathbb{F}$. We denote the operator case as $\mathcal{L}(\bigotimes_i V_i) = \mathcal{L}(\bigotimes_i V_i, \\, \bigotimes_i V_i)$.

{{% /hint %}}

{{% hint title="3.18. Specialization of 3.9" %}}

There is a bijection between $\mathcal{L}(\bigotimes_i V_i, W)$ and $\mathbb{F}^{\times_i (\dim V_i)} \times \mathbb{F}^{(\dim W)}$ when $V$ and $W$ are finite-dimensional vector spaces over $\mathbb{F}$. That is, for each linear map from a tensor product over spaces $V_1, \\, \ldots, \\, V_n$ and into $W$ (all over a field $\mathbb{F}$), there is one matrix with axis lengths $(\dim V_1, \\, \ldots, \\, \dim V_n, \\, \dim W)$ with entries in $\mathbb{F}$.

{{% /hint %}}

Often, $(\dim V_1, \\, \ldots, \\, \dim V_n, \\, \dim W)$ from 3.18 is referred to as the shape of the matrix. Each entry of the shape tuple can be viewed as the sidelength of a pictographical embedding of the matrix in $\mathbb{R}^{n + 1}$. For example, the matrix in $\mathbb{R}^{2 \times 3}$

$$
M = 
\begin{bmatrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
\end{bmatrix}
$$

is said to have shape $(2, 3)$ -- once it is "drawn on paper," its "sidelengths" are $2$ and $3$. Keeping the spirit of "pictographical" representation, people often call this a $2$-dimensional array, as it can be neatly "drawn" on two dimensions. But concretely, this matrix corresponds to a $6$-dimensional linear map. This ambiguity is often resolved by calling each shape entry an "axis" instead of a dimension, understanding that it refers to the visual axis of $\mathbb{R}^n$ where we would pictographically embed it.

{{% hint title="3.19. Note" %}}

Software libraries that represent matrices often make the choice of calling them either $n$-dimensional arrays or simply tensors in an effort to maintain generality. Arguably, these terms are both misnomers. Personally, I think they should have just called them all matrices.

{{% /hint %}}

{{% hint title="3.20. Note" %}}

Appending a trailing \(1\) to the shape of a matrix does not change the underlying object. Concretely, a matrix of shape \((\alpha_1, \, \ldots, \, \alpha_n)\) can be naturally identified with one of shape \((\beta_1, \, \ldots, \, \beta_n)\) when $\prod_i \alpha_i = \prod_i \beta_i$. This is a result of the isomorphisms included in the scope of

$$
\prod_i \dim \Alpha_i = \prod_i \dim \Beta_i \iff \bigotimes_i \Alpha_i \;\cong\; \bigotimes_i \Beta_i \, .
$$

{{% /hint %}}

Some bijections of the form $f : \bigotimes_i \Alpha_i \to \bigotimes_i \Beta_i$ are often referred to as "reshapings." This concept is hard to formalize, but pictographically graspable. For example, this is one way to reshape $M$:

$$
\begin{bmatrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
\end{bmatrix}
\xrightarrow{f}
\begin{bmatrix}
1 & 2 & 3 & 4 & 5 & 6 \\
\end{bmatrix}
\xrightarrow{g}
\begin{bmatrix}
1 & 2 \\
3 & 4 \\
5 & 6 \\
\end{bmatrix}.
$$

{{% hint title="3.21. Specialization of 3.10" %}}

Every vector $v$ in a tensor product space $\bigotimes_i V_i$ can be seen as a linear map from the sapce $\mathbb{F}^1$ into $\bigotimes_i V_i$ through the definition

$$
\psi_v : \mathbb{F}^1 \to \bigotimes_i V_i \, , \;\; \psi_v(\lambda) = v \lambda.
$$

With a basis for $\bigotimes_i V_i$ fixed, the map $\psi_v$ can be represented by a matrix of shape $(1, \\, \bigotimes_i V_i)$ (as an instance of note 3.17). However, in the context of tensor product spaces, it is common to represent $\psi_v$ using a matrix of shape $(\dim V_1, \\, \ldots, \\, \dim V_n)$ (invoking 3.20). This is done to facilitate descriptions of computations involving the vector in question.

{{% /hint %}}

#### Actual Tensors 

Many people refer to vectors in tensor product spaces as tensors, especially in computationally-oriented scientific disciplines. This population has recently gained numerosity (and maybe even majority) thanks to the increasing availability of efficient computers and their applications. But traditionally, a [tensor](https://en.wikipedia.org/wiki/Tensor_(intrinsic_definition)) is a linear map associated with a vector space $V$ over $\mathbb{F}$ of the form

$$
\begin{equation}
 T_{n}^{\, m} : (\times^n \, V) \times (\times^m \, V^*) \to \mathbb{F}.
\end{equation}
$$

Here, $(m, \\, n)$ is called the "type" of the tensor $T$. Perhaps the most important restriction of this definition is that we are only talking about a single vector space $V$, making it invalid to call $\mathbb{R}^2 \otimes \mathbb{R}^3 \to \mathbb{R}$ a tensor (without first invoking some isomorphism).

{{% hint title="3.22. Note" %}}


{{% /hint %}}

{{% hint title="3.23. Note" %}}

The vector-valued tensor product $\otimes : V \times W \to V \otimes W$ provides the isomorphism 

$$
\{f : V \times W \to Z \; | \; f \text{ is linear} \} \cong \{g : V \otimes W \to Z \; | \; g \text{ is linear} \}
$$

as a corollary of 3.15. Furthermore, it is canonical in the sense that the vector-valued tensor product is static. In the case of $V = W$, $\\; \\{g : V \otimes V \to \mathbb{F}^1 \\; | \\; g \text{ is linear} \\} = \mathcal{L}(V \otimes V, \mathbb{F}^1) = (V \otimes V)^* \cong V^* \otimes V^*$. With this we can see that for all instances of $(8)$,

$$
T_n^{\, m} \in \left( \otimes^m \, V \right) \otimes \left( \otimes^n \, V^* \right).
$$

{{% /hint %}}


{{% hint title="3.24. Examples" %}}

Let us observe the types of some known tensors of form $T : (\times^n \\, V) \to \mathbb{F}$.

1. Any scalar $\lambda : \varnothing \to \mathbb{F}$ is a tensor of type $(0, \\, 0)$.
2. Any norm $|| \cdot || : v \mapsto ||v||$ is a tensor of type $(0, \\, 1)$.
3. Any quadratic form $q : v \mapsto v^\top A v$ is a tensor of type $(0, \\, 1)$.
4. Any inner product $\langle \cdot, \cdot \rangle : (v, w) \mapsto \mathbb{F}$ is a tensor of type $(0, \\, 2)$.
 
{{% /hint %}}

All the tensors in 3.24 are scalar-valued. Now I will show how we can see that vector-valued linear maps are also tensors. Consider a tensor-valued multilinear map of the form

$$
T : (\times^a \, V) \times (\times^b \, V^*) \to \left( \otimes^c \, V \right) \otimes \left( \otimes^d \, V^* \right).
$$

By showing that $T$ identifies a tensor, we will also show that an arbitrary linear map defined using a single vector space $V$ identifies a tensor (including tensor-valued maps, via 3.23). First, we define

$$
\begin{align*}
\tilde T : \; & (\times^a \, V) \times (\times^b \, V^*) \times (\left( \otimes^c \, V \right) \otimes \left( \otimes^d \, V^* \right))^* \to \mathbb{F} \\
& \text{s.t.} \;\; \tilde T(v_1, \, \ldots, \, v_a, \, w_1, \, \ldots, \, w_b,  \, \varphi) = \varphi(T(v_1, \, \ldots, \, v_a, \, w_1, \, \ldots, \, w_b)),
\end{align*}
$$

where $v_i \in V$, $w_i \in V^\*$, and $\varphi \in (\left( \otimes^c \\, V \right) \otimes \left( \otimes^d \\, V^* \right))^*$. Thanks to 3.15, there is a unique

$$
\begin{align*}
\hat T : (\otimes^a \, V) \otimes (\otimes^b \, V^*) \; \otimes & \; (\left( \otimes^c \, V \right) \otimes \left( \otimes^d \, V^* \right))^* \to \mathbb{F} \\
& \text{s.t.} \;\; \hat T(v) = \tilde T(v_1, \, \ldots, \, v_a, \, w_1, \, \ldots, \, w_b, \, \varphi).
\end{align*}
$$

Cleaning up, we can see that the form of $\hat T$ is precisely that of a tensor of type $(b + c, \\, a + d)$,

$$
\hat T : \; (\otimes^{a + d} \, V) \otimes (\otimes^{b + c} \, V^*)  \to \mathbb{F} \;\; \text{s.t.} \;\;
\hat T \in \left( \otimes^{b + c} \, V \right) \otimes \left( \otimes^{a + d} \, V^* \right).
$$

### Signals and Systems 

### Kernel Methods


[^axiom-choice]: When considering infinite-dimensional vector spaces, this statement is true if and only if one admits the axiom of choice. Perhaps this was another motivation of Axler's restriction to finite-dimensional vector spaces.
