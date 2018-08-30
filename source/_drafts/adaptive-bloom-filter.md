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
    &\underline{Input:}\     &&Positive\ integer\ n\ (number\ of\ gold\ bricks.) \\
                            &&&Positive\ real\ numbers\ w_1,...,w_n\ (weight\ of\ each\ gold\ bricks.) \\
                            &&&Positive\ real\ number\ W\ (maximum\ weight\ our\ knapsack\ can\ carry.) \\
                            &&&\underline{Assumption:}\ w_1 < w_2 < ... < w_n \\
    &\underline{Output:}\    &&Set\ S\subseteq \{1,...,n\}\ (set\ of\ brick\                            we\ take.) \\
    &\underline{Constraint:}&&\sum_{i\in S}w_i\ (maximize\ the\ weight\ of\ gold\ we\ take.)

    
\end{aligned}
$$

For example, when $n=5,w_1=2,w_2=5,w_3=5,w_4=6,w_5=9,W=10$, the brick we should take with us should be brick 2 for brick 3, and $w_2+w_3=10=W$, then the best output is $S=\{2,3\}$. For any output that equals the best output value is called an *"Optimal Solutions"*. The best objective value (optimal value) is denoted as *OPT*, sometimes we also denote the optimal solution by $S^*$.

The above question is the simplified version of a problem called knapsack. The problem is known to be NP-hard even for the simplified version.

## Algorithm
Here we have an algorithm for the simplified version of knapsack problem.

    S = empty
    For j = i to n:
        If sum(S)+w[j] <= W:
            S.add(w[j])
        Else:
            if w[j] >= sum(S):
                S=w[j]
            break;

From