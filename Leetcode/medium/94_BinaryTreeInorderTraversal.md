# Leetcode 94 Binary Tree Inorder Traversal题解
## 零，题目描述

Given a binary tree, return the *inorder* traversal of its nodes' values.

**Example:**

```
Input: [1,null,2,3]
   1
    \
     2
    /
   3

Output: [1,3,2]
```

**Follow up:** Recursive solution is trivial, could you do it iteratively?

给定一个二叉树，返回 **inorder** 也就是中序遍历的结果。



## 一，题目分析

### 方法一

首先递归方法是最简单的。这个学二叉树的时候大家都知道。。

```java
leftRecursive(currNode.left);
visit(currNode);
rightRecursive(currNode.right);
```



### 方法二

第二种就是用stack来模拟递归的展开。我想了一种使用两个Stack的方法。虽然runtime和memory都是O(N)但是比用一个Stack还是要笨重了些。

基本思路就是既然递归栈展开的时候需要保存中间节点所在的那一层，那么我就把中间节点放入rightStack（意为该栈中的节点还有右节点没有访问）。然后中间节点的**leftNode**访入leftStack中。

从leftStack中取出的节点node，放入rightStack，node的leftNode放入leftStack。如此往复。

演示如下

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/f0fdb61f69496c49a781dd74379b08f.jpg)



### 方法三

事实上，只需要一个栈就可以解决了。。。

[见Leetcode讲解2](https://leetcode.com/problems/binary-tree-inorder-traversal/solution/)

代码如下

```java
public class Solution {
    public List < Integer > inorderTraversal(TreeNode root) {
        List < Integer > res = new ArrayList < > ();
        Stack < TreeNode > stack = new Stack < > ();
        TreeNode curr = root;
        while (curr != null || !stack.isEmpty()) {
            while (curr != null) {
                stack.push(curr);
                curr = curr.left;
            }
            curr = stack.pop();
            res.add(curr.val);
            curr = curr.right;
        }
        return res;
    }
}		
```