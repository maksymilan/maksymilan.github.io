---
title: ads错题集
description: 这篇文章收集了一些关于算法和数据结构的错题，帮助大家更好地理解相关概念和技巧。
publishDate: "10 Dec 2024"
updatedDate: "12 Oct 2024"
# date: 2024-10-12 19:10:13
tags: ["algorithm", "data structure", "ads"]
---

Which of the following statements concerning a B+ tree of order *M* is TRUE?

- A. the root always has between 2 and *M* children
- B. not all leaves are at the same depth
- C. leaves and nonleaf nodes have some key values in common
- D. all nonleaf nodes have between ⌈*M*/2⌉ and *M* children

这道题选C,A选项没考虑只有根的情况，B很明显的错误，B+树的性质就是所有叶子节点都在同一深度，D选项错在没考虑根节点的特殊情况

A bipartite graph G is one whose vertex set can be partitioned into two sets A and B, such that each edge in the graph goes between a vertex in A and a vertex in B. Matching M in G is a set of edges that have no end points in common. Maximum Bipartite Matching Problem finds a matching with the greatest number of edges (over all matching).

Consider the following Gradient Ascent Algorithm:

`as long as there is an edge whose endpoints are unmatched, add it to the current matching. When there is no longer such an edge, terminate with a locally optimal matching.`

let M_1 and M_2 be matchings in a bipartite graph G. Which of the following statements is true?

A. This gradient ascent algorithm never return the maximum matching

B. Suppose that |M_1| > |2M_2|. Then there must be an edge e in M_1 such that M_2 \Union {e} is a matching in G.

C. any locally optimal matching return by the gradient ascent algorithm in a bipartite graph G is at most half as large as a maximum matching in G.

D. all of the above

------

Spanning Tree Problem: Given an undirected graph $G=(V,E)$, where $|V|=n,|E|=m$. Let $F$ be the set of all spanning trees of G. Define $d(u)$ to be the degree of a vertex $u\in V$. Define $w(e)$ to be the weight of an edge $e\in E$.

We have the following three variants of spanning tree problems:

- (1) Max Leaf Spanning Tree: find a spanning tree $T\in F$ with a maximum number of leaves.
- (2) Minimum Spanning Tree: find a spanning tree $T\in F$ with a minimum total weight of all the edges in T.
- (3) Minimum Degree Spanning Tree: find a spanning tree $T\in F$ such that its maximum degree of all the vertices is smallest.

For a pair of edges $(e,e^{'})$, where $e\in T$ and $e^{'}\in (G-T)$ such that $e$ belongs to the unique cycle of $T\cup e^{'}$, we define edge-swap$(e,e^{'})$ to be $(T-e)\cup e^{'}$.

Here is a local search algorithm:

```
T = any spanning tree in F;
while (there is an edge-swap(e,e^{'}) which reduces Cost(T)){
	T = T - e + e^{'};
}
return T;
```

Here `Cost(T)` is the number of leaves in `T` in Max Leaf Spanning Tree; or is the total weight of `T` in Minimum Spanning Tree; or else is the minimum degree of `T` in Minimum Degree Spanning Tree.

Which of the following statements is TRUE?

A. The local search always return an optimal solution for Max Leaf Spanning Tree

B. The local search always return an optimal solution for Minimum Spanning Tree

C. The local search always return an optimal solution for Minimum Degree Spanning Tree

D. For neither of the problems that this local search always return an optimal solution

------

Max-cut problem: Given an undirected graph $G=(V,E)$ with positive integer edge weights $w_e$, find a node partition$(A,B)$ such that $w(A,B)$, the total weight of edges crossing the cut, is maximized. Let us define $S^{'}$ be the neighbor of $S$ such that $S^{'}$ can be obtained from $S$ by moving one node from $A$ to $B$. We only choose a node which, when flipped, increases the cut value by at least $w(A,B)/||V$. Then which of the following is true?

A. Upon the termination of the algorithm, the algorithm returns a cut $(A,B)$ so that $2.5w(A,B) \geq w(A^*,B^*)$, where $(A^*,B^*)$ is an optimal partition.

B. The algorithm terminates after at most $O(\log{|V|}\log{W})$ flips, where $W$ is the total weight of edges.

C. Upon the termination of the algorithm, the algorithm returns a cut $(A,B)$ so that $2w(A,B)\geq w(A^*,B^*)$.

D. The algorithm terminates after at most $O(|V|^2)$ flips.

------

There are *n* jobs, and each job *j* has a processing time *t**j*. We will use a local search algorithm to partition the jobs into two groups A and B, where set A is assigned to machine *M*1 and set B to *M*2. The time needed to process all of the jobs on the two machines is$ T_1=∑_{j∈A}t_j$,$ T_2=∑_{j∈B}t_j$. The problem is to minimize $∣T_1−T_2∣$.

Local search: Start by assigning jobs 1,…,*n*/2 to *M*1, and the rest to *M*2.
The local moves are to move a single job from one machine to the other, and we only move a job if the move decreases the absolute difference in the processing times. Which of the following statement is true?

A. The problem is NP-hard and the local search algorithm will not terminate.

B. When there are many candidate jobs that can be moved to reduce the absolute difference, if we always move the job *j* with maximum *t**j*, then the local search terminates in at most *n* moves. 

C. The local search algorithm always returns an optimal solution.

D. The local search algorithm always returns a local solution with $\frac{1}{2}T_1≤T_2≤2T_1$.

## 期末刷题

知识点

- 倒排索引中的recall和precision的定义
- 用并行算法计算前缀和
- NP问题的计算复杂性比较
- 近似比的定义
- AVL树leftist heap等数据结构操作的时间复杂度
- 外部排序
- $\alpha-\beta$剪枝
- 并行算法进行归并排序
- 并行算法求最大值
- splay树的zig-zig,zig-zag
- 01背包的近似算法
- 贪心策略的要素

题目

- Suppose that the replacement selection is applied to generate longer runs with a priority queue of size 3. Given the sequence of numbers { 81, 94, 11, 96, 12, 35, 17，99，28， 58， 41，75，15}. Then 15 will belong to the 2nd run.
- In a Turnpike Reconstruction problem, given the distance set *D*={1,2,2,3,3,3,5,5,6,8}, there is a solution that does not include a point at position 6.