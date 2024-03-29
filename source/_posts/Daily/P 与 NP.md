---
title: P 与 NP
p: Daily/2021-07-24
date: 2021-07-24 08:24:01
tags:
---

什么是 P, NP, NP-Complete 和 NP-Hard。

首先，P 指的是一个判定问题(decision problem)能被确定性图灵机(deterministic Turing machine)在多项式时间(polynomial time)内解决。判定问题就是说问题的答案是 yes 或 no，问题的分类上还会有功能性问题(function problem), 最优化问题(optimization problem)；确定性图灵机或者图灵机是计算机的逻辑基础，就是一台计算机在数学逻辑上怎么进行计算；多项式时间表示对于问题大小为 n 的输入的复杂度为 *O(n^k)* 并且 k 为常数。
<!-- more -->  

NP(nondeterministic polynomial time)是指一组 P 问题的集合，或者是能够被不确定性图灵机在多项式时间呢解决的问题。
NP-Complete 问题是指存在一个多项式时间算法能验证当前的答案是否正确，但没有对应的多项式时间算法可以求解出答案。

听起来 NP-Complete 已经是不可解的问题了，那 NP-Hard 又是什么呢。在字面上的定义是难度至少不小于 NP-Complete。在 Quora 上看到了几个例子来说明这一点，时间问题来不及翻译了，直接 copy-paste 一下吧。

---

Assume that I have a problem (H) which I *believe* is NP-complete (solution is verifiable in PTIME). Let there be an intermediate step (subroutine S) which must be solved in order to solve for H. If S is said to be NP-complete, then H is at least as hard as S. In fact H is *harder* than S. Thus H is *harder* than a NP-complete problem. So it needs to be in a complexity class of its own and hence H belongs to a class known as the NP-hard class. However note that H is not *strictly* NP-hard. This leads us to conclude that NP-complete is a class of problems that fall under both NP and NP-hard - hardest of the NP problems is NP-complete and the hardest of NP-complete is NP-hard.

Example 1: Let us consider the classical version of TSP (Travelling Sales Person) problem - find the shortest distance between two cities on a map such that the sales person visits each city only once. This is a constrained optimization problem. To solve this one needs to solve for Hamiltonian Cycles first. In 1972, Prof. Richard Karp proved that Hamiltonian cycles are NP-complete. Now, the classical TSP is at least as hard as a NP-complete problem. Prof. Karp thus proved the NP-hardness of the classical TSP. So classical TSP is a NP-hard problem. Note that it is not *strictly* NP-hard.
(There is a decision version of this problem - given distance L between two cities, can you give a better l < L ? This requires a yes/no answer. This decision version of classical TSP is NP-complete and not NP-hard.)

Example 2: The halting problem is NP-hard. Given an input, will the Turing machine halt? A Turing machine can be given a constraint to halt - when it solves the SAT problem it can halt. The solution to SAT can be verified in PTIME and hence once the machine outputs the correct solution it halts. However, SAT is a NP-complete problem. So the *general* halting problem should be at least as hard as the SAT problem. Hence it is a NP-hard problem.


补充一下翻译：
假定问题 H 属于 NP-Complete，且求解 H 过程中有一个必经的子程序 S。若 S 是 NP-Complete 的，则 H 至少难度与 S 一致。而实际上 H 难度大于 S，即 H 是一个难度大于 NP-Complete 的问题，因此 H 需要有一个单独的复杂度分类即 NP-Hard。但 H 并不是严格的 NP-hard。根据上述可得到一个结论：NP-Complete 同时属于 NP 和 NP-Hard(难度最高的 NP 问题是 NP-Complete，难度最高的 NP-Complete 是 NP-Hard)

例一：考虑一下经典的旅行商问题 - 寻找城市间的最短路径使得通过每个城市恰好一次。这是一个约束优化问题，首先我们需要解决哈密顿环问题。Richard Karp 在 1972 年证明了哈密顿环是 NP-Complete 的。也就是旅行商问题至少是 NP-Complete 问题。实际上 Karp 教授也证明了其同样是 NP-hard 问题。

例二：停机问题(halting problem)是 NP-hard 的。给定一个输入，图灵机会在有限时间内终止吗。图灵机是否能被指定的约束而终止 - 解决布尔可满足性问题(SAT)时，由于 SAT 的答案可以在 PTIME 内验证，所以当机器输出了正确答案时可以终止。但 SAT 是一个 NP-Complete 完全问题，所以停机问题的难度应至少不低于 SAT 问题，即停机问题是 NP-Hard 的。

