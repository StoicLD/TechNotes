# Leetcode 96 UniqueBinarySearchTrees 题解

## 零，题目描述

Given *n*, how many structurally unique **BST's** (binary search trees) that store values 1 ... *n*?

**Example:**

```
Input: 3
Output: 5
Explanation:
Given n = 3, there are a total of 5 unique BST's:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

给定1到n的数字，组成BST（二叉搜索树）。

问有多少个结构不同的这种BST？



## 一，题目分析

首先明确一个问题。

有n个节点的结构上不同的BST总数m，是否等同于在当前限定输出下（从1到n，n个数）能够构成的BST的数目？

答案是是的，倒过来想。随便给定一个n个节点的BST，这些输入的n个数能不能填充进去？

显然是可以的，因为一个BST，一定有一个顺序（比方说中序遍历，可以从小到大遍历完）。既然有这个遍历顺序。那么就一定可以把这n个数填充进去。



因此，问题就变成了。给定n，有多少种结构上不同的BST？

这道题目我也不知道为啥我想了好久。主要是没有一开始理清楚一个通式。先给一个直观的感受

我们从根节点开始，对于一个BST中的节点，他有**leftNode**和**rightNode**，根节点也是。

比方说一共有5个节点，n = 5。那么除去根节点，还有四个节点。

因此我们可以

（1）左边0个节点，右边4个节点；

（2）左边1个节点，右边3个节点；

（3）左边2个节点，右边2个节点；

（4）左边3个节点，右边1个节点；

（5）左边4个节点，右边0个节点

如下图所示。对应于情况（2）。如果以R(n)表示n个节点构成的不同结构的BST总数。

那么R(5) = R(0) * R(4) + R(1) * R(3) + R(2) * R(2) + R(3) * R(1) + R(4) * R(0) 

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/20200315155201.png)

通式就是

> R(n) = R(0) * R(n-1) + R(1) * R(n-2) + ... + R(n-1) * R(0) 

这是一个明显的递归结构。

## 二，解决方案

### 方法一，递归

递归是最最直接的一种方式。但是直接递归的话会有很多的重复计算。比如说计算R(n-1)的时候肯定要计算R(n-2)到R(0)。因此我们占用O(n)的memory，搞一个记录数组，记录已经计算过的结果。

这种算法的时间复杂度应该是O(n^2)的。同时要占用O(n)的memory。而且递归可能会爆栈，因为至多会展开n层函数栈

```java
public class UniqueBinarySearchTrees
{
    //递归通式应该是这样的 R(N) = R(0) * R(N - 1) + R(1) * R(N - 2) + ... + R(N-1) * R(0)
    int[] record;
    public int numTrees(int n)
    {
        record = new int[n + 1];
        record[0] = record[1] = 1;
        return numTreesHelper(n);
    }

    //使用递归展开的方法实际上是会爆栈的
    public int numTreesHelper(int n)
    {
        if(n <= 1)
        {
            return record[n];
        }
        else if(record[n] != 0)
        {
            return record[n];
        }
        else
        {
            int result = 0;
            for(int i = 0; i <= n - 1; i++)
            {
                result += numTreesHelper(i) * numTreesHelper(n - 1 - i);
            }
            record[n] = result;
            return result;
        }
    }
}
```



### 方法二，动态规划DP

DP的方法就很简洁优雅了，也是我一开始没能想到的。

这种方法就是把record记录表直接填满了，从0，1，2开始一直到n。避免重复计算。

两层循环，外循环是表明从0到n填充数组。内层循环就是通式：

> R(n) = R(0) * R(n-1) + R(1) * R(n-2) + ... + R(n-1) * R(0) 

```java
/**
 * 使用Dynamic Programming也就是动态规划的方式
 * 代码简洁优雅很多
 * @param n 输入规模
 * @return 不同结构的BST的数目
 */
public int numTreesDP(int n)
{
    int[] recordDP = new int[n + 1];
    recordDP[0] = recordDP[1] = 1;
    for(int i = 2; i <= n; i++)
    {
        for(int j = 0; j < i; j++)
        {
            recordDP[i] += recordDP[j] * recordDP[i - j - 1];
        }
    }
    return recordDP[n];
}
```