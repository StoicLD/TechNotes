# Challenge Lab 9, BFS与图

## 零，前言

本lab是关于BFS广度优先搜索以及图的。

lab要求如下图所示

![](https://raw.githubusercontent.com/JokerLD/Image/master/Other/20200226154015.png)

题目大意是顾客代表顶点，顾客之间的敌对关系代表边，需要把顾客分成两组，有敌意关系的不能分到一组。

问这样的分组对于以图的形式给出的一组顾客/顾客关系是否满足。



##  一，题目分析与思路

这就是一个图问题，题目已经说的很清楚了。问题就变成了一个顶点的邻接顶点不能和该顶点放在一组里。

先从special case开始想，如果从一个点A放在第一组，那么该点的所有邻接点都是第二组的。第二组所有的邻接点（除了点A）都应该是第一组的，也就是和A一组。下图就是不可能的例子。因为D与A相连。

<img src="https://raw.githubusercontent.com/JokerLD/Image/master/Other/20200226154721.png" style="zoom:125%;" />

仔细分析后会发现，我们可以使用BFS广度优先搜索来解决这个问题。

题目中分为两组，其实也可以这么想，第一层节点是A，第二层是C，B，D。我们把奇数层看作是第一组，偶数层看作是第二组。

对于每一个顶点，不能有相邻的顶点和自己位于同一层。

**相邻**表示相连

**位于同一层**表示属于同一组

如果这么想，代码就如下所示

```java
    public boolean isSeparable() {
        // TODO: Fix me
        //获取全部的顶点
        Set<String> set = g.labels();
        //bfs使用队列来实现
        Queue<String> bfsQueue = new Queue<>();

        //每次有效循环是遍历一个连通分图
        for(String label : set)
        {
            //确保每个节点只访问一次，O(V)的时间复杂度
            if(marked.get(label) != null)
                continue;

            //root 节点入队，设置为group1
            bfsQueue.enqueue(label);
            //标记root节点
            marked.put(label, 1);
            String currLabel;
            //一直BFS遍历，直到该联通分图遍历完毕
            while(!bfsQueue.isEmpty())
            {
                currLabel = bfsQueue.dequeue();
                //当前节点所属group，null表示还未标记（未访问）
                //1表示第一组，-1表示第二组
                int currGroupNum = marked.get(currLabel);
                //访问当前节点的所有邻居
                for (String neighbor : g.neighbors(currLabel))
                {
                    Integer markValue = marked.get(neighbor);
                    //没有访问过，标记且入队
                    if (markValue == null)
                    {
                        marked.put(neighbor, -currGroupNum);
                        bfsQueue.enqueue(neighbor);
                    }
                    //邻接节点与当前节点同属一组，违反了性质，说明该图无法构成合理的分组
                    else if (markValue == currGroupNum)
                    {
                        return false;
                    }
                }
            }
        }
        return true;
    }
```

