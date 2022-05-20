#### 简介
抛出错误是一件好事情！他们意味着当你的程序有错时运行时可以成功确认，并且通过停止执行当前堆栈上的函数来让你知道，结束当前进程（在Node中），在控制台中用一个堆栈跟踪提示你。


#### 不要忽略捕捉到的错误
对捕捉到的错误不做任何处理不能给你修复错误或者响应错误的能力。向控制台记录错误 (`console.log`)也不怎么好，因为往往会丢失在海量的控制台输出中。如果你把任意一段代码用`try/catch`包装那就意味着你想到这里可能会错，因此你应该有个修复计划，或者当错误发生时有一个代码路径。


**不好的：**
```
try {
    functionThatMightThrow();
} catch (Exception error) {
    console.log(error);
}
```


**好的：**
```
try {
    functionThatMightThrow();
} catch (Exception error) {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
}
```