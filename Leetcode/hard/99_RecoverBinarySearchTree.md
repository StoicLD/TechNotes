# Leetcode 99 Recover Binary Search Tree题解

## 零，题目描述

Two elements of a binary search tree (BST) are swapped by mistake.

Recover the tree without changing its structure.

**Example 1:**

```
Input: [1,3,null,null,2]

   1
  /
 3
  \
   2

Output: [3,1,null,null,2]

   3
  /
 1
  \
   2
```

**Example 2:**

```
Input: [3,1,4,null,null,2]

  3
 / \
1   4
   /
  2

Output: [2,1,4,null,null,3]

  2
 / \
1   4
   /
  3
```

**Follow up:**

- A solution using O(*n*) space is pretty straight forward.
- Could you devise a constant space solution?

给定一颗BST，但是其中又两个节点的数值交换了。

让我们给换回来。



## 一，题目分析

### 方法一，占据O(N)空间的中序遍历解决方案

一上来的第一反应就是inorder中序遍历。因为一颗正常的BST inorder 遍历一定是从小到大遍历的。那么不妨inorder遍历之后看看有那两个节点是反常的。直接交换。

如下所示的树，其中7与20交换了

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/20200319144640.png)

inorder遍历之后如下所示，20和7的位置是有问题的。

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/20200319145051.png)

是从头开始遍历，当当前节点比其前一个节点大的时候，我们就锁定第一个交换的点。比如这里，我们是遍历到8的时候，发现前一个节点20要比8大。因此**firstSwapNode**锁定在20。后面当我们遍历到7的时候，又发现7的前一个接待你18要比7大。因此锁定第二个要交换的节点就是7。



关于锁定交换节点的操作，我们需要考虑两种情况，第一种情况如上所示。

第二种情况则如下图，两个要交换的节点是相邻的。这种情况下，如果我们只当前一个节点比当前的值要大的时候。

才锁定交换节点，是有问题的。我们需要锁定第一个节点的时候，就把第二个节点先临时的锁定了

也就是curr=3的时候，firstSwapNode被设置为1，secondSwapNode被设置为3。

如此一来，如果后面再次出现反常情况，我们就更换secondSwapNode。

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/20200319150102.png)

代码如下

```java
    public void recoverTree2(TreeNode root)
    {
        Stack<TreeNode> stack = new Stack<>();
        if(root == null || (root.left == null && root.right == null))
            return;
        TreeNode currNode = root;
        //inorder遍历中的前一个节点
        TreeNode preNode = null;
        //第一个出问题的节点
        TreeNode firstSwapNode = null;
        //第二个出问题的节点
        TreeNode secondSwapNode = null;
        //inorder遍历
        while(currNode != null || !stack.empty())
        {
            while (currNode != null)
            {
                stack.push(currNode);
                currNode = currNode.left;
            }
            currNode = stack.pop();
            if(preNode != null)
            {
                //发现出问题了，前一个节点本来应该比当前小的
                //现在比当前节点要大
                if(preNode.val > currNode.val)
                {
                    if(firstSwapNode == null)
                    {
                        firstSwapNode = preNode;
                        secondSwapNode = currNode;
                    }
                    else
                    {
                        secondSwapNode = currNode;
                        break;
                    }
                }
            }
            preNode = currNode;
            currNode = currNode.right;
        }
        //交换节点
        if(firstSwapNode != null && secondSwapNode != null)
        {
            int temp = firstSwapNode.val;
            firstSwapNode.val = secondSwapNode.val;
            secondSwapNode.val = temp;
        }
    }

```



### 方法二，占据O(N)空间的Morris Traversal方法

这是一个非常巧妙地方案，**Morris Traversal**使得我们可以只保存两个节点指针，就能完成inorder中序遍历。

参考这篇博客。

[[Morris Traversal方法遍历二叉树（非递归，不用栈，O(1)空间）](https://www.cnblogs.com/AnnieKim/archive/2013/06/15/MorrisTraversal.html)

概括地说，就是在遍历每个有leftNode的节点（当前节点称为curr）时，我们都去找他的直接前驱（predecessor），然后把这个predecessor的rightNode改成curr。大体步骤如下所示。

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/14214057-7cc645706e7741e3b5ed62b320000354.jpg)

```java
public void recoverTree3(TreeNode root)
{
    if(root == null || (root.left == null && root.right == null))
        return;
    TreeNode curr = root;
    //这个pre并不是直接前驱，而是上一个节点
    TreeNode pre = null;
    //这个是真正的直接前驱
    TreeNode preValueNode = null;
    TreeNode firstSwapNode = null;
    TreeNode secondSwapNode = null;
    while (curr != null)
    {
        if(curr.left == null)
        {
            if(preValueNode != null && preValueNode.val > curr.val)
            {
                if(firstSwapNode == null)
                {
                    firstSwapNode = preValueNode;
                    secondSwapNode = curr;
                }
                else
                {
                    secondSwapNode = curr;
                }
            }
            preValueNode = curr;
            curr = curr.right;
        }
        else
        {
            pre = curr.left;
            while (pre.right != null && pre.right != curr)
            {
                pre = pre.right;
            }

            //临时的修改树结构
            if(pre.right == null)
            {
                pre.right = curr;
                curr = curr.left;
            }
            else
            {
                //撤销之前修改的树结构
                pre.right = null;
                //这里是inorder visit curr的地方，做处理
                if(preValueNode != null && preValueNode.val > curr.val)
                {
                    if(firstSwapNode == null)
                    {
                        firstSwapNode = preValueNode;
                        secondSwapNode = curr;
                    }
                    else
                    {
                        //这里不能break，不然会导致撤销不了
                        secondSwapNode = curr;
                    }
                }
                preValueNode = curr;
                curr = curr.right;
            }
        }
    }
    if(firstSwapNode != null && secondSwapNode != null)
    {
        int temp = firstSwapNode.val;
        firstSwapNode.val = secondSwapNode.val;
        secondSwapNode.val = temp;
    }
}
```