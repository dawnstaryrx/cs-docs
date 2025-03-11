---
sidebar_position: 2
---

# 双指针

## [125. 验证回文串](https://leetcode.cn/problems/valid-palindrome/)

```java
class Solution {
    public boolean isPalindrome(String s) {
        String ss = s.toLowerCase();
        char[] ssChs = ss.toCharArray();
        String str = "";
        if(str.length() == 0) return true;
        for(int i = 0; i < ssChs.length; i++){
            if ((ssChs[i] >= 'a' && ssChs[i] <= 'z') || (ssChs[i] >= '0' && ssChs[i] <= '9')){
                str += String.valueOf(ssChs[i]);
            }
        }
        for(int i = 0; i < ssChs.length / 2; i++){
            if (str.charAt(i) != str.charAt(str.length()- 1 - i)){
                return false;
            }
        }
        return true;
    }
}
```

## [392. 判断子序列](https://leetcode.cn/problems/is-subsequence/)

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        char[] sChar = s.toCharArray();
        char[] tChar = t.toCharArray();
        int index = 0;
        if(sChar.length == 0) return true;
        if(tChar.length == 0) return false;
        for(int i = 0; i < tChar.length; i++){
            if(tChar[i] == sChar[index]){
                index ++;
                if(index == sChar.length){
                    return true;
                }
            }
        }
        return false;
    }
}
```

## [167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 1;
        int right = numbers.length;
        while (left < right) {
            int sum = numbers[left - 1] + numbers[right - 1];
            if (sum == target) {
                return new int[]{left, right};
            } else if (sum > target) {
                right--;
            } else {
                left++;
            }
        }
        return null;
    }
}
```

## [11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

```java
class Solution {
    public int maxArea(int[] height) {
        int l = 0;
        int r = height.length - 1;
        int area = Math.min(height[l], height[r])*(r-l);
        while(l < r){
            int newArea = 0;
            if(height[l] <  height[r]){
                l ++;
            } else {
                r --;
            }
            newArea = Math.min(height[l], height[r])*(r-l);
            if(newArea > area) area = newArea;
        }
        return area;
    }
}
```

## [15. 三数之和](https://leetcode.cn/problems/3sum/)

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums); // 先排序
        List<List<Integer>> res = new ArrayList<>();
        
        for (int i = 0; i < nums.length; i++) {
            // 跳过重复元素
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            
            // 双指针，目标是找到 nums[l] + nums[r] = -nums[i]
            int l = i + 1, r = nums.length - 1;
            int target = -nums[i];
            
            while (l < r) {
                int sum = nums[l] + nums[r];
                if (sum == target) {
                    res.add(Arrays.asList(nums[i], nums[l], nums[r]));
                    l++;
                    r--;
                    // 跳过重复元素
                    while (l < r && nums[l] == nums[l - 1]) l++;
                    while (l < r && nums[r] == nums[r + 1]) r--;
                } else if (sum < target) {
                    l++;
                } else {
                    r--;
                }
            }
        }
        
        return res;
    }
}
```

