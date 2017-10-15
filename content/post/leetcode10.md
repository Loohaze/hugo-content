---
title: "LeetCode10题解"
date: 2017-10-10T10:44:35+08:00
author: "loohaze"
---

### 题目: Regular Expression Matching
```
Implement regular expression matching with support for
'.' and '*'.

'.' Matches any single character.
'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

The function prototype should be:
bool isMatch(const char *s, const char *p)

Some examples:
isMatch("aa","a") → false
isMatch("aa","aa") → true
isMatch("aaa","aa") → false
isMatch("aa", "a*") → true
isMatch("aa", ".*") → true
isMatch("ab", ".*") → true
isMatch("aab", "c*a*b") → true
```
目的是为了实现正则表达式的匹配， 用'.'来匹配任意一个字符，'*'用来匹配任意个'\*'前的字符。

匹配的过程都有规律可寻，使用动态规划的方法，用$boolean$  $dp[text.length()+1][pattern.length()+1]$来表示匹配结果

其中$dp[i][j]$表示**text的前i位**与**pattern的前j位**匹配。

***


### 代码

```java
public boolean isMatch2(String text, String pattern){   //动态规划
        int m = text.length(), n = pattern.length();

        boolean dp[][] = new boolean[m+1][n+1];
        dp[0][0] = true;

        //初始化第0行，除了[0][0]为空串匹配空串，其他均不能匹配
        for (int i = 1; i <= m ; i++){
            dp[i][0] = false;
        }

        //当且仅当p[0.... j-2] 匹配空串、pattern.charAt(j) == '*'时，p[0...j]匹配空串
        for (int j = 1; j <= n ; j++){
            dp[0][j] = (j > 1) && (pattern.charAt(j-1) == '*') && dp[0][j-2];
        }

        for (int i = 1; i <= m ; i++){
            for (int j = 1; j <= n; j++){
                if (pattern.charAt(j-1) == '*'){
                    // dp[i][j-2] : x* 匹配空串  （重复0次）
                    // dp[i][j-1] : x* 匹配x    （重复1次）
                    // dp[i-1][j] && text[i+1] == x : x* 匹配x*x （重复>=2次）
                    dp[i][j] = dp[i][j-2] && (j-1) > 0 || dp[i][j-1] || dp[i-1][j] && (j-1) > 0 && (text.charAt(i-1) == pattern.charAt(j-2) || pattern.charAt(j-2) == '.');
                }else{  //当pattern这一位不是*时
                    dp[i][j] = (pattern.charAt(j-1) == '.' || text.charAt(i-1) == pattern.charAt(j-1)) && dp[i-1][j-1];
                }
            }
        }
        return dp[m][n];
    }
```
