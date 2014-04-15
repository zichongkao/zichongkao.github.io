---
layout: post
title:  "When is the Greedy Algorithm optimal for solving the Change-Making Problem?"
date:   2014-01-20 02:54:29
categories: data_science
---


The change-making problem involves determining the fewest possible coins required to represent a given value. In the greedy algorithm, we repeatedly pick the largest acceptable denomination until we make up the given value. The greedy algorithm always finds the optimal solution for some denominations of coins like 1, 5, 10 and 25 cents. However, for others like 1, 5 and 6 cents, the greedy algorithm is not optimal: It makes 20 cents using 5 coins (6+6+6+1+1) while the optimal only requires 4 (5+5+5+5).

The question is thus: When is the Greedy Algorithm always optimal for solving the Changing Making Problem?


History of the Problem
======================
Chang and Gill (1970): Let 1 = c_1 < ... < c_m be any system of coins and x be the value we seek to give change for. The greedy solution is always optimal once we have checked that it is optimal over the range:
c_3 < x < c_m(c_m c_{m-1} + c_m - 3 c_{m-1})/c_m - c_{m-1}

Kozen and Zaks (1994) tighten the bounds to c_3 + 1 < x < c_m + c_{m-1}. We can now check for optimality in linear rather than cubic time with regard to the denomination of the largest coin. But each test will take O(n) arithmetic operations, giving an O(n c_m) algorithm.

Pearson (1994) derives O(n^2) possible values which contain smallest counter example. Each can be tested with O(n) arithmetic operations, giving O(n^3) algorithm.

In 2009 and 2010, there were a couple of new papers, but their work seem to be mostly focused on extensions of the core problem. 

http://www.cs.cornell.edu/~kozen/papers/change.pdf

