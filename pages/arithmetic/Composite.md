#### LRU缓存机制


![LRU](/images/Arithmetic/LRU.PNG)


* 解法


```
public class LRUCache {

    class DLinkedNode {
        int key;
        int value;
        DLinkedNode prev;
        DLinkedNode next;

        public DLinkedNode() {
        }

        public DLinkedNode(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private Map<Integer, DLinkedNode> cache = new HashMap<>();
    private int size;
    private int capacity;
    private DLinkedNode head, tail;

    public LRUCache(int capacity) {
        this.size = 0;
        this.capacity = capacity;
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head.next = tail;
        tail.prev = head;
    }

    private void addToHead(DLinkedNode node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    private void removeNode(DLinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void modeToHead(DLinkedNode node) {
        removeNode(node);
        addToHead(node);
    }

    private DLinkedNode removeTail() {
        DLinkedNode node = tail.prev;
        removeNode(node);
        return node;
    }


    public int get(int key) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            return -1;
        }
        modeToHead(node);
        return node.value;
    }

    public void put(int key, int value) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            DLinkedNode newNode = new DLinkedNode(key, value);
            cache.put(key, newNode);
            addToHead(newNode);
            ++size;
            if (size > capacity) {
                DLinkedNode tail = removeTail();
                cache.remove(tail.key);
                --size;
            }
        } else {
            node.value = value;
            modeToHead(node);
        }
    }
}
```


#### 字符串相乘


![multiply](/images/Arithmetic/multiply.PNG)


* 解法


```java
class Solution {
    public String multiply(String num1, String num2) {
        int m = num1.length(), n = num2.length();
        int[] res = new int[m + n];
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                int p1 = i + j, p2 = i + j + 1;
                int sum = mul + res[p2];
                res[p2] = sum % 10;
                res[p1] += sum / 10;
            }
        }
        int i = 0;
        while (i < res.length && res[i] == 0) {
            i++;
        }
        StringBuilder stringBuilder = new StringBuilder();
        while (i < res.length) {
            stringBuilder.append(res[i]);
            i++;
        }
        return stringBuilder.length() == 0 ? "0" : stringBuilder.toString();
    }
}
```


#### 计数质数
![countPrimes](/images/Arithmetic/countPrimes.PNG)


* 解法


```java
class Solution {
    public int countPrimes(int n) {
        boolean[] isPrim = new boolean[n];
        Arrays.fill(isPrim, true);
        for (int i = 2; i * i < n; i++) {
            if (isPrim[i]) {
                for (int j = i * i; j < n; j += i) {
                    isPrim[j] = false;
                }
            }
        }
        int count = 0;
        for (int i = 2; i < n; i++) {
            if (isPrim[i]) {
                count++;
            }
        }
        return count;
    }
}
```


#### 超级次方
![superPow](/images/Arithmetic/superPow.PNG)


* 解法


```java
class Solution {
    private int base = 1337;
    
    private int myPow(int a, int k) {
        a %= base;
        int res = 1;
        for (int i = 0; i < k; i++) {
            res *= a;
            res %= base;
        }
        return res;
    }

    public int superPow(int a, int[] b) {
        if (b.length == 0) {
            return 1;
        }
        int last = b[b.length - 1];
        b = Arrays.copyOfRange(b, 0, b.length - 1);
        int part1 = myPow(a, last);
        int part2 = myPow(superPow(a, b), 10);
        return (part1 * part2) % base;
    }
}
```


#### 接雨水
![trap](/images/Arithmetic/trap.PNG)


* 暴力解法


```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int ans = 0;
        for (int i = 1; i < n - 1; i++) {
            int leftMax = 0, rightMax = 0;
            for (int j = i; j < n; j++) {
                rightMax = Math.max(rightMax, height[j]);
            }
            for (int j = i; j >= 0; j--) {
                leftMax = Math.max(leftMax, height[j]);
            }
            ans += Math.min(leftMax, rightMax) - height[i];
        }
        return ans;
    }
}
```


* 备忘录解法


```java
class Solution {
    public int trap(int[] height) {
        if (height.length == 0) {
            return 0;
        }
        int n = height.length;
        int ans = 0;
        int[] leftMax = new int[n];
        int[] rightMax = new int[n];

        leftMax[0] = height[0];
        rightMax[n - 1] = height[n - 1];
        for (int i = 1; i < n; i++) {
            leftMax[i] = Math.max(height[i], leftMax[i - 1]);
        }
        for (int i = n - 2; i >= 0; i--) {
            rightMax[i] = Math.max(height[i], rightMax[i + 1]);
        }
        for (int i = 1; i < n - 1; i++) {
            ans += Math.min(leftMax[i], rightMax[i]) - height[i];
        }
        return ans;
    }
}
```


#### K个一组翻转链表
![reverseKGroup](/images/Arithmetic/reverseKGroup.PNG)


* 解法


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    private ListNode reverse(ListNode a, ListNode b) {
        ListNode prev, cur, next;
        prev = null;
        cur = a;
        // while 终止的条件改一下就行了
        while (cur != b) {
            next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
        }
        // 返回反转后的头结点
        return prev;
    }

    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null) {
            return null;
        }
        // 区间 [a, b) 包含 k 个待反转元素
        ListNode a, b;
        a = b = head;
        for (int i = 0; i < k; i++) {
            // 不足 k 个，不需要反转，base case
            if (b == null) {
                return head;
            }
            b = b.next;
        }
        // 反转前 k 个元素
        ListNode newHead = reverse(a, b);
        // 递归反转后续链表并连接起来
        a.next = reverseKGroup(b, k);
        return newHead;
    }
}
```


#### 回文链表
![isPalindrome](/images/Arithmetic/isPalindrome.PNG)


* 解法


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode slow, fast;
        slow = fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        if (fast != null) {
            slow = slow.next;
        }
        ListNode left = head;
        ListNode right = reverse(slow);
        while (right != null) {
            if (left.val != right.val) {
                return false;
            }
            left = left.next;
            right = right.next;
        }
        return true;
    }

    private ListNode reverse(ListNode a) {
        ListNode prev, cur, next;
        prev = null;
        cur = a;
        while (cur != null) {
            next = cur.next;
            // 逐个结点反转
            cur.next = prev;
            // 更新指针位置
            prev = cur;
            cur = next;
        }
        // 返回反转后的头结点
        return prev;
    }
}
```


#### 找到所有数组中消失的数字
![findDisappearedNumbers](/images/Arithmetic/findDisappearedNumbers.PNG)


* 解法


```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            int newIndex = Math.abs(nums[i]) - 1;
            if (nums[newIndex] > 0) {
                nums[newIndex] *= -1;
            }
        }
        List<Integer> result = new LinkedList<>();
        for (int i = 1; i <= nums.length; i++) {
            if (nums[i - 1] > 0) {
                result.add(i);
            }
        }
        return result;
    }
}
```


#### 错误的集合
![findErrorNums](/images/Arithmetic/findErrorNums.PNG)


* 解法


```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        int n = nums.length;
        int dup = -1;
        for (int i = 0; i < n; i++) {
            int index = Math.abs(nums[i]) - 1;
            if (nums[index] < 0) {
                dup = Math.abs(nums[i]);
            } else {
                nums[index] *= -1;
            }
        }
        int missing = -1;
        for (int i = 0; i < n; i++) {
            if (nums[i] > 0) {
                missing = i + 1;
            }
        }
        return new int[]{dup, missing};
    }
}
```


#### 有效的括号
![isValid](/images/Arithmetic/isValid.PNG)


* 解法


```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> left = new Stack<>();
        for (int i = 0; i < s.length(); i++) {
            char character = s.charAt(i);
            if (character == '(' || character == '{' || character == '[') {
                left.push(character);
            } else {
                if (!left.empty() && leftOf(character) == left.peek()) {
                    left.pop();
                } else {
                    return false;
                }
            }
        }
        return left.empty();
    }

    private char leftOf(Character c) {
        if (c == ')') {
            return '(';
        } else if (c == '}') {
            return '{';
        } else {
            return '[';
        }
    }
}
```


#### Nim游戏
![canWinNim](/images/Arithmetic/canWinNim.PNG)


* 解法


```java
class Solution {
    public boolean canWinNim(int n) {
        return n % 4 != 0;
    }
}
```


#### 灯泡开关
![bulbSwitch](/images/Arithmetic/bulbSwitch.PNG)


* 解法


```java
class Solution {
    public int bulbSwitch(int n) {
        return (int)Math.sqrt(n);
    }
}
```


#### 和为K的子数组
![subarraySum](/images/Arithmetic/subarraySum.PNG)


* 解法


```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int n = nums.length;
        int[] sum = new int[n + 1];
        sum[0] = 0;
        for (int i = 0; i < n; i++) {
            sum[i + 1] = sum[i] + nums[i];
        }
        int ans = 0;
        for (int i = 0; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (sum[i] - sum[j] == k) {
                    ans++;
                }
            }
        }
        return ans;
    }
}
```


#### 煎饼排序
![pancakeSort](/images/Arithmetic/pancakeSort.PNG)


* 解法


```java
class Solution {
    private List<Integer> res = new LinkedList<>();

    public List<Integer> pancakeSort(int[] arr) {
        sort(arr, arr.length);
        return res;
    }

    private void sort(int[] cakes, int n) {
        if (n == 1) {
            return;
        }
        int maxCake = 0;
        int maxCakeIndex = 0;
        for (int i = 0; i < n; i++) {
            if (cakes[i] > maxCake) {
                maxCakeIndex = i;
                maxCake = cakes[i];
            }
        }
        reverse(cakes, 0, maxCakeIndex);
        res.add(maxCakeIndex + 1);
        reverse(cakes, 0, n - 1);
        res.add(n);
        sort(cakes, n - 1);
    }

    private void reverse(int[] arr, int i, int j) {
        while (i < j) {
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            i++;
            j--;
        }
    }
}
```