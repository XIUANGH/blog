# [41. 缺失的第一个正数](https://leetcode.cn/problems/first-missing-positive/)

给你一个未排序的整数数组 `nums` ，请你找出其中没有出现的最小的正整数。

请你实现时间复杂度为 `O(n)` 并且只使用常数级别额外空间的解决方案。

 

**示例 1：**

```
输入：nums = [1,2,0]
输出：3
解释：范围 [1,2] 中的数字都在数组中。
```

**示例 2：**

```
输入：nums = [3,4,-1,1]
输出：2
解释：1 在数组中，但 2 没有。
```

**示例 3：**

```
输入：nums = [7,8,9,11,12]
输出：1
解释：最小的正数 1 没有出现。
```

 

**提示：**

- `1 <= nums.length <= 105`
- `-231 <= nums[i] <= 231 - 1`



# 解答

## 1 哈希表

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int len=nums.length;
        HashSet<Integer> set=new HashSet<>();
        int target=1;
        for(int i=0;i<len;i++){
            if(nums[i]>0){
                set.add(nums[i]);
                if(set.contains(target)){
                    target++;
                }
            }
        }
        while(set.contains(target)){
            target++;
        }
        return target;
    
}
```

使用了哈希表，时间复杂度为 *O*(*N*)，空间复杂度为 *O*(*N*)



## 2 自定义哈希

对于整数数组 `nums`来说，其元素个数为`len`，第一个未出现的数，只可能有两个情况：

- 1-len中的任意一个数字
- 要不然就是len+1这个数字

对于这两个情况，可以将一个元素值还原到他本来的位置上去，这样就可以发现哪些元素是没有出现过的，从而找到第一个没有出现的值。也就是说，对于数值1来说，其应该放在`index=0`的位置上。也就是关系是`nums[i]=i+1`

在这个数组情况下，可能出现负数的情况，对于这个情况，可以不管这个位置的元素，在不断的交换过程中会将这个元素放在正确的位置上的。

在移动的过程中，只有两个

```java
public int firstMissingPositive(int[] nums) {
    int len=nums.length;
    for(int i=0;i<len;i++){
        //已经是正确的顺序了
        if(nums[i]==i+1){
            continue;
        }
        //开始准备换元素，负数不管，如果要换过去的位置上已经是正确的元素，也不管，如果是超过len的元素，也不管。为什么可以不管：因为数组长度是len，肯定是可以放下全部的、唯一的数字，但是如果出现了一个负数、重复的1<=nums[i]<=len、大于len的数字，这个数字最终的位置就是一个flag，用于标识哪一个位置没有元素。
        while(nums[i]<=len&&nums[i]>0&&nums[nums[i]-1]!=nums[i]){
            swap(nums,i,nums[i]-1);
        }
	}
    for(int i=0;i<len;i++){
        if(i+1!=nums[i]) return i+1;
	}
    //排序全部都是正确的
    return len+1;
}
public void swap(int[] nums,int i,int j){
	int temp=nums[i];
    nums[i]=nums[j];
    nums[j]=temp;
}
```

**复杂度分析**：

- 时间复杂度：*O*(*N*)，这里 *N* 是数组的长度。

说明：`while` 循环不会每一次都把数组里面的所有元素都看一遍。如果有一些元素在这一次的循环中被交换到了它们应该在的位置，那么在后续的遍历中，由于它们已经在正确的位置上了，代码再执行到它们的时候，就会被跳过。

最极端的一种情况是，在第 1 个位置经过这个 `while` 就把所有的元素都看了一遍，这个所有的元素都被放置在它们应该在的位置，那么 `for` 循环后面的部分的 `while` 的循环体都不会被执行。

平均下来，每个数只需要看一次就可以了，`while` 循环体被执行很多次的情况不会每次都发生。这样的复杂度分析的方法叫做**均摊复杂度分析**。

最后再遍历了一次数组，最坏情况下要把数组里的所有的数都看一遍，因此时间复杂度是 *O*(*N*)。

- 空间复杂度：*O*(1)。