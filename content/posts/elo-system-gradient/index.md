---
title: "The Elo Rating System through Likelihood Gradient Ascent"
category: technical
date: 2025-04-30
math: true
---

{{< toc >}}

## Abstract

Probability and optimization are strong monsters. The [Elo rating system](https://en.wikipedia.org/wiki/Elo_rating_system), used to estimate performance in [competitive chess](https://en.wikipedia.org/wiki/FIDE_rankings), [online dating](https://www.vox.com/2019/2/7/18210998/tinder-algorithm-swiping-tips-dating-app-science), and [AI agents](https://lmsys.org/blog/2023-05-03-arena/), is an under-the-hood reminder of this fact that operates within many of the systems that need to establish comparative metrics. This piece is my contribution to the endless pile of explainers on the topic. I exercise an emphasis on bayesian statistics and optimization that should ring a bell for anyone familiar with the basics of machine learning.

---

## Background

### Mathematical Orderings

At the risk of including a needless dependency on the topic of this piece, I introduce you to the idea of an ordering. 
Colloquially, we take this to mean an arrangement (i.e., [a permutation](https://en.wikipedia.org/wiki/Permutation)) of a set of things.
We will replace that with its formal meaning, which is a specific kind of [binary relation](https://en.wikipedia.org/wiki/Binary_relation).


{{% hint title="Definition" %}}

A **binary relation** $R$ from a set $X$ to another $Y$ is a subset of $X \times Y$, where it is possible that $X = Y$.

{{% /hint %}}

This should seem odd, as a subset is in no obvious way reminiscent of a permutation. But introducing some new syntax to indicate membership in a relation,

$$
  (x, y) \in R \vdash xRy,
$$

we are an example away from making sense. In particular, consider $R = \\, \leq$ (less-than). When we say things like "$x \leq y$," we are in fact using syntactic sugar for "$(x, y) \in \\, \leq$." With this in mind, we can take a look at [partial orders](https://en.wikipedia.org/wiki/Partially_ordered_set).

{{% hint title="Definition" %}}

A **partial order** $R$ is a binary relation over a set $X$ and itself which satisfies the following:

1. Reflexivity. This means that $\forall x \in X, \\, xRx$.
2. Antisymmetry. This means that $\forall (x, y) \in X^2, \\, xRy \wedge yRx \implies x = y$.
3. Transitivity. This means that $\forall (x, y, z) \in X^3, \\, xRy \wedge yRz \implies xRz$.

{{% /hint %}}

The canonical example of a partial order is $\subseteq$ over $\mathcal P(S)$. Importantly, a partial order over a set does not imply a permutation over it, because of the possibility for two elements $x$ and $y$ to be _unrelated_, or in other words, for $\neg xRy$ and $\neg yRx$. In a [total order](https://en.wikipedia.org/wiki/Total_order), we simply do not allow this.

{{% hint title="Definition" %}}

A **total order** $R$ is a partial order that is also total, which means that $\forall (x, y) \in X^2, \\, xRy \vee yRx$.

{{% /hint %}}   

The canonical example of a total order is $\leq$ over $\mathbb{R}$. With a total order, there is a single valid ordering $\bold{x}$ (i.e. arrangement or permutation) over its set $X$ such that $x_iRx_{i + 1}$ for all $i = 0, \ldots, |X| - 1$. One more variation we can make on the idea of an order is that of a [weak order](https://en.wikipedia.org/wiki/Weak_ordering).

{{% hint title="Definition" %}}

A **weak order** $R$ is a total order that is not necessarily antisymmetric. 

{{% /hint %}}   

In other words, it is possible that for distinct elements $x$ and $y$, both $xRy$ and $yRx$. This is conceptually aligned with allowing "ties" in any resulting ordering, potentially sacrificing their uniqueness.

### Elo Ratings and Updates

We can take a look at the question that [Arpad Elo](https://en.wikipedia.org/wiki/Arpad_Elo) (kind of) answered: How can you compare the skill level of two chess players? 

{{< hcenter >}}
{{< figure src="arpad_elo.jpg" width="256" caption="Arpad Elo (August 25, 1903 – November 5, 1992)" >}}
{{< /hcenter >}}

His proposed procedure is straightforward. Each player $i \in N$ will have a real-valued rating $r_i$, which will be a proxy for their skill level. These ratings will be initialized at some predetermined value for all players. Then, when there is a match between player $i$ and $j$, the following updates are made:

$$
\begin{align*}
&r_j \gets r_j + k(s_j - e_j), \\
&r_i \gets r_i + k(s_i - e_i),
\end{align*}
$$

where[^expected-score],

$$
e_p = \frac{1}{1 + e^{-(r_p - r_{\text{other}})}}, \;\;\;\;
s_p = 
\begin{cases}
1 &\text{if \(p\) wins},\\
0.5 &\text{if draw},\\
0 &\text{if \(p\) loses},\\
\end{cases}
$$

and $k$ is a constant chosen arbitrarily. So, as players accrue matches with other players, their ratings are updated according to the above rules with the hope that they will eventually stabilize. Now, the difference between players' ratings can be used to compare their skill levels via the ordering $\leq$ on $\mathbb{R}$. 


### Maximum Likelihood Estimation 

[Maximum likelihood estimation (MLE)](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) is a method used to fit distribution parameters to samples. The setup for MLE is a random variable $Y$ of known distribution $\mathcal{D_\theta}$ (parameterized by $\theta$), with access to [IID](https://en.wikipedia.org/wiki/Independent_and_identically_distributed_random_variables) samples $\langle y_i \rangle \sim \mathcal{D}_\theta$. The objective is to estimate $\hat\theta$ such that the [likelihood function](https://en.wikipedia.org/wiki/Likelihood_function) $\mathcal{L}$ is maximized,

$$
\begin{equation}
    \hat\theta_\mathrm{MLE} 
    = \argmax_\theta \, \mathcal{L}(\theta; \langle y_i \rangle) 
    = \argmax_\theta \, \prod_i \mathbb{P}_\theta[Y = y_i].
\end{equation}
$$

In other words, MLE is the optimization procedure associated with finding the distribution parameters that were most likely to generate observed data, provided that we know or assume its distribution.

#### MAP Estimation

When there is access to a (known or assumed) prior $p(\theta)$ on the distribution of parameters, we can fold it into our optimization process by doing MLE on the posterior distribution, which by [Bayes' theorem](https://en.wikipedia.org/wiki/Bayes%27_theorem),

$$
\begin{align*}
    p(\theta \mid \langle y_i\rangle) 
    &= \frac{p(\langle y_i \rangle \mid \theta) p(\theta)}{p(\langle y_i \rangle)} 
    \; \propto \; \underbrace{p(\langle y_i \rangle \mid \theta)}_{\displaystyle{\mathcal{L}(\theta; \langle y_i \rangle)}} p(\theta).
\end{align*}
$$

The resulting parameters $\hat\theta_\mathrm{MAP}$ are then a [maximum a posteriori (MAP)](https://en.wikipedia.org/wiki/Maximum_a_posteriori_estimation) estimate,

$$
\begin{align*}
    \hat\theta_{\mathrm{MAP}}
    &= \argmax_\theta\;p\bigl(\theta \mid \langle y_i\rangle\bigr) \\
    &= \argmax_\theta\;\Bigl[\mathcal{L}\bigl(\theta;\langle y_i\rangle\bigr)\,p(\theta)\Bigr] \\
    &= \argmax_\theta\;\Bigl[\prod_{i=1}^n \mathbb{P}_\theta\bigl[Y=y_i\bigr] \times p(\theta)\Bigr].
\end{align*}
$$

#### Optimization

Sometimes, it is possible to find closed-form solutions for $\hat\theta_\mathrm{MAP}$ and $\hat\theta_\mathrm{MLE}$ through convex optimization. For example, samples with gaussian noise lead to the closed-form solution of the [OLS problem](https://en.wikipedia.org/wiki/Ordinary_least_squares) through the process of MLE. 

However, most of the time the resulting optimization objective of MLE (and hence also MAP estimation) is not convex. Here, gradient-based approaches (along with all other non-convex optimization techniques) are helpful for finding local maxima of the likelihood objective.

---

## Derivation

The derivation presented here will depart from the usual in hopes of contributing some kind of novelty. We begin with the game-theory-native idea of payoff, which we will take to be a numeric value representing a player's utility differential with respect to the start of a game $G$,

$$
    \text{payoff of player i} = p_i.
$$

Next, we will consider player performance. Just as Elo, we take the performance of a player $i$ on a game $G$ to be a real-valued random variable $X_i$, independent to other players.


{{% hint title="Observation" %}}

Perhaps Elo motivated this decision after noticing the variance of his own performance over the chess board. 

{{% /hint %}}

Then, we will expand our setup by allowing players to outperform others, which we will present through the difference between the performance of two players during $G$ (which is another RV),

$$
    \delta_{i, \, j} = X_i - X_j.
$$

We will also establish a relationship between $\delta_{i , \\, j}$ and $p_i$. For this purpose, we introduce a game-specific mapping $g$ with a noise term $\epsilon \sim \mathcal{N}(0, \sigma_\epsilon^2)$, which together form the generative process of payoffs:

$$
    p_i = g(\delta_{i, \, j}) + \epsilon.
$$

Finally, we will assert a prior on the distribution of $X_i$, which we will refer to as $\mathcal{D}(\theta_i)$ without yet deciding on a particular distribution (just that it is parameterized by $\theta_i$). This prior $\pi(x)$ will be global for all players, and its distribution parameters will be $\theta_\pi$. 

{{% hint title="Note" %}}

While this is a global prior, notice that none of the following breaks if it were player-specific from the start.

{{% /hint %}}

So far, none of this has helped us answer the question that Elo answered. For that, we will introduce one last artifact on top of our setup; each player $i$ will have a "rating" $r_i$, which we will ultimately use to order players by skill in our system or organization: 

$$
    r_i = \mathbb{E}[X_i].
$$

### MAP Estimation

Clearly, since our goal is to know players' ratings, the only additional information we will need to get them are the distribution parameters $\theta_i$. Of course, at a lack of observations, we can assert from our prior

$$
    r_i = \mathbb{E}_{X \, \sim \, \pi}[X].
$$

But what if at the end of a game $G$ between players $i$ and $j$, we observe [WLOG](https://en.wikipedia.org/wiki/Without_loss_of_generality) the payoff $p_i$? Here, we will be wishing that $g$ is neatly invertible. Assuming it is, we arrive at the following MLE for their difference in performance via application of $(1)$:

$$
\begin{equation}
    \hat\delta_{i, \, j}
    = \argmax_{\delta} \exp\!\Bigl(-\frac{(p_i-g(\delta))^2}{2\sigma_\epsilon^2}\Bigr)
    = g^{-1}(p_i).
\end{equation}
$$

{{% hint title="Note" %}}

Notice **we did not use a prior** when estimating $\hat\delta_{i, \, j}$. This assumption is due to Elo; we will not use players' history when calculating their performance for a single game. This is the design decision that, by omission, accounts for sudden changes in player skill (as a result of learning, etc.).

{{% /hint %}}

{{% hint title="Note" %}}

We just estimated the difference between the performance of the players from the payoff of a single player. The invertibility of $g$ has the hidden implication that **it is strictly monotonic**; no two differences in performance lead to the same payoff, and the greater the difference, the greater the payoff for the outperforming player.

{{% /hint %}}

Knowing this, we can perform a bayesian update to our prior through MAP estimation. Writing down the joint posterior of the parameters of $X_i$ and $X_j$,

$$
\begin{equation}
    p(\theta_i, \, \theta_j  \mid \hat\delta_{i, \, j}) 
    \propto 
    \underbrace{
        p(\hat\delta_{i, \, j} \mid \theta_i, \, \theta_j)
    }_{
        \displaystyle{p(\hat\delta_{i, \, j} \mid X_i - X_j)}
    }
    p(\theta_i)p(\theta_j).
\end{equation}
$$

Notice that we already have access to priors $p(\theta_i)$ and $p(\theta_j)$; those are quite simply $\pi(\theta_i)$ and $\pi(\theta_j)$, which we assume per our initial setup.

#### Gaussian Performance

We proceed by considering the case where $X_i \sim \mathcal{N}(r_i, \\, \sigma_i^2)$, such that $\theta_i = (r_i, \\, \sigma_i^2)$. That is, player performance is gaussian-distributed,

$$
\begin{equation}
    p_{X_i}(x_i \mid \theta_i)
    = \frac{1}{\sqrt{2\pi} \, \sigma_i}
      \exp\!\Bigl(-\frac{(x_i - r_i)^2}{2\,\sigma_i^2}\Bigr).
\end{equation}
$$

Our next goal is to set up an analytic function for the likelihood $p(\hat\delta_{i, \\, j} \mid \theta_i, \\, \theta_j)$. We observe that we have access to the conditional density of $\hat\delta_{i, \\, j}$  

$$
\begin{equation}
    p(\hat\delta_{i, \, j} \mid x_i, \, x_j)
    = \frac{1}{\sqrt{2\pi}\,\sigma_\varepsilon}
      \exp\!\Bigl(-\frac{\bigl(g(\hat\delta_{i, \, j}) - (x_i - x_j)\bigr)^2}{2\,\sigma_\varepsilon^2}\Bigr)
      \;|g'(\hat\delta_{i, \, j})|
\end{equation}
$$

by using $(2)$ implicitly through the change of variables

$$
    p(\hat\delta_{i, \, j} \mid x_i, \, x_j)
    = p_{p_i}\bigl(g(\hat\delta_{i, \, j}) \mid x_i, \, x_j \bigr)
    \;\Bigl|\frac{d}{d\hat\delta_{i, \, j}}\,g(\hat\delta_{i, \, j})\Bigr|,
$$

where we take

$$
    p\bigl(p_i \mid x_i, \, x_j \bigr)
    = \frac{1}{\sqrt{2\pi}\,\sigma_\varepsilon}
      \exp\!\Bigl(-\frac{\bigl(p_i - g(x_i - x_j)\bigr)^2}{2\,\sigma_\varepsilon^2}\Bigr).
$$

Now, we can use $(4)$ and $(5)$ to derive the desired likelihood by marginalizing,

$$
\begin{align*}
    p\bigl(\hat\delta_{i, \, j} \mid \theta_i,\theta_j\bigr)
    &= \iint
       p\bigl(\hat\delta_{i, \, j}\mid x_i,x_j\bigr)\;
       p_{X_i}(x_i \mid \theta_i)\;p_{X_j}(x_j \mid \theta_j)\,
       dx_i\,dx_j \\
    &= \iint
       \frac{1}{\sqrt{2\pi}\,\sigma_\varepsilon}
       \exp\!\Bigl(-\frac{\bigl(g(\hat\delta_{i, \, j}) - (x_i - x_j)\bigr)^2}
                         {2\,\sigma_\varepsilon^2}\Bigr)\,
       \bigl|g'(\hat\delta_{i, \, j})\bigr|
    \\[-2pt]
    &\quad\;\times\,
       \frac{1}{\sqrt{2\pi}\,\sigma_i}
       \exp\!\Bigl(-\frac{(x_i - r_i)^2}{2\,\sigma_i^2}\Bigr)
       \;\frac{1}{\sqrt{2\pi}\,\sigma_j}
       \exp\!\Bigl(-\frac{(x_j - r_j)^2}{2\,\sigma_j^2}\Bigr)
    \,dx_i\,dx_j.
    \\[6pt]
\end{align*}
$$

After another cup of coffee, we arrive at the following version of our joint likelihood $p\bigl(\hat\delta_{i, \, j} \mid \theta_i,\theta_j\bigr)$,

$$
\begin{aligned}
    &= \int
       \frac{1}{\sqrt{2\pi}\,\sigma_\varepsilon}
       \exp\!\Bigl(-\frac{\bigl(g(\hat\delta_{i, \, j}) - d\bigr)^2}{2\,\sigma_\varepsilon^2}\Bigr)
       \;|g'(\hat\delta_{i, \, j})|\; \\
    &\quad\;\quad\;\times \frac{1}{\sqrt{2\pi(\sigma_i^2+\sigma_j^2)}}
       \exp\!\Bigl(-\frac{(d - (r_i - r_j))^2}{2(\sigma_i^2+\sigma_j^2)}\Bigr)
    \,dd
\end{aligned}
$$

where $d = x_i - x_j$ (hinted at in equation $(3)$) is possible because $X_i - X_j \sim \mathcal{N}(r_i - r_j, \sigma_i^2 + \sigma_j^2)$. Finally, we obtain the following after remembering an [important fact](#gaussian-convolution) from signal processing,

$$
\begin{align}
    \mathcal{J}_\mathrm{MLE}(\theta_i, \, \theta_j; \, \hat\delta_{i, \, j})
    = \frac{|g'(\hat\delta_{i, \, j})|}{\sqrt{2\pi\,\bigl(\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2\bigr)}}\,
        \exp\!\Biggl(-\frac{\bigl(g(\hat\delta_{i, \, j}) - (r_i - r_j)\bigr)^2}
                             {2\,\bigl(\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2\bigr)}\Biggr).
\end{align}
$$

Wonderful. We then attend to the reflexes drilled into our brains from machine learning, and find the gradient of the log-likelihood with respect to learned... ahem, the ratings $\bold{r} = [r_i, \\, r_j]^\top$:

$$
\begin{equation}
    \nabla_\bold{r}\log\mathcal{J}_\mathrm{MLE}(\theta_i, \, \theta_j; \, \hat\delta_{i, \, j}) = 
    \begin{bmatrix}
        \displaystyle\frac{g(\hat\delta_{i, \, j}) - (r_i - r_j)}{\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2} \\[8pt]
        \\
        \displaystyle-\frac{g(\hat\delta_{i, \, j}) - (r_i - r_j)}{\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2}
    \end{bmatrix}.
\end{equation}
$$

{{% hint title="Note" %}}

By taking the $\log$ of the joint likelihood we achieve nothing, but we respect a very important tradition[^log-likelihood].

{{% /hint %}}

Using $\nabla_\bold{r}\log\mathcal{J}(\theta_i, \\, \theta_j; \\, \hat\delta_{i, \\, j})$ as it stands to adjust $\bold{r}$ would be tantamount to MLE on $\bold{r}$. To turn this into a proper MAP estimate we must also fold in our prior terms into $(6)$, which we assume to be gaussian:

$$
\begin{aligned}
    \mathcal{J}_\mathrm{MAP}(\theta_i, \, \theta_j; \, \hat\delta_{i, \, j})
    &= \frac{\lvert g'(\hat\delta_{i,j})\rvert}
           {\sqrt{2\pi\,\bigl(\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2\bigr)}}
      \exp\!\Bigl(-\frac{\bigl(g(\hat\delta_{i, \, j}) - (r_i - r_j)\bigr)^2}
                       {2\,\bigl(\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2\bigr)}\Bigr)\\
    &\quad\;\times\;
      \frac{1}{\sqrt{2\pi}\,\sigma_\pi}
      \exp\!\Bigl(-\frac{(r_i - r_\pi)^2}{2\,\sigma_\pi^2}\Bigr)
      \;\times\;
      \frac{1}{\sqrt{2\pi}\,\sigma_\pi}
      \exp\!\Bigl(-\frac{(r_j - r_\pi)^2}{2\,\sigma_\pi^2}\Bigr).
\end{aligned}
$$

Being again unable to ignore our instincts,

$$
\begin{equation}
    \nabla_{\mathbf r}\log \mathcal{J}_{\mathrm{MAP}}(\theta_i,\theta_j;\hat\delta_{i,j})
    = \begin{bmatrix}
    \displaystyle
    \frac{g(\hat\delta_{i,j}) - (r_i - r_j)}{\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2}
    \;-\;\frac{r_i - r_\pi}{\sigma_\pi^2}
    \\\\
    \displaystyle
    -\frac{g(\hat\delta_{i,j}) - (r_i - r_j)}{\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2}
    \;-\;\frac{r_j - r_\pi}{\sigma_\pi^2}
    \end{bmatrix}.
\end{equation}
$$

{{% hint title="Checkpoint" %}}

Let us take a step back for a second, and roughly see what is on the table. Intuitively, we are:

1. **Observing** a materialized payoff $p_i$.
2. **Inverting** $g$ to recover the latent skill gap $\hat\delta_{i, \\, j}$ that was most likely to produce $p_i$.
3. **Comparing** that inferred gap to our current belief of the skill gap $r_i - r_j$.
4. **Deriving** the change to $r_i$ and $r_j$ would bring our belief closer to $\hat\delta_{i, \\, j}$.

{{% /hint %}}

Then, the gradient-ascent update with step size $k$,

$$
\begin{equation}
    \bold{r}_{t + 1} 
    \gets 
    \bold{r}_{t} 
    + k\nabla_{\bold{r}}\log\mathcal{J}_\mathrm{MAP}(\theta_i, \, \theta_j; \, \hat\delta_{i, \, j}),
\end{equation}
$$

offers a complete recovery (and generalization) of the Elo update after an observed payoff $p_i$.

### Discussion 

#### Procedural Discrepancy 

Usually, implementations of Elo updates do not consider a prior. Instead, they simply initialize parameters at some default amount, then do MLE (as opposed to MAP estimation) to produce gradient updates. I decided to display the full MAP estimate because I think it is more principled; if you believe that ratings "start off" at some amount, that constitutes a bayesian prior in my eyes.

#### Distribution Discrepancy 

The Elo rating system assumes a logistic distribution on player performance, not gaussian. However, the above procedure will invariantly recover Elo updates as presented in the [background section](#elo-ratings-and-updates) with both distributions (at least in form). I thought it would be somewhat interesting to make it gaussian.

#### Fixed Parameters

In theory, one could estimate the variance parameters using the exact same procedure, by taking the gradient of the joint likelihood with respect to them in addition to the means (the ratings). Surprisingly, people do things similar to this -- although not in this particular way. See the [Glicko rating system](https://en.wikipedia.org/wiki/Glicko_rating_system).

#### Redundancy with $g$

You may have noticed that throughout our derivations (most notably in equations $(7)$ and $(8)$) there are $g(\hat\delta_{i, \\, j})$ terms that can be safely replaced with $p_i$ by definition, and can be therefore seen as redundant. This is a completely accurate observation.

I decided to make $g$ explicit to make the fundamental link between payoffs and performance differentials also explicit, which is something I consider to be a lot more principled. In fact, $g$ does not need to be strictly monotonic, as we never explicitly evaluate $g^{-1}(\small\bullet)$. However, not satisfying this property may result in a lack of parameter identifiability, which is easy to forget if you discard the symbol early on.

#### Weak Ordering

It is important to acknowledge that mapping player skill to $\mathbb{R}$ and then using $\leq$ to order players is a fundamentally misguided approach to how the world works. In doing so, we establish a weak ordering among players, but completely ignore that some players have qualities that make them strong against some players and weak against others (in a manner that is potentially cyclic).


{{% hint title="Example" %}}

To illustrate this, consider three players of rock-paper-scissors. One always plays rock, one always plays paper, and the other scissors. You will find that there is no way of assigning them a real number such that the player with the highest number beats both of the other players in expectation.

{{% /hint %}}

Still, sometimes we are forced to make rankings which make sense _in expectation_. In the real world, there is sufficient variance in player attributes that there are actors that can consistently beat some others. Here, systems such as Elo's bring real utility. But as a human, you should trust your intuition more than some potentially senseless number.

#### Outcome Prediciton

Further expanding on the inadequacy of ranking players via weak order, consider the very plausible machine learning task of outcome prediction, say, for the game of [Basketball](https://en.wikipedia.org/wiki/Basketball). 

It is tempting to, for example, train a network $f_\theta : \mathbb{R}^n \to \mathbb{R}$ on $n$-dimensional encodings of teams to predict a scalar value, where you then train in tandem over historic game outcomes $\langle ((a, b), \\, y)\_i \rangle_{i \in D}$. 

Here, teams $a, \\, b \in \mathbb{R}^n$ played each other and achieved outcome $y \in \\{-1, 1\\}$ for each match $i \in D$. One could optimize under the following loss,

$$
    \mathcal{L}((a, \, b), \, y; \, \theta) 
    = \log\exp\bigl( 1 + y(f_\theta(a) - f_\theta(b))\bigr) 
    + \lambda(f_\theta(a) + f_\theta(b)),
$$

where the regularization term helps with stability. Then, $f_\theta$ would essentially become a rating estimator. Whoever does this, however, will have the same fundamental problem as the Elo system; a weak order cannot capture the potentialy cyclic structure of actors' dominance on each other.

The solution, of course, is to instead train another model $f_\theta : \mathbb{R}^{2n} \to \mathbb{R}$ that admits pairings as an input via concatentaion, and implements typical binary cross-entropy loss:

$$
    \mathcal{L}((a, \, b), \, y; \, \theta) 
    = -\bold{I}_y\,\log\bigl(\sigma(f_\theta(a \Vert b))\bigr) - (1 - \bold{I}_y)\,\log\bigl(1-\sigma(f_\theta(a \Vert b))\bigr).
$$

However, there is no free lunch -- when training over _pairs_ of teams in $T$, the sample space of the task grows with the size of $T \times T$, naturally increasing the amount of out-of-distribution data for your model quadratically. Of course, this problem was ignored by the first formulation too, just in a different way. 

---

## Appendix 

### Gaussian Convolution

Here, I justify equation $(6)$ by instantiating a proof of the fact that the convolution of two gaussians is another gaussian determined by the parameters of the original gaussians.  

#### Proof
This was made via ChatGPT with `o4-mini-high` and adjusted by me, because you can probably find it in a textbook somewhere. Let

$$
    f(d)=\frac{1}{\sqrt{2\pi}\,\sigma_1}\exp\!\Bigl(-\frac{(d-\mu_1)^2}{2\,\sigma_1^2}\Bigr),
    \quad
    g(d)=\frac{1}{\sqrt{2\pi}\,\sigma_2}\exp\!\Bigl(-\frac{(d-\mu_2)^2}{2\,\sigma_2^2}\Bigr).
$$

We wish to show

$$
    \int_{-\infty}^{\infty} f(d)\,g(d)\,dd
    =\frac{1}{\sqrt{2\pi\,(\sigma_1^2+\sigma_2^2)}}\,
    \exp\!\Bigl(-\frac{(\mu_1-\mu_2)^2}{2\,(\sigma_1^2+\sigma_2^2)}\Bigr).
$$

Set $A=\sigma_1^2$ and $B=\sigma_2^2$. Then,

$$
    f(d)\,g(d)
    =\frac{1}{2\pi\sqrt{AB}}
    \exp\!\Bigl(-\tfrac12\bigl[\tfrac{(d-\mu_1)^2}{A}+\tfrac{(d-\mu_2)^2}{B}\bigr]\Bigr).
$$

Combine quadratic terms:

$$
    B(d-\mu_1)^2 + A(d-\mu_2)^2
    =(A+B)\Bigl(d-\frac{B\mu_1 + A\mu_2}{A+B}\Bigr)^2
    +\frac{AB}{A+B}(\mu_1-\mu_2)^2.
$$

Define

$$
    m=\frac{B\mu_1 + A\mu_2}{A+B},
    \quad
    C=\frac{AB}{A+B}.
$$

Then,

$$
    \int f(d)\,g(d)\,dd
    =\frac{1}{2\pi\sqrt{AB}}
    \int
    \exp\!\Bigl(-\tfrac12\bigl[\tfrac{(d-m)^2}{C}+\tfrac{(\mu_1-\mu_2)^2}{A+B}\bigr]\Bigr)
    \,dd.
$$

Factor out the constant term and use

$$
    \int \exp\Bigl(-\frac{(d-m)^2}{2C}\Bigr) \, dd
    =\sqrt{2\pi\,C}.
$$

Hence,

$$
\begin{aligned}
    \int f(d)\,g(d)\,dd
    &=\frac{\sqrt{2\pi\,C}}{2\pi\sqrt{AB}}
    \exp\!\Bigl(-\frac{(\mu_1-\mu_2)^2}{2\,(A+B)}\Bigr)\\
    &=\frac{1}{\sqrt{2\pi\,(A+B)}}
    \exp\!\Bigl(-\frac{(\mu_1-\mu_2)^2}{2\,(A+B)}\Bigr). \quad \square
\end{aligned}
$$

#### Instantiation

Consider the expression which $(6)$ was derived from, 

$$
\begin{aligned}
    &\int
       \frac{1}{\sqrt{2\pi}\,\sigma_\varepsilon}
       \exp\!
       \overbrace{
        \Bigl(-\frac{\bigl(g(\hat\delta_{i, \, j}) - d\bigr)^2}{2\,\sigma_\varepsilon^2}\Bigr)
       }^{
        \text{Quadratic term is symmetric.}
       }
       \;|g'(\hat\delta_{i, \, j})|\; \\
    &\quad\;\quad\;\times \frac{1}{\sqrt{2\pi(\sigma_i^2+\sigma_j^2)}}
       \exp\!\Bigl(-\frac{(d - (r_i - r_j))^2}{2(\sigma_i^2+\sigma_j^2)}\Bigr)
    \,dd.
\end{aligned}
$$

Now, use the substitutions 

$$
    \mu_1 = g(\hat\delta_{i, \, j}),
    \quad
    \mu_2 = r_i - r_j,
    \quad
    A = \sigma_\varepsilon^2,
    \quad
    B = \sigma_i^2 + \sigma_j^2,
$$

and re-attach the Jacobian factor $|g^\prime(\hat\delta_{i, \\, j})|$ to recover

$$
\mathcal{J}(\theta_i,\theta_j;\hat\delta_{i, \, j})
=\frac{|g'(\hat\delta_{i, \, j})|}
      {\sqrt{2\pi\,(\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2)}}\,
  \exp\!\Bigl(-\frac{(g(\hat\delta_{i, \, j}) - (r_i - r_j))^2}
                       {2\,(\sigma_\varepsilon^2 + \sigma_i^2 + \sigma_j^2)}\Bigr).
$$

[^expected-score]: I modified a term in $e_p$ to exclude scaling factors, to make it look less crazy. These scaling factors make the resulting ratings quite practical by allowing one to make comparisons like "player $i$ is 10x better than $j$ if $i$'s rating is 400 points higher."

[^log-likelihood]: Taking the $\log$ makes it easier to deal with multiple samples, as it turns the product in $(1)$ into a sum. But here, we only use one sample, so it is useless. However, tradition is important for learning. 
