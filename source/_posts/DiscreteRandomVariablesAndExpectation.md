---
title: Discrete Random Variables And Expectation 1
tags: Mathematics
categories: Math
date: 2018-03-22 15:57:45
---

# Random Variables and Expectation 1

>Definition 2.1  

*A random variable $X$ on a sample space $\Omega$ is a real-valued (measurable) function on $\Omega$: that is, $\Omega \rightarrow \mathbb{R}$. A discrete random variable is a random variable that takes on only a finite or countably infinite number of values.*

Since the random variables are functions, they are usually denoted by capital leeters, while real numbers are usually denoted by lowercase letters.

>Defination 2.2

*Two random variables $X$ and $Y$ are independent if and only if*

$$Pr((X=x)\cap(Y=y))=Pr(X=x)\cdot Pr(Y=y)$$  

*for all values x and y. Similarly, random variables $X_1$, $X_2$,.... are mutally independent if and only if, for any subset $I\subseteq[i,k]$ and any values $x_i$, $i\in I$,*

$$Pr\left(\bigcap_{i\in I}(X_i=x_i)\right)=\prod_{i \in I}Pr(X_i=x_i)$$

>Defination 2.3

*The expectation of a discrete random variable X, denoted by $E[X]$ , is given by*

$$E[X]=\sum_iiPr(X=i)$$