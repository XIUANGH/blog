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



## 3 分治 

分析方法类类似于「线段树求最长公共上升子序列问题」的 `pushup` 操作。也许读者还没有接触过线段树，没有关系，方法二的内容假设你没有任何线段树的基础。当然，如果读者有兴趣的话，推荐阅读线段树区间合并算法并解决多次询问的「区间最长连续上升子序列问题」和「区间最大子段和问题」，还是非常有趣的。

我们定义一个操作 `get(nums, 0, nums.size() - 1)`，表示查询区间内最大子段和，那么最终我们要求的答案就是 `get(nums, 0, nums.size() - 1)` 。如何分治实现这个操作呢？对于一个区间 $[l, r]$，我们取 $m = \lfloor \frac{l+r}{2} \rfloor$，对区间 $[l, m]$ 和 $[m+1, r]$ 分治求解。当递归深入直到区间长度缩小为 1 的时候，递归「开始回升」。这时候我们考虑如何通过 $[l, m]$ 区间的信息和 $[m+1, r]$ 区间的信息合并成区间 $[l, r]$ 的信息。最关键的两个问题是：

*   我们要维护区间哪些信息呢？
*   我们如何合并这些信息呢？

对于一个区间 $[l, r]$，我们可以维护四个量：

*   $lSum$ 表示 $[l, r]$ 内以 $l$ 为左端点的最大子段和
*   $rSum$ 表示 $[l, r]$ 内以 $r$ 为右端点的最大子段和
*   $mSum$ 表示 $[l, r]$ 内的最大子段和
*   $iSum$ 表示 $[l, r]$ 区间的和

以下简称 $[l, m]$ 为「左子区间」，$[m+1, r]$ 为「右子区间」。我们考虑如何维护这些量呢（如何通过左子区间的信息合并得到 $[l, r]$ 的信息）？对于长度为 1 的区间 $[i, i]$，四个量的值都和 `nums[i]` 相等。对于长度大于 1 的区间：

*   首先最好维护的是 $iSum$，$[l, r]$ 的 $iSum$ 就等于「左子区间」的 $iSum$ 加上「右子区间」的 $iSum$。
*   对于 $[l, r]$ 的 $lSum$，存在两种可能，它要么等于「左子区间」的 $lSum$，要么等于「左子区间」的 $iSum$ 加上「右子区间」的 $lSum$，二者取大。
*   对于 $[l, r]$ 的 $rSum$，同理，它要么等于「右子区间」的 $rSum$，要么等于「右子区间」的 $iSum$ 加上「左子区间」的 $rSum$，二者取大。
*   当计算好上面三个量之后，就很好计算 $[l, r]$ 的 $mSum$ 了。我们可以考虑 $[l, r]$ 的 $mSum$ 对应的区间是否跨越 $m$——它可能不跨越 $m$，也就是说 $[l, r]$ 的 $mSum$ 可能是「左子区间」的 $mSum$ 和「右子区间」的 $mSum$ 中的一个；它也可能跨越 $m$，是「左子区间」的 $rSum$ 和「右子区间」的 $lSum$ 求和。三者取大。

这样问题就得到了解决。

```java
class Solution {
    class Item{
        int lsum;
        int rsum;
        int msum;
        int isum;
    }
    public Item findItem(int[] nums,int i,int j){
        if(i==j){
            Item item=new Item();
            item.lsum=nums[i];
            item.rsum=nums[i];
            item.msum=nums[i];
            item.isum=nums[i];
            return item;
        }
        else if(i==j-1){
            Item item=new Item();
            item.isum=nums[i]+nums[j];
            item.lsum=nums[i]+nums[j]>nums[i]?nums[i]+nums[j]:nums[i];
            item.rsum=nums[i]+nums[j]>nums[j]?nums[i]+nums[j]:nums[j];
            item.msum=Math.max(Math.max(item.lsum,item.rsum),item.isum);
            return item;
        }
        int mid=(i+j)/2;
        Item leftItem=findItem(nums,i,mid-1);
        Item rightItem=findItem(nums,mid,j);
        Item item=new Item();
        item.isum=leftItem.isum+rightItem.isum;
        item.lsum=Math.max(leftItem.lsum,leftItem.isum+rightItem.lsum);
        item.rsum=Math.max(rightItem.rsum,rightItem.isum+leftItem.rsum);
        item.msum=Math.max(rightItem.msum,Math.max(leftItem.msum,leftItem.rsum+rightItem.lsum));
        return item;
    }
    public int maxSubArray(int[] nums) {
        int len=nums.length;
        Item i=findItem(nums,0,len-1);
        return i.msum;
    }
}
```

实际上：

```java
class Solution {
    public class Status {
        public int lSum, rSum, mSum, iSum;

        public Status(int lSum, int rSum, int mSum, int iSum) {
            this.lSum = lSum;
            this.rSum = rSum;
            this.mSum = mSum;
            this.iSum = iSum;
        }
    }

    public int maxSubArray(int[] nums) {
        return getInfo(nums, 0, nums.length - 1).mSum;
    }

    public Status getInfo(int[] a, int l, int r) {
        if (l == r) {
            return new Status(a[l], a[l], a[l], a[l]);
        }
        int m = (l + r) >> 1;
        Status lSub = getInfo(a, l, m);
        Status rSub = getInfo(a, m + 1, r);
        return pushUp(lSub, rSub);
    }

    public Status pushUp(Status l, Status r) {
        int iSum = l.iSum + r.iSum;
        int lSum = Math.max(l.lSum, l.iSum + r.lSum);
        int rSum = Math.max(r.rSum, r.iSum + l.rSum);
        int mSum = Math.max(Math.max(l.mSum, r.mSum), l.rSum + r.lSum);
        return new Status(lSum, rSum, mSum, iSum);
    }
}

```

### 复杂度分析

假设序列 $a$ 的长度为 $n$。

*   时间复杂度：假设我们把递归的过程看作是一颗二叉树的先序遍历，那么这颗二叉树的深度 $O(log n)$，这里的总时间相当于遍历这颗二叉树的所有节点，故总时间的渐进上界是 $O(\sum_{i=1}^{log n} 2^{i-1}) = O(n)$，故渐进时间复杂度为 $O(n)$。
*   空间复杂度：递归会使用 $O(log n)$ 的栈空间，故渐进空间复杂度为 $O(log n)$。



时间复杂度相同，但是因为使用了递归，并且维护了四个信息的结构体，运行的时间略长，空间复杂度也不如方法一优秀，而且难以理解。那么这种方法存在的意义是什么呢？

对于这道题而言，确实是如此的。但是仔细观察「方法二」，它不仅仅可以解决区间 $[0, n - 1]$，还可以用于解决任意的子区间 $[l, r]$ 的问题。如果我们把 $[0, n - 1]$ 分治下去出现的所有子区间的信息都用堆式存储的方式记忆化下来，即建成一棵真正的树之后，我们就可以在 $O(log n)$ 的时间内求到任意区间内的答案，我们甚至可以修改序列中的值，做一些简单的维护，之后仍然可以在 $O(log n)$ 的时间内求到任意区间内的答案，对于大规模查询的情况下，这种方法的优势便体现了出来。这棵树就是上文提及的一种神奇的数据结构——线段树。