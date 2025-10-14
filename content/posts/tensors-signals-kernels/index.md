---
title: "Perspectives into Tensors, Signals, and Kernel Methods"
category: technical
date: 2025-09-08
math: true
draft: false 
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

{{< hcenter >}}
{{< figure src="vera-molnar-molndrian-1974.png" width="512" caption="Vera Molnár, 'Molndrian' (1974)" >}}
{{< /hcenter >}}

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

{{% hint title="3.11. Note" %}}

We have started to use the symbol $\cong$. In this context, stating $A \cong B$ implies that there is a linear [isomorphism](https://en.wikipedia.org/wiki/Isomorphism) between $A$ and $B$. Exactly, this means that there exists some linear map $\phi : A \to B$ that is a bijection. 

This is complete as a definition of the symbol $\cong$. However, its use in the rest of this piece will often reference a choice of $\phi$ that is [canonical](https://en.wikipedia.org/wiki/Canonical_map). This means that if you see $A \cong B$, there is probably an "standard" way to obtain a $b \in B$ from one unique $a \in A$ (and vice versa) -- here, we can informally say that if $\phi(a) = b$ then $a$ "is" $b$, but it is more precise to say that $a$ "identifies" $b$. Oftentimes, $\phi$ will not be made explicit.

For example, choosing a basis $\mathcal{B}$ for a space $V$ gives the canonical isomorphism $\mathbb{F}^{(\dim V)\times(\dim V)} \cong \mathcal{L}(V)$. In this case, it makes sense to say that "a matrix is a linear transformation." But without a choice of basis there is no "standard" bijection $\Phi_\mathcal{B} : \mathcal{L}(V) \to \mathbb{F}^{(\dim V) \times (\dim V)}$ (no way to bijectively identify maps from matrices).
 
{{% /hint %}}

{{< hcenter >}}
{{< figure src="vera-molnar-untitled-square-1974.png" width="512" caption="Vera Molnár, Untitled (1974)" >}}
{{< /hcenter >}}

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

{{% hint title="3.12. Note" %}}

This makes sense when the vector space $U$ is finite-dimensional, as is the case whenever $U = \mathcal{L}(V, W)$ for finite-dimensional $V$ and $W$; the fact that we always interpret finite-dimensional vectors as column matrices is what makes this case of $\Psi_U$ "canonical." In other cases where matrix representations make no sense (e.g. the linear map of the Fourier transform $\mathcal{F}$ from 3.6), the choice of $\Psi_U$ will have to be more conscientious.

{{% /hint %}}

#### Linear Forms 

In a way that is "dual" to the statement 3.10, one could look at another representation which, while not as standard as mapping onto linear maps of column-matrix form (and hence non-canonical), can be seen as equally valid. Namely, one could map vectors to linear maps of row-matrix form,

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

{{% hint title="3.13. Note" %}}

Just as stated in 3.12 for $\Psi_U$, motivating the form $U \to \mathbb{F}^1$ from row matrices in the case of vector spaces $U$ of infinite dimension does not always work, as matrix representations sometimes make no sense there. Hence, the concrete choice of $\Psi_U^\*$ may require deeper consideration (e.g. in uncountably-infinite dimensions).

{{% /hint %}}

#### Dual Spaces 

All linear maps $\varphi_u : U \to \mathbb{F}^1$ receive the name of [linear forms](https://en.wikipedia.org/wiki/Linear_form) on the space $U$. After 3.8, this is no more than the vector space 

$$
 U^* = \{ \, \varphi : U \to \mathbb{F}^1 \; | \; \varphi \text{ is linear} \}.
$$

This is called the [dual vector space](https://en.wikipedia.org/wiki/Dual_space) of $U$, which receives the special notation $U^\*$ due to how naturally it arises. Much like elements of $U$ can be represented by column matrices, the elements of $U^\*$ (which are called covectors) can be represented by row matrices (wherever matrices make sense).

{{% hint title="3.14. Note" %}}

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

{{< hcenter >}}
{{< figure src="frigyes-riesz.jpg" width="256" caption="Frigyes Riesz (January 22, 1880 – February 28, 1956)" >}}
{{< /hcenter >}}

#### Multilinearity

We can continue talking about linearity in maps even when they have multiple arguments. For a map $T$ from multiple vector spaces $V_i$ into another $W$ (all over some field $\mathbb{F}$) where 

$$
T : V_1 \times V_2 \times \dots \times V_n \to W \;\; \text{s.t.} \;\; T(v_1, \, v_2, \, \ldots, \, v_n) = w,
$$

we say that $T$ is linear in an argument $v_i$ if, for all other arguments $v_j \neq v_i$, fixing $v_j$ makes the altered map $T^\prime : V_i \to W$ linear. If such a map $T$ is linear in all of its $n$ arguments it is called $n$-linear, and all maps like this are called [multilinear maps](https://en.wikipedia.org/wiki/Multilinear_map). The set of multilinear maps of this form is denoted

$$
\mathcal{L}(V_1, \, \ldots, \, V_n; \, W) = \{\, T : V_1 \times \dots \times V_n \to W \; | \; \text{ T is linear} \}.
$$

{{% hint title="3.15. Example" %}}

Matrix-vector multiplication can be seen as a bilinear ($2$-linear) map,

$$
B : M_{m \times n}(\mathbb{F}) \times \mathbb{F}^n \to \mathbb{F}^m.
$$

This is with the understanding that $m$-by-$n$ matrices with entries in $\mathbb{F}$, in other words $M_{m \times n}(\mathbb{F})$, indeed form a vector space over the same field $\mathbb{F}$. One can verify that fixing either argument (the vector or the matrix) forms a linear map with the other argument.

{{% /hint %}}

We may also extend linear forms with the same treatment that led us to multilinear maps. In particular, if an $n$-linear map over a field $\mathbb{F}$ has the codomain of $\mathbb{F}$ itself, it is called an $n$-linear form (where all maps like this are called [multilinear forms](https://en.wikipedia.org/wiki/Multilinear_form)).

#### Tensor Product

If one has two vector spaces $V$ and $W$ over the same field, one can naturally talk about their cartesian product $V \times W$ (as we have been doing in the case of multilinear maps). But instead of doing that, one can talk about a third vector space $V \otimes W$ (called the [tensor product](https://en.wikipedia.org/wiki/Tensor_product) of $V$ and $W$) which, while having just as much expressiveness as $V \times W$, of course has the added benefit of being a vector space itself.

{{% hint title="3.16. Note" %}}

Let $\varphi : V \times W \to V \otimes W$ be a bilinear map. Then, for each bilinear map $h : V \times W \to Z$ (into another vector space $Z$), there is a unique linear map $\tilde h : V \otimes W \to Z$ such that $h = \tilde h \circ \varphi$. This is referred to as the [universal property](https://en.wikipedia.org/wiki/Universal_property) of the tensor product, which justifies the phrase "just as much expressiveness."

{{% /hint %}}

Every tensor product $V \otimes W$ is equipped with such a bilinear map $\varphi : V \times W \to V \otimes W$ that allows the construction of vectors in $V \otimes W$. The [outer product](https://en.wikipedia.org/wiki/Outer_product) is an example of this in finite dimensions, but in other cases one must be more creative. Confusingly, this map $\varphi$ is also called a tensor product, and $\otimes$ is predominantly used instead of $\varphi$ in notation. Summarizing,

$$
\forall (v, w) \in V \times W, \; \varphi(v, w) = v \otimes w \quad \text{where} \quad v \otimes w \in V \otimes W.
$$

{{% hint title="3.17. Note" %}}

Even if the tensor product among vectors is not strictly commutative, there are canonical isomorphisms among permutations of tensor products of vector spaces. That is, for any permutation $\sigma$,

$$
V_1 \otimes \cdots \otimes V_n \;\cong\; V_{\sigma(1)} \otimes \cdots \otimes V_{\sigma(n)}.
$$

Due to this symmetry, we often write tensor products as if they were commutative without loss of generality. But when one talks about actual computation or representations, order will probably matter.

{{% /hint %}}

Totally, through 3.16 and 3.17, the tensor product is precisely designed to "linearize" multilinear maps. To elaborate, for any multilinear map $h \in \mathcal{L}(V_1, \\, \ldots, \\, V_n; \\, W)$, there exists a unique linear map[^sub-indices]

$$
\tilde h : \bigotimes_i V_i \to W \;\; \text{s.t.} \;\; \tilde h(v_1 \otimes \cdots \otimes v_n) = h(v_1, \ldots, v_n).
$$

#### More Matrices

Being now able to identify every multilinear map with a unique linear map over a tensor product space, it is possible to assert that 3.8, 3.9, and 3.10 also apply to tensor product spaces. I will reiterate the notes, dressing them up specifically for the case of tensor product spaces.

{{% hint title="3.18. Specialization of 3.8" %}}

The set of linear maps from a tensor product space $\bigotimes_i V_i$ over the field $\mathbb{F}$ to another vector space $W$ over $\mathbb{F}$ forms a vector space over $\mathbb{F}$. Symbolically,

$$
\mathcal{L}({\textstyle\bigotimes}_i V_i, W) = \left\{ \, T : \bigotimes_i V_i \to W \;\; \bigg| \;\; T \text{ is linear}  \right\}
$$

is a vector space over $\mathbb{F}$. We denote the operator case as $\mathcal{L}(\bigotimes_i V_i) = \mathcal{L}(\bigotimes_i V_i, \\, \bigotimes_i V_i)$.

{{% /hint %}}

{{% hint title="3.19. Specialization of 3.9" %}}

There is a bijection between $\mathcal{L}(\bigotimes_i V_i, W)$ and $\mathbb{F}^{\times_i (\dim V_i)} \times \mathbb{F}^{(\dim W)}$ when $V$ and $W$ are finite-dimensional vector spaces over $\mathbb{F}$. That is, for each linear map from a tensor product over spaces $V_1, \\, \ldots, \\, V_n$ and into $W$ (all over a field $\mathbb{F}$), there is one matrix with axis lengths $(\dim V_1, \\, \ldots, \\, \dim V_n, \\, \dim W)$ with entries in $\mathbb{F}$.

{{% /hint %}}

Often, $(\dim V_1, \\, \ldots, \\, \dim V_n, \\, \dim W)$ from 3.19 is referred to as the shape of the matrix. Each entry of the shape tuple can be viewed as the sidelength of a pictographical embedding of the matrix in $\mathbb{R}^{n + 1}$. For example, the matrix in $\mathbb{R}^{2 \times 3}$

$$
M = 
\begin{bmatrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
\end{bmatrix}
$$

is said to have shape $(2, 3)$ -- once it is "drawn on paper," its "sidelengths" are $2$ and $3$. Keeping the spirit of "pictographical" representation, people often call this a $2$-dimensional array, as it can be neatly "drawn" on two dimensions. But concretely, this matrix corresponds to a $6$-dimensional linear map. This ambiguity is often resolved by calling each shape entry an "axis" instead of a dimension, understanding that it refers to the visual axis of $\mathbb{R}^n$ where we would pictographically embed it.

{{% hint title="3.20. Note" %}}

Software libraries that represent matrices often make the choice of calling them either $n$-dimensional arrays or simply tensors in an effort to maintain generality. Arguably, these terms are both misnomers. Personally, I think they should have just called them all matrices.

{{% /hint %}}

{{% hint title="3.21. Note" %}}

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

{{% hint title="3.22. Specialization of 3.10" %}}

Every vector $v$ in a tensor product space $\bigotimes_i V_i$ can be seen as a linear map from the sapce $\mathbb{F}^1$ into $\bigotimes_i V_i$ through the definition

$$
\psi_v : \mathbb{F}^1 \to \bigotimes_i V_i \, , \;\; \psi_v(\lambda) = v \lambda.
$$

With a basis for $\bigotimes_i V_i$ fixed, the map $\psi_v$ can be represented by a matrix of shape $(1, \\, \bigotimes_i V_i)$ (as an instance of note 3.19). However, in the context of tensor product spaces, it is common to represent $\psi_v$ using a matrix of shape $(\dim V_1, \\, \ldots, \\, \dim V_n)$ (invoking 3.21). This is done to facilitate descriptions of computations involving the vector in question.

{{% /hint %}}

#### Homogeneous Tensors 

Many people refer to vectors in tensor product spaces as tensors, especially in computationally-oriented scientific disciplines. This population has recently gained numerosity (and maybe even majority) thanks to the increasing availability of efficient computers and their applications. But traditionally, a [tensor](https://en.wikipedia.org/wiki/Tensor_(intrinsic_definition)) is a linear map associated with a single vector space $V$ over $\mathbb{F}$ of the form

$$
\begin{equation}
 T_{n}^{\, m} : (\times^m \, V^*) \times (\times^n \, V) \to \mathbb{F}.
\end{equation}
$$

Here, $(m, \\, n)$ is called the "type" of the tensor $T$. This makes a map like $m : \mathbb{R}^2 \times \mathbb{R}^4 \to \mathbb{R}$ strictly not interpretable as a tensor, as $m$ itself is not of tensor form, and there is no standard bijection that can help us view it as a tensor. That is, we cannot always identify a tensor with a multilinear map.

{{% hint title="3.23. Note" %}}

The tensor space of type $(m, \\, n)$ defined over a vector space $V$ is denoted[^parenthesized-indices]

$$
T_{n}^{\, m}(V) = \mathcal{L}(V_{(1)}^*, \, \dots, \, V_{(m)}^*, \, V_{(1)}, \, \dots, \, V_{(n)}; \, \mathbb{F}).
$$

{{% /hint %}}

{{% hint title="3.24. Examples" %}}

Let us observe the types of some known tensors of form $T_{n}^{\, 0} : (\times^n \\, V) \to \mathbb{F}$.

1. Any scalar $\lambda : a \mapsto \lambda a$ is a tensor of type $(0, \\, 0)$.
2. Any linear form $\varphi : v \mapsto \varphi(v)$ is a tensor of type $(0, \\, 1)$.
3. Any quadratic form $q : (v, w) \mapsto v^\top A w$ is a tensor of type $(0, \\, 2)$.
4. Any inner product $\langle \cdot, \cdot \rangle : (v, w) \mapsto \mathbb{F}$ is a tensor of type $(0, \\, 2)$.
 
{{% /hint %}}

{{< hcenter >}}
{{< figure src="vera-molnar-structures-of-squares-1974.png" width="512" caption="Vera Molnár, 'Structures of Squares' (1974)" >}}
{{< /hcenter >}}

#### Tensor Identification

When the definition of a multilinear map involves many vector spaces, there will continue to be a lack of such a canonical isomorphism (at least using the insights we currently have). But if we consider arbitrary multilinear maps defined over a single vector space $V$ over a field $\mathbb{F}$ (even those without a codomain $\mathbb{F}$ ), we will see that canonically

$$
\begin{equation}
\mathcal{L}(V_{(1)}^*, \, \dots, \, V_{(m)}^*, \, V_{(1)}, \, \dots, \, V_{(n)}; \, ( \otimes^a \, V ) \otimes ( \otimes^b \, V^* ))
\cong
\left( \otimes^{m + a} \, V \right) \otimes \left( \otimes^{n + b} \, V^* \right),
\end{equation}
$$

where we always interpret $\times^0 \\, V = \mathbb{F}$ and $\otimes^0 \\, V = \mathbb{F}^1$ (as always, with $\mathbb{F} \cong \mathbb{F}^1$). This essentially claims we can uniquely identify vector-valued multilinear maps defined over a single vector space $V$ with vectors in a tensor product space defined only over $V$.

{{% hint title="3.25. Note" %}}

As a result of 3.16, for all vector spaces $V$, $W$, and $Z$,

$$
\mathcal{L}(V \otimes W, \, Z) \cong\ \mathcal{L}(V, \, W; \, Z).
$$

{{% /hint %}}

{{% hint title="3.26. Note" %}}

Any finite-dimensional vector space is canonically isomorphic to its own dual due the standard bijection

$$
\Phi : V \to V^* \;\; \text{s.t.} \;\; \Phi[v](w) = \langle v, w \rangle,
$$

where $u, w \in V$ (when $V$ is not an inner-product space one defaults to the standard dot product). However, many infinite-dimensional inner-product spaces have duals which cannot be spanned using this strategy. One exception is the set of Hilbert spaces, where 3.14 provides the bijection $J$ (still through the inner product).

{{% /hint %}}

{{% hint title="3.27. Note" %}}

For any Hilbert space or finite-dimensional vector space $V$, we have that $V^\* \otimes V^\* \cong (V \otimes V)^\*$ canonically. This is supported by the following standard choice of bijection

$$
\Phi : V^* \otimes V^* \to (V \otimes V)^* \;\; \text{s.t.} \;\; \Phi[\psi \otimes \varphi](v \otimes w) \mapsto \psi(v) \varphi(w),
$$

where $\psi, \varphi \in V^\*$ and $v, w \in V$. To be clear, $\Phi[\psi \otimes \varphi]$ is in $(V \otimes V)^\*$ and has the form $V \otimes V \to \mathbb{F}$.

{{% /hint %}}

To show $(9)$, our strategy will be to consider an arbitrary multilinear map $T$ in the set

$$
\mathcal{L}_{(m, \, n)}^{\, (a, \, b)}(V) = 
\mathcal{L}(V_{(1)}^*, \, \dots, \, V_{(m)}^*, \, V_{(1)}, \, \dots, \, V_{(n)}; \, ( \otimes^a \, V ) \otimes ( \otimes^b \, V^* )).
$$

We will introduce a bijection $\tilde \Gamma$ that identifies a unique tensor of type $(m + a, \\, n + b)$ for each $T$. Then, we will introduce another bijection $\hat \Gamma$ to show that $T_{n + b}^{\\, m + a}(V)$ is isomorphic to $\left( \otimes^{m + a} \\, V \right) \otimes \left( \otimes^{n + b} \\, V^* \right)$. Summarizing, we will construct an invertible trip 

$$
\mathcal{L}_{(m, \, n)}^{\, (a, \, b)}(V) 
\xrightarrow{\displaystyle \tilde \Gamma} 
T_{n + b}^{\, m + a}(V) 
\xrightarrow{\displaystyle \hat \Gamma} 
\left( \otimes^{m + a} \, V \right) \otimes \left( \otimes^{n + b} \, V^* \right).
$$

{{% hint title="3.28. Demonstration" %}}

Let us first consider $\tilde \Gamma$. Given a $T \in \mathcal{L}_{(n, \\, m)}^{\\, (a, \\, b)}(V)$, define $\tilde T^\prime$ where

$$
\begin{align*}
\tilde T^\prime : \; & (\times^m \, V^*) \times (\times^n \, V) \times (\left( \otimes^a \, V \right) \otimes \left( \otimes^b \, V^* \right))^* \to \mathbb{F} \\
& \text{s.t.} \;\; \tilde T(v_1, \, \ldots, \, v_m, \, w_1, \, \ldots, \, w_n,  \, \varphi) = \varphi(T(v_1, \, \ldots, \, v_m, \, w_1, \, \ldots, \, w_n)),
\end{align*}
$$

where $v_i \in V^\*$, $w_i \in V$, and $\varphi \in (\left( \otimes^c \\, V \right) \otimes \left( \otimes^d \\, V^* \right))^*$ is the covector of $T(v_1, \\, \ldots, \\, v_m, \\, w_1, \\, \ldots, \\, w_n)$ (which can be canonically determined by 3.14). Then, observe that

$$
\begin{align*}
\mathcal{L}(V_{(1)}^*, \, \dots, \, V_{(m)}^*, & \,\, V_{(1)}, \, \dots, \, V_{(n)}, \, (( \otimes^a \, V ) \otimes ( \otimes^b \, V^* ))^*; \, \mathbb{F}) \\
&\overset{\text{3.27}}{\cong} 
\mathcal{L}(V_{(1)}^*, \, \dots, \, V_{(m)}^*, \,\, V_{(1)}, \, \dots, \, V_{(n)}, \, ( \otimes^a \, V^* ) \otimes ( \otimes^b \, V ); \, \mathbb{F}) \\
&\overset{\text{3.25}}{\cong} 
\mathcal{L}(V_{(1)}^*, \, \dots, \, V_{(m + a)}^*, \,\, V_{(1)}, \, \dots, \, V_{(n + b)}; \, \mathbb{F}).
\end{align*}
$$

Using this, a map $\tilde T$ can be uniquely constructed from $\tilde T^\prime$ (invoking 3.25 and 3.17 for argument order), where 

$$
\tilde T : (\times^{m + a} \, V^*) \times (\times^{n + b} \, V)\to \mathbb{F}.
$$

This finalizes the definition of $\tilde \Gamma : T \mapsto \tilde T$. Each step of this process is bijective, therefore $\tilde \Gamma$ is bijective.

{{% /hint %}}

{{% hint title="3.29. Demonstration" %}}

Now we can consider $\hat \Gamma$. Define the tensor $T \in T_{n + b}^{\, m + a}(V)$ such that by definition

$$
T : (\times^{m + a} \, V^*) \times (\times^{n + b} \, V) \to \mathbb{F}.
$$

By repeated application of 3.25, we can always use $T$ to construct a unique

$$
\hat T^\prime : (\otimes^{m + a} \, V^*) \otimes (\otimes^{n + b} \, V) \to \mathbb{F}.
$$

This is clearly a linear form. In other words, $\hat T^\prime \in ((\otimes^{m + a} \\, V^*) \otimes (\otimes^{n + b} \\, V))^\*$. But by 3.27, there is a unique 

$$
\hat T \in (\otimes^{m + a} \, V) \otimes (\otimes^{n + b} \, V^*)
$$

for each $\hat T^\prime$ we could construct. This finalizes the definition of $\hat \Gamma : T \mapsto \hat T$. Each step above is bijective, so $\hat \Gamma$ is itself a bijection.

{{% /hint %}}

We have shown that vector-valued multilinear maps defined on a single vector space (such as operators) do identify tensors uniquely, despite not being of tensor form. Also, we have shown how tensors uniquely identify elements of tensor product spaces defined on a single vector space. This is why it is normal refer to all of these objects as tensors.

{{% hint title="3.30. Examples" %}}

Equation $(9)$ provides a formula for identifying the tensor type of elements in $\mathcal{L}_{(m, \\, n)}^{\\, (a, \\, b)}(V)$. Indeed, tensor type acts like an algebraic signature for these objects.

1. Linear operators $T : V \to V$ identify type $(1, 1)$ tensors.
2. The tensor product $\otimes : V \times V \to V \otimes V$ identifies a type $(2, 2)$ tensor.

For linear operators (represented by square matrices), we can apply $(9)$ and observe they identify elements of $V \otimes V^\*$ as they are of type $(1, 1)$. Then, square matrix multiplication represents the map

$$
T : (V \otimes V^*) \times (V \otimes V^*) \to (V \otimes V^*) \;\; \text{s.t.} \;\; T(A, \, B) = AB,
$$

where $A, B \in \mathcal{L}(V)$. But due to 3.25, we can reform $T : (\times^2 \\, V^\*) \times (\times^2 \\, V) \to V \otimes V^\*$. Applying $(9)$ one more time, we observe that this is a type $(3, 3)$ tensor. This illustrates how the tensor type of a multilinear map defined over other tensor product spaces can be computed. 

{{% /hint %}}

#### Heterogeneous Tensors 

We have used algebraic pathways to extend the idea of a tensor beyond scalar-valued multilinear maps. Our primary tool was the canonical isomorphism, which allowed us to ignore many specifics in all cases. Now, we will widen the concept of a tensor to involve many vector spaces with linear maps of form

$$
\begin{equation}
T_{B}^{\, A} : (\times_A \, V_i^*) \times (\times_B \, V_i) \to \mathbb{F}.
\end{equation}
$$

Here, we reference two indexed collections of vector spaces (over the same field $\mathbb{F}$), $\langle V_i \rangle_A$ and $\langle V_i \rangle_B$. We differentiate these tensors by calling them heterogeneous, since they involve different vector spaces. They share much theory with the homogeneous case, resulting in the isomorphism

$$
\begin{equation}
\mathcal{L}(\langle V_i^* \rangle_{A}, \, \langle V_i \rangle_{B}; \, ( \otimes_{C} \, V_i^* ) \otimes ( \otimes_{D} \, V_i ))
\cong
\left( \otimes_{A \cup D} \, V_i \right) \otimes \left( \otimes_{B \cup C} \, V_i^* \right).
\end{equation}
$$

{{% hint title="3.31. Explanation" %}}

For the sake of brevity, this will be hand-wavy. The main insight is to quotient $\mathcal{V} = A \cup B \cup C \cup D$ by simple equality. For each partition in $\mathcal{V}_k \in (\mathcal{V}/=)$ of vector spaces associated with a map

$$
T \in \mathcal{L}(\langle V_i^* \rangle_{A}, \, \langle V_i \rangle_{B}; \, ( \otimes_{C} \, V_i ) \otimes ( \otimes_{D} \, V_i )),
$$

fix an argument for all vector spaces in $\mathcal{V} \setminus \mathcal{V}_k$, creating a new multilinear map

$$
\tilde T_k \in \mathcal{L}(V_{(1)}^*, \, \dots, \, V_{(m)}^*, \, V_{(1)}, \, \dots, \, V_{(n)}; \, ( \otimes^a \, V ) \otimes ( \otimes^b \, V^* )),
$$

where $\mathcal{V}_k = [V]$ in $(\mathcal{V} / =)$ and $|\mathcal{V}_k| = a + b + m + n$ (skipping additional bookeeping). By 3.28 and 3.29, $\tilde T_k$ identifies a homogeneous tensor. Since all $\tilde T_k$ can be uniquely transformed this way, we can define a new $\bar T$ that has the cartesian product of their domains as its own domain with codomain $\mathbb{F}$ (by multilinearity). Finally, 3.25 gives the desired form (the RHS of $(11)$). These steps were through isomorphisms, hence $(11)$.

{{% /hint %}}

{{% hint title="3.32. Note" %}}

Much like $(9)$ relies on finite-dimensionality of the vector space in question for homogeneous tensors (or that it be a Hilbert space), the same restriction is needed in $(11)$ for all vector spaces in heterogeneous tensor forms.

{{% /hint %}}

It is still possible to consider tensor type in the heterogeneous case -- one simply has to keep track of one type tuple per $[V] \in (\mathcal{V}/=)$ (see 3.31). If there are $K$ such equivalence classes, a tensor type may be

$$
(m_1, \, \ldots, \, m_K, \, n_1, \, \ldots, \, n_K).
$$

With heterogeneous tensors, one must also carry a mapping of type index to corresponding vector space. Without this, we would not know which vector space each type tuple $(m_i, \\, n_i)$ corresponds to. So when it is not clear from context, we simply say that the type is $(A, \\, B)$ as implied from the syntax of $(10)$.

{{< hcenter >}}
{{< figure src="vera-molnar-untitled-1974.png" width="512" caption="Vera Molnár, Untitled (1974)" >}}
{{< /hcenter >}}

#### Tensor Contractions 

The statements of $(9)$ and $(11)$ may initially seem like a cryptic justification of our choice of vocabulary; they justify why we use the word "tensor" so liberally, with the most general use being in reference to an element of a heterogeneous tensor product space (up to isomorphism).

But beyond justifying use of language, $(9)$ and $(11)$ also provide a clear perspective on computation with tensors. These isomorphisms specify an "exchange rate" between inputs and outputs of homogeneus and heterogeneous tensors. Concretely, one may algebraically "trade" a tensor input in $V$ for a tensor product evaluation with a canonical element of $V^\*$ in the output as many times as desired while maintaining type.

{{% hint title="3.33. Example" %}}

Consider a vector $v \in V$. Earlier, 3.10 showed that this can be seen as a linear map $\psi_v : \mathbb{F} \to V$. Indeed, we can say that $v$ is a vector of type $(1, 0)$ by application of $(9)$ (which helps identify $\psi_v$ with a map in the form of $(8)$, providing its unique tensor type). Informally, we traded an application of $\cdot \otimes V$ in the codomain $V$ (turning it into $\mathbb{F}$ via $\otimes^0 \\, V \cong \mathbb{F}$) for an argument in $V^\*$ to the domain $\mathbb{F}$ (recall $\times^0 \\, V \cong \mathbb{F}$) to finally identify

$$
\hat \psi_v : V^* \to \mathbb{F} \;\; \left( \, \text{s.t.} \;\; \hat \psi_v :  \times^{(0 \, + \, 1)} \, V^* \to \otimes^{(1 \, - \, 1)} \, V \, \right).
$$

{{% /hint %}}

This becomes especially powerful in the context of composition. Indeed, we can legally do "trades" of this kind (even disregarding argument order by 3.17) to reorganize and compose tensors as needed. In other words, we may be able to compose the same two tensors in surprisingly many different ways after we use these "trades" to view each of them as one of many different linear maps they can represent.

{{% hint title="3.34. Example" %}}

Consider two linear operators $f, \\, g \in \mathcal{L}(V)$. They are of form $V \to V$ and have type $(1, 1)$. Without loss of generality, apply $(9)$ to $f$ and $g$ to identify 

$$
\hat g : \mathbb{F} \to V^* \otimes V \;\; \text{and} \;\; \hat f : V^* \otimes V \to \mathbb{F}.
$$

(Recall that 3.25 allows us to identify $\hat f$ from the form $V^\* \times V \to \mathbb{F}$.) Then, we may compose $\hat f \circ \hat g$, which is a tensor of type $(0, 0)$ (the type of a scalar). But we could have just as easily identified

$$
\tilde g : V^* \otimes V \to \mathbb{F} \;\; \text{and} \;\; \tilde f : \mathbb{F} \to V^* \otimes V,
$$

in which case $\tilde f \circ \tilde g$ would be a tensor of type $(2, 2)$. As a final case, we could compose $f$ and $g$ as defined to obtain $f \circ g$, a tensor of type $(1, 1)$. Hence, can obtain tensors of type $(0, 0)$, $(1, 1)$, and $(2, 2)$ from $f$ and $g$ via canonical identification and simple composition. We can even continue doing "trades" in these three tensors (without composition), allowing us to reach the types $(a, b)$ where $a + b \in \\{0, \\, 2, \\, 4 \\}$.

{{% /hint %}}

In the example of 3.34, the map $\otimes : (f, \\, g) \mapsto \tilde f \circ \tilde g$ receives the special name of tensor outer product. It is defined for any two tensors, just as the tensor product (which it is a special case of) is defined on any two tensor product spaces. Taking the outer product of two tensors of type $(a, b)$ and $(c, d)$ results in one more of type $(a + c, \\, b + d)$.

Likewise, the map $\langle \cdot, \\, \cdot \rangle : (f, \\, g) \mapsto \hat f \circ \hat g$ is simply a special case of an inner product. As such, it only made sense in 3.34 as it admitted two tensors that live in the same tensor product space. In such cases, the inner product of two tensors of (necessarily equal) type $(a, b)$ is a scalar of type $(0, 0)$.

{{% hint title="3.35. Note" %}}

Let us take inspiration in the extreme effect that the outer and inner products have in the types of their outputs, using the same tensors $f$ and $g$ as in the example of 3.34. We know from $(9)$ that we can identify 

$$
T_g, \, T_f \in V \otimes V^*
$$

from $f$ and $g$ (canonically). We will see that we can obtain the types $(2, 2)$, $(1, 1)$, and $(0, 0)$ without invoking composition, giving us a new perspective on tensor operations. First, the outer product identifies $\hat f \circ \hat g$ with the tensor of type $(2, 2)$ obtained by $T_f \otimes T_g \in V \otimes V^\* \otimes V \otimes V^\*$.

The next key concept is the evaluation map, which is made canonical by convention. It is defined as the tensor $\text{ev}_U : U^* \otimes U \to \mathbb{F}$ of type $(1, 1)$ such that $\text{ev}_U(\varphi \otimes u) = \varphi(u)$. We can use it to obtain another map

$$
\begin{align*}
(\text{id}_V \otimes \text{ev}_V \otimes \text{id}_{V^*}) & : V \otimes V^* \otimes V \otimes V^* \to V^* \otimes V \\
& \text{s.t.} \;\; (\text{id}_V \otimes \text{ev}_V \otimes \text{id}_{V^*})(v \otimes \varphi \otimes w \otimes \phi) = \varphi(w) (v \otimes \phi),
\end{align*}
$$

where the tensor $(\text{id}_U \otimes \text{ev}_U \otimes \text{id}_U)(T_f \otimes T_g)$ corresponds exactly to $f \circ g$ and is of type $(1, 1)$. Similarly, applying the evaluation map a second time decreases tensor type uniformly, where we can use

$$
\begin{align*}
(\text{ev}_V \otimes \text{ev}_V) : V^* & \otimes V \otimes V^* \otimes V \to \mathbb{F} \\
& \text{s.t.} \;\; (\text{ev}_V \otimes \text{ev}_V)(v \otimes \varphi \otimes w \otimes \phi) = \varphi(w) \phi(v)
\end{align*}
$$

with 3.17 (to disregard argument order) to get $(\text{ev}_V \otimes \text{ev}_V)(T_f \otimes T_g)$, corresponding exactly to $\tilde f \circ \tilde g$, whose type is $(0, 0)$ (a scalar). The pattern is becomes clear -- the evaluation map provides a canonical way to obtain a tensor of type $(a - 1, \\, b - 1)$ from another of type $(a, \\, b)$, annihilating one vector-covector argument pair of our choosing (when the input tensor is viewed as a multilinear map). After, we may still perform "trades" on the resulting tensors (independently of any idea of composition, as described in 3.33).

{{% /hint %}}

The process of "evaluation" (perhaps done over many vector-covector argument pairs simultaneously) as described in 3.35 is known as a [tensor contraction](https://en.wikipedia.org/wiki/Tensor_contraction). Note that it can involve any number of tensors, as the atomic step is the evaluation of $\text{ev}_V$ with respect to a single vector-covector pair of arguments involved in the group of tensors. The collection of tensors involved in a contraction is referred to as a [tensor network](https://en.wikipedia.org/wiki/Tensor_network). The result of a contraction is of course a single tensor, which can be seen to "compose" the tensors in the network in arbitrarily complex ways (through the perspective in 3.34).

{{% hint title="3.36. Note" %}}

Remember that $(9)$ and $(11)$ rely on the assumption that $V^\* \cong V$ exists and is canonical, as with any Hilbert space (per 3.14) or finite-dimensional vector space. This is what underlies 3.34 (the perspective of "trades and compositions") and 3.35 (the perspective of "contractions through evaluations"). The idea of "trading" described in 3.33 was central in these contexts.

While assuming a canonical $V^* \cong V$ provides a way to perform "trades" which we semantically interpret to be uniquely correct (the bijection underlying the isomorphism), many applications introduce special tensors of type $(2, 0)$ or $(0, 2)$ with the sole purpose of using them as adapters, enabling "trades" via contractions with them, for example, to contract a tensor of type $(0, 7)$ with an adapter of type $(2, 0)$ to obtain another of type $(1, 6)$.

{{% /hint %}}

#### Heterogeneous Contractions

We have mostly ignored heterogeneous tensor spaces. Now, the framework of contractions offers a great opportunity to pull them back onto our train of thought. We have implied two stages for finding the type of a tensor contraction. First, consider the tensor product of all the spaces involved. Then, repeatedly utilize the canonical map $\text{ev}_V$, once per pair of dual spaces involved in the contraction.  

{{% hint title="3.37. Note" %}}

For a homogeneous tensor network involving tensors $T_1, \\, \ldots, \\, T_n$ with $T_i$ of shape $(a_i, b_i)$, the tensor product of all the tensors in the network is

$$
\otimes_i \, T_i \in \otimes_i \, ((\otimes^{a_i} V) \otimes (\otimes^{b_i} V^*)) \;\; \text{s.t.} \;\; T_i \in T_{b_i}^{\, a_i}(V)
$$

thanks to $(9)$. Applying 3.17 to reorganize, we see that $\otimes_i \\, T_i$ is of type $(\Sigma_i a_i , \\, \Sigma_i b_i)$. We then continue to use 3.17 and compose $\text{ev}_V$ with $\text{id}_V$ to construct mappings that perform arbitrary contractions just as done in 3.35, where together with "trades," we may achieve a contraction with any type in

$$
\{ \, (a, b) \; : \; a + b = 2k \;\; \text{s.t.} \;\; 0 \leq k \leq \min(\Sigma_i a_i , \, \Sigma_i b_i) \, \}.
$$

{{% /hint %}}

Here, we notice that we can use specialized maps $\text{ev}_X$ for appearances of each different vector space $X$ in a heterogeneous tensor contraction. While I will not formulate a heterogeneous equivalent of 3.37 (as it would be exceedingly verbose), we can see that for a tensor of type 

$$
(\{A_{(1)}^*, \, \ldots, \, B_{(1)}^*, \ldots \}, \{A_{(1)}, \, \ldots, \, B_{(1)}, \ldots \}),
$$

a contraction is determined by a collection of pairs $\\{ (X_{(i)}^*, \\, X_{(j)}), \\, \ldots \\}$ where maps $\text{ev}_X$ are to be used in the manner of 3.35. It requires a significant amount of bookeeping, but its soundness is visible through an argument completely analogous to 3.31. Likewise, we can apply "trading" as in 3.33 for "like terms" in their domain and codomain (pairs of vector spaces dual to each other among their factors).

{{% hint title="3.38. Note" %}}

Bookeeping heterogeneous tensor contractions is a big practical problem. In particular, many machine learning workloads which consider heterogeneous tensors (often typed over vector spaces $\langle \mathbb{R}^d \rangle\_{d \\, \in \\, D}$) are made difficult from the need of ensuring that tensor contractions are well-formed before their coordinates can be computed.

{{% /hint %}}


#### Syntax Standards 

An appealing model of tensor operations was offered by [Sir Roger Penrose](https://en.wikipedia.org/wiki/Roger_Penrose) in 1971 within the illustrated writeup [Applications of Negative-Dimensional Tensors](https://www.mscs.dal.ca/%7Eselinger/papers/graphical-bib/public/Penrose-applications-of-negative-dimensional-tensors.pdf). There, he provided a first theory of abstract tensor networks which he called Abstract Tensor Systems (ATS), which came with a coordinate-free system for representing homogeneous tensors and contractions. This system became known as [Penrose graphical notation](https://en.wikipedia.org/wiki/Penrose_graphical_notation). It is delightful for any abstract treatment of tensors (like our own so far).

{{% hint title="3.39. Example" %}}

In Penrose graphical notation, individual tensors are represented as nodes in a graph sometimes distinguished by geometric shapes for ease of reference. The type of the tensor being represented is indicated by its number of outgoing edges. The system differentiates a "cartesian" case by the availability of a bijection $\Phi : V \to V^*$. (We have been assuming this -- see 3.36). In the cartesian case, edge direction does not matter. Otherwise, a tensor of type $(a, b)$ will have $a$ upwards pointing edges and $b$ downward pointing edges. Contractions are set by connecting corresponding edges.

{{% /hint %}}

{{< hcenter >}}
{{< figure src="roger-penrose.png" width="256" caption="Sir Roger Penrose (born August 8, 1931)" >}}
{{< /hcenter >}}

#### Overview



### Signals and Systems 

### Kernel Methods

[^axiom-choice]: When considering infinite-dimensional vector spaces, this statement is true if and only if one admits the axiom of choice. Perhaps this was another motivation of Axler's restriction to finite-dimensional vector spaces.

[^sub-indices]: Non-parenthesized sub-indices imply that the item is part of an indexed set -- $a_i$ may not be equal to $a_j$.

[^parenthesized-indices]: Parenthesized sub-indices are only used to indicate argument index. That is, $a_{(i)} = a_{(j)}$ for all $i$ and $j$.


