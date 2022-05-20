#### 二分法


* 基本模板


```java
public class Solution {

    // 最基本的二分查找算法
    int binarySearch(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] > target) {
                right = mid - 1;
            } else if (nums[mid] == target) {
                return mid;
            }
        }
        return -1;
    }

    // 寻找左侧边界的二分查找
    int leftBound(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] == target) {
                right = mid - 1;
            } else if (nums[mid] > target) {
                right = mid - 1;
            }
        }
        if (left >= nums.length || nums[left] != target) {
            return -1;
        }
        return left;
    }

    // 寻找右侧边界的二分查找
    int rightBound(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] == target) {
                left = mid + 1;
            } else if (nums[mid] > target) {
                right = mid - 1;
            }
        }
        if (right < 0 || nums[right] != target) {
            return -1;
        }
        return right;
    }
}
```


#### 二分查找
![binarySearch](/images/Arithmetic/binarySearch.PNG)


* 解法


```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] > target) {
                right = mid - 1;
            }
        }
        return -1;
    }
}
```


#### 在排序数组中查找元素的第一个和最后一个位置
![searchRange](/images/Arithmetic/searchRange.PNG)


* 解法


```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] res = new int[]{-1, -1};
        res[0] = leftBound(nums, target);
        res[1] = rightBound(nums, target);
        return res;
    }

    private int leftBound(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] == target) {
                right = mid - 1;
            } else if (nums[mid] > target) {
                right = mid - 1;
            }
        }
        if (left >= nums.length || nums[left] != target) {
            return -1;
        }
        return left;
    }

    private int rightBound(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] == target) {
                left = mid + 1;
            } else if (nums[mid] > target) {
                right = mid - 1;
            }
        }
        if (right < 0 || nums[right] != target) {
            return -1;
        }
        return right;
    }
}
```


#### 爱吃香蕉的珂珂
![minEatingSpeed](/images/Arithmetic/minEatingSpeed.PNG)


* 解法


```java
class Solution {
    private int timeOf(int n, int speed) {
        return (n / speed) + ((n % speed > 0) ? 1 : 0);
    }

    private int getMax(int[] piles) {
        int max = 0;
        for (int n : piles) {
            max = Math.max(n, max);
        }
        return max;
    }

    private boolean canFinish(int[] piles, int speed, int H) {
        int time = 0;
        for (int n : piles) {
            time += timeOf(n, speed);
        }
        return time <= H;
    }

    public int minEatingSpeed(int[] piles, int H) {
        int left = 1, right = this.getMax(piles) + 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (canFinish(piles, mid, H)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
}
```


#### 在 D 天内送达包裹的能力
![shipWithinDays](/images/Arithmetic/shipWithinDays.PNG)


* 解法


```java
class Solution {
    private int getMax(int[] weights) {
        int max = 0;
        for (int n : weights) {
            max = Math.max(n, max);
        }
        return max;
    }

    private int getSum(int[] weights) {
        int sum = 0;
        for (int item : weights) {
            sum += item;
        }
        return sum;
    }

    // 如果载重为 cap，是否能在 D 天内运完货物？
    private boolean canFinishCap(int[] w, int D, int cap) {
        int i = 0;
        for (int day = 0; day < D; day++) {
            int maxCap = cap;
            while ((maxCap -= w[i]) >= 0) {
                i++;
                if (i == w.length) {
                    return true;
                }
            }
        }
        return false;
    }

    public int shipWithinDays(int[] weights, int D) {
        // 载重可能的最小值
        int left = getMax(weights);
        // 载重可能的最大值 + 1
        int right = getSum(weights) + 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (canFinishCap(weights, D, mid)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
}
```


#### 判断子序列
![isSubsequence](/images/Arithmetic/isSubsequence.PNG)


* 解法


```java
class Solution {
    private int leftBound(ArrayList<Integer> arr, int tar) {
        int lo = 0, hi = arr.size();
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (tar > arr.get(mid)) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        return lo;
    }

    public boolean isSubsequence(String s, String t) {
        int m = s.length(), n = t.length();
        Map<Character, List<Integer>> indexMap = new LinkedHashMap<>();
        for (int i = 0; i < n; i++) {
            Character character = t.charAt(i);
            indexMap.computeIfAbsent(character, k -> new ArrayList<>());
            indexMap.get(character).add(i);
        }
        int j = 0;
        for (int i = 0; i < m; i++) {
            Character character = s.charAt(i);
            if (indexMap.get(character) == null) {
                return false;
            }
            int pos = leftBound((ArrayList<Integer>) indexMap.get(character), j);
            if (pos == indexMap.get(character).size() || pos == -1) {
                return false;
            }
            j = indexMap.get(character).get(pos) + 1;
        }
        return true;
    }
}
```





