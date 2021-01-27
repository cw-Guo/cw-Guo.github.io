---
title: 并查集的二三事
date: 2021-01-11 20:24:41
tags: Union Find
---
#### 由头
这几天在忙申请，没有什么时间写题。这个月每日一题集中考察并查集，觉得自己掌握的还不行，这篇文章简单总结一下并查集的相关知识。

#### 并查集概述
并查集是一种树形的数据结构，用于处理一些不交集(Disjoint Sets)的合并以及查询问题。 有一个Union - Find 算法。
- find: 确定元素属于那个子集。可以用来确定两个元素是否属于同一个子集
- Union: 将两个子集合并成一个集合

并(Union), 即合并
查(Find), 即查找
集(Set), 代表这是一个以字典为基础的数据结构。

典型应用：有关联通分量
并查集解决添加、合并、查找的时间复杂度都是O(1), 因此其优势在于可以应用在在线算法中。

##### 基本构成
我们上面说到，并查集是一种树形的数据结构。其特别之处在于：用字典来形成一个“树”的结构。字典的键是子节点，值是父节点。
对于每个节点，parent[x]指向x在树形结构上的父亲节点，如果是根节点，这个指针指向自己。

此外，并查集的重要思想在于，用集合中的一个元素代表集合。

``` python
class UnionFind:
    def __init__(self, M):
        self.parent = {}
        self.size = {}
        self.cnt = 0
        # init 
        # 一开始有M 个节点
        for i in range(M):
            self.parent[i] = i
            self.cnt += 1
            self.size[i] = 1

    #返回根节点
    def find(self, x): #我的理解：降低高度。
        if x != self.parent[x]: #不是根节点 路径压缩, 递归完成
            self.parent[x] = self.find(self.parent[x])
            return self.parent[x]
        return x 
    
    def find_v2(self, x): # 迭代完成，路径压缩
        
        while x != self.parent[x]:
            self.parent[x] = self.parent[self.parent[x]] # 更新x的parent
            x = self.parent[x]
        return x
    # 合并
    def union(self, p, q):
        if self.connected(p, q): return #本来就是连通的

        rootP = self.find(p)
        rootQ = self.find(q)
        #小树接到大树下面 比较平衡
        if self.size[rootP] < self.size[rootQ]:
            self.parent[rootP] = rootQ
            self.size[rootQ] += self.size[rootP]
        else:
            self.parent[rootQ] = rootP # 以rootP 为根节点
            self.size[rootP] += self.size[rootQ]

        self.cnt -= 1 # 合并后连通分量 -1
    
    def connected(self, p, q):
        return self.find(p) == self.find(q)

```

##### Union 合并
如果两个节点需要合并，则让其中一个节点的根节点接到另一个节点的根节点上。
``` python
def union(self, p, q):
    if self.connected(p, q): return  ## 如果两个节点是联通的
    self.parent[self.find(p)]  = self.find(q)

```

##### 平衡性优化： size 的作用
当我们在合并的时候，如果只是简单的把其中一个节点的根节点接到另一个节点的根节点上。那么可能会出现头重脚轻的情况。也即不平衡。
当不平衡的时候，我们的核心操作 `find`的效率可以逼近O(n).

为了优化，我们用一个字典存储每个树的节点数目， 小树接到大树下面 比较平衡


#### 带权并查集

#### 进阶： Kruskal算法，最小生成树
这部分基于 Leetcode：1584. [连接所有点的最小费用](https://leetcode-cn.com/problems/min-cost-to-connect-all-points)

##### 题目
给你一个points 数组，表示 2D 平面上的一些点，其中 points[i] = [xi, yi] 。

连接点 [xi, yi] 和点 [xj, yj] 的费用为它们之间的 曼哈顿距离 ：|xi - xj| + |yi - yj| ，其中 |val| 表示 val 的绝对值。

请你返回将所有点连接的最小总费用。只有任意两点之间 有且仅有 一条简单路径时，才认为所有点都已连接。

#### 分析
第一想法，考虑是否能够用递归。但是这个问题并不具有子结构，因为任意一个节点的加入和删除，都会影响到解。同样的由于不具有子结构，所以动态规划也不能使用。

能够满足任意两点之间有且仅有一条简单路径只有树，且这棵树包含 n 个节点。我们称这棵树为给定的图的生成树，其中总权值最小的生成树，我们称其为最小生成树

Kruskal 算法是一种常见并且好写的最小生成树算法。该算法的基本思想是从小到大加入边，是一个贪心算法。
其算法流程为：
- 将图 G={V,E} 所有的边从小到大进行排序
- 初始化图为 {V, ∅}, 扫描步骤1中的边，如果连接了两个不连通的顶点，那么我们把他加入到图中去。
- 直到最后结束

这边我们会用到并查集维护点的连通性。
整体思路为：我们首先将这张完全图中的边全部提取到边集数组中，然后对所有边进行排序，从小到大进行枚举，每次贪心选边加入答案。使用并查集维护连通性，若当前边两端不连通即可选择这条边

##### 代码
```python
class DisjointSetUnion:
    def __init__(self, n):
        self.n = n
        self.rank = [1]*n # 重量
        self.f = list(range(n)) # father
    
    def find(self, x):
        if self.f[x] == x:
            return x
        self.f[x] = self.find(self.f[x])
        return self.f[x]
        
    def unionSet(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)
        if root_x == root_y:
            return False
        if self.rank[root_x] < self.rank[root_y]:
            self.f[root_x] = root_y
            self.rank[root_y] += self.rank[root_x]
        else:
            self.f[root_y] = root_x
            self.rank[root_x] += self.rank[root_y]
        self.n -= 1
        return True

class Solution:
    def minCostConnectPoints(self, points):
        distance = lambda x,y: abs(points[x][0] - points[y][0]) + abs(points[x][1] - points[y][1])
        
        n = len(points)
        dsu = DisjointSetUnion(n)
        edges = list()

        for i in range(n):
            for j in range(i + 1, n):
                edges.append((dist(i, j), i, j))
        
        edges.sort()

        res, num = 0, 1
        for length, x, y in edges:
            if dsu.unionSet(x, y):
                res += length
                num += 1
                if num == n:
                    break
        
        return res

```


#### 参考资料
leetcode DisjointSetUnion

https://leetcode-cn.com/problems/smallest-string-with-swaps/solution/1202-jiao-huan-zi-fu-chuan-zhong-de-yuan-wgab/
https://leetcode-cn.com/problems/smallest-string-with-swaps/solution/jiao-huan-zi-fu-chuan-zhong-de-yuan-su-b-qdn9/
https://leetcode-cn.com/problems/smallest-string-with-swaps/solution/python-bing-cha-ji-dfsbfssan-chong-jie-f-yy89/

https://github.com/azl397985856/leetcode/blob/master/thinkings/union-find.md
https://blog.csdn.net/m0_43609475/article/details/112314898