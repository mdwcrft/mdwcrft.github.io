---
title: "Stats #3: Laws & Theorems"
date: 2019-02-01
tags: []
excerpt: "Expectation, Variance, CLT, & LOTUS"
encoding: UTF-8
---

---
# Expectation

E[X] is the expectation of random variable X and represents the average value that X may take. This is basically the sum of each value times it's probability.  

DISCRETE:

$$E[X] = \sum_xxf_X(x)$$

CONTINUOUS:

$$E[X] = \int_{-\infty}^{\infty} xf_X(x)dx$$

> *Example*:  
> $f_X(-1) = 0.1$  
> $f_X(2) = 0.3$  
> $f_X(3) = 0.6$  
> 
> $E[X] = (1 * 0.1) + (2 * 0.3) + (3 * 0.6)$

Expectation statements can be split by addition or subtraction (*linearity of expectation*):

$$E[X_1 + X_2] = E[X_1] + E[X_2]$$

$$E[\sum_{i=1}^N X_i] = \sum_{i=1}^NE[X_i]$$

---
# Conditional Expectation

Conditional expectation is just the expectation of some variable given some other condition. The equations are the same but use the conditional distribution for probability values.

DISCRETE:

$$E[Y|X] = \sum_yyf_{Y|X}(y)$$

CONTINUOUS:

$$E[Y|X] = \int_{-\infty}^\infty yf_{Y|X}(y)dy$$

> *Example*: Expected height given gender = Male

---
# Variance

Variance gives a measure of the spread of a random variable. Variables with a large variance are generally harder to predict as the sample space is larger.  

$$var(X) = E[(x-\mu)^2]$$

where

$$\mu = E[X]$$

> *Example*:  
> P(x=1)=0.5, P(x=0) = 0.5  
>
> $$\mu = E[X] = (1*0.5) + (0*0.5) = 0.5$$
> 
> $$var(X) = E[(X-0.5)^2] = \sum_x (x-0.5)^2*0.5$$
> 
> $$var(X) = ((1-0.5)^2*0.5) + ((0-0.5)^2*0.5)$$
> 
> $$var(X) = 0.125 + 0.125 = 0.25$$

---
# Covariance

Covariance is a measure of the degree to which two random variables are linearly related. A positive covariance value represents linearly related variables.

$$cov(X,Y) = E[(X-\mu_X)(Y-\mu_Y)]$$

---
# Correlation

Correlation is a measure of the linearity of a relationship between two variables. It is the same as covariance but it also gives a measure of the direction of a linear relationship.

$$corr(X,Y) = \frac {cov(X,Y)}{\sigma_X\sigma_Y}$$

Where:

$$\sigma_X = \sqrt{var(X)}$$

$$\sigma_Y = \sqrt{var(Y)}$$

---
# Law of Large Numbers

The law of large numbers states that the more numbers in a sample the closer it's mean is to the true mean.

For a sequence of *independent and identically distributed* [iid] (independent but from the same distribution) random variables $x_1, x_2, x_3...$  
The sample mean:

$$\bar X_N = \frac1N \sum_{i=1}^N X_i$$

Then as $N \rightarrow \infty$:

$$\bar X_N \rightarrow \mu=E[X]$$

---
# Law of the Unconscious Statistician

LOTUS is a rule that is used to calculate the expectation of a function of a random variable $g(X)$. This is useful if we know the probability distribution of $X$ but don't know the distribution of $g(X)$. The equations are the same as normal expectation, with a function $g(x)$ substituted for $x$.

$$E[Y] = E[g(X)] = \int g(x)dF_X(x)$$

DISCRETE:

$$\sum_x g(x)f_X(x)$$

CONTINUOUS:

$$\int_{-\infty}^{\infty}g(x)f_X(x)dx$$

---
# Central Limit Theorem

The CLT is vitally important to data science as it can give insight from incomplete data. The CLT states that with a large sample size, sample means are normally distributed. This holds true even when the original dataset is not normally distributed. 'Large' is generally used to mean over 30 samples.

This is useful for assessing whether a particular sample is representative of the whole set. It also tells us the likelyhood that a mean came from a particular distribution.

The CLT can be used to constuct the distribution given that your sample set is non-representative, this is called the *null distribution*. The CLT says the null distribution will be normal and can be constructed from the sample set (as long as it is large enough). The null mean is the null hypothesis. The null standard deviation is the sample standard deviation divided by the sample size this is called the *standard error*.

For a sequence of iid random variables; as you tend towards a large sample size:

$$\frac {\bar X_N - \mu}{\sqrt{var(\bar X_N)}} \sim\to N(0,1)$$

$$var(\bar X_N) = \frac1{N^2}(\sum_{i=1}^N X_i) = \frac1Nvar(X_1) = \frac{\sigma^2}N$$

The sample mean minus the expectation over the variance tends to *covariance in distribution* of a *Normal Distribution* of mean 0 and variance 1. $\sim\to$ means converging to a distribution (rather than converging to a number).

This is derived from:

$$\bar X_N \sim\to N(\mu, \frac{\sigma^2}N)$$

> *Example*: If I predict a task takes 5 minutes but my sample set gives a mean of 6 minutes the CLT will show how likely it is that a sample from the null distribution will be greater or equal to 6.

---
# Inequalities

Inequalities relate probabilities to expectations by providing bounds for the CDF of  random variable.

### Markhov's Inequality

The Markhov inequality gives an upper bound to the probability of a non-negative function of a random variable exceeding some positive constant.

### Chebychev's Inequality

The Chebychev inequality guarantees that, for a probability distribution, there is an upper bound on the fraction of values that are over a certain distance from the mean. This is similar to the idea of a normal distribution but it is weaker, stating that a minimum of just 75% of values must lie within two standard deviations of the mean and 89% within three standard deviations.