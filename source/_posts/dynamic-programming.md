---
title: 动态规划的二三事
date: 2021-01-09 17:04:39
tags: dynamic programming
---

这篇文章尝试整理一些简单的动态规划相关知识。

#### 由头： 从一道股票题说起
[Leetcode 123](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/). 买卖股票的最佳时机 III


给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。
注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。



#### 动态规划

##### 为什么叫“dynamic programming”？

> An interesting question is, ‘Where did the name, dynamic programming, come from?’The 1950s were not good years for mathematical research. We had a very interesting gentleman in Washington named Wilson. He was Secretary of Defense, and he actually had a pathological fear and hatred of the word, research. I'm not using the term lightly; I’m using it precisely. His face would suffuse, he would turn red, and he would get violent if people used the term, research, in his presence. You can imagine how he felt, then, about the term, mathematical. The RAND Corporation was employed by the Air Force, and the Air Force had Wilson as its boss, essentially. Hence, I felt I had to do something to shield Wilson and the Air Force from the fact that I was really doing mathematics inside the RAND Corporation. What title, what name, could I choose? In the first place I was interested in planning, in decision making, in thinking. But planning, is not a good word for various reasons. I decided therefore to use the word,‘programming.’ I wanted to get across the idea that this was dynamic, this was multistage, this was time-varying—I thought, let’s kill two birds with one stone. Let’s take a word that has an absolutely precise meaning, namely dynamic, in the classical physical sense. It also has a very interesting property as an adjective, and that is it’s impossible to use the word, dynamic, in a pejorative sense. Try thinking of some combination that will possibly give it a pejorative meaning. It’s impossible. Thus, I thought dynamic programming was a good name. It was something not even a Congressman could object to. So I used it as an umbrella for my activities.

##### 本质
动态规划问题的一般形式是求最值。比如求最长递增子序列等等
求最值的核心问题是 穷举，把所有可能结果都穷举一遍，从而找到最值。但这样做明显效率不高，动态规划就是在穷举上进行优化。
将问题分解成为具有重叠子结构的问题。从子结果来得到最终的结构。

所以使用动态规划的关键就在于如何将问题分解。
- 重叠子问题
- 最优子结构
- 状态转移方程。

可以进一步总结一些需要思考的点：
- 这个问题的base case 是什么
- 这个问题的“状态”是什么
- 对于每个“状态”，状态之间的联系是什么，可以通过什么操作进行状态间的转换
- 如何使用“备忘录”或者是 dp table存放这些状态的结果


归纳法的思想和DP很像，都是当前步骤建立在之前步骤的解的基础上
用之前累积下来的结果，去推测下一步会发生什么

##### 自顶向下：用递归来完成动态规划的过程
最典型的问题是斐波那契数列。
通项公式： f(n) = f(n - 1) + f(n - 2)

实际上就成了动态规划问题的状态转移方程。
一般解法用递归完成：
```python
    def fib(n):
        # base case:
        if n == 0：
            return 0
        if n == 1:
            return 1
        return fib(n - 1) + fib(n - 2)
    
```
但实际上我们可以发现，有些值的斐波那契值被多次计算了。是用动态规划的方法，在内存中用空间换时间，用一个数组存放目前已经计算过的值。如果这个值已经被计算了，那么就直接返回。

```python
    self.cache = {0: 0, 1: 1}
    def fib(n):
        if n in self.cache:
            return self.cache[n]
        else:
            result = self.fib(n - 1) + self.fib(n - 2)
            self.cache[n] = result
            return result
        

```
##### 自底向上：用迭代进行

#### 参考资料
[为什么叫“动态规划”](https://www.1point3acres.com/bbs/thread-689633-1-1.html)


