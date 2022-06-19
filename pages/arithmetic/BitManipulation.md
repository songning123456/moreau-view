### 位操作


#### 基本位操作
利用或操作“|”和空格将英文字符转换为小写
```
('a' | ' ') = 'a';
('A' | ' ') = 'a';
```


利用与操作“&”和下划线将英文字符转换为大写
```
('b' & '_') = 'B'
('B' & '_') = 'B'
```


利用异或操作“^”和空格进行英文字符大小写互换
```
('d' ^ ' ') = 'D'
('D' ^ ' ') = 'd'
```


判断两个数是否异号
```
int x = -1, y = 2;
bool f = ((x ^ y) < 0); // true

int x = 3, y = 2;
bool f = ((x ^ y) < 0); // false
```


不用临时变量交换两个数
```
int a = 1, b = 2;
a ^= b;
b ^= a;
a ^= b;
// 现在 a = 2, b = 1
```


加一
```
int n = 1;
n = -~n;
// 现在 n = 2
```


减一
```
int n = 2;
n = ~-n;
// 现在 n = 1
```


#### 位1的个数
![hammingWeight](/images/Arithmetic/hammingWeight.PNG)


* 解法


```
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int res = 0;
        while (n != 0) {
            n = n & (n - 1);
            res++;
        }
        return res;
    }
}
```


#### 2的幂
![isPowerOfTwo](/images/Arithmetic/isPowerOfTwo.PNG)


* 解法


```
class Solution {
    public boolean isPowerOfTwo(int n) {
        if (n <= 0) {
            return false;
        }
        return (n & (n - 1)) == 0;
    }
}
```
