#### 单调栈


* 基本模板


```
public int[] nextGreaterElement(int[] nums) {
    int[] ans = new int[nums.length];
    Stack<Integer> s = new Stack<>();
    for (int i = nums.length - 1; i >= 0; i--) {
        while (!s.isEmpty() && s.peek() <= nums[i]) {
            s.pop();
        }
        ans[i] = s.empty() ? -1 : s.peek();
        s.push(nums[i]);
    }
    return ans;
}
```


#### 下一个更大元素I
![nextGreaterElementI](/images/Arithmetic/nextGreaterElementI.PNG)


* 解法


```
public int[] nextGreaterElement(int[] nums1, int[] nums2) {
    int[] convert = this.nextGreaterElement(nums2);
    Map<Integer, Integer> convertMap = new HashMap<>(2);
    for (int i = 0; i < nums2.length; i++) {
        convertMap.put(nums2[i], convert[i]);
    }
    int[] res = new int[nums1.length];
    for (int i = 0; i < nums1.length; i++) {
        res[i] = convertMap.get(nums1[i]);
    }
    return res;
}
```


#### 下一个更大元素II
![nextGreaterElements2](/images/Arithmetic/nextGreaterElements2.PNG)


* 解法


```
public int[] nextGreaterElement(int[] nums) {
    int n = nums.length;
    int[] ans = new int[n];
    Stack<Integer> s = new Stack<>();
    for (int i = 2 * n - 1; i >= 0; i--) {
        while (!s.isEmpty() && s.peek() <= nums[i % n]) {
            s.pop();
        }
        ans[i % n] = s.empty() ? -1 : s.peek();
        s.push(nums[i % n]);
    }
    return ans;
}
```


#### 滑动窗口最大值
![maxSlidingWindow](/images/Arithmetic/maxSlidingWindow.PNG)


* 解法


```
public class Solution {

    static class MonotonicQueue {

        private Deque<Integer> data;

        MonotonicQueue() {
            data = new ArrayDeque<>();
        }

        public void push(int n) {
            while (!data.isEmpty() && data.getLast() < n) {
                data.removeLast();
            }
            data.addLast(n);
        }

        public int max() {
            return data.getFirst();
        }

        public void pop(int n) {
            if (!data.isEmpty() && data.getFirst() == n) {
                data.removeFirst();
            }
        }
    }

    public int[] maxSlidingWindow(int[] nums, int k) {
        MonotonicQueue window = new MonotonicQueue();
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            if (i < k - 1) {
                window.push(nums[i]);
            } else {
                window.push(nums[i]);
                res.add(window.max());
                window.pop(nums[i - k + 1]);
            }
        }
        return res.stream().mapToInt(Integer::valueOf).toArray();
    }
}
```