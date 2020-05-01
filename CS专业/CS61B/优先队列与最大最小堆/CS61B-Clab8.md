# CS61B Challenge Lab8: Heaps and Hashes

## 零，前言

本次挑战lab的内容是关于堆的，一共有两个Task，我就做了第一个Task。第二个有缘再见吧（逃

题目如下

大意是我有一个Flight Object记录所有的航班，包括以下三个变量

（1）开始时间startTime

（2）结束时间endTime

（3）航班人数passengers

然后我们需要求得同一时间在空中乘客最多的数目。

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/截图1.png)



## 一，分析

画一张图简单的看一下

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/截图2.png)

碰到这道题我的第一反应就是先把每个航班按照时间的顺序排序。

我们想象有一个指针T更随着时间轴从最早的**startTime**时间点开始向右移动，这个指针停留的每个时间点。

都会有一些航班（上图蓝色的条），被包含在里面。比方说15这个时刻，前三条航班都被包含在里面了。

我们的时间指针只需要耕者开始时间点不断地向右移动即可，每个停留的时间点统计一下被包含在该时间点内的所有航班，在与记录的最大人数比较一下。

为了实现这个目标，我们需要两个优先队列PriorityQueue。

（1）以开始时间startTime为数据的最小堆**startTimePQ**.

用于遍历全部的航班。

（2）以结束时间endTime为数据的最小堆**endTimePQ**。

存有当前时间点上可以同时存在的所有航班



## 二，代码实现

初始化的时候先把**startTimePQ**填满。

每次从startTimePQ中取出最小的元素，其开始时间**currTime**就是我们的时间指针所指的位置。

如果endTimePQ的最小元素比**currTime**小，表明需要剔除这个航班。

如此往复，直到遍历完全部的航班。

总的时间复杂度是O(N*log(N))

因为PriorityQueue的poll操作和add操作都是O(log(N))的。

```java
public class FlightSolver {
    private ArrayList<Flight> fights;
    PriorityQueue<Flight> startTimePQ;      //开始时间从小到大
    PriorityQueue<Flight> endTimePQ;        //结束时间从小到大

    private Comparator<Flight> byEndTime = Comparator.comparing((Flight f) -> (f.endTime));
    private Comparator<Flight> byStartTime = Comparator.comparing((Flight f) -> (f.startTime));


    public FlightSolver(ArrayList<Flight> flights) {
        /* FIX ME */
        this.fights = flights;
        startTimePQ = new PriorityQueue<>(flights.size(), byStartTime);
        endTimePQ = new PriorityQueue<>(flights.size(), byEndTime);

        //一开始只是初始化按照开始时间的最小堆
        for(int i = 0; i < flights.size(); i++)
        {
            startTimePQ.add(flights.get(i));
        }
    }

    public int solve() {
        /* FIX ME */
        int maxNumbers = 0;
        int currNumbers = 0;
        Flight currFlight;
        while(!startTimePQ.isEmpty())
        {
            currFlight = startTimePQ.poll();
            endTimePQ.add(currFlight);
            currNumbers += currFlight.passengers;

            if(!endTimePQ.isEmpty())
            {
                //这里用while循环是因为可能有多个航班结束时间相同
                while (!endTimePQ.isEmpty() && (endTimePQ.peek().endTime < currFlight.startTime))
                {
                    currNumbers -= endTimePQ.poll().passengers;
                }
            }
            maxNumbers = Math.max(maxNumbers, currNumbers);
        }
        return maxNumbers;
    }
}
    
```

