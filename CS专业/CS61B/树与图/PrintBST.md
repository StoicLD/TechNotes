# 打印二叉树

## 零，前言

再写CS61B Project2B的时候想到需要一个打印二叉树的方法来帮助我debug。

所以去StackOverflow上找了一个代码，再加上自己的研究，po在这里。

## 一，分析

一个基本的完全二叉树结构如下。

定义L指的是当前节点所在层数，maxLevel则是树高。count指的是节点数据占的位长（我默认全部一样长）

三个变量，分别是

1.两个节点间距： Between(L)
$$
Between(L) = (2^{(maxLevel - L + 1)} - 1) * count
$$
2.每行首段间距 First(L)
$$
First(L) = (2^{(maxLevel - L)} - 1) * count
$$
3.斜杠行数 EdgeLines(L)
$$
EdgeLines(L) = Max(2^{(maxLevel - L - 1)} - 1, 0) * count
$$

同时我们令所有的输入树都是完全树，然后采用层序遍历的方法。把所有的节点按照 **层序** 放入一个List中去。

如果一个节点的left或者right是空的，那就放一个空节点进去（为了对齐）。



![](https://raw.githubusercontent.com/JokerLD/Image/master/Other/%E6%89%AB%E6%8F%8F%202020%E5%B9%B42%E6%9C%8822%E6%97%A5%2017.53.jpg)



## 二，代码实现

```java
package bearmaps;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class BTreePrinter
{
    public static void printNode(KDTree.KdNode root, int unitLength)
    {
        int maxLevel = BTreePrinter.maxLevel(root);

        printNodeInternal(Collections.singletonList(root), 1, maxLevel, unitLength);
    }

    //递归打印
    private static void printNodeInternal(List<KDTree.KdNode> nodes, int level, int maxLevel, int count)
    {
        if (nodes.isEmpty() || BTreePrinter.isAllElementsNull(nodes))
            return;

        int floor = maxLevel - level;
        int endgeLines = (int) Math.max(Math.pow(2, floor - 1), 0);
        endgeLines *= count;
        int firstSpaces = (int) Math.pow(2, (floor)) - 1;
        firstSpaces *= count;
        int betweenSpaces = (int) Math.pow(2, (floor + 1)) - 1;
        betweenSpaces *= count;

        BTreePrinter.printWhitespaces(firstSpaces);

        List<KDTree.KdNode> newNodes = new ArrayList<>();
        for (KDTree.KdNode node : nodes)
        {
            if (node != null)
            {
                System.out.print(node.getPoint().getX() + ", " + node.getPoint().getY());
                newNodes.add(node.leftNode);
                newNodes.add(node.rightNode);
            }
            else
            {
                newNodes.add(null);
                newNodes.add(null);
                System.out.print(String.format("%" + count + "s", " "));
            }

            BTreePrinter.printWhitespaces(betweenSpaces);
        }
        System.out.println("");

        //打印斜杠
        for (int i = 1; i <= endgeLines; i++)
        {
            for (int j = 0; j < nodes.size(); j++)
            {
                BTreePrinter.printWhitespaces(firstSpaces - i);
                if (nodes.get(j) == null)
                {
                    //BTreePrinter.printWhitespaces(endgeLines + endgeLines + i + 1);
                    BTreePrinter.printWhitespaces(endgeLines + endgeLines + i + count + count);
                    continue;
                }

                if (nodes.get(j).leftNode != null)
                    System.out.print("/");
                else
                    BTreePrinter.printWhitespaces(1);

                BTreePrinter.printWhitespaces(count + i + i - 2);

                if (nodes.get(j).rightNode != null)
                    System.out.print("\\");
                else
                    BTreePrinter.printWhitespaces(1);

                BTreePrinter.printWhitespaces(endgeLines + endgeLines - i);
            }

            System.out.println("");
        }

        printNodeInternal(newNodes, level + 1, maxLevel, count);
    }

    private static void printWhitespaces(int count)
    {
        for (int i = 0; i < count; i++)
            System.out.print(" ");
    }

    private static int maxLevel(KDTree.KdNode node)
    {
        if (node == null)
            return 0;

        return Math.max(BTreePrinter.maxLevel(node.leftNode), BTreePrinter.maxLevel(node.rightNode)) + 1;
    }

    private static boolean isAllElementsNull(List list)
    {
        for (Object object : list)
        {
            if (object != null)
                return false;
        }

        return true;
    }

}

```

