# LeetCode 114 Flatten Binary Tree to Linked List题解

## 零，题目描述

Given a binary tree, flatten it to a linked list in-place.

For example, given the following tree:

```
    1
   / \
  2   5
 / \   \
3   4   6
```

The flattened tree should look like:

```
1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
```

按照preorder遍历的顺序来flatten成一个list



## 一，题目分析

一道明显的考察树的遍历的问题。我想了两种解法

### 方法一，先序遍历再设置

非常粗暴的想法，直接preorder遍历一遍，加入一个LinkedList里，然后在遍历一遍这个LinkedList，设置每个节点的right为LinkedList中的下一个节点。这一方法速度比较慢，而且占据空间大。

代码如下所示

```java
public void flatten(TreeNode root)
{
    if (root == null)
        return;
    LinkedList<TreeNode> nodes = new LinkedList<>();
    flattenHelper(root, nodes);
    TreeNode pre = root;
    for (Iterator<TreeNode> it = nodes.listIterator(1); it.hasNext(); )
    {
        TreeNode temp = pre;
        temp.left = null;
        pre = it.next();
        temp.right = pre;
    }
}

public void flattenHelper(TreeNode node, LinkedList<TreeNode> nodes)
{
    if (node == null)
        return;
    nodes.add(node);
    flattenHelper(node.left, nodes);
    flattenHelper(node.right, nodes);
}
```



### 方法二，稍复杂的递归方法

只递归一遍就搞定，只是需要很多的corner case的判定。代码并不优雅。

**flattenHelper2** 这个函数有两个作用

（1）遍历的时候修改完成所有以该节点node为root的子树下面的结构。

（2）同时返回一个还需要修改right的节点。

这需要分类讨论好几种情况，因为我觉得实在不是一个优雅的方法，我就不多阐述了。

```java
public void flatten2(TreeNode root)
{
    flattenHelper2(root);
}

public TreeNode flattenHelper2(TreeNode node)
{
    if(node == null)
        return null;
    if(node.left != null)
    {
        //next不会为空的，至少是自己
        TreeNode next = flattenHelper2(node.left);
        //返回的一个需要修改的节点
        next.right = node.right;
        //右子树中需要修改的一个节点
        TreeNode currNext = flattenHelper2(node.right);
        //修改自身，right改成left
        node.right = node.left;
        //left改为空
        node.left = null;
        if(currNext != null)
            return currNext;
        else
            return next;
    }
    else if(node.right != null)
    {
        TreeNode next = flattenHelper2(node.right);
        return next;
    }
    else
        return node;
}
```



### 方法三，优雅的post order递归方法

这是网上的一种解决方法，很巧妙的改进了一般的post order。

一般的post order是如下的顺序

> Function(LeftNode)
>
> Function(ReftNode)
>
> Visit(CurrNode)

但是这种解决方法把left和right互换了一下。真正意义上做到了从最后一个节点开始访问。

代码如下，非常的简洁和优雅。还有问题可以参考[原解答](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/discuss/36977/My-short-post-order-traversal-Java-solution-for-share)

```java
private TreeNode prev = null;

public void flatten(TreeNode root) {
    if (root == null)
        return;
    flatten(root.right);
    flatten(root.left);
    root.right = prev;
    root.left = null;
    prev = root;
}
```