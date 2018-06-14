---
title: Dirichlet Process 1 - Bernoulli Process and Bayes Theorem
tags:
  - Mathematics
  - Statistics
categories:
  - Math
date: 2018-06-14 19:02:24
---


# What is Dirichlet Process

**Dirichlet Process** is a concept in probability theory, in fact, they are a family of stochastic processes whose realizations (or observations) are probability distributions. It is often used in Bayesian inference to describe the prior knowledge about the distribution of random variables -- how likely it is that the random variables are distributed according to one or another particular distribution.

# Why Dirichlet Process

In information search, NLP and many other fields, DP are widely used in topic models. Meanwhile, in the interpretability field, Dirichlet process has a very important property: It can be generalized into infinite dimensions, and keep itself still computable.

---------------
Before we started our introduction to Dirichlet Process, I'd like to start with **Bernoulli Process** and **Beta Distribution**, for they are the two-dimension entrance of the relatively complex Dirichlet Distribution and Dirichlet Process.

# Bernoulli Process

**Bernoulli Trail**

$$f(q)=\{
    \begin{aligned}
    0, && prob=q\\
    1, && otherwise
    \end{aligned}  
$$

We can consider this trail as tossing a coin, but this coin can be unbalanced, which means there's a possibility of $q (0\leq q \leq 1)$ to be head up, rather than 50%. We take head as $X=1$ and tail as $X=0$.

And **Bernoulli Process** is repeating a series of independent Bernoulli trail. By repeating the Bernoulli trail n times, we record the counting of $X=0$ as k.

Now consider a situation, that we wanted to know how unbalanced the coin is, so we tossed it n times and counted the k, now how do we find the probability of getting a head (which means $X=0$).

Well we'd always hope we can toss it forever and at last $k/n$ would of course convergence to q. But in real life, in most cases we can not try it so many times. For instance, we can only toss the coin four times, and you get "*head head tail tail*". Well, here $k/n$ is absolutely unreliable, so we can just give it a guess, and that we can only say "**it sounds more reasonable if q is a certain value in $[ 0,1]$**". And we can not say "Yes, it is the q value."

Consider if we get *{head, head, tail, tail}* in our four trails, absolutely we'd 0.5 would be a reasonable guess, and 0.2 or 0.8 would sound kind of unlikely to be, 0.05 and 0.95 would be totally a joke to most people.

So as guys who love math and computer science, we'd like to have some tools in math to describe this, like use a probability density function. Here we see another problem, we want to update this function based on the prior we already have if we keep trying and get new observations. Naturally, we'd feel **Bayes theorem** would fit this well, because Bayes can update probability with continuous observations, and every time we update all we need is the prior of the previous status. Now we describe this problem with a more formal language.

When tossing a coin, we get a random sample $X=(x_1, x_2, ..., x_n)$, now we want to estimate the reasonable value of q in $[0,1]$ based on these n observations. Since we can't use a single certain value to describe q, we use a distribution function to do the job: a probability density function about q. Definitely we'd write this into a conditional probability distribution: $f(q|X)$, because we're guessing q under the condition of observing X.

Now let's have a look a **Bayes Theorem**"
$$P(q|x)P(x)=P(X=x|q)P(q)$$

$P(q)$ is the prior probability about q, $P(q|x)$ is the posterior probability. Notice that we are saying "probability" rather than "probability density function". In order to combine Bayes theorem with probability density function, we can get $f(q)$ from $P(q)$, then get $f(q|x)$ from $P(q|X)$.

Here $P(x)$ is a constant value, so we can have a conclusion, that is **The posterior probability density function $f(q|x)$ of p is proportional to it's prior $f(q|X)$ times a conditional probability**, which is:
$$f(q|x) \sim P(X=x|p)f(q)$$

With this result, let's go back to the coin problem.

By tossing the coin n times, it would be a Bernoulli process,  so when q is constant, the result of the n times tossing are certain,and we get an observation of k times tail, we can describe it as $P(X=x|p)=q^k(1-q)^{n-k}$.
So $f(q|x)$ is proportional to prior and the conditional probability above, which we say:
$$f(q|x) \sim q^k(1-q)^{n-k}f(q)$$

Now let's have a look at $f(q)$. Naturally, when we know nothing about the coin, we should consider the q can be any value in $[0,1]$. So we'd prefer to think $f(q)$ to be uniformly distributed in this interval, which is $f(q)=1$ for q in $$[0,1].

Now we can see in the formula $f(q|x) \sim q^k(1-q)^{n-k}f(q)$. $q^k(1-q)^{n-k}$ times a $[0,1]$ uniform distribution would result in a **Beta Distribution**.

# What's next
In this chapter we discussed the Bernoulli Process and how it was combined with Bayes Theorem, it may still look a little bit far from Dirichlet Process, but they are very fundamental knowledge and with a little bit variation, we will get our Dirichlet.

In the next chapter, we'll discuss about Beta Distribution, talk a little bit about an important property called **conjugate**, which indeed made Dirichlet process so important.