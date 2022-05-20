#### 滑动窗口


* 基本模板


```
public void slidingWindow(String s, String t) {
    Map<Character, Integer> window = new HashMap<>(2);
    Map<Character, Integer> needs = new HashMap<>(2);
    for (int i = 0; i < t.length(); i++) {
        Character character = t.charAt(i);
        needs.put(character, needs.getOrDefault(character, 0) + 1);
    }
    int left = 0, right = 0;
    int valid = 0;
    while(right < s.size()) {
        // c是将移入窗口的字符
        Character c = s.charAt(right);
        // 右移窗口
        right++;
        // 进行窗口内数据的一系列更新
        // ...

        // 判断左侧窗口是否要收缩
        while(window needs shrink) {
            // d是将移除窗口的字符
            Character d = s.charAt(left);
            // 左移窗口
            left++;
            // 左移窗口内数据的一系列更新
            // ...
        }
    }
}
```


#### 字符串的排列
![checkInclusion](/images/Arithmetic/checkInclusion.PNG)


* 解法


```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        List<Integer> res = new ArrayList<>();
        int left = 0, right = 0;
        Map<Character, Integer> needs = new HashMap<>(2);
        Map<Character, Integer> window = new HashMap<>(2);
        for (int i = 0; i < s1.length(); i++) {
            Character character = s1.charAt(i);
            needs.put(character, needs.getOrDefault(character, 0) + 1);
        }
        int match = 0;
        while (right < s2.length()) {
            Character c1 = s2.charAt(right);
            if (needs.containsKey(c1)) {
                window.put(c1, window.getOrDefault(c1, 0) + 1);
                if (window.get(c1).equals(needs.get(c1))) {
                    match++;
                }
            }
            right++;
            while (match == needs.size()) {
                if (right - left == s1.length()) {
                    res.add(left);
                }
                char c2 = s2.charAt(left);
                if (needs.containsKey(c2)) {
                    window.put(c2, window.get(c2) - 1);
                    if (window.get(c2) < needs.get(c2)) {
                        match--;
                    }
                }
                left++;
            }
        }
        return res.size() != 0;
    }
}
```


#### 最小覆盖子串
![minWindow](/images/Arithmetic/minWindow.PNG)


* 解法


```Java
class Solution {
    public String minWindow(String s, String t) {
        int start = 0, minLen = Integer.MAX_VALUE;
        int left = 0, right = 0;
        Map<Character, Integer> window = new HashMap<>(2);
        Map<Character, Integer> needs = new HashMap<>(2);
        for (int i = 0; i < t.length(); i++) {
            Character character = t.charAt(i);
            needs.put(character, needs.getOrDefault(character, 0) + 1);
        }
        int match = 0;
        while (right < s.length()) {
            Character c1 = s.charAt(right);
            if (needs.containsKey(c1)) {
                window.put(c1, window.getOrDefault(c1, 0) + 1);
                if (window.get(c1).equals(needs.get(c1))) {
                    match++;
                }
            }
            right++;
            while (match == needs.size()) {
                if (right - left < minLen) {
                    start = left;
                    minLen = right - left;
                }
                char c2 = s.charAt(left);
                if (needs.containsKey(c2)) {
                    window.put(c2, window.get(c2) - 1);
                    if (window.get(c2) < needs.get(c2)) {
                        match--;
                    }
                }
                left++;
            }
        }
        return minLen == Integer.MAX_VALUE ? "" : s.substring(start, start + minLen);
    }
}
```


#### 找到字符串中所有字母异位词
![findAnagrams](/images/Arithmetic/findAnagrams.PNG)


* 解法


```java
class Solution {
    public List<Integer> findAnagrams(String s, String t) {
        List<Integer> res = new ArrayList<>();
        int left = 0, right = 0;
        Map<Character, Integer> needs = new HashMap<>(2);
        Map<Character, Integer> window = new HashMap<>(2);
        for (int i = 0; i < t.length(); i++) {
            Character character = t.charAt(i);
            needs.put(character, needs.getOrDefault(character, 0) + 1);
        }
        int match = 0;
        while (right < s.length()) {
            Character c1 = s.charAt(right);
            if (needs.containsKey(c1)) {
                window.put(c1, window.getOrDefault(c1, 0) + 1);
                if (window.get(c1).equals(needs.get(c1))) {
                    match++;
                }
            }
            right++;
            while (match == needs.size()) {
                if (right - left == t.length()) {
                    res.add(left);
                }
                char c2 = s.charAt(left);
                if (needs.containsKey(c2)) {
                    window.put(c2, window.get(c2) - 1);
                    if (window.get(c2) < needs.get(c2)) {
                        match--;
                    }
                }
                left++;
            }
        }
        return res;
    }
}
```


#### 无重复字符的最长子串
![lengthOfLongestSubstring](/images/Arithmetic/lengthOfLongestSubstring.PNG)


* 解法


```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int left = 0, right = 0;
        Map<Character, Integer> window = new HashMap<>(2);
        int res = 0;
        while (right < s.length()) {
            Character c1 = s.charAt(right);
            window.put(c1, window.getOrDefault(c1, 0) + 1);
            right++;
            while (window.get(c1) > 1) {
                Character c2 = s.charAt(left);
                window.put(c2, window.get(c2) - 1);
                left++;
            }
            res = Math.max(res, right - left);
        }
        return res;
    }
}
```