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

