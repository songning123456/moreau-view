### 水塘抽样


#### 链表随机节点
![linkRandomNode](/images/Arithmetic/linkRandomNode.PNG)


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

    private ListNode head;

    /** @param head The linked list's head.
        Note that the head is guaranteed to be not null, so it contains at least one node. */
    public Solution(ListNode head) {
        this.head = head;
    }
    
    /** Returns a random node's value. */
    public int getRandom() {
        if (this.head == null) {
            return -1;
        }
        Random r = new Random();
        int i = 0, res = 0;
        ListNode p = head;
        // while 循环遍历链表
        while (p != null) {
            // 生成一个 [0, i) 之间的整数
            // 这个整数等于 0 的概率就是 1/i
            if (r.nextInt(++i) == 0) {
                res = p.val;
            }
            p = p.next;
        }
        return res;
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(head);
 * int param_1 = obj.getRandom();
 */
```


#### 随机数索引
![RandomIndex](/images/Arithmetic/RandomIndex.PNG)


* 解法


```java
class Solution {

    private int[] nums;

    public Solution(int[] nums) {
       this.nums = nums;
    }
    
    public int pick(int target) {
        Random random = new Random();
        int n = 0;
        int index = 0;
        for(int i = 0;i < nums.length;i++)
            if(nums[i] == target){
            //我们的目标对象中选取。
                n++;
                //我们以1/n的概率留下该数据
                if(random.nextInt() % n == 0) {
                    index = i;
                }
            }
        return index;
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(nums);
 * int param_1 = obj.pick(target);
 */
```


