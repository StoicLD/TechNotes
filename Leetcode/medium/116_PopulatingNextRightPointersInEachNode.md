# Leetcode 116 Populating Next Right Pointers in Each Node 题解

## 零，题目描述

You are given a **perfect binary tree** where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:

```c++
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`.

**Follow up:**

- You may only use constant extra space.
- Recursive approach is fine, you may assume implicit stack space does not count as extra space for this problem.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)

```
Input: root = [1,2,3,4,5,6,7]
Output: [1,#,2,3,#,4,5,6,7,#]
Explanation: Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.
```

**Constraints:**

- The number of nodes in the given tree is less than `4096`.
- `-1000 <= node.val <= 1000`

其实就是让你把这颗完美的二叉树，搞个层序遍历。



## 一，题目分析

### 方法一，递归

最直接的方法就是递归解决，虽然函数栈展开是要占用空间的。

递归的时候写个helper方法，把当前节点对应的next传进去。

```java
public Node connectRecursive(Node root)
{
    return connectHelper(root, null);
}

public Node connectHelper(Node node, Node next)
{
    if(node == null)
        return node;
    node.next = next;
    connectHelper(node.left, node.right);
    if(next != null)
        connectHelper(node.right, next.left);
    else
        connectHelper(node.right, null);
    return node;
}
```



## 方法二，常数空间复杂度的方法

这是需要重点介绍的。因为之前收到Morris Traversal的影响，想了两个小时一直想的是

通过临时修改next的方向，比方说临时把右子节点的next指向自己这种方式来保存递归栈上一层所需要的信息。

但是对于这个问题貌似是行不通的。

直接上代码说明吧。我们只需要维持pre和curr两个节点。pre在每次遍历的时候固定，curr是可变换的临时节点。

对于当前节点curr，我们让他的left的next变成right，这是很自然的。然后我们访问这一层的下一个节点curr.next，让这个节点的left成为curr的right的next。

如下图所示，是curr为节点5的时候

![](http://q7h5flrml.bkt.clouddn.com/20200324172911.png)

```java
public Node connect(Node root)
{
    if(root == null)
        return root;
    Node pre = root;
    Node curr = null;
    while (pre.left != null)
    {
        curr = pre;
        while (curr != null)
        {
            curr.left.next = curr.right;
            if(curr.next != null)
            {
                curr.right.next = curr.next.left;
            }
            curr = curr.next;
        }
        pre = pre.left;
    }
    return root;
}
```

该算法的思路就是每次往下走一层，从最左边开始，不断地next往右，然后把所有的节点都连接在一起。



## 二，总结

这道题，我想了很久很久就一直想用Morris Traversal的方法，尝试了很久，结果还是白费力气。

以后30min没想出来就还是直接看答案算了。