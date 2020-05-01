# LeetCode 560 SubarraySumEqualsK题解

## 零，题目描述

Given an array of integers and an integer **k**, you need to find the total number of continuous subarrays whose sum equals to **k**.

**Example 1:**

```
Input:nums = [1,1,1], k = 2
Output: 2
```



**Note:**

1. The length of the array is in range [1, 20,000].
2. The range of numbers in the array is [-1000, 1000] and the range of the integer **k** is [-1e7, 1e7].

给定一个数组和一个指定**target数字**，让我们找到连续累加和为target的子数组，有多少个



## 一，题目分析

### 方法一，暴力O(N<sup>2</sup>)法

这个想法很直接，两个for循环，看代码就懂了

```java
public int subarraySum(int[] nums, int k)
{
    int sum = 0;
    int count = 0;
    for (int i = 0; i < nums.length; i++)
    {
        sum = 0;
        for (int j = i; j < nums.length; j++)
        {
            sum += nums[j];
            if (sum == k)
                count++;
        }
    }
    return count;
}
```



### 方法二，使用HashTable的O(N)方法

这个方法非常巧妙。如果我们用

sum(i) 表示从下标0直到下标i的累加和。

那么 sum(i) == sum(j) 则表明，**i** 与 **j** 之间所有数的和为0。（i < j)

同理，如果 sum(j) - sum(i) == k，表明 **i** 与 **j** 之间所有数的和为k。

下面具体分析下面的例子。

```
3,4,7,2,-3,1,-1,1
```

看如下代码

[参考解答](https://leetcode.com/problems/subarray-sum-equals-k/solution/)

```java
    public int subarraySum2(int[] nums, int k)
    {
        int count = 0, sum = 0;
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        for (int i = 0; i < nums.length; i++)
        {
            sum += nums[i];
            if (map.containsKey(sum - k))
                count += map.get(sum - k);
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        return count;
    }	
```

