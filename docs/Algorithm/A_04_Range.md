---
sidebar_position: 4
---

# 区间

## [228. 汇总区间](https://leetcode.cn/problems/summary-ranges/)

```java
class Solution {
    public List<String> summaryRanges(int[] nums) {
        List<String> list = new ArrayList<>();
        if(nums.length == 0){
            return list;
        }
        int l = nums[0], r = nums[0];
        for(int i = 1; i < nums.length; i++){
            if(nums[i] == nums[i-1] + 1){
                r = nums[i];
            } else {
                r = nums[i - 1];
                if(l == r){
                    list.add(String.valueOf(l));
                } else {
                    list.add(String.valueOf(l) + "->" + String.valueOf(r));
                }
                if(i < nums.length) {
                    l = nums[i];
                }
            }
        }
        //if(r!=nums[nums.length-1]){
            if(l == nums[nums.length - 1]){
                list.add(String.valueOf(l));
            } else {
                list.add(String.valueOf(l) + "->" + String.valueOf(nums[nums.length-1]));
            }
        //}
        return list;
    }
}
```

