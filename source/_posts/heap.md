---
title: heap
date: 2020-12-30 22:35:25
tags: Algorithm
hide: true
---

本篇文章主要基于一道算法题，梳理一下堆的相关知识。

#### Leetcode 1046 最后一块石头的重量
[这里](https://leetcode-cn.com/problems/last-stone-weight/)可以看到原题。

有一堆石头，每块石头的重量都是正整数。

每一回合，从中选出两块 最重的 石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，且 x <= y。那么粉碎的可能结果如下：

- 如果 x == y，那么两块石头都会被完全粉碎；
- 如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y-x。

最后，最多只会剩下一块石头。返回此石头的重量。如果没有石头剩下，就返回 0。

#### 分析
第一想法当然是按照语言描述模拟这个过程：
- 排序
- 执行“粉碎”过程。
- 重复上述过程直到只剩一个数或均为零

可以很明显地察觉到时间复杂度过高，每次循环都需要对数组进行重新排序。时间复杂度在$O(n^2lgn)$

一个较为直接的优化方法就是简化这个重新排序的过程：维护一个大顶堆。

#### 堆

堆本质上并不是一个树，它利用完全二叉树的结构来维护一组数据。我们可以认为堆是一个数组。
每个节点 A[i]的左右两个子节点为:
- A[2i+1]
- A[2i+2]

实际上，i + 1 的二进制表示可以认为是从根节点到元素A[i]的路径（第一个bit总为1）
eg: 3+1（4）的二进制表示为：100，也即，从根节点出发连续左移两位
由此我们也可以推得：A[2 - 1] 的左子树为A[4 - 1] (4 = 2 * 2 也即向左移动一位)

- 2 = 1 + 1
- 4 = 3 + 1
- 8 = 7 + 1

堆有两种：小根堆和大根堆。
需要注意的是，这两个名称都是说根节点的属性，根节点是最大还是最小。（对于每一个子结构都成立）
与其它非根节点的大小没有关系。节点的大小和节点的下标没有关系。

##### 堆和其他树形结构的区别

- 内存占用
  普通树包含有指针结构，而堆则是数组，不用指针
- 平衡
  二叉搜索树必须是“平衡”的情况下，其大部分操作的复杂度才能达到O(log n)
  但堆并不需要保持这种数据间的有序性，依旧可以保证O(log n) 的性能
- 搜索性能
  在二叉树中搜索会很快，但是在堆中搜索会很慢。
  使用堆的目的是将最大（或者最小）的节点放在最前面，从而快速的进行相关插入、删除操作。


#### 堆的实现
``` python
class Heap:
    def __init__(self, desc = False): # 初始化，构建一个小顶堆
      self.heap = []
      self.desc = desc
    
    @property
    def size(self):
      return len(self.heap)

    def top(self):
    """
        返回堆顶
    """
      if self.size:
        return self.heap[0]
      return None
    
    def push(self, item):
      """
        向堆中添加一个元素
      """
      self.heap.append(item)
      self._sift_up(self.size - 1)
    
    def pop(self):
      """
        弹出堆顶
        第一步，记录堆顶元素的值
        第二步，交换堆顶元素与末尾元素
        第三步，删除数组末尾元素
        第四步，新的堆顶元素向下调整
        第五步，返回答案
      """
      self._swap(0, self.size - 1)
      item = self.heap.pop()
      self._sift_down(0)
      return item

    def _smaller(self, lhs, rhs):
      """ RHS: Right-Hand-Side
          LHS: Left-Hand-Side
          此处的逻辑和 sift up 和 sift down 相关
      """

      return lhs > rhs if self.desc else lhs < rhs

    def _sift_up(self, index):
      """
      向上调整。
      在末端插入元素时调用这个函数
      对于小根堆：如果子节点小于父节点 那么需要交换 如上：返回 lhs < rhs
      对于大根堆，如果子节点大于父节点 那么需要交换
      """
      while index: # start from self.size - 1
        parent = (index - 1)// 2
        if self._smaller(self.heap[parent], self.heap[index]): # 子节点大 不需要操作
          break
        
        self._swap(parent, index)
        index = parent
 
    def _sift_down(self, index):
      """
      向下调整。
      在pop的时候调用这个函数
      对于小根堆：如果子节点小于父节点 那么需要交换 如上：返回 lhs < rhs
      对于大根堆，如果子节点大于父节点 那么需要交换
      """
      while 2 * index + 1 < self.size:
        smallest = index 
        left = 2 * index + 1
        right = 2 * index + 2

        if self._smaller(self.heap[left], self.heap[smallest]):
          smallest = left
        if right < self.size and self._smaller(self.heap[right], self.heap[smallest]):
          smallest = right
        
        if smallest == index:
          break
        
        self._swap(index, smallest) # 交换之后，index 的位置成了最小的，但是smallest 位置的大小不确定
        index = smallest

    def _swap(self,i ,j):
      self.heap[i], self.heap[j] = self.heap[j], self.heap[i]

```

#### 堆排序
堆排序（Heapsort）是指利用堆这种数据结构所设计的一种排序算法。堆排序可以说是一种利用堆的概念来排序的选择排序。
由于堆的内部顺序并不确定，确定的只有堆顶。

##### 直观方法
最基本的方法是：
 - 构造得到一个堆
 - 不断从堆中pop出堆顶
 - 将上述值存下来，最终输出

缺点：需要额外的内存存储。

  ``` python
    def heap_sort(lst):
      
      if not lst:
        return lst
      out = []
      # construct a heap
      heap = Heap()
      for elem in lst:
        heap.push(elem)
      
      while heap.size > 0:
        out.append(heap.pop())
      return out
  ```
  
##### Inplace 的堆排序
- 堆的特性
  如果A[0, end) 是一个堆，从堆中获取最小元素并删除后，元素存放在A[0, end - 1), 而 A[end - 1] 则成为一个空位（但是它在最右边）

为了能够充分利用这个空位：
- 建立一个最大堆
- 不断获取最大值，把这个值存放在堆最后的空位上：这样以后，我们的前半部分是一个堆，后半部分是一个有序序列
- 堆为空的时候，数组就是一个排好序的序列




#### 应用
leetcode 347

原则：最大堆求前n小，最小堆求前n大。
前k小：构建一个k个数的最大堆，当读取的数小于根节点时，替换根节点，重新塑造最大堆
前k大：构建一个k个数的最小堆，当读取的数大于根节点时，替换根节点，重新塑造最小堆

#### 参考文章
[数据结构：堆](https://www.jianshu.com/p/6b526aa481b1)
[leetcode 1046 题解, by Hao Kun Yang](https://leetcode-cn.com/problems/last-stone-weight/solution/python-zui-da-dui-diao-ku-shou-xie-shi-x-utdj/)
[python手写堆（leetcode347）](https://blog.csdn.net/lmy_xxn/article/details/103800683)
[Python 堆排序](https://www.runoob.com/python3/python-heap-sort.html)
[堆排、python实现堆排](https://www.cnblogs.com/shiqi17/p/9694938.html)
[堆排序的Python实现](https://www.jianshu.com/p/d174f1862601)

[python 装饰器](https://www.liaoxuefeng.com/wiki/1016959663602400/1017451662295584)