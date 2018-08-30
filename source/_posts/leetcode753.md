---
title: Leetcode753. Cracking the safe
tags:
  - Leetcode
  - Algorithm
  - Graph Theory
  - Sequence Algorithm
categories:
  - Leetcode
date: 2018-08-30 22:16:49
---

This is a programming problem on leetcode, and the interesting part is, it can be solved with two different points of
view. We're going to analyze the problem, prove some properties and lemma before we use them, at last write them into code.
The problem is as below:

## Leetcode.753. Cracking the Safe

There is a box protected by a password. The password is **n** digits, where each letter can be one of the first **k** digits **0, 1, ..., k-1**.

You can keep inputting the password, the password will automatically be matched against the last **n** digits entered.

For example, assuming the password is **"345"**, I can open it when I type **"012345"**, but I enter a total of 6 digits.

Please return any string of minimum length that is guaranteed to open the box after the entire string is inputted.

## Approach \#1: Graph Theory.
It's easy to take this problem as a graph theory, Let's take any number with **n-1** digits as a vertex, to form a single and unique trial of the password, we extend a edge from this node, forming a **n** digits number. It also leads to the new node that was indicated by it's [1:n] digits. So this problem is turned in to a graph problem defined as below:

*Given a directed graph with $k^{n-1}$ nodes, each nodes has $k$ edges, pointed to another node. Find a path that visits every edges exactly once.*

Sounds familiar, right? This is a very classic question in graph theory, called [Eulerian path](https://en.wikipedia.org/wiki/Eulerian_path). By definition, a Eulerian path is a trail in a finite graph which visits every edges exactly once. Eulerian cycle is an Eulerian path that starts and ends at the same vertex. Eulerian path and Eulerian cycle have following properties:
- An undirected graph has an Eulerian cycle if and only if every vertex has even degree, and all of its vertices with nonzero degree belong to a single connected component.
- A directed graph has an Eulerian cycle if and only if every vertex has equal in degree and out degree, and all of its vertices with nonzero degree belong to a single strongly connected component.
  
Both properties are easy to prove: For the first property, for each node, it must have an edge in and an edge out, to form a Eulerian path. For the second property, it's still the same: for each node, one in, one out.

To solve this problem, we have a theory called *Hierholzer's Algorithm*. The algorithm goes as follows:
- Starting from a vertex u, we walk through (unvisited) edges until we get stuck. Because the in-degrees and out-degrees of each node are equal, we can only get stuck at u, which forms a cycle.
- Now, for any node v we had visited that has unvisited edges, we start a new cycle from v with the same procedure as above, and then merge the cycles together to form a new cycle.
  
An figure below may helps you to understand the procedure.
![](/images/leetcode753_1.png)

Now the problem seems easy to solve. All we need to do is track every edge and mark them as visited. To prevent us from getting stuck (So we don't need extra memory to store unvisited edges), we start from 000...0, and go to the next node from k-1 to 0. The code is as followed:

*java*

```java
class Solution {
    Set<String> seen;
    StringBuilder ans;

    public String crackSafe(int n, int k) {
        if (n == 1 && k == 1) return "0";
        seen = new HashSet();
        ans = new StringBuilder();

        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < n-1; ++i)
            sb.append("0");
        String start = sb.toString();

        dfs(start, k);
        ans.append(start);
        return new String(ans);
    }

    public void dfs(String node, int k) {
        for (int x = 0; x < k; ++x) {
            String nei = node + x;
            if (!seen.contains(nei)) {
                seen.add(nei);
                dfs(nei.substring(1), k);
                ans.append(x);
            }
        }
    }
}
```

## Approach \#2: Inverse Burrows-Wheeler Transform
If we are familiar with the theory of combinatorics on words, recall that a Lyndon Word L is a word that is the unique minimum of it's rotations.

One important mathematical result (due to Fredericksen and Maiorana), is that the concatenation in lexicographic order of Lyndon words with length dividing n, forms a de Bruijin sequence: a sequence where every every word (from the $k^n$​​ available) appears as a substring of length n (where we are allowed to wrap around.)

For example, when n = 6, k = 2, all the Lyndon words with length dividing n in lexicographic order are (spaces for convenience): 0 000001 000011 000101 000111 001 001011 001101 001111 01 010111 011 011111 1. It turns out this is the smallest de Bruijin sequence.

We can construct a de Bruijin sequence by Inverse Burrows-Wheeler Transform. Mathematically, an inverse Burrows—Wheeler transform on a word w generates a multi-set of equivalence classes consisting of strings and their rotations. These equivalence classes of strings each contain a Lyndon word as a unique minimum element, so the inverse Burrows—Wheeler transform can be considered to generate a set of Lyndon words. It can be shown that if we perform the inverse Burrows—Wheeler transform on a word w consisting of the size-k alphabet repeated $k^{n-1}$ times (so that it will produce a word the same length as the desired de Bruijn sequence), then the result will be the set of all Lyndon words whose length divides n. It follows that arranging these Lyndon words in lexicographic order will yield a de Bruijn sequence B(k,n), and that this will be the first de Bruijn sequence in lexicographic order. The following method can be used to perform the inverse Burrows—Wheeler transform, using its standard permutation:
- Sort the characters in the string w, yielding a new string w'
- Position the string w' above the string w, and map each letter's position in w' to its position in w while preserving order. This process defines the standard permutation.
- Write this permutation in cycle notation with the smallest position in each cycle first, and the cycles sorted in increasing order.
- For each cycle, replace each number with the corresponding letter from string w' in that position.
- Each cycle has now become a Lyndon word, and they are arranged in lexicographic order, so dropping the parentheses yields the first de Bruijn sequence.

For example, to construct the smallest B(2,4) de Bruijn sequence of length 24 = 16, repeat the alphabet (ab) 8 times yielding w=abababababababab. Sort the characters in w, yielding w'=aaaaaaaabbbbbbbb. Position w' above w as shown, and map each element in w' to the corresponding element in w by drawing a line. Number the columns as shown so we can read the cycles of the permutation:
![](/images/leetcode753_2.png)

Starting from the left, the cycles are: (1) (2 3 5 9) (4 7 13 10) (6 11) (8 15 14 12) (16).

Then, replacing each number by the corresponding letter in w' from that column yields: (a)(aaab)(aabb)(ab)(abbb)(b).

These are all of the Lyndon words whose length divides 4, in lexicographic order, so dropping the parentheses gives B(2,4) = aaaabaabbababbbb.

The code is as followed:
```java
class Solution {
    public String crackSafe(int n, int k) {
        int M = (int) Math.pow(k, n-1);
        int[] P = new int[M * k];
        for (int i = 0; i < k; ++i)
            for (int q = 0; q < M; ++q)
                P[i*M + q] = q*k + i;

        StringBuilder ans = new StringBuilder();
        for (int i = 0; i < M*k; ++i) {
            int j = i;
            while (P[j] >= 0) {
                ans.append(String.valueOf(j / M));
                int v = P[j];
                P[j] = -1;
                j = v;
            }
        }

        for (int i = 0; i < n-1; ++i)
            ans.append("0");
        return new String(ans);
    }
}
```
-------
The most attractive part is that, at the first look into this problem, this does not look like a graph problem or a description problem. However, we can use the method from two different aspect to help us understand the problem in different point of view. If we look deeper into it, we can even found that, description, text problems and graph theories are closely related to each other, which showed the beauty of math and computer science.