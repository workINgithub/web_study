# 并查集

并查集是一种树形的数据结构（通常用数组来表示，下标代表节点，value代表祖先节点的下标）。它用于处理一些不交集的合并及查询问题。

### 两种操作

1. 查询

通过下标，不断的向上追溯父节点，找到祖先节点。因为中间遍历父节点没必要，所以会有一个优化操作，叫压缩路径。
**及每个树的节点都指向祖先节点**，因为我们只需要知道祖先节点是谁，而不关心谁是谁的父亲。

2. 合并

将两棵有关联的树，合并到一起，这个操作实际就是将A树的祖先，指向B树的祖先。
但为了后面查询操作的用时更小（合并操作只是让A树的祖先指向B树），就得让小的树合并到大的树里面。因为你可以想象，如果是大的树合并到小的树上去，大树下面的子节点，要花更多的时间去查询。**那这种策略就是启发式合并（按秩合并）**

### 时间复杂度

同时使用路径压缩和启发式合并之后，并查集的每个操作平均时间仅为O(a(n)，其中a为阿克曼函数的反函数，其增长极其缓慢，也就是说其单次操作的平均运行时间可以认为是一个很小的常数。

阿克曼(Ackermann函数)A(m, n)的定义是这样的
          n+1               if m = 0
A(m, n) = A(m-1, 1)         if m > 0 and n = 0
          A(m-1, A(m, n-1)) otherwise

而反Ackermann函数a(n)的定义式阿克曼函数的反函数，即为最大的整数m使得A(m, m)<= n;

### 空间复杂度
O(n), 因为我们有两个数组，长度都为n（节点数量）。

> 参考链接
  [并查集](https://oi-wiki.org/ds/dsu/#_5)
  [并查集时间复杂度分析](https://oi-wiki.org/ds/dsu-complexity/)