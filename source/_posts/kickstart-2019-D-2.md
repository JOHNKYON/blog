---
title: KickStart 2019 Round D Question B. Latest Guests
tags:
  - Computer Science
  - Algorithm
categories: Computer Science
date: 2019-08-08 15:37:08
---


## Problem Link
See [here](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000051061/0000000000161427).

## Problem

The city of Circleburg has a large circular street with N consulates along it. The consulates are numbered 1, 2, ..., N in clockwise order.

Today G guests, numbered 1, 2, ..., G will drive along the circular street for M minutes. Each guest is either a clockwise guest (denoted by the character C) or an anti-clockwise guest (denoted by the character A).

The i-th guest starts at the consulate numbered $$H_i$$ and at the end of each minute will drive to an adjacent consulate. The i-th guest starts at the j-th consulate. If that guest is:

- a clockwise guest, they will drive to the (j+1)-th consulate (unless they are at the N-th consulate, then they will drive to the 1st consulate).
- an anti-clockwise guest, they will drive to the (j-1)-th consulate (unless they are at the 1st consulate, then they will drive to the N-th consulate).

Each consulate will only remember the guest that visited them last. If there are multiple guests who visited last, then the consulate will remember all of those guests.

For each guest, determine how many consulates will remember them.

## Input
The first line of the input gives the number of test cases, T. T test cases follow. Each testcase begins with a line containing the three integers N, G and M, which are the number of consulates, the number of guests and the number of minutes respectively. Then, G lines follow. The i-th line contains the integer $$H_i$$ followed by a single character; C if the i-th guest is a clockwise guest or A if the i-th guest is an anti-clockwise guest.

## Output
For each test case, output one line containing Case #x: y1 y2 ... yG, where x is the test case number (starting from 1) and yi is the number of consulates that remember the i-th guest.

## Sample input and output
Input
```
4
5 3 2
5 C
2 A
1 A
2 4 0
1 A
1 C
1 A
1 C
3 2 10
3 C
2 A
6 1 6
4 A
```

Output
```
Case #1: 2 2 1
Case #2: 1 1 1 1
Case #3: 2 2
Case #4: 6
```

## Solution

Instead of testing you with the knowledge of smart algorithms or tricky skills, this problem is more of an engineer skill testing: there are too many corner cases that requires consideration. Thus, how to write the code so that it could be maintainable would be a real challenge.

In order to do so, I'll try to give a vague explanation of the methods we used to solve this problems, and all other details would remain in the sample code, and I wish the readers can understand it by reading the code.

### Analysis
Let's consider the clockwise guests only at the beginning. For any guest, finishing many circles would result in the same thing as finishing at most one circle, thus *m* could be reduced to a much smaller number.

Then let's look at the problem from the consulates' point of view. Each consulates only tracks it's latest visited guest, and to track that information, it's just natural to think about using **Queue** (linkedlist). Here's what we do: we keep a sliding window of length *m* starting at 0, and add each guests to queue from the furthest to closest **anti-clockwise** (for clockwise guests). And in each move, we let the sliding window move one step forward clockwise, and add the new guest in the new position into the queue if it exists; Also we poll out the guest who stands in the position which just left the window in this move, also only when such guest exists.

In each move, the one that can be remembered by a consulate is the one that is the furthest from it, which is just the tail of the queue when the queue is not empty (things are a little bit different for anti-clockwise). And be sure to only remember the furthest one between the clockwise candidate and the anti-clockwise candidate. If they're of the same distance, remember both.

Furthermore, the guests starting at the same positions would behave totally the same, thus we can group them together and track the group behavior only, and assign the results to each member in a group when the calculation is finished. Consider the situation that both *n* and *g* are large, but all *g* starts at the same position. If we track each guests independently, we'll need $$g \times m$$ time just for recording. If we track the group, we'll only need $$g + m$$.

### Code
There're too many corner cases, so I left every detail in the code, hope you can get it.

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.*;

public class Solution {
    public static void main(String[] args) {
        Solution solution = new Solution();
        Scanner in = new Scanner(new BufferedReader(new InputStreamReader(System.in)));
        int T = in.nextInt(); // Scanner has functions to read ints, longs, strings, chars, etc.
        for (int t = 1; t <= T; ++t) {
            int n = in.nextInt();
            int g = in.nextInt();
            int m = in.nextInt();
            int[] dirs = new int[g];
            int[] poses = new int[g];
            in.nextLine();
            for (int i=0; i<g; i++) {
                String[] s = in.nextLine().split(" ");
                // Reset the index from 1-based to 0-based
                poses[i] = Integer.parseInt(s[0])-1;
                dirs[i] = (s[1].equals("C"))? 1: -1;
            }
            int[] ans = solution.solve(n, g, m, dirs, poses);
            System.out.print("Case #" + t + ":");
            for (int i: ans) {
                System.out.print(" " + i);
            }
            System.out.println("");
        }
    }

    private int[] solve(int n, int g, int m, int[] dirs, int[] poses) {
        if (m > n) {
            m = (m % n) + n;
        }
        int[] ans = new int[g];
        int[] clockCounts = new int[n];
        int[] antiCounts = new int[n];

        boolean[] clockGuests = new boolean[n];
        boolean[] antiGuests = new boolean[n];

        // Group quests with same starting positions.
        initializeGuestsArray(clockGuests, antiGuests, g, dirs, poses, ans);

        // Travel each consulate in a clockwise order
        LinkedList<Integer> queueClock = new LinkedList<>();
        LinkedList<Integer> queueAnti = new LinkedList<>();
        int clockTail =  -m;
        while (clockTail < 0) {
            clockTail += n;
        }
        int antiHead = m % n;
        int antiTail = 0;
        for (int i=0; i<n; i++) {
            if (i == 0) {
                initializeWindow(queueClock, clockGuests, n, m);
                initializeAntiWindow(queueAnti, antiGuests, n, m);
            } else {
                // Move window
                if (!queueClock.isEmpty() && queueClock.peek() == clockTail) {
                    queueClock.poll();
                }
                if (clockGuests[i]) {
                    queueClock.offer(i);
                }
                clockTail++;
                if (clockTail == n) {
                    clockTail = 0;
                }

                // Anti window moving
                if (!queueAnti.isEmpty() && queueAnti.peek() == antiTail) {
                    queueAnti.poll();
                }
                antiHead++;
                if (antiHead == n) {
                    antiHead = 0;
                }
                if (antiGuests[antiHead]) {
                    queueAnti.offer(antiHead);
                }
                antiTail++;
                if (antiTail == n) {
                    antiTail = 0;
                }
            }
            // Record the result of this step.
            recordStepResult(queueClock, queueAnti, i, n, m, clockCounts, antiCounts);
        }

        // Get answers
        for (int i=0; i<g; i++) {
            if (ans[i] < 0) {
                ans[i] = antiCounts[-(ans[i]+1)];
            } else {
                ans[i] = clockCounts[ans[i]];
            }
        }
        return ans;
    }

    private void recordStepResult(LinkedList<Integer> queueClock, LinkedList<Integer> queueAnti,
                                  int i, int n, int m, int[] clockCounts, int[] antiCounts) {
        int clockDis = -1, antiDis = -1;
        if (!queueClock.isEmpty()) {
            clockDis = i - queueClock.peek();
            // If one quest visited it multiple times,
            // the distance would be within m and as large as possible
            while (clockDis + n <= m) {
                clockDis += n;
            }
        }
        if (!queueAnti.isEmpty()) {
            antiDis = queueAnti.peekLast() - i;
            if (antiDis < 0) {
                antiDis += n;
            }
            // If one quest visited it multiple times,
            // the distance would be within m and as large as possible
            while (antiDis + n <= m) {
                antiDis += n;
            }
        }
        int dis = Math.max(antiDis, clockDis);
        if (dis != -1) {
            if (dis == clockDis) {
                addIntoAnswer(clockCounts, queueClock.peek());
            }
            if (dis == antiDis) {
                addIntoAnswer(antiCounts, queueAnti.peekLast());
            }
        }
    }

    private void initializeAntiWindow(LinkedList<Integer> queueAnti, boolean[] antiGuests, int n, int m) {
        for (int i=0; i<=m; i++) {
            int pos = (i >= n)? i % n: i;
            if (antiGuests[pos]) {
                queueAnti.offer(pos);
            }
        }
    }


    private void initializeWindow(LinkedList<Integer> queue, boolean[] clockGuests, int n, int m) {
        int pos = 0 - m;
        while (pos < 0) {
            pos += n;
        }
        while (m-- >= 0) {
            if (clockGuests[pos]) {
                queue.offer(pos);
            }
            pos++;
            if (pos == n) {
                pos = 0;
            }
        }
    }

    private void initializeGuestsArray(boolean[] clockGuests, boolean[] antiGuests,
                                       int g, int[] dirs, int[] poses, int[] ans) {
        for (int i=0; i<g; i++) {
            int dir = dirs[i], pos = poses[i];
            if (dir == -1) {
                antiGuests[pos] = true;
                ans[i] = -pos - 1;
            } else {
                clockGuests[pos] = true;
                ans[i] = pos;
            }
        }
    }

    private void addIntoAnswer(int[] counts, int pos) {
        counts[pos]++;
    }
}
```