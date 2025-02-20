---
sidebar_position: 1
---

# 数组/字符串

## [88. 合并两个有序数组](https://leetcode.cn/problems/merge-sorted-array/)

### 从头开始比较

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

### 从后向前比较

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

## [121. 买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)

若想达到最高利润，**则一定选择价格最低的交易日买入**。

```java
class Solution {
    public int maxProfit(int[] prices) {
        int min = Integer.MAX_VALUE;
        int maxProfit = 0;
        for(int i = 0; i < prices.length; i ++){
            if(prices[i] < min){
                min = prices[i];
            } else {
                if(prices[i] - min > maxProfit){
                    maxProfit = prices[i] - min;
                }
            }
        }
        return maxProfit;
    }
}
```

## [122. 买卖股票的最佳时机 II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

把所有的涨幅都赚到，把所有的跌幅都躲过，就是最大利润。

```java
class Solution {
    public int maxProfit(int[] prices) {
        int maxProfit = 0;
        for(int i = 0; i < prices.length - 1; i++){
            if(prices[i+1] - prices[i] > 0){
                maxProfit += prices[i+1] - prices[i];
            }
        }
        return maxProfit;
    }
}
```

## [55. 跳跃游戏](https://leetcode.cn/problems/jump-game/)

动态更新最远可到达的位置。

```java
class Solution {
    public boolean canJump(int[] nums) {
        int maxJump = 0;
        if(nums.length == 1) return true;
        if(nums[0] == 0) return false;
        for(int i = 0; i < nums.length - 1; i++){
            if(i <= maxJump){
                if(i + nums[i] > maxJump){
                    maxJump = i + nums[i];
                }
            }
            if(maxJump >= nums.length - 1) return true;
        }
        return false;
    }
}
```

## [45. 跳跃游戏 II](https://leetcode.cn/problems/jump-game-ii/)

```java
class Solution {
    public int jump(int[] nums) {
        int pos = nums.length - 1;
        int jumpNums = 0;
        while(pos != 0){
            int newPos = pos;
            for(int i = pos - 1; i >= 0; i --){
                if(i + nums[i] >= pos){
                    newPos = i;
                }
            }
            pos = newPos;
            jumpNums ++;
        }
        return jumpNums;
    }
}
```

## [274. H 指数](https://leetcode.cn/problems/h-index/)

```java
class Solution {
    public int hIndex(int[] citations) {
        // 首先对引用次数进行排序
        Arrays.sort(citations);
        int n = citations.length;
        // 从后向前遍历，寻找 h 指数
        for (int i = 0; i < n; i++) {
            // 计算当前论文的引用次数和论文数量
            if (citations[i] >= n - i) {
                return n - i; // n - i 是当前 h 指数
            }
        }
        
        return 0; // 如果没有找到合适的 h 指数，返回 0
    }
}
```



## [13. 罗马数字转整数](https://leetcode.cn/problems/roman-to-integer/)

```java
class Solution {
    public int romanToInt(String s) {
        char[] chs = s.toCharArray();
        HashMap<String, Integer> hashMap = new HashMap<>();
        hashMap.put("I", 1);
        hashMap.put("V", 5);
        hashMap.put("X", 10);
        hashMap.put("L", 50);
        hashMap.put("C", 100);
        hashMap.put("D", 500);
        hashMap.put("M", 1000);
        int sum = hashMap.get(String.valueOf(chs[chs.length-1]));
        for (int i = 0; i < chs.length-1; i++){
            if (hashMap.get(String.valueOf(chs[i])) < hashMap.get(String.valueOf(chs[i+1]))){
                sum -= hashMap.get(String.valueOf(chs[i]));
            }else{
                sum += hashMap.get(String.valueOf(chs[i]));
            }
        }
        return sum;
    }
}
```



## [58. 最后一个单词的长度](https://leetcode.cn/problems/length-of-last-word/)

```java
class Solution {
    public int lengthOfLastWord(String s) {
        String[] ss = s.trim().split(" ");
        return ss[ss.length - 1].length();
    }
}
```



## [14. 最长公共前缀](https://leetcode.cn/problems/longest-common-prefix/)

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if(strs.length == 0) return "";
        String s = strs[0];
        for(String string : strs){
            while(!string.startsWith(s)){
                if(s.length() == 0) return "";
                s = s.substring(0, s.length() - 1);
            }
        }
        return s;
    }
}
```

## [28. 找出字符串中第一个匹配项的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

```java
class Solution {
    public int strStr(String haystack, String needle) {
        char[] haystackArray = haystack.toCharArray();
        char[] needleArray = needle.toCharArray();
        for(int i = 0; i < haystackArray.length - needleArray.length + 1; i++){
            int k = 0;
            boolean isStrStr = true;
            if(haystackArray[i] == needleArray[0]){
                while(haystackArray[i+k] == needleArray[k]){
                    k++;
                    if(k >= needleArray.length){
                        return i;
                    }
                }
            }
        }
        return -1;
    }
}
```

