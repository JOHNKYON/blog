---
title: Knapsack and Adaptive Bloom Filter
tags: 
    - Computer Science
    - Algorithm
categories: Computer Science
---

**Bloom Filter** is a space-efficient data structure, which offers constant time method to test whether an element is a member of a set. The false is always correct, but if it returns a *True*, there's a possibility for that to be a "False Positive". Here, we start from a classic problem called **"Knapsack Problem"**, and we'll show you how to optimize the Bloom filter from the "Knapsack Problem".

# Knapsack Problem
## Definition
Here we have a knapsack, whose capability of weight is $W$, and we have $n$ gold bricks, whose weights ad denoted by $w_1,...w_n$. We want to carry as much gold as possible, 

*Input:*

    Positive integer n

$$
\begin{aligned}
    \underline{Input:}\     &\ Positive\ integer\ n\ (number\ of\ gold\ bricks.) \\
                            &\ Positive\ real\ numbers\ w_1,...,w_n\ (weight\ of\ each\ gold\ bricks.) \\
                            &\ Positive\ real\ number\ W\ (maximum\ weight\ our\ knapsack\ can\ carry.) \\
                            &\ \underline{Assumption:}\ w_1 < w_2 < ... < w_n \\
    \underline{Output:}\    &\ Set\ S\subseteq \{1,...,n\}\ (set\ of\ brick\                            we\ take.) \\
    \underline{Constraint:}\ &\sum_{i\in S}w_i\ (maximize\ the\ weight\ of\ gold\ we\ take.)

    
\end{aligned}
$$