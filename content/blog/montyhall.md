---
title: "Conditional Probability and Monty Hall"
author: "Nathaniel Forde"
date: "2016-05-08T08:22:07+01:00"
draft: no
type: post
image: "/portfolio/montyHall/monty_hall_2.png"
---

Probability is difficult because it is hard to properly model the relationships between events, and the facts even when recognised are unintuitive. Conditional probability is a tool for assessing the probability of a hypothesis given some evidence. To represent this mathematically we have to distinguish over all possible events, and assess the frequency of when the hypothesis is confirmed within the subset of possible worlds where our evidence is deemed to have occurred. Getting this process correct requires subtle modelling considerations as we will see below. First examine the definition:

$\text{ A conditional probability space is a tuple } (W, \mathcal{F}, \mathcal{F'}, \textbf{P}) \text{ where } \mathcal{F} \times \mathcal{F'}$ $\text{ is a Popper algebra over } W \text { i.e. subsets of } W \times W$ $\text{ such that (i) } F \text{ is an algebra over } W, \text{ (ii) } F' \neq \emptyset\subseteq F$ $\text{ (iii) } F' \text{ is closed under supersets.$ 
$The function } \textbf{P}: F \times F' \rightarrow [0, 1] \text{ satisfies:}$

$1. \textbf{P}(H \mid H) = 1 \text{ if } U \in \mathcal{F'} $

$2. \textbf{P}(H \mid E) = \dfrac{\textbf{P}(H \cap E)}{\textbf{P}(E)} \text{ if } \textbf{P}(E) > 0 \text{ and } E \in \mathcal{F'}, H \in \mathcal{F}$

$3. \textbf{P}(H\_{1} \cup H\_{2} \mid E) = \textbf{P}(H\_{1} \mid E) + \textbf{P}(H\_{2} \mid E) \text{ if } H\_{1} \cap H\_{2} = \emptyset, H\_{1}, H\_{2} \in \mathcal{F} \text{ and } E \in \mathcal{F'}$

$\textbf{P}(H\_{1} \cap H\_{2} \mid E) = \textbf{P}(H\_{1} \mid H\_{2} \cap E) \times \textbf{P}(H\_{2} \mid E) \text{ if } H\_{2} \cap E \in \mathcal{F'}, H\_{1} \in \mathcal{F}$
The idea is that we wish to test the probability of a given hypothesis on previously attained evidence. To begin we shall illustrate why it's important to discern conditional probabilities, and then we shall showcase some of the properties useful for speeding the calculation of such considerations. After this we will continue to illustrate the infamous Monty Hall problem.

Consider the two claims regarding the first two draws in a game of Texas hold'em:

$E:$The first card is an ace

$H:$The second card is an ace

We know that each proposition considered individually is such that $\textbf{P}(H) = \dfrac{1}{13} = \textbf{P}(E)$but the consecutive considerations of these claims are dependent, specifically we know that given $E$, then $H$is less likely as the first ace has been withdrawn without replacement. In fact we can easily read off $\textbf{P}(H \mid E) = \dfrac{3}{51} = \dfrac{1}{17}.$Hence the joint probability of $\textbf{P}(E \cap H) = \textbf{P}(E)\textbf{P}(H \mid E) = $$\textbf{P}(\dfrac{1}{13}\cdot\dfrac{1}{17}) = \dfrac{1}{221}$because the events are dependent.

In general it is crucial to see that the joint probability of two events can always be calculated in terms of conditional probability. To see this note the multiplication rule for assessing the probability of joint dependent events:

$\textbf{P}(H \cap E) = \textbf{P}(E) \dfrac{\textbf{P}(H \cap E)}{\textbf{P}(E)} = \textbf{P}(E)\textbf{P}(H \mid E)$

which allows us to calculate the probability of joint dependent events in terms of conditional probabilities. Another convenient rule is Bayes' rule which is written $\textbf{P}(H \mid E) = \dfrac{ \textbf{P}(E \mid H)\textbf{P}(H)}{\textbf{P}(E)}$.

Theorem: Bayes' Rule

$\dfrac{\textbf{P}(E \mid H)\textbf{P}(H)}{\textbf{P}(E)} = \dfrac{\textbf{P}(E \cap H)\textbf{P}(H)}{\textbf{P}(E)\textbf{P}(H)} = \dfrac{\textbf{P}(E \cap H)}{\textbf{P}(E)} = \textbf{P}(H \mid E)$

The rule can be rewritten as follows

$\textbf{P}(H \mid E) = \dfrac{ \textbf{P}(E \mid H)\textbf{P}(H)}{\textbf{P}(E)} = \dfrac{ \textbf{P}(E \mid H)\textbf{P}(H)}{\textbf{P}(E \cap H) \cup \textbf{P}(E \cap H^{c})}$ $= \dfrac{\textbf{P}(E \mid H)\textbf{P}(H)}{ \textbf{P}(E \mid H)\textbf{P}(H) + \textbf{P}(E \mid H^{c})\textbf{P}(H^{c})}$

We begin now with the idea that there are relations of probabilistic independence.

$\textbf{Independence on a CPS}$

We now define a relation of independence. Any pair of events which is not independent is taken to be dependent to some degree. The challenge, as always, is learning to specify what degree of dependence holds amongst observed events.

$\textbf{Independence with respect a CPS}$

Let $H$and $T$are probabilistically independent with respect to a conditional probability space ($W, \; \mathcal{F}, \; \mathcal{F}' \; \textbf{P})$if $H \in$$\mathcal{F}'$implies that $\textbf{P}(T \mid H)$= $\textbf{P}(T)$and $T \in\mathcal{F}'$implies $\textbf{P}(H \mid T) = \textbf{P}(H)$.
In some textbooks you often see what is known as the multiplicative definition of independence which states that two events $H$and $T$are probabilistically independent just when $\textbf{P}(H \cap T) = \textbf{P}(H)\textbf{P}(T).$Fortunately we can show this to be equivalent to the above conditional definition.

Theorem: Equivalence of Multiplicative and Conditional Definitions of Independence

Suppose that $\textbf{P}(H \cap T) = \textbf{P}(H)\textbf{P}(T)$and that $\textbf{P}(T) \neq 0$, just to avoid the trivial case. We want to show that $\textbf{P}(H \mid T) = \textbf{P}(H)$, but $\textbf{P}(H \mid T) = \dfrac{\textbf{P}(H \cap T)}{\textbf{P}(T)}$, which by assumption is the same as $\dfrac{\textbf{P}(H) \textbf{P}(T)}{\textbf{P}(T)}$. This cancels giving us $\textbf{P}(H)$. By the transitivity of identity we are done. For the other direction suppose that $\textbf{P}(H) = \textbf{P}(H \mid T).$We want to show that $\textbf{P}(H \cap T) = \textbf{P}(H)\textbf{P}(T).$ But from $\textbf{P}(H \cap T)$, by the multiplication rule we have $\textbf{P}(H \mid T)\textbf{P}(T).$By our initial assumption this is the same as $\textbf{P}(H)\textbf{P}(T)$, as desired. From this observation we can prove the following theorem.

Theorem: If $T $and $H$are independent then so are $T^{c}, H$and $T, H^{c}$and $T^{c}, H^{c}$.

We work with the multiplicative definition of independence. Observe:

$\textbf{P}(H^{c}\cap T) = \textbf{P}(T\setminus H) = \textbf{P}(T) - \textbf{P}(H \cap T)$ 
$ = \textbf{P}(T) - \textbf{P}(H)\textbf{P}(T) = (1 - \textbf{P}(H))\textbf{P}(T) = \textbf{P}(H^{c})\textbf{P}(T).$

Given this result the other equivalences follow by a similar proof. Another important observation is the chain rule.

Theorem: The Chain Rule

$\textbf{P}((H\_{1}) \cap..... \cap(H\_{n})) = \textbf{P}(H\_{1})\textbf{P}(H\_{2} \mid H\_{1})\textbf{P}(H\_{3}\mid H\_{1} \cap H\_{2})$....$\textbf{P}(H\_{n} \mid H\_{1} \cap.....\cap(H\_{n-1})).$

The proof follows by observing that:

$\textbf{P}(H\_{1})\textbf{P}(H\_{2}\mid H\_{1})\textbf{P}(H\_{3}\mid H\_{1}\cap H\_{2})....\textbf{P}(H\_{n}\mid H\_{1}\cap.....\cap(H\_{n-1}))$ 
$= \textbf{P}(H\_{1})\dfrac{\textbf{P}(H\_{2}\cap H\_{1})}{\textbf{P}(H\_{1})} \dfrac{\textbf{P}(H\_{3}\cap H\_{2}\cap H\_{1})}{\textbf{P}(H\_{1}\cap H\_{2})}..... \dfrac{\textbf{P}(H\_{n}\cap ... \cap(H\_{1})}{\textbf{P}(H\_{1})\cap....\cap(H\_{n-1})}$

Multiplying out we see that for any conditional claim in the sequence the numerator cancels with the denominator of the previous conditional in the sequence. Hence, we are left with just the numerator of the nth conditional in our sequence. In other words, we have shown the equivalence as desired.

These results allow us to test the relations between two variables but what if we wish to test whether independence holds between two variables in light of a potential influence.

$\textbf{Definition:}$ Conditional Independence with respect to a third parameter

$H$is probabilistically independent of $T$when conditional on $E$with respect to $\textbf{P}$: written $\textit{I}\_{\textbf{P}}(H, T \mid E)$if $\textbf{P}(T \cap E) \neq 0$implies $\textbf{P}(H \mid T \cap E) = \textbf{P}( H \mid E).$In words, learning $T$is irrelevant to determining the value for $H$given $E$.

The natural analogue of the multiplicative definition of independence in this setting the following claim: $H$is independent of $T$given $E$iff $\textbf{P}(H \cap T \mid E) = \textbf{P}(H \mid E)\textbf{P}(T \mid E).$Again this is provably equivalent to the above definition of conditional independence. This definition is often convenient for proving the following important properties:

Symmetry: If $\textit{I}\_{\textbf{P}}(H, T \mid E)$ then $\textit{I}\_{\textbf{P}}(T, H \mid E)$

Contraction: If $\textit{I}\_{\textbf{P}}(H, T \mid E)$ and $\textit{I}\_{\textbf{P}}(H, S \mid T \cup E)$then $\textit{I}\_{\textbf{P}}(H, T \cup S \mid E).$

Weak Union: If $\textit{I}\_{\textbf{P}}( H, T \cup S \mid E)$ then $\textit{I}\_{\textbf{P}}(H, T \mid S \cup E)$

Decomposition: If $\textit{I}\_{\textbf{P}}(H, T \cup S \mid E)$ then $\textit{I}\_{\textbf{P}}(H, T \mid E)$

Composition: If $\textit{I}\_{\textbf{P}}(H, T \mid E)$and $\textit{I}\_{\textbf{P}}(H, S \mid E)$ then $\textit{I}\_{\textbf{P}}(H, S \cup T \mid E).$

Symmetry states that if $H$is independent of $T$given $E$, then so too is $T$independent of $H$given $E$. Judea Pearl paraphrases Weak Union in terms of relevance stating that the axiom ensures that "learning irrelevant information $S$ cannot help irrelevant information $T$ become any more relevant to $H.$" The paraphrase is suggestive in that we might now think to read the Symmetry axiom as stating that if $T$is irrelevant to $H$, then $H$is irrelevant to $T$. Or analogously, if we learn nothing about $H$from $T$we can learn nothing about $T$from $H.$The Contraction axiom states that if we learn that some $S$is irrelevant to $H$, after learning some irrelevant $T$, then both $S$and $T$together must also be irrelevant to $H$before we learned $T$also. Whereas Decomposition states that if $S$and $T$are together irrelevant for the truth of $H$, then they are also irrelevant separately.

We quickly show how to prove that Symmetry is valid on any CPS. Due to the nature of the independence predicate $\textit{I}\_{\textbf{P}}$as defined above, we need only prove an equivalence result. Assume $\textbf{P}(H \mid T \cap E) = \textbf{P}( H \mid E).$We need to show that $\textbf{P}(T \mid H \cap E) = \textbf{P}( T \mid E).$

$\textbf{P}(T \mid H \cap E) = \dfrac{\textbf{P}(H \cap E \mid T)\textbf{P}(T)}{\textbf{P}(H \cap E)}$by Bayes' Rule.

$= \dfrac{\textbf{P}(T\cap E \cap H)}{\textbf{P}(H \cap E)}$By the Multiplication rule and Rearranging.

$= \dfrac{\textbf{P}(E \mid T)\textbf{P}(H \mid E \cap T)\textbf{P}(T)}{\textbf{P}(H \mid E)\textbf{P}(E)}$Rearranging and the Chain rule.

$= \dfrac{\textbf{P}(E \mid T)\textbf{P}(H \mid E)\textbf{P}(T)}{\textbf{P}(H \mid E)\textbf{P}(E)}$By our Assumption.

$= \dfrac{\textbf{P}(E \mid T)\textbf{P}(T)}{\textbf{P}(E)}$By Cancelling

$= \textbf{P}(T \mid E)$by Bayes' Rule.

So again, by the transitivity of identity Symmetry holds on any conditional probability space. The proofs for the other properties are analogous.

$\textbf{Monty Hall and Discovering Dependence}$

We now consider an example of a surprising discovery of dependence which indicates how probability does not gel well with our intuitions.

Imagine you are on a game show where you are asked to choose between three closed door ways. Behind one the doors is a prize, and behind the other two doors are goats. Making a decision you choose one of the doors, and then the gameshow host picks and opens another door revealing a goat. At this point you are asked whether you would like to change your mind or stick with your initial choice. This puzzle, as we shall see, demonstrates the counter-intuitive nature of probabilistic reasoning. Intuitively it seems that there is no advantage to switching as you are not obviously any wiser about what remains behind the remaining doors.

To model this correctly we will be considering four propositions. Name the three doors $a, b, c$We have the following three propositions:

$A : \text{ The prize is behind door a }$

$B : \text{ The prize is behind door b }$

$C : \text{ The prize is behind door c }$

In the first round of the game we assume that the prize has been distributed at random behind one of the doors i.e.

$\textbf{P}(A) = \textbf{P}(B) = \textbf{P}( C ) = \dfrac{1}{3}$.

Now without loss of generality we will stipulate that you have chosen door $a$in the first round of the game and that our host has subsequently revealed the goat behind door $b$. So our considerations of the conditional probability of either $A, B, C $will depend on:

$G\_{b} : \text{ There is a goat behind door b }$

So in effect the puzzle before us questions whether the following equality holds:

$\textbf{P}(A \mid G\_{b}) = \textbf{P}(C \mid G\_{b})?$

In English, does our new information make us reevaluate the assumption of equality made in the first round? Why should it? We calculate all conditional statements using Bayes' theorem. To do so we need to evaluate the following conditionals:

$\textbf{P}(G\_{b} \mid A) = \dfrac{1}{2}$ because both options $b, c$ are available.

$\textbf{P}(G\_{b} \mid B) = 0$ because the game would stop prematurely.

$\textbf{P}(G\_{b} \mid C) = 1$ because $b$ is the only choice which doesn't end the game prematurely.

In addition we need to consider the probability of $G\_{b}$which may be calculated using the law of total probability, which results in the fact that:

$\textbf{P}(G\_{b}) = \textbf{P}(G\_{b} \cap A) + \textbf{P}(G\_{b} \cap B) + \textbf{P}(G\_{b} \cap C) =$ 
$\textbf{P}(A)\textbf{P}(G\_{b} \mid A) + \textbf{P}(B)\textbf{P}(G\_{b} \mid B) + \textbf{P}( C )\textbf{P}(G\_{b} \mid C)$
$= \dfrac{1}{3} \cdot \dfrac{1}{2} + \dfrac{1}{3} \cdot 0 + \dfrac{1}{3} \cdot 1 = $$\dfrac{1}{6} + 0 + \dfrac{1}{3} = \dfrac{1}{6} + \dfrac{2}{6} $$= \dfrac{3}{6} = \dfrac{1}{2}$

With this information we can finally come to consider the question at hand using Bayes' theorem, for consider:

$\textbf{P}(A \mid G\_{b}) = \dfrac{ \textbf{P}(G\_{b} \mid A)\textbf{P}(A)}{\textbf{P}(G\_{b})} $$= \dfrac{ \dfrac{1}{2} \cdot \dfrac{1}{3}}{\dfrac{1}{2}} $$= \dfrac{1}{3}$

while on the other hand:

$\textbf{P}(C \mid G\_{b}) = \dfrac{ \textbf{P}(G\_{b} \mid C)\textbf{P}( C )}{\textbf{P}(G\_{b})} = \dfrac{ \dfrac{1}{3} \cdot 1 }{\dfrac{1}{2}} = \dfrac{2}{3}$

which resolves the question contrary to all intuition.