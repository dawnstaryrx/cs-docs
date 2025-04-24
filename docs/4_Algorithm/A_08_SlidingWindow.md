---
sidebar_position: 8
---

# 滑动窗口

## [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int n = nums.length;
        if(n == 0) return 0;

        int ans = Integer.MAX_VALUE;
        for(int i = 0; i < n; i++){
            int sum = 0;
            for(int j = i; j < n; j++){
                sum += nums[j];
                if(sum >= target){
                    ans = Math.min(ans, j-i+1);
                    break;
                }
            }
        }
        return ans == Integer.MAX_VALUE ? 0 : ans;
    }
}
```

滑动窗口

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int n = nums.length;
        if(n == 0) return 0;

        int ans = Integer.MAX_VALUE;
        int start = 0, end = 0;
        int sum = 0;
        while(end < n){
            sum += nums[end];
            while(sum >= target) {
                ans = Math.min(ans, end - start + 1);
                sum -= nums[start];
                start ++;
            }
            end ++;
        }
        return ans == Integer.MAX_VALUE ? 0 : ans;
    }
}
```

## [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        char[] ss = s.toCharArray();
        Set<Character> set = new HashSet<>();
        int res = 0;
        for(int left = 0, right = 0; right < s.length(); right ++){
            char ch = ss[right];
            while(set.contains(ch)){
                set.remove(ss[left]);
                left ++;
            }
            set.add(ss[right]);
            res = Math.max(res, right - left  + 1);
        }
        return res;
    }
}
```

