---
title: "Stats #1: Probability"
date: 2019-02-01
tags: []
excerpt: "Definitions & Random Variables"
encoding: UTF-8
---

# Definitions

### Spaces

|Name|Symbol|Description|
|---|---|---|
|Sample Space|$\Omega$|Set of all possible outcomes|
|Singletons|$\omega$|Individual element from sample space|
|Event|A|A subset of the sample space|

### Probability Types

|Name|Symbol|Description|
|---|---|---|
|Marginal Probability|P(A)|Probability of event A|
|Joint Probability|P(A$\cap$B)|Probability of event A AND B|
|Conditional Probability|P(A\|B)|Probability of event A GIVEN B|
|'or' Probability|P(A$\cup$B)|Probability of A OR B|

### Probability Axioms

1. Non-Negativity: $P(A) \geq 0$
2. Probability of all events adds to 1: $P(\Omega) = 1$
3. Disjoint additivity: For mutually exclusive A1 & A2; $P(A1 \cup A2) = P(A1) + P(A2)$

### Symbols

\* = Best  
^ = Estimated from data

$x_j^{(i)}$ = The $j$th predictor (col) of the $i$th observation (row) of $x$

---
# Probabilities on Finite Sample Spaces

### Marginal Probability

The probabilty of any single event happening is just the event space in question divided by the total event space. Suppose $\Omega$ has finite size and P($\omega$) for all singletons is *constant*.  
$$P(A) = \frac{size(A)}{size(\Omega)}$$

> *Example*: Probability of drawing a club from deck of cards is $\frac{size(clubs)}{size(deck)} = \frac{13}{52} = \frac14$

### The General Multiplication Rule

The general mulitplication rule is an equation that links all types of probability (marginal, joint, & conditional).

$$P(A|B) = \frac{P(A \cap B)}{P(B)}$$

> *Example*: Probability of drawing a 4 given a set of clubs is $\frac{P(4 \cap clubs)}{P(clubs)} = \frac{\frac{1}{52}}{\frac{1}{4}} = \frac1{13}$

### Independence

Two events are independent if the occurance of one has no effect on the probability of the other.  

Conditional:  

$$P(A|B) = P(A)$$  

Joint:  

$$P(A \cap B) = P(A) P(B)$$  

> *Example*: Probability of drawing an ace then flipping a coin on heads is $P(ace \cap H) = P(ace) * P(H) = \frac14 * \frac12 = \frac18$

### 'OR' Probability

If A and B are **dependent** the probability of either A or B happening is the addition of their individual probabilities minus the intersecting joint probability (otherwise it would be accounted for twice).

$$P(A \cup B) = P(A) + P(B) - P(A \cap B)$$

> *Example*: Probability of rolling a 6 OR flipping a heads is $P(6) + P(H) - P(6 \cap H) = \frac16 + \frac12 - \frac1{12} = \frac7{12}$

If A and B are **independent** then you don't have to minus the intersection as there is none.

---
# Random Variables

A random variable is a function mapping elements of $\Omega$ to $\mathbb R$ (set of real numbers). The mapping is denoted by X.  

$X:$  

$$\Omega \rightarrow \mathbb R$$  

$$\omega \rightarrow X(\omega)$$

> *Example*: Where $\omega$ is the results of two coin tosses and $X(\omega)$ is the number of heads.
> 
> |$\omega$|$X(\omega)$|
> |---|---|
> |HH|2|
> |HT|1|
> |TH|1|
> |TT|0|

### Probabilities on Random Variables

Probabilities on random variables can be described using the *inverse image* function. The inverse function of a mapping X can be described as the $\omega_i$ values for which $X(\omega_i)$ is in the positive set s.

$$
P(X \in s) = P(X^{-1}(s)) = P(\{\omega_i|X(\omega_i)\})
$$

> *Example*: Where the mapping X is heads in a two-coin toss, find P(X=1):  
> Here the set s=1 and we must find the set of i's for which $X(\omega_i) = 1$
> $$P(X=1) = P(X^{-1}(s)) = P(\{\omega_i|X(\omega_i)=1\}) = P(\{HT,TH\}) = \frac{1}{2}$$