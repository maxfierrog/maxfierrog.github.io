---
title: "Unifying Perspectives of Tensors, Signals, and Kernel Methods"
category: technical
date: 2025-09-08
draft: true 
plots: false 
math: true
---

{{< toc >}}

## Abstract

Tensor algebra, signal processing, and machine learning methods are (one of many groups of) topics with natural relationships enclosed in a dense shell of mathematics. This article approaches these topics individually through somewhat unusual logical paths, making an upfront payment in mathematical accessibility to arrive at perspectives which reveal romantic links between their concepts. All these perspectives are well-known, but it is rare to see many of them presented together in a structured narrative. 

---

## Overview 

This article has four primary parts, each on a particular topic. They are intended to be consumed in order. I assume eingineering-oriented first courses in linear algebra, signal processing, and machine learning. For readers in need of comprehensive review or first-time coverage, I leave these resources:

* **Kernel methods.** [_Foundations of Machine Learning_](https://cs.nyu.edu/~mohri/mlbook/) by Mohri, Rostamizadeh, and Talwalkar,
* **Signals and Systems.** [_Signals & Systems: Theory and Applications_](https://ss2-2e.eecs.umich.edu/) by Ulaby and Yagle,
* **Linear algebra.** [_Linear Algebra Done Right_](https://linear.axler.net/) by Axler.

### Dimensionality

I begin by considering infinite-dimensional vector spaces. I demonstrate how the definition of span  can a be relaxed to allow the coordinate-based treatement of vector spaces of dimensions with different (infinite) cardinalities, and what the notion of a basis becomes under these relaxations. This leads us to Banach and Hilbert spaces while being a digestible way to ramp-up for the following sections.

### Tensor Spaces

I organize the semantics behind matrices, vectors, and linear maps. After introducing some preliminary concepts not usually covered in a first course in linear algebra (e.g. canonical isomorphisms), I provide an abstract introduction to tensors and tensor spaces, showing how they are "many things at once" up to (quite practical) isomorphism. After the abstract introduction, I share notation standards for coordinate-based and coordinate-free tensor algebra paired with real-world examples from differential geometry and deep learning.

{{% hint title="2.1. Note" %}}

The logical procedures and syntax conventions of this section are shown to be valid for Hilbert spaces and tensor products thereof, although there are other infinite-dimensional cases where they also apply. But it would be too difficult to structure these nuances in a complete way, and they would not enjoy much overlap with subsequent topics regardless.

{{% /hint %}}

### Signals and Systems 

TODO

### Kernel Methods

TODO

---

## Dimensionality 

In what could nowadays be seen as the canon of linear algebra education, Sheldon Axler opens with the statement below to set the stage for the rest of _Linear Algebra Done Right_:

> Linear algebra is the study of linear maps on finite-dimensional vector spaces.

The restriction of vector spaces to the finite-dimensional case was one the most mathematically respectful ways to negotiate generality with practical pedagogy. But the spirit of linear algebra lives beyond the finite-dimensional case.

### Hamel Bases

Most engineers are familiar with the concept of a [Hamel basis](https://en.wikipedia.org/wiki/Basis_(linear_algebra)) of a vector space (even if they are not aware of this). For a vector space $V$ over a field $\mathbb{F}$ and a Hamel basis $\mathcal{B}$, then "$\mathcal{B}$ spans $V$" translates to the familiar

$$
\begin{equation}
    \forall v \in V, \; v = \sum_{i \, = \, 0}^{k} c_i b_i \;\; \text{s.t.} \;\; c_i \in \mathbb{F}, \, b_i \in \mathcal{B}, \, k \in \mathbb{N}.
\end{equation}
$$

Importantly, for $\mathcal{B}$ to be a Hamel basis, the sum in $(1)$ must have finite terms. Note that this is allowed even in cases where $\mathcal{B}$ is infinite (or in other words, where $V$ is infinite-dimensional), as one does not necessarily assign nonzero coefficients $c_i$ to each element of $\mathcal{B}$.

{{% hint title="3.1. Example" %}}

The vector space of polynomials (of finite terms) with coefficients in a field $\mathbb{F}$,

$$
\mathbb{F}[x] = \left\{ \sum_{i \, = \, 0}^n a_i x^i \;\Big|\; n \in \mathbb{N},\ a_i \in \mathbb{F} \right\},
$$

has the infinite basis $\mathcal{B}_{\mathbb{F}[x]} = \\{1, x, x^2, x^3, \dots \\}$. However, each of its elements is the linear combination of a finite number of basis elements. For example, the polynomial $p(x) = 3 + 4x^2 + x^3$ can be expressed as the linear combination

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

Here we imposed a standard representation such that, for example, $x^2 = \left[ 0, \\, 0, \\, 1, \\, 0, \\, {\dots} \right]^\top$. We see that, despite each basis vector being infinite-dimensional, all polynomials are combine a finite number of them -- $\mathcal{B}_{\mathbb{F}[x]}$ is a Hamel basis.

{{% /hint %}}

{{< hcenter >}}
{{< figure src="vera-molnar-molndrian-1974.png" width="512" caption="Vera Molnár, 'Molndrian' (1974)" >}}
{{< /hcenter >}}

### Schauder Bases

Under a very strict interpretation of Axler, $\mathbb{F}[x]$ is already beyond linear algebra since it is of [semi-infinite](https://en.wikipedia.org/wiki/Semi-infinite) dimension. However, it is definitionally a perfectly valid vector space. Just as finite dimensionality is not necessary in order to access the theorems of linear algebra, having a countable Hamel basis is also not strictly necessary. While all vector spaces do have some Hamel basis[^axiom-choice], not all of them have a countable one.

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

The vector space of $p$-summable (with $p > 0$) sequences of real numbers,

$$
\ell^p = \left\{ (x_1, x_2, x_3, \dots) \;:\; \sum_{n \, = \, 1}^\infty |x_n|^p < \infty \right\},
$$

has no Hamel basis because, no matter how you define one, there is an element of $\ell^p$ requiring decomposition into an infinite number of basis elements (which is not allowed). But it does have the following countably-infinite Schauder basis:

$$
\mathcal{B}_{\ell^p} = \left\{ 
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

### Taxonomy of Spaces

Hidden in $(2)$ is the need for all infinite series over basis elements to converge for each vector in $V$. But standalone vector spaces do not include any operation that computes the "closeness" of two vectors, so additional concepts are needed to make sense of convergence. Abstractly, one needs to equip the vector space of interest with a [topology](https://en.wikipedia.org/wiki/Topological_space#topology). The way of doing so that we will consider is by assuming a [norm](https://en.wikipedia.org/wiki/Norm_(mathematics)) over the space, such that we can concretely declare the definition of an infinite series

$$
\begin{equation}
    \lim_{n \to \infty} \left\| x - \sum_{k \, = \, 1}^n a_k \right\| = 0 \iff x = \sum_{k \, = \, 1}^{\infty} a_k.
\end{equation}
$$

Vector spaces that have a norm are called normed vector spaces. If a normed vector space is [complete](https://en.wikipedia.org/wiki/Complete_metric_space) under the norm-induced [metric](https://en.wikipedia.org/wiki/Metric_space) $d : (x_1, \\, x_2) \mapsto \\| x_1 - x_2 \\|$, then it is also called a [Banach space](https://en.wikipedia.org/wiki/Banach_space). With the example of $\ell^p$, we have completeness through the conventional $\ell^p$-norm, defined with

$$
\|x\|_p =
\begin{cases}
\; \left( \displaystyle\sum_{n=1}^\infty |x_n|^p \right)^{1/p} & \text{if} \;\; 1 \le p < \infty, \\[1.8em]
\;\; \sup_{n} |x_n| & \text{if} \;\; p = \infty.
\end{cases}
$$

{{% hint title="3.4. Note" %}}

Banach spaces do not necessarily have a Schauder basis. The reason for this is technical and out of scope. Additionally, not all normed vector spaces that have a Schauder basis are Banach spaces, because they may not be complete. But for the remainder of this article, completeness can be comfortably assumed. Indeed, most of the time anyone talks about a Schauder basis in a practical context, it spans a Banach space (such as $\ell^p$).

{{% /hint %}}

If a Banach space is also equipped with an [inner product](https://en.wikipedia.org/wiki/Inner_product_space) in such a way that $\langle x, \\, x \rangle = \\| x \\|^2$, then it is also called a [Hilbert space](https://en.wikipedia.org/wiki/Inner_product_space). With the understanding that a vector space with an inner product defined is called an inner-product space, we arrive at the following:

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

### Continuous Bases

We will now consider are vector spaces of uncountably-infinite dimension. We have been comfortable in our use of syntax like "$x = [ 1, \\, 2, \\, \ldots ]^\top$" to point at vectors of countably-infinite dimension. This will no longer be possible with uncountably-infinite dimensions, so we must revisit our notation.

{{% hint title="3.5. Reminder" %}}

A vector $v$ is an abstract object and exists independently of a choice of basis. To write $v$ as a tuple $[c_1, \dots, c_n]$, one must choose a basis $\{b_1, \\, \dots, \\, b_n\}$ and expand (in the finite-dimensional case) 

$$
v = \sum_{i \, = \, 1}^n c_i b_i.
$$

Hence, $[c_1, \ldots, c_n]^\top$ means “the coefficients of $v$ in basis $b_1, \\, \ldots, \\, b_n$.” Changing the basis can change the coefficients (sometimes referred to as coordinaates), but not the vector itself. This also applies to infinite-dimensional cases.

{{% /hint %}}

Observing that, once a basis is chosen, a vector in a vector space $V$ over a field $\mathbb{F}$ is determined by the coefficients in its representation as a linear combination of basis vectors, we can introduce a new type of linear combination to evolve the translation of "$\mathcal{B}$ spans $V$" to involve a [Lebesgue integral](https://en.wikipedia.org/wiki/Lebesgue_integral),

$$
\begin{equation}
    \forall v \in V, \; v = \int_{\mathcal{\Omega}} c(\omega) b(\omega) \, d\mu(\omega) \;\; \text{s.t.} \;\; c : \Omega \to \mathbb{F}, \, b : \Omega \to \mathcal{B},
\end{equation}
$$

where a measure ${\mu}$ over $\Omega$ is provided. Here, the index $\omega$ intuitively replaces the index $i$ from $(2)$, where there is a map $c$ "choosing" a coefficient $c(\omega)$ and a map $b$ "choosing" a basis element $b(\omega)$ per index $\omega$. This way, once a map $b : \Omega \to \mathcal{B}$ and a measure $\mu$ over $\Omega$ have been agreed upon, a vector can still be represented (intuitively) by "its coefficients," which are now encoded by the map $c$.

{{% hint title="3.6. Example" %}}

We can consider the Banach space of all $p$-integrable (with $p > 0$) functions on a measurable set $X$

$$
L^p(X, \, \mu) = 
\Bigl\{\, f: X \to \mathbb{C} \; \text{measurable} \; \big| \; \int_{X} |f(x)|^p \, d\mu(x) < \infty \,\Bigr\}.
$$

Much like the $\ell^p$-norm is standard for $\ell^p$, the $L^p$-norm is standard for $L^p$. It is defined in the same way, the only difference being the promotion of the sum to a Lebesgue integral on $X$ using the measure $\mu$:

$$
\|f\|_p =
\begin{cases}
\; \left( \displaystyle\int_{X} |f(x)|^p \, d\mu(x) \right)^{1/p} & \text{if} \;\; 1 \le p < \infty, \\[1.4em]
\;\; \sup_x |f(x)| & \text{if} \;\; p = \infty.
\end{cases}
$$

Notice that if $X = \mathbb{N}$ and $\mu$ is the counting measure, $L^p(X, \\, \mu)$ becomes $\ell^p$ and the $L^p$-norm is the $\ell^p$-norm (the latter is one countable case of the former). In the important case of $L^2(\mathbb{R})$, the inner product defined by

$$
\langle f_1(x), f_2(x) \rangle = \int_{\mathbb{R}} f_1(x) \overline{f_2(x)} \, dx
$$

actually ensures that $\langle f, f \rangle = \\| f \\|\_2$, making $L^2(\mathbb{R})$ a Hilbert space. A natural choice of "continuous basis" for $L^2(\mathbb{R})$ is the complex exponentials indexed by frequency, $b_\omega(t) = e^{2 \pi i \omega t}$ with $\omega \in \mathbb{R}$. So for all $f \in L^2(\mathbb{R})$,

$$
f(t) = \int_{\mathbb{R}} c(\omega)b(\omega) \, d\mu(\omega) = \int_{-\infty}^{\infty} c(\omega)\, b_\omega(t)\, d\omega.
$$

The measure $d\omega$ is the typical Lebesgue measure on $\mathbb{R}$. Here, we see that any $f \in L^2(\mathbb{R})$ can be expressed as a continuous linear combination of basis elements in the form $e^{2 \pi i \omega t}$ which, to reiterate, are other functions parameterized by $t$ and indexed by $\omega \in \mathbb{R}$. In this case, the [Fourier transform](https://en.wikipedia.org/wiki/Fourier_transform) of $f(t)$ is in fact $c(\omega)$:

$$
c(\omega) =
\int_{-\infty}^{\infty} f(t) \, \overline{b_\omega(t)} \, dt
= \int_{-\infty}^{\infty} f(t)\, e^{-2 \pi i \omega t} \, dt.
$$

In a finite-dimensional case, we would compute $\langle v, \\, e_n \rangle$ to observe the "contribution" of a basis element $e_n$ to the vector $v$, obtaining the coefficient it would be assigned in its decomposition as a linear combination of basis elements. The Fourier transform does exactly the same thing per basis index $\omega$, where $v = f(t)$ and $e_n = b_\omega(t)$:

$$
c(\omega) = \langle f(t), \, b_\omega(t) \rangle.
$$

It is not difficult to show algebraically that the Fourier transform $\mathcal{F} : L^2(\mathbb{R}) \to L^2(\mathbb{R})$ is a linear operator over this Hilbert space. (So if it were countably-infinite dimensional, it would have a matrix representation.)

{{% /hint %}}

### Overview

We have diluted the finite-dimensional barrier of vector spaces to allow those with countably- or uncountably-infinite dimensions. To do so, we had to slowly relax our concept of a linear combination from $(1)$ (which already spanned certain infinite-dimensional spaces like $\mathbb{F}[x]$), to $(2)$ (which was able to span a more countably-infinite-dimensional spaces like $\ell^p$), and finally $(5)$ (which can span uncountably-infinite dimensional spaces like $L^2(\mathbb{R})$). We have also encountered Banach and Hilbert spaces, which allow us to assume structure in abstract contexts.

{{< hcenter >}}
{{< figure src="david-hilbert.jpg" width="256" caption="David Hilbert (January 23, 1862 – February 14, 1943)" >}}
{{< /hcenter >}}

{{% hint title="3.7. Note" %}}

$(1)$ is a special case of $(2)$, which is itself a special case of $(5)$. So this was truly a relaxation of the linear combination, as we did not lock ourselves out of any vector spaces we could already span. To be explicit, in the case of $(5)$ and $(2)$,

$$
\Omega = \mathbb{N} \iff \forall v \in V, \; v = \int_{\Omega} c(\omega)b(\omega) \, d{\mu}(\omega) = \sum_{i \, = \, 0}^{\infty} c_i b_i.
$$

In the case of $(2)$ and $(1)$, when all vectors in $V$ are a linear combination of a finite number of basis elements, observe that much like in the case where $V = \mathbb{F}[x]$

$$
\forall v \in V, \; v = \sum_{i \, = \, 0}^{\infty} c_i b_i = \sum_{i \, = \, 0}^{k} c_i b_i \;\; \text{s.t.} \;\; k \in \mathbb{N}.
$$

{{% /hint %}}

## Tensor Spaces

Let us revisit the opening scene of _Linear Algebra Done Right_:

> Linear algebra is the study of linear maps on finite-dimensional vector spaces.

Another thing to note (apart from the restriction to finite dimensions) is the lack of mention of matrices and vectors in this description. This section will live up to this witholding; we will first adopt a linear-map-centric view of the objects in linear algebra and, afterwards, gain an understanding of tensor spaces.

### Matrices

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

There is a bijection between $\mathcal{L}(V, W)$ and $\mathbb{F}^{(\dim V) \times (\dim W)}$, where $V$ and $W$ are vector spaces on the same field $\mathbb{F}$ and are finite-dimensional. In other words, for each linear map $T$ from a vector space of dimension $n$ to another of dimension $m$, there is exactly one $m$-by-$n$ matrix with entries in $\mathbb{F}$. When bases are fixed, $T$ and its corresponding matrix can be translated in a standard way (the way that makes matrix multiplication work as expected).

{{% /hint %}}

{{% hint title="3.10. Note" %}}

A vector $v$ in a space $V$ over $\mathbb{F}$ can be regarded as a linear map from the space $\mathbb{F}^1$ into $V$, via 

$$
\psi_v : \mathbb{F}^1 \to V, \;\; \psi_v(\lambda) = v \lambda.
$$

When a basis for $V$ is fixed, the map $\psi_v$ is represented by an $n \times 1$ matrix (as an instance of 3.9) -- the familiar column of "coordinates" of $v$. In particular, scalar multiplication can be seen as matrix multiplication with a single-dimensional vector,

$$
\psi_v(\lambda) = 
\begin{bmatrix}
v_1 \\
\vdots \\
v_{(\dim V)}
\end{bmatrix}
\begin{bmatrix}
\lambda_1
\end{bmatrix} =
\begin{bmatrix}
\lambda_1 v_1 \\
\vdots \\
\lambda_1 v_{(\dim V)}
\end{bmatrix} = 
\lambda_1 v.
$$

{{% /hint %}}

This conceptually organizes vectors, matrices, and linear maps. With fixed bases, we see that the set of linear maps from $V$ to $W$ is already a vector space (via 3.8) and also that all vectors in a space $U$ over a field $\mathbb{F}$ are canonically a linear map from $\mathbb{F}^1$ into $U$ (via 3.10), hence 

$$
\text{Linear maps} \cong \text{Vectors}.
$$

Also, we see that there is a one-to-one correspondence between linear maps and matrices (via 3.9) by the bijection $\mathcal{L}(V, W) \leftrightarrow \mathbb{F}^{(\dim W) \times (\dim V)}$, so at least in finite dimensions ($\cong^\!$)

$$
\text{Matrices} \cong^! \text{Linear maps}.
$$

Indeed, we can adopt the perspective that a vector can be represented as a matrix solely through its identifiability as a linear map, turning the linear map into the central object of our understanding of linear algebra. In summary,

$$
\begin{equation}
    \text{Matrices} \cong^! \text{Linear maps} \cong \text{Vectors}.
\end{equation}
$$

{{% hint title="3.11. Note" %}}

We have started to use the symbol $\cong$. In this context, stating $A \cong B$ implies that there is a linear [isomorphism](https://en.wikipedia.org/wiki/Isomorphism) between $A$ and $B$. Exactly, this means that there exists some linear map $\phi : A \to B$ that is a bijection. 

This is complete as a definition of the symbol $\cong$. However, its use in the rest of this article will often reference a choice of $\phi$ that is [canonical](https://en.wikipedia.org/wiki/Canonical_map). This means that if you see $A \cong B$, there is probably an "standard" way to obtain a $b \in B$ from one unique $a \in A$ (and vice versa) -- here, we can informally say that if $\phi(a) = b$ then $a$ "is" $b$, but it is more precise to say that $a$ "identifies" $b$. Oftentimes, $\phi$ will not be made explicit.

For example, choosing a basis $\mathcal{B}$ for a space $V$ gives the canonical isomorphism $\mathbb{F}^{(\dim V)\times(\dim V)} \cong \mathcal{L}(V)$. In this case, it makes sense to say that "a matrix is a linear transformation." But without a choice of basis there is no "standard" bijection $\Phi_\mathcal{B} : \mathcal{L}(V) \to \mathbb{F}^{(\dim V) \times (\dim V)}$ (no way to bijectively identify maps from matrices).
 
{{% /hint %}}

{{< hcenter >}}
{{< figure src="vera-molnar-untitled-square-1974.png" width="512" caption="Vera Molnár, Untitled (1974)" >}}
{{< /hcenter >}}

### Vector Translation

The statement of 3.10 is somewhat nuanced. Consider the case of a linear map $T \in \mathcal{L}(V, W)$ represented by a matrix $M$ (under fixed bases). According to 3.8 that map is a vector, but according to 3.10 it is identified by some _other_ linear map $\psi_v$, which is identified by some _other_ column matrix $M^\prime$,

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

### Linear Forms 

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

### Dual Spaces 

All linear maps $\varphi_u : U \to \mathbb{F}^1$ receive the name of [linear forms](https://en.wikipedia.org/wiki/Linear_form) on the space $U$. Applying 3.8, this is no more than the vector space 

$$
 U^* = \{ \, \varphi : U \to \mathbb{F}^1 \; | \; \varphi \text{ is linear} \}.
$$

This is called the [dual vector space](https://en.wikipedia.org/wiki/Dual_space) of $U$, which receives the special notation $U^\*$ due to how naturally it arises. Much like elements of $U$ can be represented by column matrices, the elements of $U^\*$ (which are called covectors) can be represented by row matrices (wherever matrices make sense).

{{% hint title="3.14. Note" %}}

In any Hilbert space $H$, for every continuous linear form $\varphi \in H^*$, there is a unique vector $u \in H$ with

$$
 \forall \, v \in H, \; \varphi(v) = \langle u, v \rangle.
$$

Furthermore, $\\| u \\| = \\| \varphi \\|$. This gives a natural identification between elements of $H$ and $H^*$ by the canonical bijection $J : u \mapsto \varphi$. Here, we say "canonical" because it is provided uniquely by the inner product. This is the statement of the [Riesz representation theorem](https://en.wikipedia.org/wiki/Riesz_representation_theorem). 

{{% /hint %}}

It is not difficult to see that for any vector space $V$ over a field $\mathbb{F}$, in fact, $\Psi_V$ and $\Psi_V^\*$ are both bijections. Since they are canonical (in the sense that they are naturally defined), it is only right to assert that 

$$
V \cong \mathcal{L}(\mathbb{F}^1, V) \;\; \text{and} \;\; V^* \cong \mathcal{L}(V, \mathbb{F}^1).
$$

{{< hcenter >}}
{{< figure src="frigyes-riesz.jpg" width="256" caption="Frigyes Riesz (January 22, 1880 – February 28, 1956)" >}}
{{< /hcenter >}}

### Multilinearity

We can continue talking about linearity in maps even when they have multiple arguments. For a map $T$ from multiple vector spaces $V_i$ into another $W$ (all over some field $\mathbb{F}$) where 

$$
T : V_1 \times \dots \times V_n \to W \;\; \text{s.t.} \;\; T(v_1, \, \ldots, \, v_n) = w,
$$

we say that $T$ is linear in the argument $v_i$ if, for all other arguments $v_j \neq v_i$, fixing $v_j$ makes the newly altered map $T^\prime : V_i \to W$ linear. If such a map $T$ is linear in all of its $n$ arguments it is called $n$-linear, and all maps like this are called [multilinear maps](https://en.wikipedia.org/wiki/Multilinear_map). The set of multilinear maps of this form is denoted

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

We may also extend linear forms with the same treatment that led us to multilinear maps. In particular, if an $n$-linear map over a field $\mathbb{F}$ has $\mathbb{F}$ itself as a codomain, it is generally referred to as an $n$-linear form (where all maps like this are called [multilinear forms](https://en.wikipedia.org/wiki/Multilinear_form)).

### Tensor Product

For two vector spaces $V$ and $W$ over the same field, one can of course construct their cartesian product $V \times W$ (as we have been doing in the case of multilinear maps). But instead of doing that, we can construct a third vector space $V \otimes W$ (called the [tensor product](https://en.wikipedia.org/wiki/Tensor_product) of $V$ and $W$) which, while being as expressive as $V \times W$, enjoys the added benefit of being a vector space itself.

{{% hint title="3.16. Note" %}}

Let $\varphi : V \times W \to V \otimes W$ be a bilinear map. Then, for each bilinear map $h : V \times W \to Z$ (into another vector space $Z$), there is a unique linear map $\tilde h : V \otimes W \to Z$ such that $h = \tilde h \circ \varphi$. This is referred to as the [universal property](https://en.wikipedia.org/wiki/Universal_property) of the tensor product, and it justifies the phrase "as expressive as $V \times W$."

{{% /hint %}}

Every tensor product $V \otimes W$ is equipped with a bilinear map $\varphi : V \times W \to V \otimes W$ that allows the construction of vectors in $V \otimes W$. The [outer product](https://en.wikipedia.org/wiki/Outer_product) is an example of this in finite dimensions, but in other cases one must be more creative. Confusingly, this map $\varphi$ is also called a tensor product, and $\otimes$ is predominantly used instead of $\varphi$ in notation. Summarizing this overloaded notation,

$$
\forall (v, w) \in V \times W, \; \varphi(v, w) = v \otimes w \quad \text{where} \quad v \otimes w \in V \otimes W.
$$

{{% hint title="3.17. Note" %}}

Even if the tensor product among vectors is not strictly commutative, there are induced canonical isomorphisms between all permutations of tensor products of vector spaces. That is, for any permutation $\sigma$,

$$
V_1 \otimes \cdots \otimes V_n \;\cong\; V_{\sigma(1)} \otimes \cdots \otimes V_{\sigma(n)}.
$$

Due to this symmetry, we often write tensor products as if they were commutative without loss of generality. But order will matter in actual computation or representation -- we simply have to track what permutations we apply. In that sense, these permutations represent bijections that are "situationally" canonical (because we will always know which ones we used).

{{% /hint %}}

Through 3.16 and 3.17, the tensor product is precisely designed to "linearize" multilinear maps. In other words, for any multilinear map $h \in \mathcal{L}(V_1, \\, \ldots, \\, V_n; \\, W)$, there exists a unique linear map[^sub-indices]

$$
\tilde h : \bigotimes_i V_i \to W \;\; \text{s.t.} \;\; \tilde h(v_1 \otimes \cdots \otimes v_n) = h(v_1, \ldots, v_n).
$$

### More Matrices

Being now able to identify every multilinear map with a unique linear map over a tensor product space, it is possible to assert that 3.8, 3.9, and 3.10 also apply to tensor product spaces. I will reiterate the notes, dressing them up specifically for the case of tensor product spaces.

{{% hint title="3.18. Specialization of 3.8" %}}

The set of linear maps from a tensor product space $\bigotimes_i V_i$ over the field $\mathbb{F}$ to another vector space $W$ over $\mathbb{F}$ forms a vector space over $\mathbb{F}$. Symbolically,

$$
\mathcal{L}({\textstyle\bigotimes}_i V_i, W) = \left\{ \, T : \bigotimes_i V_i \to W \;\; \bigg| \;\; T \text{ is linear}  \right\}
$$

is a vector space over $\mathbb{F}$. We denote the operator case as $\mathcal{L}(\bigotimes_i V_i) = \mathcal{L}(\bigotimes_i V_i, \\, \bigotimes_i V_i)$.

{{% /hint %}}

{{% hint title="3.19. Specialization of 3.9" %}}

The basis-induced isomorphism $\mathcal{L}(\bigotimes_i V_i, W) \cong \mathbb{F}^{(\times_i \dim V_i) \times (\dim W)}$ (where $V$ and $W$ are finite-dimensional over $\mathbb{F}$) exists, but is canonical only through a generalization of matrix multiplication (seen in 3.41). To be clear, 3.9 is still true in that $\mathcal{L}(\bigotimes_i V_i, W) \cong \mathbb{F}^{(\Pi_i \dim V_i) \times (\dim W)}$, but it is not canonical for tensor product spaces. 

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

Appending a trailing \(1\) to the shape of a matrix does not change the underlying object. Concretely, a matrix of shape \((\alpha_1, \, \ldots, \, \alpha_n)\) can be naturally identified with one of shape \((\beta_1, \, \ldots, \, \beta_n)\) when $\prod_i \alpha_i = \prod_i \beta_i$. This is a result of the (finite-dimensional) isomorphisms included in the scope of

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

With a basis for $\bigotimes_i V_i$ fixed, the map $\psi_v$ can be represented by a matrix of shape $(1, \\, \Pi_i V_i)$ (invoking 3.19). But for tensor product spaces, it is canonical to represent $\psi_v$ using a matrix of shape $(\dim V_1, \\, \ldots, \\, \dim V_n)$ (invoking 3.21). We will see why in 3.41.

{{% /hint %}}

### Homogeneous Tensors 

Many people refer to vectors in tensor product spaces as tensors, especially in computationally-oriented scientific disciplines. This population has recently gained numerosity (and maybe even majority) thanks to the increasing availability of efficient computers and their applications. Prior to taking that perspective, we will understand [tensors](https://en.wikipedia.org/wiki/Tensor_(intrinsic_definition)) as linear maps associated with a single vector space $V$ over $\mathbb{F}$ of the form

$$
\begin{equation}
 T_{n}^{\, m} : (\times^m \, V^*) \times (\times^n \, V) \to \mathbb{F}.
\end{equation}
$$

Here, $(m, \\, n)$ is named the "type" of the tensor $T$, where $m + n$ is referred to as $\text{rank}(T)$. This makes a $2$-linear map like $m : \mathbb{R}^2 \times \mathbb{R}^4 \to \mathbb{R}$ not (yet) identifiable as a tensor, since it is not of tensor form and there is no canonical isomorphism to help us coerce its form into $(8)$.

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

### Tensor Identification

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

As a result of 3.16, for all vector spaces $V$, $W$, and $Z$, the tensor product canonicalizes 

$$
\mathcal{L}(V \otimes W, \, Z) \cong\ \mathcal{L}(V, \, W; \, Z).
$$

{{% /hint %}}

{{% hint title="3.26. Note" %}}

Due to 3.14, any Hilbert space is canonically isomorphic to its own dual by the bijection

$$
\Phi : V \to V^* \;\; \text{s.t.} \;\; \Phi[v](w) = \langle v, w \rangle_V
$$

where $u, w \in V$. Above, $\Phi$ is exactly $J$ fom 3.14 and is canonical by convention in abstract contexts. Outside of an entirely abstract context, one only needs to "manually" specify a canonical $\Phi$ to make $V \cong V^\*$ canonical (this is a subtle point which will be important for 3.36).

{{% /hint %}}

{{% hint title="3.27. Note" %}}

For any Hilbert space $V$, we have that $V^\* \otimes V^\* \cong (V \otimes V)^\*$ canonically. This is supported by the following standard choice of bijection

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

where $v_i \in V^\*$, $w_i \in V$, and $\varphi \in (\left( \otimes^c \\, V \right) \otimes \left( \otimes^d \\, V^* \right))^*$ is the covector of $T(v_1, \\, \ldots, \\, v_m, \\, w_1, \\, \ldots, \\, w_n)$ (which can be canonically determined by 3.26). Then, observe that

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

### Heterogeneous Tensors 

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

Just like $(9)$ relies on a canonical isomorphism $V \cong V^\*$ (see 3.26), $(11)$ requires an equivalent restriction for the vector spaces involved.

{{% /hint %}}

It is still possible to consider tensor type in the heterogeneous case -- one simply has to keep track of one type tuple per $[V] \in (\mathcal{V}/=)$ (see 3.31). This embodies a perspective where heterogeneous tensors are informally "superpositions" of homogeneous tensors (via the tensor product). Observing this, we can write the type of a heterogeneous tensor $T$ as

$$
\langle (m_V, \, n_V) \rangle_{[V] \, \in \, (\mathcal{V} /= )} \;\; \text{s.t.} \;\; \text{rank}(T) = \langle \, \text{rank}_V(T) \, \rangle_{[V] \, \in \, (\mathcal{V} /= )} \;\; \big( \, \text{rank}_V(T) = m_V + n_V \, \big).
$$

With heterogeneous tensors, one must also carry a mapping of type index to corresponding vector space. Without this, we would not know which vector space each type tuple $(m_i, \\, n_i)$ corresponds to. So when it is not clear to use the above syntax, we just write the type as $(A, \\, B)$ as implied from $(10)$.

{{< hcenter >}}
{{< figure src="vera-molnar-untitled-1974.png" width="512" caption="Vera Molnár, Untitled (1974)" >}}
{{< /hcenter >}}

### Tensor Contractions 

The statements of $(9)$ and $(11)$ may initially seem like a cryptic justification of our choice of vocabulary; they justify why we use the word "tensor" so liberally, with the most general use being in reference to an element of a heterogeneous tensor product space (up to isomorphism).

But beyond justifying use of language, $(9)$ and $(11)$ also provide a clear perspective on computation with tensors. These isomorphisms specify an "exchange rate" between inputs and outputs of homogeneus and heterogeneous tensors -- using 3.26, one can algebraically "trade" a tensor input in $V$ for a tensor product evaluation with a canonical element of $V^\*$ in the output as many times as desired while maintaining type.

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

In the example of 3.34, the map $\otimes : (f, \\, g) \mapsto \tilde f \circ \tilde g$ receives the special name of tensor outer product. It exists for any two tensors, just as a tensor product (which it is a special case of) exists for any two tensor product spaces. Taking the outer product of two tensors of type $(a, b)$ and $(c, d)$ results in one more of type $(a + c, \\, b + d)$.

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

Remember that $(9)$ and $(11)$ rely on the assumption that $V \cong V^\*$ exists and is canonical. This allowed us to obtain tensor forms of "redistributed" type by "trading,"

$$
(a, b) \xrightarrow{c \text{ trades}} (a + c, b - c).
$$

Recall 3.26, and that the canonicity of $V \cong V^\*$ can be "manually" induced by providing a canonical bijection to underlie it. Many applications specify tensors which enable "trades" via contractions with them for this purpose. This is often done by replacing the standard inner product in a set with another bilinear map (see 3.40).

{{% /hint %}}

### Heterogeneous Contractions

We have mostly ignored heterogeneous tensor spaces. Now, the framework of contractions offers a great opportunity to pull them back onto our train of thought. We have implied two stages for finding the type of a tensor contraction. First, consider the tensor product of all the spaces involved. Then, repeatedly utilize the canonical map $\text{ev}_V$, once per pair of dual spaces involved in the contraction.  

{{% hint title="3.37. Note" %}}

For a homogeneous tensor network involving tensors $T_1, \\, \ldots, \\, T_n$ with $T_i$ of shape $(a_i, b_i)$, the tensor product of all the tensors in the network is

$$
\otimes_i \, T_i \in \otimes_i \, ((\otimes^{a_i} V) \otimes (\otimes^{b_i} V^*)) \;\; \text{s.t.} \;\; T_i \in T_{b_i}^{\, a_i}(V)
$$

thanks to $(9)$. Applying 3.17 to reorganize, we see that $\otimes_i \\, T_i$ is of type $(\Sigma_i a_i , \\, \Sigma_i b_i)$. We then continue to use 3.17 and compose $\text{ev}_V$ with $\text{id}_V$ to construct mappings that perform arbitrary contractions just as done in 3.35, where together with "trades," we may achieve a contraction with any type

$$
(a, b) \;\; \text{s.t.} \;\; a + b = \text{rank(T)} - 2k, \;\; 0 \leq k \leq \lfloor \text{rank}(T) \, / \, 2 \rfloor.
$$

{{% /hint %}}

Here, we notice that we can use specialized maps $\text{ev}_X$ for appearances of each different vector space $X$ in a heterogeneous tensor contraction. While I will not formulate a heterogeneous equivalent of 3.37 (as it would be exceedingly verbose), we can see that for a tensor of type 

$$
(\{A_{(1)}^*, \, \ldots, \, B_{(1)}^*, \ldots \}, \{A_{(1)}, \, \ldots, \, B_{(1)}, \ldots \}),
$$

a contraction is determined by a collection of pairs $\\{ (X_{(i)}^*, \\, X_{(j)}), \\, \ldots \\}$ where maps $\text{ev}_X$ are to be used in the manner of 3.35. It requires a significant amount of bookeeping, but its soundness is visible through an argument completely analogous to 3.31. Likewise, we can apply "trading" as in 3.33 for "like terms" in their domain and codomain (pairs of vector spaces dual to each other among their factors).

{{% hint title="3.38. Note" %}}

One subtlety of the heterogeneous tensor contraction is that different vector spaces in a heterogeneous tensor network may have different canonical bijections through which "trades" are done, making bookeeping harder. 

{{% /hint %}}

### Graphical Notation 

An appealing model of tensor operations was offered by [Sir Roger Penrose](https://en.wikipedia.org/wiki/Roger_Penrose) in 1971 within the illustrated writeup [Applications of Negative-Dimensional Tensors](https://www.mscs.dal.ca/%7Eselinger/papers/graphical-bib/public/Penrose-applications-of-negative-dimensional-tensors.pdf). There, he provided a first theory of abstract tensor networks which he called Abstract Tensor Systems (ATS), which came with a coordinate-free system for representing homogeneous tensors and contractions. This system became known as [Penrose graphical notation](https://en.wikipedia.org/wiki/Penrose_graphical_notation). It provides its users with a feeling of intuitive dominance on tensor algebra.

{{< hcenter >}}
{{< figure src="roger-penrose.png" width="256" caption="Sir Roger Penrose (born August 8, 1931)" >}}
{{< /hcenter >}}

{{% hint title="3.39. Note" %}}

Many recent online sources give loose overviews of graphical notataion, for example [here](https://tensornetwork.org/diagrams/) and [here](https://www.tensors.net/intro). However, they miss out on subtle details that this section has set the stage for. The [original monograph](https://www.mscs.dal.ca/%7Eselinger/papers/graphical-bib/public/Penrose-applications-of-negative-dimensional-tensors.pdf) does include the aforementioned details, so I encourage you to read it after 3.42 (a notational prerequisite). Note the connection between what Penrose calls a "Cartesian ATS" and the availability of a canonical $V \cong V^\*$ in 3.36, showing the way in which "trades" as we understand them are done in graphical notation.

{{% /hint %}}

By ignoring any details needed to determine tensor instances, Penrose notation enjoys better ergonomics for working with abstract tensors. But before Penrose (and the study of abstract tensors spaces itself), the early appearances of these objects first appeared in service of fields like [differential geometry](https://en.wikipedia.org/wiki/Differential_geometry), only being baptized as "tensors" at a later time by physicists.

{{% hint title="3.40. Example" %}}

Recall 3.36, which remarked that some applications would introduce a special tensor as a canonical bijection in the stead of a canonical link $V \cong V^\*$ to help manage "trades." An early example is the [metric tensor](https://en.wikipedia.org/wiki/Metric_tensor) defined in the field of differential (Riemannian) geometry. Consider a sphere embedded in $\mathbb{R}^3$, which is the set 

$$
\mathcal{E}(S^2) = \left\{ p \in \mathbb{R}^3 : \|p\|_2 = r \right\}.
$$

Here, $r$ is the radius of the sphere and $\\| \cdot \\|\_2$ is the $\ell^2$-norm. Above, $S^2$ is a [manifold](https://en.wikipedia.org/wiki/Manifold), and not a vector space -- this is a completely different mathematical environment. It would be difficult to attempt a complete explanation of manifolds now, so I will summarize. A manifold $\mathcal{M}$ is in aggregate

* an underlying set $M$ with a topology $\tau$ (so it is also a [topological space](https://en.wikipedia.org/wiki/Topological_space)),
* an index $\mathcal{A} = \langle \\, \varphi_\alpha : U_\alpha \to \mathbb{R}^n \\, \rangle_{\alpha \\, \in \\, A}$ of [homeomorphisms](https://en.wikipedia.org/wiki/Homeomorphism) with $\cup_\alpha \\, U_\alpha = M$,
* and compatibility and smoothness guarantees ensuring coherence.

Above, $\mathcal{A}$ is called an atlas. This name is very accurate -- just as a [real-world atlas](https://en.wikipedia.org/wiki/Atlas) is a collection of charts that represent patches of big places in a flat page until the entire place is mapped, $\mathcal{A}$ also contains [charts](https://en.wikipedia.org/wiki/Atlas_(topology)) $\varphi_\alpha$ that represent local patches $U_\alpha$ of $\Mu$ in a "flat" space $\mathbb{R}^n$. The key insight is that we can "traverse the entire world" of $M$ by moving between patches, allowing us to describe $M$'s local geometry in Euclidean terms, with global consistency ensured by the compatibility of the charts.

If we can "move between" the patches of $M$ smoothly using atlas charts, we call $\mathcal{M}$ a [differentiable manifold](https://en.wikipedia.org/wiki/Differentiable_manifold). This lets us do calculus on $M$, describing characteristics (like curvature) that would be otherwise inaccessible. Usually, manifolds are defined in an ambient space which they are a subset of. For example, $S^2 \subset \mathbb{R^3}$. But an [intrinsic](https://en.wikipedia.org/wiki/Manifold#:~:text=Intrinsic%20and%20extrinsic%20view%5Bedit%5D) treatment of manifolds is also possible, such that $S^2$ exists independently of $\mathbb{R}^3$. Here,

$$
\mathcal{E} : S^2 \hookrightarrow \mathbb{R}^3 \;\; \text{s.t.} \;\; \mathcal{E}(p) = [x, \, y, \, z]^\top
$$

is an [embedding](https://en.wikipedia.org/wiki/Embedding) mapping a point $p$ in $S^2$ to another point in $\mathbb{R}^3$, decoupling the concepts. This comes with the added benefit of being able to identify points in $S^2$ with more ergonomic "coordinates." In particular, we can do a conversion to polar coordinates $P = \\{ x \in [0, \pi] \times [0, 2 \pi ) \\}$ where, even though there is no smooth global map $S^2 \to P$ (you cannot continuously deform a sphere into a plane), each chart we define over $P$ is smooth. This is the benefit of our "patchwork" strategy, so we define all $\varphi_\alpha(p)$ as restrictions $\mathcal{E} |\_{U_\alpha}(p)$ of

$$
\mathcal{E} : S^2 \hookrightarrow \mathbb{R}^3 \;\; \text{s.t.} \;\; \mathcal{E}(p) = \Phi(\Psi(p)) = \Phi(\theta, \, \phi) = r
\begin{bmatrix}
\sin\theta \cos\phi \\
\sin\theta \sin\phi \\
\cos\theta
\end{bmatrix}
$$

where $\Psi : S^2 \to P$ is a bijection between $S^2$ and the set of polar coordinates $P \in \mathbb{R}^2$. Now, we can analyze how distances in $P$ (which have an easy relationship to distances in $S^2$ by design) translate to distances in the ambient space $\mathbb{R}^3$. In doing so, we will "pull back" the Euclidean metric of $\mathbb{R}^3$ to get another that describes $S^2$, while our charts work entirely in the background as theoretical aid (while we use $\mathcal{E} = \Phi \circ \Psi$). We call


$$
\mathcal{J} \mathcal{\Phi} : T_{(\theta, \, \phi)}P \to T_{\Phi(\theta, \, \phi)}\mathbb{R}^3 \;\; \text{s.t.} \;\; 
\mathcal{J} \mathcal{\Phi} = r \begin{bmatrix}
\cos\theta \cos\phi & -\sin\theta \sin\phi \\
\cos\theta \sin\phi & \sin\theta \cos\phi \\
-\sin\theta & 0
\end{bmatrix}
$$

(which is just the Jacobian of $\Phi$) the [pushforward](https://en.wikipedia.org/wiki/Pushforward_(differential)) differential (where we denote the vector space tangent to $p$ in the manifold $M$ with $T_p M$). This map tells us how a tiny differential change from one point in $P$ is translated to another tiny differential change around its image in $\mathbb{R}^3$ under our embedding of $S^2$, which appears linear. Note that $\mathcal{J} \mathcal{\Phi}$ gives a rank $2$ matrix, since you can only "move" in two directions on the surface $S^2$. Finally, 

$$
\langle \mathcal{J} \mathcal{\Phi}(v), \, \mathcal{J} \mathcal{\Phi}(w) \rangle_{\mathbb{R}^3} 
= v^\top (\mathcal{J} \mathcal{\Phi})^\top (\mathcal{J} \mathcal{\Phi}) \, w
= \begin{bmatrix}
v_\phi & v_\theta
\end{bmatrix}
\begin{bmatrix}
r^2 & 0 \\
0 & r^2 \sin^2 \theta
\end{bmatrix}
\begin{bmatrix}
w_\phi \\
w_\theta
\end{bmatrix}
$$

provides a tensor $g_c : (v, w) \mapsto \langle \mathcal{J} \mathcal{\Phi}(v), \\, \mathcal{J} \mathcal{\Phi}(w) \rangle_{\mathbb{R}^3}$ which, for two points differential to a point $c = (\phi, \\, \theta)$ in $P$ (and hence in $S^2$ via $\Psi$), determines their geometric relationship (as it is an inner product). This defines a [tensor field](https://en.wikipedia.org/wiki/Tensor_field) $G$ that assigns one such tensor $g_c = G(c)$ of type $(0, 2)$ per $T_c P$ which fixes the geometry of $\mathcal{M}$ independently of the ambient space $\mathbb{R}^3$. Here, $g_c$ is the (pointwise) [pullback](https://en.wikipedia.org/wiki/Pullback_(differential_geometry)) of the $\mathbb{R}^3$ metric (given by $\langle \cdot, \cdot \rangle_{\mathbb{R}^3}$) to $S^2$ and we say that $\mathbb{R}^3$ "induced" a metric on $S^2$. 

{{% /hint %}}

In 3.40, the spaces $T_p M$ have no abstract canonical isomorphism to their own dual, as the inner product (which is precisely the tensor $g_p$ as described) depends on the geometry of $\mathcal{M}$. This is an example of the statement of 3.36, showing that a purely abstract treatment of tensor spaces is not always productive. But before people approached tensors abstractly, coordinate-based approaches were the norm. 

{{< hcenter >}}
{{< figure src="mark-wilson-1e90-1990.png" width="512" caption="Mark Wilson, \'1e90\' (1990)" >}}
{{< /hcenter >}}

### Einstein Notation

The dominant model for tensor operations in coordinates is indisputably [Einstein notation](https://en.wikipedia.org/wiki/Einstein_notation). Coincidentally, Penrose introduced ATS in Einstein notation. It is a data-oriented system where indices corresponding to each argument in $V$ and $V^\*$ of a tensor in scalar-valued map form (see $(8)$ and $(10)$) are tracked. Credit for its creation is given to differential geometer [Ricci-Curbastro](https://en.wikipedia.org/wiki/Gregorio_Ricci-Curbastro) (as [Ricci Calculus](https://en.wikipedia.org/wiki/Ricci_calculus)), but it was hugely popularized by [Einstein](https://en.wikipedia.org/wiki/Albert_Einstein) and its novel use in physics with his [field equations](https://en.wikipedia.org/wiki/Einstein_field_equations) in 1915.

{{% hint title="3.41. Note" %}}

Einstein notation is very well-motivated. It will be easier to see how we could come up with it ourselves. Let us consider a tensor $m$ of type $(1, 1)$ in multilinear map form by attempting to represent it as a matrix,

$$
m : V \to V \;\; \text{s.t.} \;\; M_\mathcal{B}(m) = 
\begin{bmatrix}
 m_{1,1} & m_{1,2} & m_{1,3} \\
 m_{2,1} & m_{2,2} & m_{2,3} \\
 m_{3,1} & m_{3,2} & m_{3,3}
\end{bmatrix}.
$$

Here, $M_\mathcal{B}(m)$ is simply the matrix representation of $m$ under the basis $\mathcal{B}$. Through 3.19, we observe that we can do this precisely because there is a function from entry indices to values which makes matrix multiplication work precisely as we expect it to, which is

$$
\text{entry}_m(i, j) = m_{i,j}.
$$

From a computational perspective, this is a unique proxy for $m$ when considering maps in $\mathcal{L}(V, W)$ (via 3.9). For example, the [Frobenius norm](https://en.wikipedia.org/wiki/Matrix_norm#Frobenius_norm) $\\| m \\|\_F^2 = \sum_{(i, j)} |m_{i,j}|^2$ makes use of this notation. While this made sense for a linear map of form $V \to W$, issues arise for tensors of other forms. Consider $g_c$ from 3.40, 

$$
g_{(\theta, \phi)}(v, w) = 
\begin{bmatrix}
v_\phi & v_\theta
\end{bmatrix}
\begin{bmatrix}
r^2 & 0 \\
0 & r^2 \sin^2 \theta
\end{bmatrix}
\begin{bmatrix}
w_\phi \\
w_\theta
\end{bmatrix}
\;\; \text{s.t.} \;\;
M_\mathcal{B}(g_{({\theta}, {\phi})}) = 
\begin{bmatrix}
r^2 & 0 \\
0 & r^2 \sin^2 \theta
\end{bmatrix}.
$$

If given only the matrix $M_\mathcal{B}(g_{({\theta}, {\phi})})$, there would be no way of knowing it represents the map of (inner-product) form $g_{(\theta, \phi)} : P \times P \to \mathbb{R}$ (and not an operator in $\mathcal{L}(V, V)$) precisely because it is canonical to interpret it as a map in $\mathcal{L}(V, V)$. This interpretation is only canonical because matrix-vector multiplication is defined with 

$$
(Av)_i = \sum_{j} A_{ij} v_j.
$$

Hence, one must revisit matrix multiplication to have a basis-induced canonical isomorphism between tensors and matrices (see 3.19). To this end, we will adopt the tensor contraction in coordinates as matrix multiplication for tensors. A rank-$n$ tensor will be represented as a matrix with $n$ axes (such that $n$ indices identify an entry), such that for a tensor $t$ of type $(a, b)$, 

$$
{\large t_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_a}}
$$

denotes an entry of its matrix representation. Taking the tensor product of $t$ with a new tensor $g$ of type $(c, d)$ is very simple, as each entry of the product (by the same logic) requires $a + c$ upper and $b + d$ lower indices to be found. So we simply denote their tensor product

$$
t \otimes g = 
{\large t_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_a} \, g_{\delta_1 \, \cdots \, \delta_d}^{\gamma_1 \, \cdots \, \gamma_c}}.
$$

To perform contractions as specified in 3.35, one does a weighted sum across a pair of indices, one upper and one lower (which may belong to different tensors in a tensor product). For example, one could contract $\alpha_a$ with the index $\delta_1$ in the product $t \otimes g$ to obtain

$$
{\large t_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_{a - 1}} \, 
g_{\delta_2 \, \cdots \, \delta_d}^{\gamma_1 \, \cdots \, \gamma_c}} =
\sum_k \,
{\large t_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_{a - 1} \, k} \,
g_{k \, \delta_2 \, \cdots \, \delta_d}^{\gamma_1 \, \cdots \, \gamma_c}}.
$$

Note that in the homogeneous case, one can always do this for any upper-lower index pair. The heterogeneous case just requires knowing which upper indices can be contracted with which lower indices (which can be done by tracking which index pairs correspond to duals of the same vector space). Finally, tensors of equal type may be summed entrywise,

$$
{\large t_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_{a}}} +
{\large q_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_{a}}} = 
{\large h_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_{a}}},
$$

with the understanding that the tensor product is distributive over tensor sums. This completes the system that Ricci-Curbastro outlined, allowing an axiomatic treatment for tensors in coordinates. The convention to eschew summation notation in contractions (only implying it when a symbol appears twice as an index) was introduced and popularized by Einstein (effectively his only contribution to [Ricci calculus](https://en.wikipedia.org/wiki/Ricci_calculus)):

$$
{\large t_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_{a - 1} \, k} \,
g_{k \, \delta_2 \, \cdots \, \delta_d}^{\gamma_1 \, \cdots \, \gamma_c}} =
\sum_k \,
{\large t_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_{a - 1} \, k} \,
g_{k \, \delta_2 \, \cdots \, \delta_d}^{\gamma_1 \, \cdots \, \gamma_c}}.
$$

Note that at any point we can find an entry in the matrix representation by replacing all indices with values. This means that Einstein notation is simply element-wise treatment of tensor matrices, with careful consideration of contraction compatibility by use of upper and lower indices. Here, "trades" translate to index raising or lowering, where (depending on context) one may be allowed to

$$
{\large t_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_a}} \xrightarrow{\text{raise } \beta_b}
{\large t_{\beta_1 \, \cdots \, \beta_{b - 1}}^{\alpha_1 \, \cdots \, \alpha_a \, \beta_b}} \xrightarrow{\text{lower } \beta_b}
{\large t_{\beta_1 \, \cdots \, \beta_b}^{\alpha_1 \, \cdots \, \alpha_a}}.
$$

If allowed to raise or lower indices (which is context-dependent as seen in 3.36 and 3.40), a contraction can be done on two lower or upper indices. So summation (and legality of index lowering or raising) remains implied in cases where $V \cong V^\*$ canonically (which Penrose called "cartesian" in ATS). For example,

$$
{\large t_{\beta_1 \, \cdots \, \beta_b \, k}^{\alpha_1 \, \cdots \, \alpha_{a}} \,
g_{\delta_1 \, \cdots \, \delta_d \, k}^{\gamma_1 \, \cdots \, \gamma_c }} =
\sum_k \,
{\large t_{\beta_1 \, \cdots \, \beta_b \, k}^{\alpha_1 \, \cdots \, \alpha_{a}} \,
g_{\delta_1 \, \cdots \, \delta_d \, k}^{\gamma_1 \, \cdots \, \gamma_c }}.
$$

{{% /hint %}}

Notice that the "reduction" done by way of summation in a tensor contraction can use any operation with the effect of [folding](https://en.wikipedia.org/wiki/Fold_(higher-order_function)) a collection (returning a single value from multiple values of the same type), although this may break any and all algebraic invariants (depending on context). This could look like

$$
w_{abc}^{ik} = \text{reduce}_j \left( v_{a b c}^{i j k} \right).
$$

Since the user is giving up all algebraic gurantees they may annihilate any index. In the worst case this essentially treats the tensor matrix as pure data, but many choices of $\text{reduce}$ do preserve linearity. This shows that Einstein notation can be useful beyond representing linear maps when working with "pure data," but remains generalizable to linear maps over infinite-dimensional tensor spaces. Inspired by $(5)$, we could decompose $f(x) \in L^2(\mathbb{R})$ with

$$
f(x) = f^x = 
c^{\omega} \,
b_{\omega}^{x} =
\int_{\Omega} \,
c^{\omega} \,
b_{\omega}^{x} \, d\mu(\omega).
$$

{{% hint title="3.42. Example" %}}

The cornerstone of the [transformer architecture](https://en.wikipedia.org/wiki/Transformer_(deep_learning_architecture)) is generally regarded to be the [attention mechanism](https://en.wikipedia.org/wiki/Attention_(machine_learning)). This is no more than a tensor-valued function $\mathbf{A} : \mathbb{R}^{d \times n} \to \mathbb{R}^{d_v \times n}$ with tunable parameters, generally defined as

$$
\mathbf{A}(X) = \text{softmax}\left(\frac{(KX)^\top (QX)}{\sqrt{d_k}}\right) VX.
$$

Here, $X \in \mathbb{R}^{d \times n}$, and the matrices $Q \in \mathbb{R}^{d_k \times d}$ (query projection matrix), $K \in \mathbb{R}^{d_k \times d}$ (key projection matrix), and $V \in \mathbb{R}^{d_v \times d}$ (value projection matrix) are optimized. You rarely see it in Einstein notation as an equation of heterogeneous tensors, which helps when $X$ has many channels to mix (indexed by $c$),

$$
\mathbf{A}(X)_{t}^{ic} = \text{softmax} \left(\frac{g_{jm} K_p^j X_s^{pc} Q_q^m X_t^{qc}}{\sqrt{d_k}}\right) V_k^i X_s^{kc}.
$$

I replaced the row-wise dot product application $(KX)^\top (QX)$ with an explicit metric tensor $g_{jm}$, which brings a subtle geometric interpretation to the foreground just by way of notation. Observing this could have motivated us to add another metric tensor $m_{c_1 c_2}$ to capture channel-wise relationships while mixing,

$$
\mathbf{A}(X)_{t}^{ic} = \text{softmax} \left(\frac{g_{jm} m_{c_1 c_2} K_p^j X_s^{pc_1} Q_q^m X_t^{qc_2}}{\sqrt{d_k}}\right) V_k^i X_s^{kc}.
$$

With the [`einsum` NumPy API](https://numpy.org/doc/stable/reference/generated/numpy.einsum.html), we can write the above in Python still using Einstein notation. For simplicity, only a single-channel $X$ will be considered. Below, the strategy for determining the tensor `g` is left unspecified.

{{< highlight python "lineNos=true, lineNoStart=1" >}}

# einsum ignores whether indices are upper/lower 
# i.e. assumes raising/lowering is generally OK

KX = np.einsum('jk,ks->js', K, X) # keys
QX = np.einsum('ml,lt->mt', Q, X) # queries

# attention scores (similarity under g)
sim = np.einsum('jm,js,mt->st', g, KX, QX) 

scores = sim / np.sqrt(Q.shape[0])
weights = np.exp(scores - np.max(scores, axis=0, keepdims=True))
weights = weights / np.sum(weights, axis=0, keepdims=True)

VX = np.einsum('ij,jt->it', V, X) # values
result = np.einsum('is,st->it', VX, weights)
{{< /highlight >}}

{{% /hint %}}

### Overview

We began this section by organizing the concepts of matrices, linear maps, and vectors. We then took a look at linear forms and dual spaces, being careful of infinite-dimensional cases. Later, we expanded the concept of linearity to multilinear maps, and showed how the tensor product "linearizes" them. That way, we saw how the way we organized matrices, vectors, and linear maps early on applied to tensor product spaces in very natural ways. Finally, tensors allowed us to speak about these objects as unified under a single umbrella woven by tight isomorphisms.

Although this trajectory was predominantly abstract, we were also exposed to practical notation and uses of tensors in coordinates, with examples from the early history of tensors in differential geometry and from modern applications in deep learning. These examples showed that tensors can be found at many points of the spectrum between raw data and abstract algebraic objects, and that their theory is robust to a wide range of uses -- some more disrespectful than others, but never enough to erase their basic qualities.

{{< hcenter >}}
{{< figure src="mark-wilson-e67109-2012.png" width="512" caption="Mark Wilson, \'e67109\' (2012)" >}}
{{< /hcenter >}}

## Signals and Systems 



## Kernel Methods

[^kernel-semantics]: These two "kernels" received their names for a superficial reason -- because the symbols that represent them show up inside other symbols. One could imagine that people started calling them "kernel" independently just to avoid saying the phrase "that term in in the middle" while pointing at a blackboard. As such, the connecting view of the convolution and reproducing kernels in this article does not "generalize" to kernels in other contexts (many of which received their names the same non-profound reason). 


[^axiom-choice]: When considering infinite-dimensional vector spaces, this statement is true if and only if one admits the axiom of choice. Perhaps this was another motivation of Axler's restriction to finite-dimensional vector spaces.

[^sub-indices]: Non-parenthesized sub-indices imply that the item is part of an indexed set -- $a_i$ may not be equal to $a_j$.

[^parenthesized-indices]: Parenthesized sub-indices are only used to indicate argument index. That is, $a_{(i)} = a_{(j)}$ for all $i$ and $j$.


