---
title: DisjointSetUnion
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



###### 压缩路径

leetcode DisjointSetUnion

https://leetcode-cn.com/problems/smallest-string-with-swaps/solution/1202-jiao-huan-zi-fu-chuan-zhong-de-yuan-wgab/
https://leetcode-cn.com/problems/smallest-string-with-swaps/solution/jiao-huan-zi-fu-chuan-zhong-de-yuan-su-b-qdn9/
https://leetcode-cn.com/problems/smallest-string-with-swaps/solution/python-bing-cha-ji-dfsbfssan-chong-jie-f-yy89/

https://github.com/azl397985856/leetcode/blob/master/thinkings/union-find.md
https://blog.csdn.net/m0_43609475/article/details/112314898