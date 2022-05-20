#### 回溯法


* 基本模板


```
List result = [];
public void backtrack(路径, 选择列表) {
    if (满足结束条件) {
        result.add(路径);
        return;
    }
    for (选择 in 选择列表) {
        做选择;
        backtrack(路径, 选择列表);
        撤销选择;
    }
}
```


#### 全排列
![permute](/images/Arithmetic/permute.PNG)


* 解法


```java
class Solution {
    private List<List<Integer>> res = new ArrayList<>();

    public List<List<Integer>> permute(int[] nums) {
        LinkedList<Integer> track = new LinkedList<>();
        backtrack(nums, track);
        return res;
    }

    private void backtrack(int[] nums, LinkedList<Integer> track) {
        if (track.size() == nums.length) {
            res.add(new LinkedList<>(track));
            return;
        }
        for (int num : nums) {
            if (track.contains(num)) {
                continue;
            }
            track.add(num);
            backtrack(nums, track);
            track.removeLast();
        }
    }
}
```


#### 全排列II
![permuteUnique](/images/Arithmetic/permuteUnique.PNG)


* 解法


```java
// https://leetcode-cn.com/problems/permutations-ii/
```


#### 子集
![subsets](/images/Arithmetic/subsets.PNG)


* 解法


```java
class Solution {
    private List<List<Integer>> list = new ArrayList<>();

    public List<List<Integer>> subsets(int[] nums) {
        LinkedList<Integer> track = new LinkedList<>();
        backtrack(0, nums, track);
        return list;
    }

    private void backtrack(int i, int[] nums, LinkedList<Integer> track) {
        list.add(new ArrayList<>(track));
        for (int j = i; j < nums.length; j++) {
            track.add(nums[j]);
            backtrack(j + 1, nums, track);
            track.removeLast();
        }
    }
}
```


#### 子集II
![subsetsWithDup](/images/Arithmetic/subsetsWithDup.PNG)


* 解法


```java
// https://leetcode-cn.com/problems/subsets-ii/
```


#### 组合
![combine](/images/Arithmetic/combine.PNG)


* 解法


```java
class Solution {
    private List<List<Integer>> combineList = new ArrayList<>();

    public List<List<Integer>> combine(int n, int k) {
        backtrack(1, n, k, new LinkedList<>());
        return combineList;
    }

    private void backtrack(int i, int n, int k, LinkedList<Integer> track) {
        if (track.size() == k) {
            combineList.add(new LinkedList<>(track));
            return;
        }
        for (int j = i; j <= n; j++) {
            track.add(j);
            backtrack(j + 1, n, k, track);
            track.removeLast();
        }
    }
}
```


#### 组合II
![combinationSum2](/images/Arithmetic/combinationSum2.PNG)


* 解法


```java
// https://leetcode-cn.com/problems/combination-sum-ii/
```


#### N皇后
![solveNQueens](/images/Arithmetic/solveNQueens.PNG)


* 解法


```java
class Solution {
   private List<List<String>> result = new ArrayList<>();

    public List<List<String>> solveNQueens(int n) {
        List<String> border = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            StringBuilder stringBuilder = new StringBuilder();
            for (int j = 0; j < n; j++) {
                stringBuilder.append(".");
            }
            border.add(stringBuilder.toString());
        }
        backtrack(border, 0);
        return result;
    }

    private void backtrack(List<String> border, int row) {
        if (row == border.size()) {
            List<String> temp = new ArrayList<>(border);
            result.add(temp);
            return;
        }
        int n = border.get(row).length();
        for (int col = 0; col < n; col++) {
            if (!isValid(border, row, col)) {
                continue;
            }
            this.replace(border, row, col, 'Q');
            backtrack(border, row + 1);
            this.replace(border, row, col, '.');
        }

    }

    private boolean isValid(List<String> border, int row, int col) {
        int n = border.size();
        for (int i = 0; i < n; i++) {
            if (border.get(i).charAt(col) == 'Q') {
                return false;
            }
        }
        for (int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
            if (border.get(i).charAt(j) == 'Q') {
                return false;
            }
        }
        for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
            if (border.get(i).charAt(j) == 'Q') {
                return false;
            }
        }
        return true;
    }

    private void replace(List<String> params, int row, int col, Character newVal) {
        String oldStr = params.get(row);
        StringBuilder sb = new StringBuilder(oldStr);
        sb.setCharAt(col, newVal);
        params.set(row, sb.toString());
    }
}
```