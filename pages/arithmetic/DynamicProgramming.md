### 动态规划


#### 动态规划


* 基本模板


```
// 初始化base
dp[0][0][...] = base
// 进行状态转移
for 状态1 in 状态1的所有取值:
    for 状态2 in 状态2的所有取值:
        for ...
            dp[状态1][状态2][...] = 求最值(选择1, 选择2...)
```


#### 斐波那契数列
![fib](/images/Arithmetic/fib.PNG)


* 解法一


```java
class Solution {
   public int fib(int N) {
        if (N < 1) {
            return 0;
        }
        Map<Integer, Integer> memory = new HashMap<>(2);
        return helper(memory, N);
    }

    private Integer helper(Map<Integer, Integer> memory, int n) {
        if (n == 1 || n == 2) {
            return 1;
        }
        if (memory.get(n) != null) {
            return memory.get(n);
        }
        memory.put(n, helper(memory, n - 1) + helper(memory, n - 2));
        return memory.get(n);
    }
}
```


* 解法二


```java
class Solution {
    public int fib(int N) {
        if (N == 0) {
            return 0;
        }
        if (N == 1 || N == 2) {
            return 1;
        }
        int[] dp = new int[N + 1];
        dp[1] = dp[2] = 1;
        for (int i = 3; i <= N; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[N];
    }
}
```


* 解法三


```java
class Solution {
    public int fib(int N) {
        if (N == 0) {
            return 0;
        }
        if (N == 1 || N == 2) {
            return 1;
        }
        int prev = 1, cur = 1;
        for (int i = 3; i <= N; i++) {
            int sum = prev + cur;
            prev = cur;
            cur = sum;
        }
        return cur;
    }
}
```


#### 凑零钱问题
![coinChange](/images/Arithmetic/coinChange.PNG)


* 解法一


```java
class Solution {
   private Map<Integer, Integer> dpMap = new HashMap<>();

    public int coinChange(int[] coins, int amount) {
        return dp(coins, amount);
    }

    private int dp(int[] arr, int n) {
        if (n == 0) {
            return 0;
        }
        if (n < 0) {
            return -1;
        }
        if (dpMap.containsKey(n)) {
            return dpMap.get(n);
        }
        int res = Integer.MAX_VALUE;
        for (int item : arr) {
            int subProblem = dp(arr, n - item);
            if (subProblem == -1) {
                continue;
            }
            res = Math.min(res, 1 + subProblem);
        }
        if (res == Integer.MAX_VALUE) {
            res = -1;
        }
        dpMap.put(n, res);
        return res;
    }
}
```


* 解法二


```java
class Solution {
   public int coinChange(int[] coins, int amount) {
        int[] dpTable = new int[amount + 1];
        Arrays.fill(dpTable, amount + 1);
        dpTable[0] = 0;
        for (int i = 0; i < dpTable.length; i++) {
            for (int coin : coins) {
                if (i - coin < 0) {
                    continue;
                }
                dpTable[i] = Math.min(dpTable[i], 1 + dpTable[i - coin]);
            }
        }
        if (dpTable[amount] == amount + 1) {
            return -1;
        } else {
            return dpTable[amount];
        }
    }
}
```


#### 目标和
![findTargetSumWays](/images/Arithmetic/findTargetSumWays.PNG)


* 解法一(回溯法)


```java
class Solution {
    private int result = 0;

    public int findTargetSumWays(int[] nums, int S) {
        if (nums.length == 0) {
            return 0;
        }
        backtrack(nums, 0, S);
        return result;
    }

    private void backtrack(int[] nums, int i, int rest) {
        if (i == nums.length) {
            if (rest == 0) {
                result++;
            }
            return;
        }
        rest += nums[i];
        backtrack(nums, i + 1, rest);
        rest -= nums[i];

        rest -= nums[i];
        backtrack(nums, i + 1, rest);
        rest += nums[i];
    }
}
```


* 解法二(动态规划)


```java
class Solution {
    Map<String, Integer> dpMap = new HashMap<>();

    public int findTargetSumWays(int[] nums, int S) {
        if (nums.length == 0) {
            return 0;
        }
        return dp(nums, 0, S);
    }

    private int dp(int[] nums, int i, int rest) {
        if (i == nums.length) {
            if (rest == 0) {
                return 1;
            }
            return 0;
        }
        String key = i + "," + rest;
        if (dpMap.containsKey(key)) {
            return dpMap.get(key);
        }
        int result = dp(nums, i + 1, rest - nums[i]) + dp(nums, i + 1, rest + nums[i]);
        dpMap.put(key, result);
        return result;
    }
}
```


#### 最长递增子序列
![lengthOfLIS](/images/Arithmetic/lengthOfLIS.PNG)


* 解法


```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1);
        for (int i = 0; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }

        int res = 0;
        for (int value : dp) {
            res = Math.max(res, value);
        }
        return res;
    }
}
```


#### 俄罗斯套娃信封问题
![maxEnvelopes](/images/Arithmetic/maxEnvelopes.PNG)


* 解法


```java
class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        int length = envelopes.length;
        Arrays.sort(envelopes, (o1, o2) -> o1[0] == o2[0] ? (o2[1] - o1[1]) : (o1[0] - o2[0]));
        int[] height = new int[length];
        for (int i = 0; i < length; i++) {
            height[i] = envelopes[i][1];
        }
        return lengthOfLIS(height);
    }

    private int lengthOfLIS(int[] nums) {
        int piles = 0, n = nums.length;
        int[] top = new int[n];
        for (int poker : nums) {
            int left = 0, right = piles;
            while (left < right) {
                int mid = (left + right) / 2;
                if (top[mid] >= poker) {
                    right = mid;
                } else {
                    left = mid + 1;
                }
            }
            if (left == piles) {
                piles++;
            }
            top[left] = poker;
        }
        return piles;
    }
}
```


#### 最大子序和
![maxSubArray](/images/Arithmetic/maxSubArray.PNG)


* 解法一


```java
class Solution {
    public int maxSubArray(int[] nums) {
        int length = nums.length;
        if (length == 0) {
            return 0;
        }
        int[] dp = new int[length];
        dp[0] = nums[0];
        for (int i = 1; i < length; i++) {
            dp[i] = Math.max(nums[i], nums[i] + dp[i - 1]);
        }
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < length; i++) {
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```


* 解法二


```java
class Solution {
    public int maxSubArray(int[] nums) {
        int length = nums.length;
        if (length == 0) {
            return 0;
        }
        int cur = nums[0];
        int next, res = cur;
        for (int i = 1; i < length; i++) {
            next = Math.max(nums[i], nums[i] + cur);
            cur = next;
            res = Math.max(res, next);
        }
        return res;
    }
}
```


#### 最长公共子序列
![longestCommonSubsequence](/images/Arithmetic/longestCommonSubsequence.PNG)


* 解法一


```java
class Solution {
    private Map<String, Integer> dpMap = new HashMap<>(2);

    public int longestCommonSubsequence(String text1, String text2) {
        return dp(text1.toCharArray(), text2.toCharArray(), text1.length() - 1, text2.length() - 1);
    }

    private int dp(char[] str1, char[] str2, int i, int j) {
        if (i == -1 || j == -1) {
            return 0;
        }
        if (dpMap.containsKey(i + "-" + j)) {
            return dpMap.get(i + "-" + j);
        }
        int res;
        if (str1[i] == str2[j]) {
            res = dp(str1, str2, i - 1, j - 1) + 1;
        } else {
            res = Math.max(dp(str1, str2, i - 1, j), dp(str1, str2, i, j - 1));
        }
        dpMap.put(i + "-" + j, res);
        return res;
    }
}
```


* 解法二


```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length(), n = text2.length();
        // 构建 DP table 和 base case
        // dp[i][j] 表示： 字符串 str1[0:i] 和字符串 str2[0:j] 的最大公共子序列
        int[][] dp = new int[m + 1][n + 1];
        // 进行状态转移
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                // 若两个字符相等，必然可以构成子问题的最优解
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                    // 这个字符存在于lcs之中
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    // 此时text1[i]!= text2[j]则表示至少有一个不在lcs中(要么text1[i]不在，要 text2[j]不在，或者都不在)
                    // 所以当前结果就相当于之前结果的中最大的那一个
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[m][n];
    }
}
```


#### 分割等和子集
![canPartition](/images/Arithmetic/canPartition.PNG)


* 解法


```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        if (sum % 2 != 0) {
            return false;
        }
        int length = nums.length;
        sum /= 2;
        boolean[][] dp = new boolean[length + 1][sum + 1];
        for (int i = 0; i < dp.length; i++) {
            for (int j = 0; j < dp[0].length; j++) {
                dp[i][j] = false;
            }
        }
        for (int i = 0; i <= length; i++) {
            dp[i][0] = true;
        }
        for (int i = 1; i <= length; i++) {
            for (int j = 1; j <= sum; j++) {
                if (j - nums[i - 1] < 0) {
                    dp[i][j] = dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i - 1]];
                }
            }
        }
        return dp[length][sum];
    }
}
```


#### 零钱兑换 II
![change](/images/Arithmetic/change.PNG)


* 解法


```java
class Solution {
    public int change(int amount, int[] coins) {
        int length = coins.length;
        int[][] dp = new int[length + 1][amount + 1];
        for (int i = 0; i <= length; i++) {
            dp[i][0] = 1;
        }
        for (int i = 1; i <= length; i++) {
            for (int j = 1; j <= amount; j++) {
                if (j - coins[i - 1] >= 0) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - coins[i - 1]];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[length][amount];
    }
}
```


#### 石子游戏
![stoneGame](/images/Arithmetic/stoneGame.PNG)


* 解法


```java
class Solution {
    public boolean stoneGame(int[] piles) {
        int n = piles.length;
        int[][][] dp = new int[n][n][2];
        for (int i = 0; i < n; i++) {
            for (int j = i; j < n; j++) {
                dp[i][j][0] = 0;
                dp[i][j][1] = 0;
            }
        }
        for (int i = 0; i < n; i++) {
            dp[i][i][0] = piles[i];
            dp[i][i][1] = 0;
        }
        for (int i = 2; i <= n; i++) {
            for (int j = 0; j <= n - i; j++) {
                int k = i + j - 1;
                int left = piles[j] + dp[j + 1][k][1];
                int right = piles[k] + dp[j][k - 1][1];
                if (left > right) {
                    dp[j][k][0] = left;
                    dp[j][k][1] = dp[j + 1][k][0];
                } else {
                    dp[j][k][0] = right;
                    dp[j][k][1] = dp[j][k - 1][0];
                }
            }
        }
        int[] res = dp[0][n - 1];
        return res[0] - res[1] > 0;
    }
}
```


#### 四键键盘
![fourKeyboard](/images/Arithmetic/fourKeyboard.png)


* 解法


```java
// todo
```


#### 正则表达式匹配
![isMatch](/images/Arithmetic/isMatch.PNG)


* 解法


```java
// todo
```


#### 鸡蛋掉落
![superEggDrop](/images/Arithmetic/superEggDrop.PNG)


* 解法


```java
class Solution {
    public int superEggDrop(int K, int N) {
        int[][] dp = new int[N + 1][K + 1];
        for (int i = 0; i < N + 1; i++) {
            for (int j = 0; j < K + 1; j++) {
                dp[i][j] = 0;
            }
        }
        for (int j = 1; j <= K; j++) {
            dp[1][j] = 1;
        }
        for (int i = 1; i <= N; i++) {
            dp[i][1] = i;
        }
        for (int i = 2; i <= N; i++) {
            for (int j = 2; j <= K; j++) {
                dp[i][j] = binaryValley(i, j, dp);
            }
        }
        return dp[N][K];
    }
    
    private int binaryValley(int floors, int eggs, int[][] dp) {
        int left = 1;
        int right = floors;
        while (left < right) {
            int mid = left + (right - left) / 2;
            int broken = dp[mid - 1][eggs - 1];
            int notBroken = dp[floors - mid][eggs];
            if (notBroken > broken) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return Math.max(dp[right - 1][eggs - 1], dp[floors - right][eggs]) + 1;
    }
}
```


#### 戳气球
![maxCoins](/images/Arithmetic/maxCoins.PNG)


* 解法


```java
// todo
```


#### 买卖股票的最佳时机
![maxProfit](/images/Arithmetic/maxProfit.PNG)


* 解法


```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices.length == 0) {
            return 0;
        }
        int n = prices.length;
        int[][] dp = new int[n][2];
        for (int i = 0; i < n; i++) {
            if (i == 0) {
                dp[i][0] = 0;
                dp[i][1] = -prices[i];
                continue;
            }
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
            dp[i][1] = Math.max(dp[i - 1][1], -prices[i]);
        }
        return dp[n - 1][0];
    }
}
```


#### 实现strStr()
![strStr](/images/Arithmetic/strStr.PNG)


* 解法


```java
class Solution {
    class KMP {

        private int[][] dp;
        private String pat;

        public KMP(String pat) {
            this.pat = pat;
            int M = pat.length();
            dp = new int[M][256];
            dp[0][pat.charAt(0)] = 1;
            int X = 0;
            for (int j = 1; j < M; j++) {
                for (int c = 0; c < 256; c++) {
                    dp[j][c] = dp[X][c];
                }
                dp[j][pat.charAt(j)] = j + 1;
                X = dp[X][pat.charAt(j)];
            }
        }

        public int search(String txt) {
            int M = pat.length();
            int N = txt.length();
            int j = 0;
            for (int i = 0; i < N; i++) {
                j = dp[j][txt.charAt(i)];
                if (j == M) {
                    return i - M + 1;
                }
            }
            return -1;
        }
    }
    public int strStr(String haystack, String needle) {
        if (needle.length() == 0){
            return 0;
        }
        KMP kmp = new KMP(needle);
        return kmp.search(haystack);
    }
}
```


#### 编辑距离
![minDistance](/images/Arithmetic/minDistance.PNG)


* 解法一


```java
class Solution {
    private Map<String, Integer> dpMap = new HashMap<>();

    public int minDistance(String word1, String word2) {
        return dp(word1.toCharArray(), word2.toCharArray(), word1.length() - 1, word2.length() - 1);
    }

    private int dp(char[] word1, char[] word2, int i, int j) {
        if (i == -1) {
            return j + 1;
        }
        if (j == -1) {
            return i + 1;
        }
        if (dpMap.containsKey(i + "-" + j)) {
            return dpMap.get(i + "-" + j);
        }
        int res;
        if (word1[i] == word2[j]) {
            res = dp(word1, word2, i - 1, j - 1);
        } else {
            res = Math.min(dp(word1, word2, i, j - 1) + 1, Math.min(dp(word1, word2, i - 1, j) + 1, dp(word1, word2, i - 1, j - 1) + 1));
        }
        dpMap.put(i + "-" + j, res);
        return res;
    }
}
```


* 解法二


```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            dp[i][0] = i;
        }
        for (int j = 1; j <= n; j++) {
            dp[0][j] = j;
        }
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.min(dp[i - 1][j] + 1, Math.min(dp[i][j - 1] + 1, dp[i - 1][j - 1] + 1));
                }
            }
        }
        return dp[m][n];
    }
}
```


