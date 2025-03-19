---
sidebar_position: 10
---

# 数学

## [204. 计数质数](https://leetcode.cn/problems/count-primes/)

### 枚举

```java
class Solution {
    public int countPrimes(int n) {
        int count = 0;
        int num = 0;
        while(num < n){
            if(isPrime(num)) count++;
            num ++;
        }
        return count;
    }
    private boolean isPrime(int num){
        if(num == 0 || num == 1) return false;
        if(num == 2) return true;
        boolean res = true;
        for(int i = 2; i*i <= num; i++){
            if(num % i == 0) res = false;
        }
        return res;
    }
}
```

### 埃氏筛

```java
class Solution {
    public int countPrimes(int n) {
        if (n <= 2) return 0;
        
        boolean[] isPrime = new boolean[n];
        Arrays.fill(isPrime, true);
        isPrime[0] = isPrime[1] = false;
        
        int count = 0;
        for (int i = 2; i * i < n; i++) {
            if (isPrime[i]) {
                for (int j = i * i; j < n; j += i) {
                    isPrime[j] = false;
                }
            }
        }
        
        for (boolean val : isPrime) {
            if (val) count++;
        }
        return count;
    }
}
```

