# Leetcode 98 ValidBinarySearchTree 题解

## 零，题目描述

Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

- The left subtree of a node contains only nodes with keys **less than** the node's key.
- The right subtree of a node contains only nodes with keys **greater than** the node's key.
- Both the left and right subtrees must also be binary search trees.

**Example 1:**

```
    2
   / \
  1   3

Input: [2,1,3]
Output: true
```

**Example 2:**

```
    5
   / \
  1   4
     / \
    3   6

Input: [5,1,4,null,null,3,6]
Output: false
Explanation: The root node's value is 5 but its right child's value is 4.
```

判定一个给定二叉树是否是BST。



## 一，题目分析

说实话，这个题目挺简单的。五分钟想好，30分钟写代码，1个小时才提交成功（我也太菜了。。。。

### 方法一

直接采用BST的性质，递归求解。BST就是对于任意一个节点，他的左子树全部小于他自己，他的右子树全部大于他自己。我这里采用的是后置判断。也就是对于当前节点只判断value是否在最大值最小值之间。

比方说对于下面这个树，在root判断的时候给定最大值（Long类型的最大值），最小值（Long类型的最小值）。

不在范围内就false，再范围内就递归传递判断**left**和**right**。

这样对于2来说，最大值就成了4，最小值还是Long类型的最小值。

对于6来说，最大值是Long类型的最大值，最小值成了4。

```
    4
   / \
  2   6
 /
1
```

代码如下

```java
/**
 * 第一种方法，递归判定
 * @param root 根节点
 * @return 是否是valid的BST
 */
public boolean isValidBST(TreeNode root)
{
    //这里记得用Long，因为输入会有Interger最大值
    return isValidBSTHelper(root, Long.MIN_VALUE, Long.MAX_VALUE);
}

/**
 * Helper函数，记得
 * @param node 当前节点
 * @param minVal 当前node的最小值（后续所有值必须小于这个数）
 * @param maxVal 当前node的最大值（后续所有值必须大于这个数）
 * @return
 */
public boolean isValidBSTHelper(TreeNode node, long minVal, long maxVal)
{
    if(node == null)
        return true;
    boolean isValid = true;
    if(node.val <= minVal || node.val >= maxVal)
        return false;
    return isValidBSTHelper(node.left, minVal, node.val) && isValidBSTHelper(node.right, node.val, maxVal);
}
```



### 方法二，inorder遍历法

这个方法是我最先想到的，因为之前刚刚做过一道题也是inorder遍历。

inorder遍历BST的时候，是从小到大遍历的。因此我们遍历的过程中一旦发现当前值比前一个值要小，就说明这颗二叉树不符合BST的性质。

这里贴上的是别人的解答，我自己的代码略有不同。这种使用Stack来保存节点的时候。

我们一定要记住一些invariance，也就是不变量

- Stack中的节点不会再访问其LeftNode
- Stack中弹出的节点不能再进入Stack

当root（或者说当前节点）为null的时候，表明现在该弹栈了，我们已经遍历到了一个叶子节点或者已经访问过LeftNode的节点。

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/IMG_20200316_114821.jpg)

```java
    public boolean isValidBSTOthers(TreeNode root) {
        if (root == null) return true;
        Stack<TreeNode> stack = new Stack<>();
        TreeNode pre = null;
        while (root != null || !stack.isEmpty()) {
            //一路向左通到底
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            //这个谈栈操作相当于inorder中的visit，访问当前节点，接下来就是遍历RightNode了
            root = stack.pop();
            //不满足inorder顺序，不符合BST特征，返回false
            if(pre != null && root.val <= pre.val) return false;
            pre = root;
            root = root.right;
        }
        return true;
    }
```

