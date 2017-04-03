## Dynamic Programming
动态规划看书也看不懂，反正书上也是用例子来讲解，还不如实际做两道题就领悟的快。动态规划既可以理解成带记忆的递归，也可以理解成数学归纳法。
#### 62. Unique Paths
A robot is located at the top-left corner of a m x n grid. The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid. How many possible unique paths are there? Note: m and n will be at most 100.

Solution: 这个题目是二维动态规划的“母题”，很简单，想清楚2*2的情况就可以做出来。

ps: 高中讲数学归纳法的意义就在这了，动态规划的思想跟数学归纳法很相似，只需要证明两个情况下（0->1, x->x+1）状态转移方程成立即可。
~~~~
public int uniquePaths(int m, int n) {
    int[][] dp = new int[m][n];
    for (int i = 0; i<m; i++)
        dp[i][0] = 1;
    for (int i = 0; i<n; i++)
        dp[0][i] = 1;
    for (int i = 1; i<m; i++)
        for (int j = 1; j<n; j++)
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
    return dp[m - 1][n - 1];
}
~~~~
#### 10. Regular Expression Matching
Implement regular expression matching with support for '.' and '*'.

'.' Matches any single character.

'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

使用动态规划。字符串为s，正则表达式为p。

预处理：用dp[i][j]表示s串的前i个字符和p串的前j个字符是否匹配。dp[0][0] = true；

如果p[i]为'\*'，则dp[0][i] 需要p一直为'\*'才为真

1. 如果s[i] == p[j]或p[j] == '\.'，dp[i+1][j+1] = dp[i][j]；
2. 如果p[j] == '\*'
    1. 如果s[i] == p[j-1] 或p[j1] == '\.'，dp[i+1][j+1]取下列三者的或
        1. dp[i][j+1] （a*代表多个a）
        2. dp[i+1][j] （a*代表一个a）
        3. dp[i+1][j-1] （a*不代表a）
    2. 否则的话dp[i+1][j+1] = dp[i+1][j1]
~~~~
public boolean isMatch(String s, String p) {
    if (s == null || p == null)
        return false;
    boolean [][] dp = new boolean[s.length()+1][p.length()+1];
    dp[0][0] = true;
    for (int i = 0; i<p.length(); i++)
        if (p.charAt(i) == '*' && dp[0][i - 1])
            dp[0][i + 1] = true;
    for (int i = 0; i<s.length(); i++)
        for (int j = 0; j<p.length(); j++){
            if (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.')
                dp[i+1][j+1] = dp[i][j];
            else if (p.charAt(j) == '*') {
                if (s.charAt(i) == p.charAt(j-1) || p.charAt(j-1) == '.')
                    dp[i+1][j+1] = dp[i+1][j-1] || dp[i+1][j] || dp[i][j+1];
                else
                    dp[i+1][j+1] = dp[i+1][j-1];
            }
        }
    return dp[s.length()][p.length()];
}
~~~~
#### 44. Wildcard Matching
Implement wildcard pattern matching with support for '?' and '\*'.

'?' Matches any single character.

'\*' Matches any sequence of characters (including the empty sequence).

The matching should cover the entire input string (not partial).

这题和第10题几乎完全一样，只是关键字略有不同
~~~~
public boolean isMatch(String s, String p) {
    boolean[][] dp = new boolean[s.length()+1][p.length()+1];
    dp[0][0] = true;
    for (int i = 1; i <= p.length(); i++)
        dp[0][i] = p.charAt(i-1) == '*' && dp[0][i-1];
    for (int i = 0; i<s.length(); i++)
        for (int j = 0; j<p.length(); j++){
            if (s.charAt(i) == p.charAt(j) || p.charAt(j) == '?')
                dp[i+1][j+1] = dp[i][j];
            else if (p.charAt(j) == '*')
                dp[i+1][j+1] = dp[i+1][j] || dp[i][j+1];
        }
    return dp[s.length()][p.length()];
}
~~~~
#### 152. Maximum Product Subarray
不管你信不信，此题不是双指针而是dp，姊妹题53. Maximum Subarray，这两题是有一定区别的
~~~~
def maxProduct(self, nums):
    if nums is None:
        return 0
    [maxherepre, minherepre, maxsofar] = [nums[0], nums[0], nums[0]]
    for i in range(1, len(nums)):
        maxhere = max(maxherepre * nums[i], minherepre * nums[i], nums[i]);
        minhere = min(maxherepre * nums[i], minherepre * nums[i], nums[i]);
        maxsofar = max(maxhere, maxsofar);
        maxherepre = maxhere;
        minherepre = minhere;
    return maxsofar
~~~~
![alt text](https://leetcode.com/uploads/files/1485048726667-screen-shot-2017-01-21-at-8.31.48-pm.jpg "DP 1")
