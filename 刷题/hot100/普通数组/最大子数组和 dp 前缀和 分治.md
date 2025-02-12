# [53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

给你一个整数数组 `nums` ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。



**子数组**是数组中的一个连续部分。



 

**示例 1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**示例 2：**

```
输入：nums = [1]
输出：1
```

**示例 3：**

```
输入：nums = [5,4,-1,7,8]
输出：23
```

 

**提示：**

- `1 <= nums.length <= 105`
- `-104 <= nums[i] <= 104`

 

**进阶：**如果你已经实现复杂度为 `O(n)` 的解法，尝试使用更为精妙的 **分治法** 求解。



# 解答

## 1 动态规划

状态转移关系：

dp[i]代表当前的最大子数组和，其状态转移只有两个可能性：

- dp[i-1]+nums[i]，也就是从上一个状态转移过来
- 直接一个num[i]，也就是单独一个为子数组

在转移的过程中，如果上一个dp[i-1]是负数的话，加上不如不加，因此状态的关系也可以写成: 

dp[i]=max(dp[i-1],0)+nums[i]

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int len=nums.length;
        int[] dp=new int[len];
        dp[0]=nums[0];
        int res=dp[0];
        for(int i=1;i<len;i++){
            dp[i]=Math.max(nums[i],dp[i-1]+nums[i]);
            res=Math.max(res,dp[i]);
        }
        return res;
    }
}
```

在这个地方只使用了前一个dp，也可以进行优化

#### 复杂度分析

- 时间复杂度：O(*n*)，其中 *n* 为 *nums* 的长度。
- 空间复杂度：O(1)。仅用到若干额外变量。



## 2 前缀和

求子数组可以利用前缀和进行求解，类似于 题目： “和为K的子数组”[560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

在每次求出当前的前缀和的时候，同时要维护一个遍历到的最小的前缀和，然后作差，同时注意顺序的问题，由于当前的前缀和一定要晚于最小的前缀和，否则顺序有问题，因此比较的时候，需要注意！

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int len=nums.length;
        int res=Integer.MIN_VALUE;
        int minVal=0;
        int sum=0;
        for(int i:nums){
            sum+=i;//先求出前缀和当前的
            res=Math.max(res,sum-minVal);//先进行比较
            minVal=Math.min(minVal,sum);//然后再更新最新的前缀和
        }
        return res;
    }
}
```

#### 复杂度分析

- 时间复杂度：O(*n*)，其中 *n* 为 *nums* 的长度。
- 空间复杂度：O(1)。仅用到若干额外变量。



## 3 分治 //TODO