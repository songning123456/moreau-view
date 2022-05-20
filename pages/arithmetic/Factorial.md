#### 阶乘后的零
![trailingZeroes](/images/Arithmetic/trailingZeroes.PNG)


* 解法


```java
class Solution {
    public int trailingZeroes(int n) {
        int res = 0;
        long divisor = 5;
        while (divisor <= n) {
            res += n / divisor;
            divisor *= 5;
        }
        return res;
    }
}
```


#### 阶乘函数后K个零
![preimageSizeFZF](/images/Arithmetic/preimageSizeFZF.PNG)


* 解法


```java
class Solution {
    private long trailingZeroes(long n) {
        long res = 0;
        long divisor = 5;
        while (divisor <= n) {
            res += n / divisor;
            divisor *= 5;
        }
        return res;
    }

    private long leftBound(int target) {
        long lo = 0, hi = Long.MAX_VALUE;
        while (lo < hi) {
            long mid = lo + (hi - lo) / 2;
            if (trailingZeroes(mid) < target) {
                lo = mid + 1;
            } else if (trailingZeroes(mid) > target) {
                hi = mid;
            } else {
                hi = mid;
            }
        }
        return lo;
    }

    private long rightBound(int target) {
        long lo = 0, hi = Long.MAX_VALUE;
        while (lo < hi) {
            long mid = lo + (hi - lo) / 2;
            if (trailingZeroes(mid) < target) {
                lo = mid + 1;
            } else if (trailingZeroes(mid) > target) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        return lo - 1;
    }

    public int preimageSizeFZF(int K) {
        return (int) (rightBound(K) - leftBound(K) + 1);
    }
}
```
