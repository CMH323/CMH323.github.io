---
title: Double Pointer
date: 2024-01-09 11:28:29
tags:
- Algorithm
- Double Pointer
---
# Double Pointer
双指针适用于有单调性的情境中。
<!--more-->
## 同向双指针
[167. 两数之和II - 输入有序的数组](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

- 我们要找到唯一的相加为target的两数index
- 数组是递增的，所以我们将左右指针放在数组两端
- 若是两数之和大于target,说明右端点的数太大了，right--
- 若是两数之和小于target,说明左端点的数太小了，left++

```java
class Solution {
    // 时间复杂度 O(n)
    // 空间复杂度 O(1)
    public int[] twoSum(int[] numbers, int target) {
        int[] ans = new int[2];
        int n = numbers.length;
        int left = 0;
        int right = n - 1;
        int sum = 0;
        while(left < right){
            sum = numbers[left] + numbers[right];
            if(sum == target){
                break;
            }
            else if(sum > target){
                right --;
            }
            else{
                left ++;
            }
        }
        ans[0] = left + 1;
        ans[1] = right + 1;
        return ans;
    }
}
```

[15. 三数之和](https://leetcode.com/problems/3sum/description/)

- 要首先对数组进行排序才能用双指针做
- 题目已知返回结果中的三元数组内部顺序不重要
- 逻辑类似两数之和，三数下标分别为i < j < k
- 答案中不可以包含重复的三元组，所以i、j、k在各自走的时候，如果遇到相邻的i、j、k是一样的，要跳过

优化：<br>
1. 若数组进行递增排序后，最小的前三数之和仍大于0，则不存在符合条件的，直接break
2. 若数组当前最左端点加上最末两数的和仍旧小于0，说明最左端点太小了，要继续往右走，需要continue


```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        // 时间复杂度 O(n^2)
        // 空间复杂度 O(1)
        Arrays.sort(nums);
        // 三元组的顺序并不重要
        // i < j < k
        // 答案中不可以包含重复的三元组
        int n = nums.length;
        int sum = 0;
        int j = 0;
        int k = 0;
        List<List<Integer>> ans = new ArrayList<>();
        for(int i = 0; i < n - 2;i ++){
            if(i > 0 && nums[i] == nums[i-1])
                continue;
            if(nums[i] + nums[i+1] + nums[i+2] > 0)
                break;
            if(nums[i] + nums[n-2] + nums[n-1] < 0)
                continue;
            j = i + 1;
            k = n - 1;
            while(j < k){
                sum = nums[i] + nums[j] + nums[k];
                if(sum < 0){
                    j ++;
                }
                else if(sum > 0){
                    k --;
                }
                else{
                    ans.add(Arrays.asList(nums[i], nums[j], nums[k]));
                    j++;
                    while(j < k && nums[j] == nums[j-1]){
                        j++;
                    }
                    k --;
                    while(j < k && nums[k] == nums[k+1]){
                        k --;
                    }
                }
            }
        }
        return ans;
    }
}
```



[42. 接雨水](https://leetcode.com/problems/trapping-rain-water/)

- pre_max存储前缀最大值
- suf_max存储后缀最大值
- 每一格能接的雨水是pre_max和suf_max的最小值,再减去那一格的高度

做法一： 遍历数组三次，先分别求pre_max和suf_max，最后一遍计算接雨水总量
```java
//时间复杂度 O(n)
//空间复杂度 O(n)
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int[] pre_max = new int[n];
        int[] suf_max = new int[n];
        int ans = 0;
        pre_max[0] = height[0];
        suf_max[n-1] = height[n-1];
        for(int i = 1; i < n; i++){
            pre_max[i] = Math.max(pre_max[i-1],height[i]);
        }
        for(int i = n - 2; i >= 0; i--){
            suf_max[i] = Math.max(suf_max[i+1],height[i]);
        }
        for(int i = 0; i < n; i++){
            ans += Math.min(pre_max[i],suf_max[i]) - height[i];
        }
        return ans;
    }
}
```

做法二: pre_max从左往右走，suf_max从右往左走。因为pre_max从左往右是单调递增的，suf_max从左往右是单调递减的，所以suf_max在左边的时候一定大于等于pre_max，所以可以直接计算pre_max - height[left],不会有问题。
- 如果pre_max < suf_max, suf_max往左走
- 如果pre_max > suf_max, pre_max往右走

```java
//时间复杂度 O(n)
//空间复杂度 O(1)
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int pre_max = 0;
        int suf_max = 0;
        int ans = 0;
        int left = 0;
        int right = n - 1;
        while(left <= right){
            pre_max = Math.max(pre_max,height[left]);
            suf_max = Math.max(suf_max,height[right]);
            if(pre_max < suf_max){
                ans += pre_max - height[left];
                left ++;
            }
            else{
                ans += suf_max - height[right];
                right --;
            }
        }
        return ans;
    }
}
```

## 同向双指针

[209. 长度最小的子数组](https://leetcode.com/problems/minimum-size-subarray-sum/description/)

- 不断向右缩小左端点
- 先从头开始向右延伸数组，如果当前数组的和大于target,将数组从左端点开始收缩
- res的初始值要大于n，且返回时需要判断res是否大于n,是的话返回0
```java
// 时间复杂度 O(n)
// 空间复杂度 O(1)
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0;
        int sum = 0;
        int n = nums.length;
        int res = n + 1;
        for(int i = 0; i < n; i++){
            sum += nums[i];
            // 本题中要求最小的子数组长度，所以要收缩到最短
            while(sum - nums[left] >= target){
                sum -= nums[left];
                left ++;
            }
            if(sum >= target){
                res = Math.min(res,i-left+1);
            }
        }
        return res > n ? 0 :res;
    }
}
```

[713. 乘积小于k的子数组](https://leetcode.com/problems/subarray-product-less-than-k/description/)

- 不断向右缩小左端点
- 先从头开始向右延伸数组，如果当前数组的乘积大于等于k,将数组从左端点开始收缩
- [l,r] 这一段包含[l,r],[l+1,r]...[r,r]，一共(r-l+1)个数组
```java
// 时间复杂度 O(n)
// 空间复杂度 O(1)
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        if(k <= 1)
            return 0;
        int product = 1;
        int n = nums.length;
        int left = 0;
        int ans = 0;
        for(int i = 0; i < n;i ++){
            product *= nums[i];
            // 此处实际上求出从右端点i开始的、最长的、满足乘积小于k的数组
            while(product >= k){
                product /= nums[left];
                left ++;
            }
            ans += i - left + 1;
        }
        return ans;
    }
}
```

[3. 无重复字符的最长子串](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

- 用HashMap来存当前左右两端点之间的数组的字母分布和数量
- 依旧是遍历右端点，收缩左端点
- 如果右端点的那个字符在HashMap中大于一个，则不断收缩左端点
- 收缩至符合条件记录当前的数组长度，不断取max

 ```java
// 时间复杂度 O(n)
// 空间复杂度 O(128) O(1) O(len(set(s)))
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        int left = 0;
        int ans = 0;
        int right_num = 0;
        int left_num = 0;
        HashMap<Character,Integer> hash = new HashMap<Character,Integer>();
        for(int i = 0; i < n; i++){
            char right_char = s.charAt(i);
            right_num = hash.getOrDefault(right_char,0);
            hash.put(right_char,right_num+1);
            while(hash.get(right_char) > 1){
                char left_char = s.charAt(left);
                left_num = hash.getOrDefault(left_char,0);
                hash.put(left_char,left_num-1);
                left ++;
            }
            ans = Math.max(ans, i - left + 1);
        }
        return ans;
    }
}

```

[2958. 最多 K 个重复元素的最长子数组](https://leetcode.com/problems/length-of-longest-subarray-with-at-most-k-frequency/description/)

- 遍历右指针，收缩左指针
- 若右指针的键对应数量大于k, 则收缩左指针


```java
class Solution {
    public int maxSubarrayLength(int[] nums, int k) {
        HashMap<Integer,Integer> hash = new HashMap<>();
        int n = nums.length;
        int l = 0;
        int ans = 0;
        for(int i = 0; i < n; i ++){
            int right_count = hash.getOrDefault(nums[i], 0);
            right_count += 1;
            hash.put(nums[i],right_count);
            while(l <= i && hash.get(nums[i]) > k){

                int left_count = hash.getOrDefault(nums[l], 0);
                hash.put(nums[l],left_count - 1);
                l ++;
            }
            ans = Math.max(ans,i - l + 1);
        }
        return ans;
    }
}
```


[870. 优势洗牌](https://leetcode.com/problems/advantage-shuffle/)

- 此题逻辑较难，参照田忌赛马：
    1. 优等马为数组中最大值，劣等马为数组中最小值
    2. 若num1中劣等马 > nums2中劣等马： 答案中nums2中劣等马下标的位置放num1中劣等马
       若num1中劣等马 <= nums2中劣等马： 答案中nums2中优等马下标的位置放num1中劣等马
- 从小到大遍历nums1数组，根据情况从两端遍历nums2数组
- nums1按照升序排列，nums2扩展成二维数组包含下标后升序排列

```java
class Solution {
    public int[] advantageCount(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int[] ans = new int[n];
        // nums1 按照升序排列
        Arrays.sort(nums1);
        int[][] nums2WithIndex  = new int[n][2];
        for (int i = 0; i < n; i++) {
            nums2WithIndex[i] = new int[]{nums2[i], i};
        }
        // 根据 nums2WithIndex 数组的第一列（即 nums2 数组的元素值）进行升序排序
        Arrays.sort(nums2WithIndex, (a, b) -> Integer.compare(a[0], b[0]));
        // left, right 分别对应nums1中最小值和最大值的下标
        int left = 0;
        int right = n - 1;
        // 从小到大遍历nums1数组，根据情况从两端遍历nums2数组
        for (int i = 0; i < n; i++) {
            // 如果nums1 中最小值大于 nums2中最小值
            if(nums1[i] > nums2WithIndex[left][0]){
                // 结果中nums2最小值的idx的位置是nums1最小值 
                ans[nums2WithIndex[left][1]] = nums1[i];
                // left对应nums2未处理过的数里的最小值的index
                left ++;
            }
            else{
                // 结果中nums2最大值的idx的位置是nums1最小值 
                ans[nums2WithIndex[right][1]] = nums1[i];
                // right对应nums2未处理过的数里的最大值的index
                right --;
            }
        }
        return ans;
    }
}
```


[2824. 统计和小于目标的下标对数目](https://leetcode.cn/problems/count-pairs-whose-sum-is-less-than-target/description/)

- 此题需我们找到**和小于目标的下表对的数量**,由于只需要找到**数量**，我们可以进行排序
- 由于数组是有序的，若左右指针对应的数之和小于target，则左指针与右指针之左的数之和也满足条件，所以count可以加(right - left)

```java
class Solution {
    public int countPairs(List<Integer> nums, int target) {
        int n = nums.size();
        int left = 0;
        int right = n - 1;
        Collections.sort(nums);
        int count = 0;
        while(left < right){
            int sum = nums.get(left) + nums.get(right);
            if(sum >= target){
                right --;
            }
            else{
                count ++;
            }
        }
        return count;
    }
}
```