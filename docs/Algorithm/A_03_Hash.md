---
sidebar_position: 3
---

# 哈希表

## [383. 赎金信](https://leetcode.cn/problems/ransom-note/)

```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        if(ransomNote.length() > magazine.length()) return false;
        int[] cnt = new int[26];
        for(char ch : magazine.toCharArray()){
            cnt[ch - 'a'] ++;
        }
        for(char ch : ransomNote.toCharArray()){
            cnt[ch - 'a'] --;
        }
        for(int i : cnt){
            if(i < 0) return false;
        }
        return true;
    }
}
```

## [205. 同构字符串](https://leetcode.cn/problems/isomorphic-strings/)

```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        HashMap<Character, Character> st = new HashMap<>();
        HashMap<Character, Character> ts = new HashMap<>();
        for(int i = 0; i < s.length(); i++){
            if(!st.containsKey(s.charAt(i)) ){
                st.put(s.charAt(i), t.charAt(i));
            }
            if(!ts.containsKey(t.charAt(i)) ){
                ts.put(t.charAt(i), s.charAt(i));
            }
            if(st.get(s.charAt(i)) != t.charAt(i) || ts.get(t.charAt(i)) != s.charAt(i)){
                return false;
            }
        }
        return true;
    }
}
```

## [290. 单词规律](https://leetcode.cn/problems/word-pattern/)

```java
class Solution {
    public boolean wordPattern(String pattern, String s) {
        HashMap<Character, String> pattern2string = new HashMap<>();
        HashMap<String, Character> string2pattern = new HashMap<>();
        String[] sArr = s.split(" ");
        if (pattern.length() != sArr.length) {
            return false;
        }
        for (int i = 0; i < pattern.length(); i++) {
            char pChar = pattern.charAt(i);
            String word = sArr[i];

            // 如果映射不存在，添加映射
            if (!pattern2string.containsKey(pChar)) {
                pattern2string.put(pChar, word);
            }
            if (!string2pattern.containsKey(word)) {
                string2pattern.put(word, pChar);
            }

            // 检查映射是否一致
            if (!pattern2string.get(pChar).equals(word) || 
                !string2pattern.get(word).equals(pChar)) {
                return false;
            }
        }
        return true;
    }
}
```

## [242. 有效的字母异位词](https://leetcode.cn/problems/valid-anagram/)

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length()) return false;
        int[] cnt = new int[26];
        for(int i = 0; i < s.length(); i++){
            cnt[s.charAt(i) - 'a'] ++;
        }
        for(int i = 0; i < t.length(); i++){
            cnt[t.charAt(i) - 'a'] --;
            if(cnt[t.charAt(i) - 'a'] < 0) return false;
        }
        return true;
    }
}
```

## [202. 快乐数](https://leetcode.cn/problems/happy-number/)

```java
class Solution {
    public boolean isHappy(int n) {
        HashSet<Integer> seen = new HashSet<>();
        
        while (n != 1 && !seen.contains(n)) {
            seen.add(n);
            n = getNext(n);
        }
        
        return n == 1;
    }
    public int getNext(int number){
        int res = 0;
        while (number > 0) {
            int digit = number % 10; 
            res += digit * digit;
            number /= 10; // 去掉最后一位数字
        }
        return res;
    }
}
```

## [219. 存在重复元素 II](https://leetcode.cn/problems/contains-duplicate-ii/)

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < nums.length; i++){
            if(!map.containsKey(nums[i])){
                map.put(nums[i], i);
            } else {
                if(i - map.get(nums[i]) <= k){
                    return true;
                } else {
                    map.put(nums[i], i);
                }
            }
        }
        return false;
    }
}
```

## [128. 最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        // 如果输入数组为空，返回 0
        if (nums.length == 0) {
            return 0;
        }
        Arrays.sort(nums);
        int maxLength = 1, nowLength = 1;
        for(int i = 1; i < nums.length; i++){
            if(nums[i] == nums[i-1]) continue;
            if(nums[i] == nums[i-1] + 1){
                nowLength ++;
                if(nowLength > maxLength){
                    maxLength = nowLength;
                }
            } else {
                nowLength = 1;
            }
        }
        return maxLength;
    }
}
```

