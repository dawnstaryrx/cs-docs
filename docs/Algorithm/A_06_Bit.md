---
sidebar_position: 6
---

# 位运算

## [136. 只出现一次的数字](https://leetcode.cn/problems/single-number/)

### 哈希表

```java
class Solution {
    public int singleNumber(int[] nums) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < nums.length; i++){
            map.put(nums[i], map.getOrDefault(nums[i], 0) + 1);
        }
        for(int i = 0; i < nums.length; i++){
            if(map.get(nums[i]) == 1){
                return nums[i];
            }
        }
        return 0;
    }
}
```

### 异或运算

对于这道题，可使用异或运算 ⊕。异或运算有以下三个性质。

- 任何数和 0 做异或运算，结果仍然是原来的数，即 a⊕0=a。
- 任何数和其自身做异或运算，结果是 0，即 a⊕a=0。
- 异或运算满足交换律和结合律，即 a⊕b⊕a=b⊕a⊕a=b⊕(a⊕a)=b⊕0=b。

```java
class Solution {
    public int singleNumber(int[] nums) {
        int single = 0;
        for (int num : nums) {
            single ^= num;
        }
        return single;
    }
}
```

## [191. 位1的个数](https://leetcode.cn/problems/number-of-1-bits/)

观察这个运算：*n* & (*n*−1)，其运算结果恰为把 *n* 的二进制位中的最低位的 1 变为 0 之后的结果。

这样我们可以利用这个位运算的性质加速我们的检查过程，在实际代码中，我们不断让当前的 n 与 n−1 做与运算，直到 n 变为 0 即可。因为每次运算会使得 n 的最低位的 1 被翻转，因此运算次数就等于 n 的二进制位中 1 的个数。

```java
class Solution {
    public int hammingWeight(int n) {
        int ret = 0;
        while(n != 0){
            n &= n-1;
            ret ++;
        }
        return ret;
    }
}
```

