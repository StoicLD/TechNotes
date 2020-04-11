# LeetCode 124 Binary Tree Maximum Path Sum题解

## 零，题目描述

Given a **non-empty** binary tree, find the maximum path sum.

For this problem, a path is defined as any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The path must contain **at least one node** and does not need to go through the root.

**Example 1:**

```
Input: [1,2,3]

       1
      / \
     2   3

Output: 6
```

**Example 2:**

```
Input: [-10,9,20,null,null,15,7]

   -10
   / \
  9  20
    /  \
   15   7

Output: 42
```

给定一颗二叉树，让我们找到值最大的路径。这条路径有一个起始节点和结束节点。这条路径是不重复的。比如从上面例子中9开始走，可以经过-10，到20，到15，到7。



## 一，题目分析

拿到这道题，首先要转换问题。我们仔细的分析一下这条路径的特质，发现这条路径中一定会有一个最高点。

比如下面几种可能的情况中，-10，20分别作为路径中的最高点。

```
   -10       
   / \
  9  20
```

```
   -10
   / \
  9  20
    /  
   15   
```

```
    20
   /  \
  15   7
```

因此，问题可以转换为以二叉树中每一个节点node为最高点的所有路径中，找到总和最大的哪条路径。

而这个最高点TopNode有左边一条单一路径，右边一条单一路径（单一路径即路径是单向的，即一级一级高度下降，路径中的前一个节点一定是后一个节点的parent）。

比方说对于-10为TopNode，左边只有两条单一路径，9或者0（即空），右边四条路径（0，-20，-20 -> 15, -20 -> 7）

这所有路径中的最大值加上-10，即以-10为TopNode的最大路径。

```
   -10
   / \
  9  -20
    /  \
   15   7
```

因此，我们要解决两个问题

1. 以node为TopNode的**路径（人字型）**的最大值。
2. 以node为TopNode的**单一路径**（竖着的，纵向的）的最大值。

最终的答案是**1**中所有路径的最大值。比方说如下的是一条以-10为TopNode的路径，它由

9->1这条左侧的单一路径和 -20->3这条右侧的单一路径组成。

```
   -10
   / \
  9  -20
 /	   \
1	    3
```

因此我们可以想到一种递归的解决方案。

在递归的过程中同时解决这两个问题，这就要求我们维护一个全局的变量最大值。

代码如下

```java
public class BinaryTreeMaximumPathSum
{
    //全局变量，在递归的过程中不断地更新
    static int maxSum;
    public int maxPathSum(TreeNode root)
    {
        if(root == null)
            return 0;
        maxSum = root.val;
        int rootSum = singlePathMaxSum(root.left) + singlePathMaxSum(root.right) + root.val;
        return Math.max(maxSum, rootSum);
    }

    //返回以node为TopNode的单一路径中的最大值
    public int singlePathMaxSum(TreeNode node)
    {
        //两个功能，一是计算以当前node为topNode的路径是否比maxSum大，是则更新
        //二是，返回以当前node为TopNode的单一路径的最大值
        if(node == null)
            return 0;
        //左侧单一路径最大值
        int leftSum = singlePathMaxSum(node.left);
        //右侧单一路径最大值
        int rightSum = singlePathMaxSum(node.right);
        //以当前node为TopNode的路径最大值
        int currSum = leftSum + rightSum + node.val;
        //更新数值
        if(currSum > maxSum)
            maxSum = currSum;
        //与0作比较，如果比0小表示以node为TopNode的单一路径最大值也比0小，返回的路径即没有节点
        return Math.max(0, Math.max(leftSum + node.val, rightSum + node.val));
    }
}
```