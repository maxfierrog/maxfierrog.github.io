---
title: "Representation Concepts in Game-Theoretic Systems"
date: 2024-04-20
math: true
---

{{< toc >}}

## Introduction

I gave an introductory talk about how computer systems represent, compute, and store noteworthy attributes about a particular class of games. This was part of [Sprouts '24](http://kyleburke.info/sprouts/), an undergraduate-oriented conference primarily dedicated to combinatorial game theory.

Here, I share the materials I used during my presentation and share a longer-form (but very different) exploration of the topic I covered. Generically, it can be useful for all problems where one must run a domain-specific algorithm on a graph that is not materialized in memory, but can be traversed in linear time from a starting node and a set of functions that derive adjacent edges and nodes from existing ones (a so-called [implicit graph](https://en.wikipedia.org/wiki/Implicit_graph)).

As a concrete case of this abstract class of problems, I present concepts that support the process of finding a Nash Equilibrium for a specific subclass of games through cousins of the minimax algorithm. However, these concepts are also applicable to other such problems (e.g., the membership problem[^cfg-membership] for decidable subclasses of context-free grammars).

---

## Materials

The slides I used during my talk can be found below. Anyone can use them without my permission.

{{< embed-pdf url="/pdf/slides-sprouts-2024.pdf" >}}

### Errata

Here are the mistakes I have found in the slides:

1. In slide 10, the first bullet point should also restrain the set of games under consideration to be extensive-form and non-collaborative, as implied by the subsequent definition in slides 11-15.
2. In slide 20, the formulation $\langle N, S, p, u \rangle$ should also include a transition function $t : S \to \mathcal{P}(S)$, where if $s$ is a state corresponding to history $h$, then the history $h'$ corresponding to $s'$ should be the same as $h$ with an additional action appended for all $s' \in t(s)$.
3. In slide 28, the partition should not necessarily minimize the sum of the conductance of all cuts that produce the partition. Instead, the ideal partition would be a solution to the [balanced partition problem](https://en.wikipedia.org/wiki/Graph_partition#:~:text=%5B4%5D-,Problem,-%5Bedit%5D), where optimal parameters are determined from hardware-related constraints (such as the cost of inter-process communication). The goal is to balance parallelism with its own overhead.

---

## Background

In the interest of accessibility, I will briefly cover useful basics in game theory and computer science that seldom find their way into students' syllabi or are otherwise worth refreshing. If you think you can safely skip this, you are probably right.

### Game theory

The generic setup of a game is some amount of "players" taking actions according to their own interests or preferences, potentially affecting other players in the process. From the point of view of a single player, a game is an optimization problem that seeks to find an "optimal strategy" from the information available to them. This is an assumption known as "individual rationality" that pervades most of game theory.

But from a global point of view, there is no obvious question to ask about a game. This is why games are not problems; they are situations that we can ask different questions about. But _per se_, games are not aching to be solved. To ask specific questions with some hope of rigor, a lot of effort has been placed into defining classes of games that posses different characteristics.

#### Taxonomy of games

There are a lot of classes of games. They are separated by the mathematical properties of their setting and participants, among other factors.

There is no global dictionary or atlas for game classes, as interpretations can become nuanced to the extent of opinion. Hence, anytime someone makes a statement in game theory they must specify the class of games it targets. In this article, we will target games that are:

1. **Perfect-information.** Here, all players know everything in the universe that could possibly help them make or avoid any decision, except the decisions that other players will make. Most forms of Poker are not perfect-information, as the exact location of the cards is unknown.

2. **Deterministic.** Here, if all players choose a strategy and never change it, there is only one possible outcome for the game. Chess is deterministic, because if players make the exact same moves in two different games they are guaranteed to achieve the same result.

3. **Sequential.** More intuitive superset of {{< hidden-link "https://cdn.nba.com/headshots/nba/latest/1040x760/2544.png" "extensive-form games" >}}. Here, all actions that players can take are indivisible. Soccer is not discrete, because players' movements constitute their actions and it is possible to divide any movement into a shorter one.

4. **Extensive-form.** The adjective "extensive-form" refers to games that can be written in extensive form, which is a kind of mathematical template. Here, games are defined in terms of the histories of actions that could be observed by the players, and the preferences each player has among them.

#### Solution concepts

There are some questions that are so broadly-applicable in terms of the classes of games they can target that they achieve the special status of a **solution concept.** This is a term that refers to a characteristic can be observed in a useful set of game classes.

A very human thing to ask about broad categories of games is who will win. As it turns out, there is no real answer to this question most of the time, because it can come across obstacles like chance, incomplete information, and lack of clarity around the word "win." An equally important yet more applicable question, however, is what strategy each player should take to achieve the best possible result for themselves.

In many cases, it is necessary to tack on additional nuances to this question to be able to answer it. One such refinement of the question (which revolutionized economics) asks which strategy each player should adopt so that no single player could change their own and benefit from it. A pairing of players to strategies is known as a **strategy profile**, and those that satisfy the above property are known as **Nash Equilibria**.

The strategies and strategy profiles that allow players to act probabilistically are called **mixed**. Mixed strategies are tantamount to sampling [probability distributions](https://en.wikipedia.org/wiki/Probability_distribution) of **pure strategies**, which themselves specify deterministic actions. In 1950, John Nash defined the concept of a Nash Equilibrium (NE), additionally proving that there exists such a mixed strategy profile in all games [^nash-dissertation].

{{% hint title="Note" %}}

Nash Equilibrium is an overloaded term, as it refers to both a solution concept and a strategy profile that satisfies the solution concept. You will need to tell which is which from context.

{{% /hint %}}

### Computer science

The possibility of players taking actions simultaneously (among other things) can make the existence of a pure-strategy NE impossible. But if sequential play is assumed, it is straightforward to show that there always exists a pure-strategy NE [^zermelos-thm]. As mentioned above, this article benefits from this assumption.

Because finding a NE is such a popular desire, most of the discussion here will focus on the procedure of finding a pure-strategy NE in the class of games we specified previously. This is a costly process, which is why it calls for techniques that help minimize use of computational resources. However, you will notice that the things that make this an inherently costly process for some games are actually factors that have nothing to do with game theory.

Hence, it is possible that the concepts I will discuss are applicable beyond the problem of finding a pure-strategy NE. To elaborate, a maximally generic yet snobby version of this article would perhaps be titled _Techniques for Implementing Solutions to Search Problems on Implicit Graphs_. The meanings of these terms are:

1. **Implementing.** Bring into the real world.

2. **Solutions.** In this context, algorithms that solve problems.

3. **Search Problem.** A problem that asks you to find something.

4. **Implicit Graph.** [Graph](https://en.wikipedia.org/wiki/Graph_(abstract_data_type)) representation in terms of an initial element and a collection of functions which allow you to perform a traversal. Useful but unconventional term.

In particular, the section titled "Representation" will explain the link between the definition of an extensive game and the representation of its structure as an implicit graph, and will introduce a trick that can be used to end up with a significantly simpler traversals. This trick is also applicable to problem domains other than games, but I only present it with regard to games because its implementation depends on the underlying problem. Everything else is applicable as soon as you have an implicit graph in your hands.

While explaining these concepts, it will be useful to have access to ideas in complexity theory. Below are some domain-specific remarks and definitions introducing language that will be of relevance later.

#### Complexity theory

In computer science, complexity is a measure[^formal-measure] of the minimal number of elementary operations that must be composed to complete a target operation. The relevant elementary operations correspond to the kind of complexity in question:

1. **Time complexity.** Here, elementary operations are other operations whose time is assumed to be a known constant. Elementary operations should always be specified.

2. **Space complexity.** Here, the elementary operation is setting a bit. A more formal definition of space complexity depends on the [model of computation](https://en.wikipedia.org/wiki/Model_of_computation).

For the sake of expressibility, complexity is usually expressed in terms of [asymptotic characteristics](https://en.wikipedia.org/wiki/Asymptotic_analysis). In particular, symbolisms like [Big-O notation](https://web.mit.edu/16.070/www/lecture/big_o.pdf) help compare the asymptotic complexity of different algorithms.

[Computational problems](https://en.wikipedia.org/wiki/Computational_problem) can be put into [complexity classes](https://en.wikipedia.org/wiki/Complexity_class). For example, the problem of finding a mixed-strategy NE, known as $\text{N}\small{\text{ASH}}$, is in the time complexity class [$\text{FNP}$](https://en.wikipedia.org/wiki/FNP_(complexity)), which has led many people to look for other solution concepts that are more computationally favorable[^replicator-dynamics]. The equivalent problem for the class of games we are considering, however, is in [$FP$](https://en.wikipedia.org/wiki/FP_(complexity)). In other words, $\text{N}\small{\text{ASH}}$ can be solved [efficiently](https://en.wikipedia.org/wiki/Algorithmic_efficiency) on this restricted domain of games[^restricted-nash-linearity].

---

## Representation

One can find solutions to instances of many search and decision problems over games without incurring large computational expenses. This is possible by deriving a logical analysis on a case-by-case basis, using the mathematical properties of the components of the game in question.

{{% hint title="Definition" %}}

An **extensive-form game** is a 4-tuple $\langle N, H, p, (\succsim_i) \rangle$, where:

* $N$ is a set of players, usually $\\{1, \\; \ldots, \\; n\\}$ for simplicity.
* $H$ is a set of strings of actions where $h \in H \implies h' \in H$ where $h'$ is the string resulting from removing the last action from the string $h$.
* $p : H \to N$ assigns a player to each non-terminal history.
* The player $i \in N$ has a preference relation $\succsim_i$ on the set $Z \subseteq H$ of terminal histories (which is reflexive and transitive).

{{% /hint %}}

For a game provided in the extensive form, its instantiation of the above abstractions can be logically leveraged to prove statements about the game. But given that it can be defined arbitrarily, sometimes it is impossible to achieve this solely through formal rewriting.

In some of these cases it is possible to simply expand the component definitions into their explicit forms in order to later compute a solution using these expansions. But of course, doing this can also be extremely impractical. For example, it is common for $H$ to be of very high cardinality.

{{% hint title="Example" %}}

Consider a [Rubik's Cube](https://en.wikipedia.org/wiki/Rubik%27s_Cube) that is initialized to specific starting colors, which can be set into the extensive form via the following instantiations:

* $N = \\{1\\}$.
* $H = \\{ h \\; | \\; h \text{ is a sequence of 90° rotations of a plane in the cube} \\}$.
* $p : h \mapsto 1$ for all $h \in H$.
* $h_i \succsim_1 h_j$ for all $h_i$ that leave the cube solved.

Here, the set $H$ is countably infinite, so it is impossible to expand it to its elements in order to later compute a property of this puzzle. One such property is the smallest number of actions that can solve the cube[^rubiks-god-number].

{{% /hint %}}

### Rulesets

Before introducing tools to deal with this, there is another representation that is common when dealing with [abstract strategy games](https://en.wikipedia.org/wiki/Abstract_strategy_game). A ruleset, as it is known in [combinatorial game theory](https://en.wikipedia.org/wiki/Combinatorial_game_theory), is the most familiar kind of representation of a game.

In particular, a ruleset specifies exactly what actions are permitted to whom and when. It also explains the [utility](https://en.wikipedia.org/wiki/Utility) obtained by each participating player when no further actions are available. These characteristics are expressed in terms of the mutable state of a proxy (e.g., a board with pieces).

{{% hint title="Example" %}}

The game `10-to-0-by-1-or-2` is generated by the following ruleset:

* There is a collection of 10 items.
* 2 players take alternating turns removing either 1 or 2 items from the collection.
* Player 1 starts. The player who takes the last item from the collection wins.

In this game, the collection of items that is mutated by players' actions is a proxy that allows players to judge what they are allowed to do and to determine who wins the game.

{{% /hint %}}

This way, the information contained in the state of a proxy is a representation of the history of actions that produced it. These representations are called **game states**. This makes rulesets implicit graphs over the set of game states (denoted $S$). While they do not act directly over a set of histories, rulesets include enough information to generate an equivalent extensive-form representation. The resulting structure of generated action histories is hence intimately tied to the nature of the proxy.

{{% hint title="Definition" %}}

Given a directed graph $G = \langle S, E \rangle$, the corresponding **implicit graph** $G^I$ is a 3-tuple $\langle S, t, s_0 \rangle$, where:

* $S$ is a set of states.
* $s_0 \in S$ is a starting element.
* $t : S \to \mathcal{P}(S)$ is a transition dynamics function with $(s_i, s_j) \in E \iff s_j \in t(s_i)$.

A proof of the bijection between implicit and directed graphs is omitted.

{{% /hint %}}

Furthermore, because many actions could be globally or locally commutative with respect to proxies' mutable state, sets of histories in the extensive form are usually of much higher cardinality than sets of possible states for rulesets' proxies. This is of course computationally favorable, as finite ruelesets (whose proxies have a finite number of possible states) can generate even infinite extensive forms.

{{< hint title="Example" >}}
{{< split widths="9,5" >}}

In the diagram to the right, let $S_0$, $S_1$, $S_2$, and $S_3$ be allowed states under a ruleset, and $A = \\{a, b\\}$ allowed actions. We have that:

* The set of histories is $H = \\{ \epsilon, a, b, ab, ba \\}$.
* The set of states is $S = \\{ S_0, S_1, S_2, S_3 \\}$.

As you can see, $|H| > |S|$ despite how $|A| < |S|$. The difference in size between $H$ and $S$ scales rapidly in the general case.

<--->

{{< vcenter >}}
{{< hcenter >}}

{{< figure src="commutative_diagram.png" width="128" >}}

{{< /hcenter >}}
{{< /vcenter >}}

{{< /split >}}

{{< /hint >}}

### Abstraction

In real life, games come mostly in the form of rulesets. We are usually aware of the environment they transpire in (the so-called intuitive proxy) and the laws that describe how it can change as a function of players' actions. Because of this, much of applied theory is centered around semantics that involve mutable state. For example, a [Markov Decision Process (MDP)](https://en.wikipedia.org/wiki/Markov_decision_process) from [reinforcement learning](https://en.wikipedia.org/wiki/Reinforcement_learning) strongly reflects the nature of a ruleset.

However, all of these constructs have a latent yet equivalent extensive form representation. This will motivate the technique of **state abstraction**[^abstraction-rl]: The necessity for action histories to be directly prefixed implies that they have a directed tree structure, and because all directed graphs have an equivalent implicit graph representation, the relationship between action histories and ruleset states maps an implicit graph to another one that retains important information about the original.

{{% hint title="Definition" %}}

An **abstraction map** $a : S_{\text{pre}} \to S_{\text{post}}$ maps the states in an implicit graph $G^I_{\text{pre}}$ to the states in another implicit graph $G^I_{\text{post}}$ with the structure-preserving condition

$$ s_j \in t_{\text{pre}}(s_i) \iff a(s_j) \in t_{\text{post}}(a(s_i)). $$

{{% /hint %}}

As shown in the last example, the set of action histories is usually of much greater cardinality than its corresponding set of ruleset states, with the difference being possibly infinite. Altogether, this is a hint that, much like the implicit jump from action histories to ruleset states, it is possible to jump to more abstract state sets of smaller cardinality but equal representational power.

{{< hint title="Example" >}}

{{< md >}}
An abstraction mapping between the action histories (left) and states (right) from the previous example. Notice how action histories always imply a tree, and the process of abstraction folds it into a potentially cyclic graph.

[]() <!-- This is for rendering the above as a <p> element -->

{{< /md >}}

{{< hcenter >}}
{{< figure src="abstraction.png" width="475" >}}
{{< /hcenter >}}

{{< /hint >}}

Therefore, much like imposing a ruleset proxy causes many of the action histories to fold into equivalent states, we can determine for a problem $\text{P}$ which states would be $\text{P}$-equivalent under the ruleset's laws. This way, we can create new abstractions $a_i : S_{i - 1} \to S_i$ that can be composed to obtain a reduced state space that is equivalent to the original under $\text{P}$ for a significant computational upside.

{{% hint title="Example" %}}

Consider the ruleset underlying the game of Tic-Tac-Toe. Denote $B_i$ a board state. Then, any algorithm that computes a NE for this game through its ruleset is invariant to the abstraction $a$, where

$$ a(B_i) = a(B_j) \iff B_i \text{ is symmetrical to } B_j. $$

Further, the number of board states this algorithm will need to visit is reduced by a factor $>5$.

{{% /hint %}}

---

## Design

So far, discussion has brought us to implicit graphs and abstractions through the lens of game theory. The objective of this section will be to motivate these concepts beyond game theory, while supplying references to concrete programming ideas.

To do this, we will cover a representation of an implicit graph in a real programming language, apply it to a new problem domain, and make improvements that bring real-world utility. Examples will still be given in terms of games, as they also happen to fit under our new focus. In doing so, we will design a solution to a broad problem using our new toolset items.

### Interface items

There are a number of considerations to make when encoding an implicit graph, whose importance will vary depending on the object being represented. This section will introduce only the example of graphs of subproblems in the context of [dynamic programming](https://en.wikipedia.org/wiki/Dynamic_programming) (DP), and will iterate on the following [Rust interface](https://doc.rust-lang.org/book/ch10-02-traits.html) to eventually allow their solutions to be found in parallel through a special kind of abstraction.

{{< highlight rust "lineNos=true, lineNoStart=1" >}}

trait ImplicitGraph<C>
where
  C: IntoIterator<Item = Self::State>,
{
  type State;
  fn start() -> Self::State;
  fn transition(state: Self::State) -> C;
}

{{< /highlight >}}

The elements of this interface declaration relate to the implicit graph $G^I = \langle S, t, s_0 \rangle$ as follows:

* The generic parameter `Start` is the type of the elements in $S$.
* The generic parameter `C` is the type of the elements in $\mathcal{P}(S)$.
* `transition` is the template of $t$.
* `start` simply returns $s_0$.

{{% hint title="Example" %}}

Implementation of the game `10-to-0-by-1-or-2` from the section on Rulesets as an implicit graph.

{{< highlight rust "lineNos=true, lineNoStart=1" >}}

/// The game `10-to-0-by-1-or-2`.
struct ZeroBy;

impl ImplicitGraph<Vec<(u32, bool)>> for ZeroBy {
  // Tuple of (items, turn).
  type State = (u32, bool);

  // Returns (10 items left, player 0's turn).
  fn start() -> (u32, bool) {
    (10, false)
  }

  // Returns states with one and two less items on the opposing player's turn.
  fn transition(state: (u32, bool)) -> Vec<(u32, bool)> {
    let (items, turn) = state;
    let mut next = Vec::new();
    if items == 1 {
      next.push((0, !turn));
    } else if items > 1 {
      next.push((items - 1, !turn));
      next.push((items - 2, !turn));
    }
    next
  }
}

{{< /highlight >}}

{{% /hint %}}

### Parallel DP

A popular characterization of DP is to establish dependency [relations](https://en.wikipedia.org/wiki/Relation_(mathematics)) on sets of subproblems, defining a [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph) (DAG) for any properly formulated subproblem definition. A natural link to implicit graphs exists through their bijection with general graphs.

Having established that a DP problem can be characterized as an implicit graph of subproblems, it is also worth mentioning that most unorganized solution implementations (i.e., that do not organize solutions or information about subproblems in a tensor) make use of stack-like data structures to aid traversals of subproblems in [postorder](https://en.wikipedia.org/wiki/Tree_traversal).

{{% hint title="Example" %}}

DP algorithm to compute who will win a game of `10-to-0-by-1-or-2`, with the below subproblem relation[^minimax-relation]:

$$ W(s) = \max_{s' \in \\, t(s)} \min_{s' \in \\, t(s)} W(s'). $$

Here, $W : S \to \\{ 0, \\, 1 \\} $ maps state information (including the number of items remaining and player turn) to whether the player whose turn it is at $s$ would win under optimal play, with $t$ being the transition function of the implicit graph over this game's states. This uses the implementation from the previous example.

{{< highlight plaintext "lineNos=true, lineNoStart=1" >}}

procedure:
  temp ← empty stack
  stack ← empty stack
  visited ← empty set

  stack.push(ZeroBy::start())
  while stack is not empty:
    current ← stack.pop()
    if current is not in visited:
      visited.add(current)
      temp_stack.push(current)
      for each state in ZeroBy::transition(current):
        if state is not in visited:
          stack.push(state)

  solution ← empty map
  while temp_stack is not empty:
    current ← temp_stack.pop()
    solution[current] = W(current)

  return solution[ZeroBy::start()]

{{< /highlight >}}

Note the use of $W$ in line 19. Also note that the subproblem relation does not necessarily generalize to other games and, for the sake of brevity, is not defined for base cases (where $t(s) = \varnothing$).

{{% /hint %}}

Attempts to parallelize this setup must first identify a method to partition the subproblem graph in a way optimizes the tradeoff between parallelism and its own overhead. Of course, this depends significantly on the specific resources that will be used to execute the resulting program.

I will introduce one way of doing this that follows naturally from the use of the implicit graph interface. Concretely, a carefully chosen abstraction $\pi : S \to \mathbb{N}$ that connects the graph of subproblems to a DAG of enumerated sets of states will provide a parallelization scheme.

{{< highlight rust "lineNos=true, lineNoStart=1" >}}

trait ImplicitGraph<C>
where
  C: IntoIterator<Item = Self::State>,
{
  type State;
  fn start() -> Self::State;
  fn partition(state: Self::State) -> u64; // <-- NEW
  fn transition(state: Self::State) -> C;
}

{{< /highlight >}}

Here, `partition` is the template of $\pi$. The big idea is that during a traversal, we can observe a change in the value of `*::partition(current)`, where `current` is the current state in the traversal. This way, we can build a graph of the outputs of this function based on their adjacency in the subproblem graph. Finally, we analyze the resulting graph to find sets of states that can be traversed simultaneously.

{{% hint title="Example" %}}

On the left, a graph over a set of states $S = \\{ s_0, \\, \ldots, \\, s_{11} \\}$. On the right, a graph over a set $\Pi \subset \mathbb{N}$ with four elements. They are related by an abstraction $\pi : S \to \Pi$ that is special in that the resulting graph over the elements of $\Pi$ is acyclic. Hence, the [fibers](https://en.wikipedia.org/wiki/Fiber_(mathematics)) $\pi^{-1}(\\{\pi_i\\})$ of certain distinct elements $(\pi_i)$ of $\Pi$ could possibly be traversed in parallel. An example of groupings of elements in  $\Pi$ whose fibers under $\pi$ could be traversed in parallel is provided in dotted boxes on the graph of $\Pi$.

{{< figure src="partition.png" width="550" >}}

{{% /hint %}}

Finding a suitable $\pi$ depends on the chosen state (subproblem) representation and is of course highly problem-specific. However, a general strategy is to ensure that $\pi$ outputs a different label if and only if an irreversible change is made to some form of mutable state. As a high-level criterion, this helps construct an abstraction that assuredly maps onto a DAG.

Having found such an abstraction, the next perplexity of parallelizing a postorder traversal of subproblems is managing efficient and clear use of shared data structures. Here, a zoo of approaches with varyingly personable tradeoffs are available. I will introduce only one, which involves an additional interface item in the form of a function $t' : S \to \mathcal{P}(S)$ called `retrograde`.

{{< highlight rust "lineNos=true, lineNoStart=1" >}}

trait ImplicitGraph<C>
where
  C: IntoIterator<Item = Self::State>,
{
  type State;
  fn start() -> Self::State;
  fn partition(state: Self::State) -> u64;
  fn transition(state: Self::State) -> C;
  fn retrograde(state: Self::State) -> C; // <-- NEW
}

{{< /highlight >}}

In an ideal world, `retrograde` is the equivalent of `transition` for the [transpose](https://en.wikipedia.org/wiki/Transpose_graph) of the graph being represented. Unfortunately, it is not generally tractable to have a perfect implementation of `retrograde` without first materializing the entire graph (defeating the purpose of an implicit graph representation). Thus, we only make sure that `retrograde` returns a superset of what `transition` would return for the transpose of the graph, which is easy to ensure in many useful cases.

With $\pi$ and $t'$ in our hands, we can provision the following procedure for parallelizing the execution of an unorganized dynamic programming algorithm over an implicit graph $G^I$ of subproblems:

{{% hint title="Procedure" %}}

1. Traverse $G^I$ to obtain the set of subproblems $S$ in $\mathcal{\Theta}(|G^I|)$ time and $\mathcal{\Theta}(|S|)$ space.
2. During (1), track the subset of subproblems $S_{base} = \\{ s \\; | \\; t(s) = \varnothing \\}$ at no additional cost.
3. During (1), construct a graph $G_\Pi$ over a set of partition labels $\Pi$ using $\pi$, at a $\pi$-dependent cost.
4. Use $G_\Pi$ to generate a plan[^parallel-plan] of labeled parallel tasks in $\mathcal{\Theta}(|G_\Pi|)$ time and $\mathcal{\Theta}(|\Pi|)$ space.
5. Delegate tasks, starting exploration from popped elements of $S_{base}$ with the task's label.
6. Use $t'$ for backward intra-partition traversal (using $S$ for existence checks) in $t'$-dependent time.
7. When a change in $\pi(s)$ is observed on the current state $s$, add $s$ to $S_{base}$, leaving it unexplored.
8. When a partition is completely explored, finish its task and free the parallel unit.
9. Repeat from (5) on new elements of $S_{base}$ until there are no tasks remaining.

{{% /hint %}}

These general steps skip some details, but they present an arbitrarily parallel stack-free traversal that can be implemented over a single shared data structure whose size scales in the order of $\mathcal{\Theta}(|S|)$ (ignoring structures related to partitions, whose size is assumed to be negligible). This kind of map-like [thread-safe](https://en.wikipedia.org/wiki/Thread_safety) functionality is available in many database implementations which automatically bring the added benefit of disk usage, making this method applicable to "bigger" problems.

---

## Meta-content

The section titled "Representation" got us to stumble across the new concepts of implicit graphs and abstractions by looking at different forms for game representations. The following section extrapolated these ideas to the domain of dynamic programming, and showed how it is possible to incorporate them into the design of solutions to real-world problems.

Something interesting is that games made their way into the second section, despite being decidedly out of scope at that point. In a dying hope of getting this article back on track, I will point out that the particular example of parallelizing DP algorithms was conveniently chosen because it is used to [solve](https://en.wikipedia.org/wiki/Solved_game) bigger games faster than was previously possible (through DP algorithms that consume representations of them).

This way, I can say that this whole article was in fact about game-theoretic systems. But we both know that it was really about implicit graphs and abstractions. Maybe, if we squint our eyes, it can be about both topics. Either way, I hope the lack of clarity was more stimulating than it was confusing.

[^cfg-membership]: The problem of deciding whether or not a string is in the language of a context-free grammar.

[^nash-dissertation]: For his doctoral dissertation, [_Non-Cooperative Games_](https://gametheory.online/projects/documents/1521541344.pdf).

[^zermelos-thm]: This fact is known as [Zermelo's theorem](https://en.wikipedia.org/wiki/Zermelo%27s_theorem_(game_theory)).

[^abstraction-rl]: Term generalized from [its use in reinforcement learning](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2001/CSD-01-1156.pdf).

[^formal-measure]: In both [the mathematical](https://en.wikipedia.org/wiki/Measure_(mathematics)) and informal sense.

[^replicator-dynamics]: For example, [Christos Papadimitrou on replicator dynamics](https://www.youtube.com/watch?v=-aSBlRhpwVc).

[^restricted-nash-linearity]: $\text{N}\small{\text{ASH}}$ asks to find a mixed-strategy NE. A pure-strategy NE is a case of a mixed-strategy NE. [Zermelo's theorem](https://en.wikipedia.org/wiki/Zermelo%27s_theorem_(game_theory)) shows a pure-strategy NE always exists for the class of games in question. Then, [backward induction](https://en.wikipedia.org/wiki/Backward_induction) algorithms can find one in linear time for finite representations of games.

[^rubiks-god-number]: It is possible to obtain this number for a specific starting configuration of a Rubik's Cube, but no one knows the length of the longest minimal sequence of moves necessary to solve it across all starting configurations. This is somewhat dramatically known as [God's Number](https://web.archive.org/web/20141109174500/http://digitaleditions.walsworthprintgroup.com/article/The_Quest_For_God%E2%80%99s_Number/532775/50242/article.html).

[^minimax-relation]: This subproblem relation monomorphizes the generic formulation of the [minimax algorithm](https://en.wikipedia.org/wiki/Minimax).

[^parallel-plan]: A parallelization plan is a data structure that can dispense information on which task must be worked on next when a parallel unit becomes available for work. It may also communicate the need to wait until another unit completes its work.
