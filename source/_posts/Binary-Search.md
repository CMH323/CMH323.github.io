---
title: Binary Search
date: 2024-01-11 11:14:11
tags:
- Algorithm
- Binary Search
---
# Binary Search
This post is all about binary search.
<!--more-->
## 二分查找 红蓝染色法
[34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/description/)

- 要求nums是非递减的，即 `nums[i] <= nums[i + 1]`
- 返回最小的满足 `nums[i] >= target` 的 `i`
- 闭区间 `[left, right]`
- 如果不存在， 返回`nums.length`

```java
class Solution {
    public int lowerBound(int[] nums, int target){
        // 时间复杂度 O(logn)
        // 空间复杂度 O(1)
        int n = nums.length;
        int left = 0;
        int right = n - 1;
        // 闭区间 [left, right]
        int mid = 0;
        while(left <= right){//区间不为空
            mid = left + (right - left)/2;
            if(nums[mid] < target){
                left = mid + 1; // [mid + 1, right]
            }
            else
                right = mid - 1; // [left, mid - 1]
        }
        return left;
    }
    public int[] searchRange(int[] nums, int target) {
        int n = nums.length;
        int[] ans = new int[2];
        int start = lowerBound(nums, target);
        if(start == n || nums[start] != target){
            ans[0] = -1;
            ans[1] = -1;
            return ans;
        }
        int end = lowerBound(nums, target+1) - 1;
        ans[0] = start;
        ans[1] = end;
        return ans;
    }
}
```

## 二分查找 搜索旋转排序数组
[153. 寻找旋转排序数组中的最小值](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/submissions/1142998437/)

数组可能是以下两种情况之一：
1. 数组从头到位递增 e.g. [ 11, 13, 15, 17]
2. 数组分两端递增，并且数组开始值一定大于数组末尾值 e.g. [ 3, 4, 5, 1, 2]


```java
class Solution {
    public int findMin(int[] nums) {
        // 蓝色表示false, 即最小值左侧
        // 红色表示true, 即最小值及其右侧
        int n = nums.length;
        int left = 0;
        int right = n - 2; // 数组最后一个值一定是蓝色，在最小值右侧或为最小值
        int mid = 0;
        while(left <= right){
            mid = left + (right - left)/ 2;
            if(nums[mid] < nums[n - 1])  // 蓝色
               right = mid - 1;
            else left = mid + 1;
        }
        return nums[right + 1];
    }
}
```

[33. 搜索旋转排序数组](https://leetcode.com/problems/search-in-rotated-sorted-array/)

- <font size=3>此题逻辑较为复杂，需要进行分类讨论，数组可能是以下两种情况之一：</font>
1. 数组从头到位递增 e.g. [ 11, 13, 15, 17]。
2. 数组分两端递增，并且数组开始值一定大于数组末尾值 e.g. [ 3, 4, 5, 1, 2]。
<br>我们需要考虑target是在前半段还是在后半段。

```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length;
        int left = 0;
        int right = n - 2;
        int mid = 0;
        int low_index = 0;
        for(int i = 0; i < n - 1; i++){
            if(nums[i+1] > nums[i]) low_index ++;
            else break;  
        }   
        if(target >= nums[0] && target <= nums[low_index]){
            left = 0;
            right = low_index;
            while(left <= right){
                mid = (left + right)/2;
                if(nums[mid] < target){
                    left = mid + 1;
                }
                else right = mid - 1;
            }
        }
        else{
            left = low_index + 1;
            right = n - 1;
            while(left <= right){
                mid = (left + right)/2;
                if(nums[mid] < target){
                    left = mid + 1;
                }
                else right = mid - 1;
            }
        }
        if(left < n && nums[left] == target) return left;
        else return -1;
 
    }
}
```



[875. 爱吃香蕉的珂珂](https://leetcode.com/problems/koko-eating-bananas/description/)

- 二分查找关键要找到对什么进行二分，此题中我们要求的是最小吃香蕉速度k, 那就对吃香蕉速度的值域进行二分，从而查找出最小速度。
- 二分判断条件是否取等号要根据题目要求来灵活确定。 本题中要求最小k，所以我们的h要达到最大，那么不符合条件的情况是`h_now > h`.
- 向上取整的时候`Math.ceil()`可能会失效，注释掉的部分是优雅的向上取整
- 选用`long h_now`是满足大数的情况。

```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        Arrays.sort(piles);
        int n = piles.length;
        // int min = piles[0];
        int max = piles[n - 1];
        int ans = 0;
        int left = 1;
        int right = piles[n - 1];
        int mid = 0;
        while(left <= right){
            mid = left + (right - left) / 2;
            long h_now = 0;
            for(int i = 0; i < n; i++){
                h_now += (piles[i] % mid == 0) ? (piles[i]/mid) : (piles[i]/mid + 1);
            }
            // 优雅的向上取整写法
            // long res = 0;
            // for(int pile : piles){
            //     res += (pile + mid - 1 )/mid;
            // }
            if(h_now <= h)
                right = mid - 1;
            else
                left = mid + 1;
        }
        return left;

        
    }
}
```

[1760. 袋子里最少数目的球](https://leetcode.com/problems/minimum-limit-of-balls-in-a-bag/description/)

- 本题要求的是袋子中球数量经过操作后的最大值，其实本质上跟875题一样。我们对最终的最小数量进行二分，求出在此数量下需要进行的操作数，判断操作数根我们给出的maxOperations的大小关系来调整
- 再次重复二分的重点首先是确定二分对象及其值域，其次是找出判别条件。

```java
class Solution {
    public int maxNum(int[] nums){
        int max = 0;
        for(int i = 0;i < nums.length;i++){
            if(nums[i] > max){
                max = nums[i];
            }
        }
        return max;
    }
    public int minimumSize(int[] nums, int maxOperations) {
        int n = nums.length;
        int left = 1;
        int right = maxNum(nums);
        while(left <= right){
            int mid = left + (right - left) / 2; 
            int count = 0;
            for(int i = 0; i < n;i++){
                // 计算当前值为最大值需要做几次opration
                count += (nums[i] + mid - 1) / mid - 1;
            }
            if(count <= maxOperations){
                right = mid - 1;
            }
            else left = mid + 1;
        }
        return left;
    }
}
```


[1011. 在D天内送达包裹的能力](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/description/)

- 本题一个重大的坑在于left的最小值要从weights的最大值开始取，一艘船至少要运走一天的东西，不然题目中的条件就不成立。
- 本题的研究对象为ship的值，取值范围是weights中的最大值, 到weights整个数组的和。
- 判断条件为当前的ship值下，运送完所有货物需要的天数count, 与当前mid的关系。

```java
class Solution {
    public int shipWithinDays(int[] weights, int days) {
        int n = weights.length;
        int total = 0;
        int max = 0;
        for(int i = 0; i < n; i ++){
            total += weights[i];
            max = Math.max(max,weights[i]);

        }
        int left = max;
        int right = total;
        int mid = 0;
        while(left <= right){
            mid = left + (right - left) / 2;
            int count = 1;
            int ship = 0;
            for(int i = 0; i < n; i ++){
                if(ship <= mid){
                    ship += weights[i];
                }
                if(ship > mid){
                    count ++;
                    ship = 0;
                    ship += weights[i];
                }     
            }
            if(count <= days){
                right = mid - 1;
            }
            else left = mid + 1;
        }
        return left;
    }
}
```