# Leetcode 95 Binary Tree Inorder Traversal Ⅱ题解

## 零，题目描述

Given an integer *n*, generate all structurally unique **BST's** (binary search trees) that store values 1 ... *n*.

**Example:**

```
Input: 3
Output:
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
Explanation:
The above output corresponds to the 5 unique BST's shown below:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

找到符合数值在1-n的n个节点的不同结构的BST。返回包含这些BST的List。



## 一，题目分析

### 方法一，递归法

对于一个根节点root，其left都比自己小，right都比自己大。

因此我们可以给定一个最大值最小值的范围，然后递归的生成子树。

```java
public class Solution {
    public List<TreeNode> generateTrees(int n) {
        
        return genTrees(1,n);
    }
        
    public List<TreeNode> genTrees (int start, int end)
    {

        List<TreeNode> list = new ArrayList<TreeNode>();

        if(start>end)
        {
            list.add(null);
            return list;
        }
        
        if(start == end){
            list.add(new TreeNode(start));
            return list;
        }
        
        List<TreeNode> left,right;
        for(int i=start;i<=end;i++)
        {
            
            left = genTrees(start, i-1);
            right = genTrees(i+1,end);
            
            for(TreeNode lnode: left)
            {
                for(TreeNode rnode: right)
                {
                    TreeNode root = new TreeNode(i);
                    root.left = lnode;
                    root.right = rnode;
                    list.add(root);
                }
            }
                
        }
        
        return list;
    }
}
```



### 方法二，动态规划DP

这个方法是我一开始想到的。最开始构想这个问题的时候，因为有leetcode 96 这道前置题。我知道构造

通式就是

> R(n) = R(0) * R(n-1) + R(1) * R(n-2) + ... + R(n-1) * R(0) 

所以，想要得到n，就必须知道从0到n-1的所有可能的BST子树。那么这部分我们就存储起来。

因此，可以倒换一下顺序。我们从0，1，2开始构造。先构造没有节点的子树（null），然后是只有一个节点的所有可能的BST的集合R(1)，然后是有两个不同节点的所有可能的BST集合R(2)，以此类推。

一开始我们不考虑从1到n这些数值的赋值问题。只考虑不同结构的BST构造。

如同第96题一样，对于R(5)，左边可能是R(0)，R(1)，R(2)，R(3)，R(4)，右边有可能是R(4)，R(3)，R(2)，R(1)，R(0)。

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/20200315155201.png)

因此我们通过如下的代码构造。

构造完成所有的子树之后，我们再inorder中序遍历，来保证树中每个节点的数值符合要求。（把1-n的数值赋值上去）

```java
    public List<TreeNode> generateTrees(int n)
    {
        if(n == 0)
            return new LinkedList<>();
        List<List<TreeNode> > constructTreeList = new LinkedList<>();
        //这里添加一个空链表，是为了满足
        LinkedList<TreeNode> s = new LinkedList<>();
        s.add(null);
        constructTreeList.add(s);
        constructTreeList.add(new LinkedList<>(Arrays.asList(new TreeNode(1))));

        for(int i = 2; i <= n; i++)
        {
            List<TreeNode> currList = new LinkedList<>();
            for(int j = 0; j < i; j++)
            {
                //C(0) * C(n - 1)的自由组合
                List<TreeNode> list1 = constructTreeList.get(j);
                List<TreeNode> list2 = constructTreeList.get(i - 1 - j);

                for(int leftIndex = 0; leftIndex < list1.size(); leftIndex++)
                {
                    TreeNode leftRoot = list1.get(leftIndex);
                    for(int rightIndex = 0; rightIndex < list2.size(); rightIndex++)
                    {
                        TreeNode root = new TreeNode(i);
                        root.left = copyTree(leftRoot);
                        root.right = copyTree(list2.get(rightIndex));
                        currList.add(root);
                    }
                }
            }
            constructTreeList.add(currList);
        }
        
        //完成构造之后，先序遍历
        List<TreeNode> result = constructTreeList.get(n);
        for(TreeNode root : result)
        {
            Stack<TreeNode> stack = new Stack<>();
            int val = 1;
            TreeNode curr = root;
            while (curr != null || !stack.isEmpty())
            {
                while (curr != null)
                {
                    stack.push(curr);
                    curr = curr.left;
                }
                curr = stack.pop();
                curr.val = val;
                val++;
                curr = curr.right;
            }
        }
        return result;
    }

    public TreeNode copyTree(TreeNode root)
    {
        if(root == null)
            return null;
        TreeNode copyRoot = new TreeNode(root.val);
        copyRoot.left = copyTree(root.left);
        copyRoot.right = copyTree(root.right);
        return copyRoot;
    }

```

这样做的代价是比较大的。实际上我们可以在构造树的时候，就直接inorder遍历（因为构造树，也是递归构造的，这个过程中进行常数级别的赋值操作，不影响整体的时间复杂度）

```java
public List<TreeNode> generateTrees2(int n)
{
    if(n == 0)
        return new LinkedList<>();
    //每个元素表示节点总数为x(x小于等于n)的BST所拥有的全部子树
    List<List<TreeNode> > constructTreeList = new ArrayList<>(n + 1);
    //这里添加一个空的链表，是为了后续代码不做corner case的处理
    LinkedList<TreeNode> s = new LinkedList<>();
    s.add(null);
    constructTreeList.add(s);
    constructTreeList.add(new LinkedList<>(Arrays.asList(new TreeNode(1))));

    //从最小的BST开始构造
    for(int i = 2; i <= n; i++)
    {
        List<TreeNode> currList = new LinkedList<>();
        for(int j = 0; j < i; j++)
        {
            //C(0) * C(n - 1)的自由组合
            //表示当前节点的左子树从list1中选择
            List<TreeNode> list1 = constructTreeList.get(j);
            //当前节点的右子树从list2中选择
            List<TreeNode> list2 = constructTreeList.get(i - 1 - j);

            for(int leftIndex = 0; leftIndex < list1.size(); leftIndex++)
            {
                //当前节点的左子树
                TreeNode leftRoot = list1.get(leftIndex);
                for(int rightIndex = 0; rightIndex < list2.size(); rightIndex++)
                {
                    //因为左子树大小为j，而左子树所有节点比root小，右子树所有节点比右子树大。
                    //因此root的值是j+1
                    TreeNode root = new TreeNode(j + 1);
                    //左子树无需调整
                    root.left = copyTreeGiveValue(leftRoot, 0);
                    currList.add(root);
                    //右子树所有数值需要加上j+1（表示比左子树和root所有节点都要大）
                    //如果不进行这个操作，右子树是从1到i-j-1的数值组成的BST
                    root.right = copyTreeGiveValue(list2.get(rightIndex), j + 1);
                }
            }
        }
        constructTreeList.add(currList);
    }

    //完成构造之后，先序遍历
    return constructTreeList.get(n);
}

public TreeNode copyTreeGiveValue(TreeNode root, int addValue)
{
    if(root == null)
        return null;
    TreeNode node = new TreeNode(root.val + addValue);
    node.left = copyTreeGiveValue(root.left, addValue);
    node.right = copyTreeGiveValue(root.right, addValue);
    return node;
}
```

