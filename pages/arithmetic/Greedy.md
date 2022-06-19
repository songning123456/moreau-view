### 贪心算法


#### 无重叠区间
![eraseOverlapIntervals](/images/Arithmetic/eraseOverlapIntervals.PNG)


* 解法


```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        return intervals.length - intervalSchedule(intervals);
    }

    private int intervalSchedule(int[][] intervals) {
        if (intervals.length == 0) {
            return 0;
        }
        Arrays.sort(intervals, Comparator.comparingInt(o -> o[1]));
        int count = 1;
        int xEnd = intervals[0][1];
        for (int[] interval : intervals) {
            int start = interval[0];
            if (start >= xEnd) {
                count++;
                xEnd = interval[1];
            }
        }
        return count;
    }
}
```


#### 用最少数量的箭引爆气球
![findMinArrowShots](/images/Arithmetic/findMinArrowShots.PNG)


* 解法


```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        if (points.length == 0) {
            return 0;
        }
        Arrays.sort(points, Comparator.comparingInt(o -> o[1]));
        int count = 1;
        int xEnd = points[0][1];
        for (int[] point : points) {
            int start = point[0];
            if (start > xEnd) {
                count++;
                xEnd = point[1];
            }
        }
        return count;
    }
}
```


#### 跳跃游戏
![canJump](/images/Arithmetic/canJump.PNG)


* 解法


```java
class Solution {
    public boolean canJump(int[] nums) {
        int n = nums.length;
        int farthest = 0;
        for (int i = 0; i < n - 1; i++) {
            farthest = Math.max(farthest, i + nums[i]);
            if (farthest <= i) {
                return false;
            }
        }
        return farthest >= n - 1;
    }
}
```


#### 跳跃游戏 II
![jump](/images/Arithmetic/jump.PNG)


* 解法


```java
class Solution {
    public int jump(int[] nums) {
        int n = nums.length;
        int end = 0, farthest = 0;
        int jumps = 0;
        for (int i = 0; i < n - 1; i++) {
            farthest = Math.max(nums[i] + i, farthest);
            if (end == i) {
                jumps++;
                end = farthest;
            }
        }
        return jumps;
    }
}
```


