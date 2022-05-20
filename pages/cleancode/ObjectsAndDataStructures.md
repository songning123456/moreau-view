#### 使用getters和setters
使用getters和setters来访问对象上的数据比简单的在一个对象上查找属性要好得多。“为什么？” 你可能会问，好吧，原因请看下面的列表：


* 当你想在获取一个对象属性的背后做更多的事情时，你不需要在代码库中查找和修改每一处访问；
* 使用**set**可以让添加验证变得容易；
* 封装内部实现；
* 使用getting和setting时，容易添加日志和错误处理；
* 继承这个类，你可以重写默认功能；
* 你可以延迟加载对象的属性，比如说从服务器获取。


**不好的：**
```
class BankAccount{
    public int balance=1000;
}
BankAccount bankAccount=new BankAccount();
bankAccount.balance-=100;
```


**好的：**
```
class BankAccount{
    private int blance=1000;
    public int getBlance() {
        return blance;
    }

    public void setBlance(int blance) {
        if(verifyIfAmountCanBeSetted(blance)){
            this.blance = blance;
        }
    }

    void verifyIfAmountCanBeSetted(int amount){
        //...
    }
}
BankAccount bankAccount=new BankAccount();
bankAccount.setBlance(2000);
int balance=bankAccount.getBlance();
```