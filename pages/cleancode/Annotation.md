#### 仅仅对包含复杂业务逻辑的东西进行注释
注释是代码的辩解，不是要求。多数情况下，好的代码就是文档。


**不好的：**
```
void hashIt(String data) {
    // The hash
    long hash = 0;

    // Length of string
    int length = data.length();

    // Loop through every character in data
    for (int i = 0; i < length; i++) {
        // Get character code.
        char mChar = data.charAt(i);
        // Make the hash
        hash = ((hash << 5) - hash) + mChar;
        // Convert to 32-bit integer
        hash &= hash;
    }
}
```


**好的：**
```
void hashIt(String data) {
    long hash = 0;
    int length = data.length();

    for (int i = 0; i < length; i++) {
        char mchar = data.charAt(i);
        hash = ((hash << 5) - hash) + mchar;

        // Convert to 32-bit integer
        hash &= hash;
    }
}
```


#### 不要在代码库中保存注释掉的代码
因为有版本控制，把旧的代码留在历史记录即可。


**不好的：**
```
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```


**好的：**
```
doStuff();
```


#### 不要有日志式的注释
记住，使用版本控制！不需要僵尸代码，注释掉的代码，尤其是日志式的注释。使用`git log`来获取历史记录。


**不好的：**
```
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
 
void combine(String a, String b) {
    return a + b;
}
```


**好的：**
```
void combine(String a, String b) {
        return a + b;
}
```


#### 避免占位符
它们仅仅添加了干扰。让函数和变量名称与合适的缩进和格式化为你的代码提供视觉结构。


**不好的：**
```
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
String[] model = {"foo","bar"};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
void action(){
    //...
}
```


**好的：**
```
String[] model = {"foo","bar"};
void action(){
    //...
}
```