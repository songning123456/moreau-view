#### 简介
格式化是主观的。就像其它规则一样，没有必须让你遵守的硬性规则。重点是不要因为格式去争论，这里有[大量的工具](http://standardjs.com/rules.html)来自动格式化，使用其中的一个即可！因为做为工程师去争论格式化就是在浪费时间和金钱。


针对自动格式化工具不能涵盖的问题（缩进、制表符还是空格、双引号还是单引号等），这里有一些指南。


#### 使用一致的大小写
JavaScript是无类型的，所以大小写告诉你关于你的变量、函数等的很多事情。这些规则是主观的，所以你的团队可以选择他们想要的。重点是，不管你们选择了什么，要保持一致。


**不好的：**
```
int DAYS_IN_WEEK = 7;
int  daysInMonth = 30;

String[] songs = {"Back In Black", "Stairway to Heaven", "Hey Jude"};
String[] Artists = {"ACDC", "Led Zeppelin", "The Beatles"};

void eraseDatabase() {}
void restore_database() {}

class animal {}
class Alpaca {}
```


**好的：**
```
int DAYS_IN_WEEK = 7;
int DAYS_IN_MONTH = 30;

String[] songs = {"Back In Black", "Stairway to Heaven", "Hey Jude"};
String[] artists = {"ACDC", "Led Zeppelin", "The Beatles"};

void eraseDatabase() {}
void restoreDatabase() {}

class Animal {}
class Alpaca {}
```


#### 函数的调用方与被调用方应该靠近
如果一个函数调用另一个，则在代码中这两个函数的竖直位置应该靠近。理想情况下，保持被调用函数在被调用函数的正上方。我们倾向于从上到下阅读代码，就像读一章报纸。由于这个原因，保持你的代码可以按照这种方式阅读。


**不好的：**
```
class PerformanceReview {
    String employee;
    public PerformanceReview(String employee) {
        this.employee = employee;
    }

    String lookupPeers() {
        return db.lookup(this.employee, "peers");
    }

    String lookupManager() {
        return db.lookup(this.employee, "manager");
    }

    void getPeerReviews() {
        String  peers = this.lookupPeers();
        // ...
    }

    public void perfReview() {
        this.getPeerReviews();
        this.getManagerReview();
        this.getSelfReview();
    }

    void getManagerReview() {
        String  manager = this.lookupManager();
    }

    void getSelfReview() {
        // ...
    }
}

PerformanceReview  review = new PerformanceReview("user");
review.perfReview();
```


**好的：**
```
class PerformanceReview {
    String employee;
    public PerformanceReview(String employee) {
        this.employee = employee;
    }

    void perfReview() {
        this.getPeerReviews();
        this.getManagerReview();
        this.getSelfReview();
    }

    void getPeerReviews() {
        String  peers = this.lookupPeers();
        // ...
    }

    String lookupPeers() {
        return db.lookup(this.employee, "peers");
    }

    void getManagerReview() {
        String  manager = this.lookupManager();
    }

    String lookupManager() {
        return db.lookup(this.employee, "manager");
    }

    public void getSelfReview() {
        // ...
    }
}

PerformanceReview review = new PerformanceReview("user");
review.perfReview();
```