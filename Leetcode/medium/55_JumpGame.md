# LeetCode 55 Jump Game 题解

## 零，题目描述

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

**Example 1:**

```
Input: [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Example 2:**

```
Input: [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum
             jump length is 0, which makes it impossible to reach the last index.
```

给定一个数组，每个位置的数值表示最长step。问我们是否能从index为0的起点处走到最后一个index



## 一，题解

一道很明显的DP题目。

我们从后往前考虑（或者称之为bottom-up）。以下面这个数组为例子

```
[2,3,0,1,4]
```

最后一个index自然不需要考虑，我们从倒数第二个index，也就是max-step为1的这个位置开始。

最小只需要1步就可以到达结尾。

倒数第三个index，由于前一个index是通的，因此最少也只需要1步就可以到达了。但是这个位置的数值为0。

因此倒数第四个index需要至少两步，才能到达。

起点位置只需要1步就可以了。

图示如下。

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/20200426111450.png)

也就是说，如果当前位置可以到达一个后续连接到终点的位置（称为中间点），那么前一个位置只需要最小步长为1就可以通过当前位置到达终点了。也就是对前一个位置来说，当前位置是一个最小的中间点。

但如果当前位置的最大步长范围内都到不了一个可达的中间点的话，那么前一个位置至少需要m+1步才能到达终点（当前位置至少需要m步才能到达一个可达终点的中间点）

因此我们可以发现DP的规律

```java
dp[i] = nums[i] >= dp[i + 1] ? 1 : dp[i + 1] + 1
```



代码如下所示。

```java
/**
 * O(N)的DP算法
 * @param nums
 * @return
 */
public boolean canJump2(int[] nums)
{
    if(nums == null || nums.length <= 1)
        return true;
    int n = nums.length;
    int[] dp = new int[n];      //存满足可达路径的最小值
    dp[n - 1] = 1;
    for(int i = n - 2; i >= 0; i--)
    {
        if(nums[i] >= dp[i + 1])
            dp[i] = 1;
        else
            dp[i] = dp[i + 1] + 1;
    }
    return nums[0] >= dp[1];
}		
```

实际上这个代码可以优化，因为我们不需要O(n)的空间，只要记载到达一个**可达终点的中间点**所需的**最小步长**即可

因此优化过后如下所示

```java
/**
 * 只需要 O(N)时间和 O(1)空间
 * @param nums
 * @return
 */
public boolean canJump3(int[] nums)
{
    if(nums == null || nums.length <= 1)
        return true;
    int n = nums.length;
    int lastMin = 1;
    for(int i = n - 2; i >= 0; i--)
    {
        if(nums[i] >= lastMin)
            lastMin = 1;
        else
            lastMin += 1;
    }
    return lastMin == 1;
}
```