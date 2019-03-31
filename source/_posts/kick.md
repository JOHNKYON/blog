---
title: Kick Start2019 Round A Question A. Training
tags:
  - Computer Science
  - Algorithm
categories: Computer Science
date: 2019-03-31 23:07:37
---

## Problem
As the football coach at your local school, you have been tasked with picking a team of exactly P students to represent your school. There are N students for you to pick from. The i-th student has a skill rating Si, which is a positive integer indicating how skilled they are.

You have decided that a team is fair if it has exactly P students on it and they all have the same skill rating. That way, everyone plays as a team. Initially, it might not be possible to pick a fair team, so you will give some of the students one-on-one coaching. It takes one hour of coaching to increase the skill rating of any student by 1.

The competition season is starting very soon (in fact, the first match has already started!), so you'd like to find the minimum number of hours of coaching you need to give before you are able to pick a fair team.

## Input
The first line of the input gives the number of test cases, T. T test cases follow. Each test case starts with a line containing the two integers N and P, the number of students and the number of students you need to pick, respectively. Then, another line follows containing N integers Si; the i-th of these is the skill of the i-th student.

## Output
For each test case, output one line containing `Case #x: y` , where x is the test case number (starting from 1) and y is the minimum number of hours of coaching needed, before you can pick a fair team of P students.

## Sample
Input 
```
3
4 3
3 1 9 100
6 2
5 5 1 2 3 4
5 5
7 7 1 7 7
```
 
 
Output 
 
```
Case #1: 14
Case #2: 0
Case #3: 6
```
  
## Solution
This is another question that requires a certain property in a fixed amount of elements among a long array. For this kind of problem, it's pretty natural to think that a sliding window would be a great solution, for most time, a sliding window algorithm only takes O(n) time.

Back to this problem, it's easy to think that if we put all the players with similar ability together would result in a smaller training time, and to do so, we want the array to be sorted.

The algorithm is split into two parts: Sort the array and track the training time required.

Say we have a window starting at index $$i$$, and we know the training time for it is $$A_i$$. We also know the the last element of this window is $$a_{i+k-1}$$, and the first element is $$a_i$$. What's the training time for the next window? The answer is $$A_{i+1} = (A_i - (a_{i+k-1} - a_i) + (a_{i+k} - a{i+k-1} * (k-1))$$ . Notice that $$a_{i+k}$$ is the new highest number.
 
Do also be aware that the number may overflows Integer, use Long instead. The code is as below:

```java 
class SolutionA {
    long train(long[] nums, int n, int k) {
        Arrays.sort(nums);
        long window = 0l;
        int i = 0;
        long high = nums[k-1];
        for (i=0; i<k; i++) {
            window += high - nums[i];
        }
        long ans = window;
        long prevHigh = high;
        for(; i<n; i++) {
            high = nums[i];
            window -= prevHigh - nums[i-k];
            window += (high - prevHigh) * (k-1);
            ans = Math.min(ans, window);
            prevHigh = high;
        }
        return ans;
    }
}
```