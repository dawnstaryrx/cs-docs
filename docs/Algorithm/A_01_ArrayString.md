---
sidebar_position: 1
---

# 数组/字符串

## [88. 合并两个有序数组](https://leetcode.cn/problems/merge-sorted-array/)

1. 开一个m+n数组；
2. 从头开始比较。

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int[] nums3 = new int[m + n];
        int k1 = 0;
        int k2 = 0;
        int k3 = 0;
        if(n == 0) return;
        while(k1 < m || k2 < n){
            if(k1 < m && (k2 >= n || nums1[k1] <= nums2[k2])){
                nums3[k3] = nums1[k1];
                k3 ++;
                k1 ++;
            } else {
                nums3[k3] = nums2[k2];
                k3 ++;
                k2 ++;
            }
        }
        for(int i = 0; i < m+n; i++){
            nums1[i] = nums3[i];
        }
    }
}
```

从后向前比较。

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int k1 = m-1;
        int k2 = n-1;
        int k3 = m+n - 1;
        // 从后向前合并两个数组
        while (k1 >= 0 && k2 >= 0) {
            if (nums1[k1] > nums2[k2]) {
                nums1[k3] = nums1[k1];
                k1--;
            } else {
                nums1[k3] = nums2[k2];
                k2--;
            }
            k3--;
        }
        // 如果 nums2 还有剩余元素，复制到 nums1 前面
        while (k2 >= 0) {
            nums1[k3] = nums2[k2];
            k2--;
            k3--;
        }
        // 不需要处理 nums1，因为它已经在正确的位置
    }
}
```

## [27. 移除元素](https://leetcode.cn/problems/remove-element/)

快慢指针。

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int k = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != val) {
                nums[k] = nums[i];
                k++;
            }
        }
        return k;
    }
}
```

## [26. 删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)

快慢指针。

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int fast = 0;
        int slow = 0;
        int k = 0;
        while(fast < nums.length - 1){
            if(nums[fast] != nums[fast + 1]){
                nums[slow] = nums[fast];
                slow ++;
                k ++;
            }
            fast ++;
        }
        nums[k] = nums[nums.length - 1];
        k++; 
        return k;
    }
}
```

## [80. 删除有序数组中的重复项 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/)

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length <= 2) {
            return nums.length;
        }
        int slow = 2;
        for (int fast = 2; fast < nums.length; fast++) {
            if (nums[fast] != nums[slow - 2]) {
                nums[slow] = nums[fast]; 
                slow++;
            }
        }
        return slow;
    }
}
```



