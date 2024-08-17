---
{"dg-publish":true,"dg-path":"Notes/Dynamic Programming.md","permalink":"/notes/dynamic-programming/"}
---


[Explore - LeetCode](https://leetcode.com/explore/featured/card/dynamic-programming/630/an-introduction-to-dynamic-programming/4034/)

- can be used to solve problems that fulfill both these criteria:
    - can be broken down into **overlapping subproblems**: smaller versions of the original problem that are reused multiple times
        - compare to a **greedy problem**, where you make the locally optimal choice at each step, but don't need to consider previous steps
    - has an **optimal substructure**: by finding optimal solutions to the subproblems, you form an optimal solution to the original problem
- because subproblems overlap, DP solutions aren't easy to parallelize, unlike divide and conquer solutions
- example: calculate the Fibonacci number *F(n)* for a given value of *n*
    - the subproblems are *F(n-1)* and *F(n-2)* - they overlap because to find both, you'll need *F(n-3)*
    - the original problem has an optimal substructure, because if you solve *F(n-1)* and *F(n-2)* you find the solution to *F(n)*
- two ways to solve a DP problem:
    - **bottom-up** (tabulation): start at the base cases and work up using **iteration**
        - for the example, start with *F(0) = 0* and *F(1) = 1*, then calculate *F(2)*, *F(3)*, etc up to *F(n)*
    - **top-down** (memoization): solve the subproblems using **recursion** from the top down, and save (**memoize**) the results of each subproblem so you don't need to repeat calculations
        - for example, to calculate *F(5)* you need to calculate *F(2)* three times, unless you save the result (typically in a hashmap)
            - ![06aaae37322e9e0ff4e72cc8dffe8ada_MD5.png](/img/user/Development/%E2%80%A2%20Attachments/06aaae37322e9e0ff4e72cc8dffe8ada_MD5.png)
    - ==bottom-up tends to be faster, but top-down is easier to write (because the order of solving subproblems doesn't matter)==
- to identify DP problems:
    - typically ask for an optimum value (minimum, maximum, longest, etc), or whether it's possible to reach a certain point
    - future steps depend on previous steps
- another example, **House Robber**:

> You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and it will automatically contact the police if two adjacent houses were broken into on the same night.
>
> Given an integer array nums representing the amount of money of each house, return the maximum amount of money you can rob tonight without alerting the police.

- for the case `[2, 7, 9, 3, 1]`, the optimal solution is `[2, 9, 1]`
- the first decision to make is whether to rob the first or second house, but this affects which options are available in future decisions
    - a greedy solution would choose to rob the second house first (since 7 > 2), but this prevents robbing the third house - ==decisions affect other decisions==
