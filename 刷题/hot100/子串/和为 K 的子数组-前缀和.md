# [560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 *该数组中和为 `k` 的子数组的个数* 。

子数组是数组中元素的连续非空序列。

 

**示例 1：**

```
输入：nums = [1,1,1], k = 2
输出：2
```

**示例 2：**

```
输入：nums = [1,2,3], k = 3
输出：2
```

 

**提示：**

- `1 <= nums.length <= 2 * 104`
- `-1000 <= nums[i] <= 1000`
- `-107 <= k <= 107`



# 解答

## 1 不适合使用滑动窗口

滑动窗口需要满足**单调性**，当右端点元素进入窗口时，窗口元素和是不能减少的。本题 *nums* 包含**负数**，当负数进入窗口时，窗口左端点反而要向左移动，导致算法复杂度不是线性的。



## 2 前缀和 暴力

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int len=nums.length;
        int[] sumArr=new int[len];
        sumArr[0]=nums[0];
        for(int i=1;i<len;i++){
            sumArr[i]=sumArr[i-1]+nums[i];
        }
        int res=0;
        for(int i:sumArr){
            if(i==k)res++;
        }
        for(int i=0;i<len-1;i++){
            for(int j=i+1;j<len;j++){
                if(sumArr[j]-sumArr[i]==k){
                    res++;
                }
            }
        }
        return res;
    }
}
```

使用了前缀和之后，仍然采用枚举两个点之间的差进行求解，并且单独求前缀和本身为k的情况

复杂度$O(N^2)$



也可以不使用额外的前缀和数组：

```java
public class Solution {
    public int subarraySum(int[] nums, int k) {
        int count = 0;
        for (int start = 0; start < nums.length; ++start) {
            int sum = 0;
            for (int end = start; end >= 0; --end) {
                sum += nums[end];
                if (sum == k) {
                    count++;
                }
            }
        }
        return count;
    }
}

```

在遍历的过程中，从当前的i往前遍历，遍历的过程相当于是求和的过程



## 3 前缀和+哈希

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int len=nums.length;
        HashMap<Integer,Integer> map=new HashMap<>();
        int sum=0;
        int res=0;
        map.put(0,1);
        for(int i=0;i<len;i++){
            sum+=nums[i];
            if(map.containsKey(sum-k)){
                res+=map.get(sum-k);
            }
            map.put(sum,map.getOrDefault(sum,0)+1);
        }
        return res;
    }
}
```

添加代码`map.put(0,1);`的原因是，如果本身前缀和就是k的话，sum-k求得的是0，因此需要提前添加到map中

求解的过程动态求解即可，前缀和动态添加到map中，这样也不会出现重复的情况，类似 **两数之和中的哈希表使用**

哈希表中存放的是次数，代表前面有多少个满足条件的前缀

**复杂度分析**

- 时间复杂度：*O*(*n*)，其中 *n* 为数组的长度。我们遍历数组的时间复杂度为 *O*(*n*)，中间利用哈希表查询删除的复杂度均为 *O*(1)，因此总时间复杂度为 *O*(*n*)。
- 空间复杂度：*O*(*n*)，其中 *n* 为数组的长度。哈希表在最坏情况下可能有 *n* 个不同的键值，因此需要 *O*(*n*) 的空间复杂度。

