### 区间调度


#### 交集
![intervalIntersection](/images/Arithmetic/intervalIntersection.PNG)


* 解法


```java
class Solution {
    public int[][] intervalIntersection(int[][] A, int[][] B) {
        int i = 0, j = 0;
        List<int[]> res = new ArrayList<>();
        while (i < A.length && j < B.length) {
            int a1 = A[i][0];
            int a2 = A[i][1];
            int b1 = B[j][0];
            int b2 = B[j][1];
            if (b2 >= a1 && a2 >= b1) {
                int[] temp = new int[2];
                temp[0] = Math.max(a1, b1);
                temp[1] = Math.min(a2, b2);
                res.add(temp);
            }
            if (b2 < a2) {
                j += 1;
            } else {
                i += 1;
            }
        }
        return res.toArray(new int[0][]);
    }
}
```


#### 并集
![merge](/images/Arithmetic/merge.PNG)


* 解法


```java
class Solution {
    public int[][] merge(int[][] intervals) {
        if (intervals.length == 0) {
            return new int[0][];
        }
        Arrays.sort(intervals, (o1, o2) -> {
            if (o1[0] == o2[0]) {
                return o1[1] - o2[1];
            } else {
                return o1[0] - o2[0];
            }
        });
        List<int[]> res = new ArrayList<>();
        int[] cur, last;
        res.add(intervals[0]);
        for (int i = 1; i < intervals.length; i++) {
            cur = intervals[i];
            last = res.get(res.size() - 1);
            if (cur[0] <= last[1]) {
                last[1] = Math.max(last[1], cur[1]);
            } else {
                res.add(cur);
            }
        }
        return res.toArray(new int[0][]);
    }
}
```
