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

#### 股票题的解
由于有2次交易机会，
我们可以认为每一天都可以有以下五种状态： 
- 手上没有股票，但是有2次交易机会，可以买入  
- 手上有股票，并且还剩下一次交易机会，可以卖出
- 手上没有股票，但是有一次机会，可以买入
- 手上有股票，并且还剩下零次交易机会，可以卖出
- 手上没有股票，但是没有交易机会，此时等于 i-1天的最高收益 （收益完全由昨天的收益确定）
其中最后一种情况对我们意义不大，因为它的可能的最大收益完全由前一天决定

我们这里用四个变量记录这四个有意义的状态。
 - buy1: 执行第一次买
 - sell1：执行第一次卖
 - buy2： 执行第二次买
 - sell2：执行第二次卖


```python
def maxProfit(self, prices: List[int]) -> int:
    n = len(prices)

    buy1 = buy2 = - prices[0] # 因为要购买股票所以收益是负的
    sell1 = sell2 = 0

    for i in range(1, n): # 从第二天开始
        buy1 = max(buy1, - prices[i]) # 负的所以用max 这样得到的是价格最低的，如果今天的价格更低，那么我买入的价格以今天的为主
        sell1 = max(sell1, prices[i] + buy1)
        buy2 = max(buy2, sell1 - prices[i]) # 第二次购买，我们以第一次卖出作为“本钱”
        sell2 = max(sell2, prices[i] + buy2) 
    
    return sell2

```
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
但实际上我们可以发现，有些斐波那契值被多次计算了。如果我们在内存中用空间换时间，用一个数组存放目前已经计算过的值，那么可以省下非常多的时间。

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
为什么称之为自顶向下呢，是说我们从一个规模较大的问题，逐步分解成规模比较小的问题，直到分解到base case。
一般这种思路也和递归同时出现。

##### 自底向上：用迭代进行
递归的方法有很多优点。比如说：
- 思路简单，容易形成算法
- 在一些数据结构中使用比较方便。比如说树形结构。

但是它也有几个缺点：
- 每一次函数调用，都需要在内存栈中分配空间以保存参数、返回地址以及临时变量，而往栈中压入数据和弹出数据都需要时间。
- 上述的函数调用栈有可能导致溢出

所以我们一般采用自底向上的想法。从规模最小的问题出发，往上推直到推到我们想要的答案。
比如说我们的斐波那契数列可以这样求解:

```python
    def fib(n):
        if n < 2:
            return n
        
        p = 0
        q = 0
        result = 1
        for i in range(2, n + 1):
            p, q = q, result
            result = p + q
        return result

```
值得一提的是，斐波那契数列问题本质上不是一个动态规划问题，只不过其通项公式恰巧满足了"状态转移方程"的形式。
典型的动态规划问题是求解最值问题。



#### 其他股票题，股票题的变种
to be finished

#### 最长回文子串
to be finished

#### 参考资料
[为什么叫“动态规划”](https://www.1point3acres.com/bbs/thread-689633-1-1.html)
[Leetcode 123 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/solution/mai-mai-gu-piao-de-zui-jia-shi-ji-iii-by-wrnt/)


