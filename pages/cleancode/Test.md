#### 简介
测试比发布更加重要。如果你没有测试或者测试不够充分，每次发布时你就不能确认没有破坏任何事情。测试的量由你的团队决定，但是拥有100%的覆盖率(包括所有的语句和分支)是你为什么能达到高度自信和内心的平静。这意味着需要一个额外的伟大的测试框架，也需要一个好的[覆盖率工具](http://gotwarlost.github.io/istanbul/)。


没有理由不写测试。这里有[大量的优秀的 JS 测试框架](http://jstherightway.org/#testing-tools)，选一个适合你的团队的即可。当为团队选择了测试框架之后，接下来的目标是为生产的每一个新的功能／模块编写测试。如果你倾向于测试驱动开发(TDD)，那就太棒了，但是要点是确认你在上线任何功能或者重构一个现有功能之前， 达到了需要的目标覆盖率。


#### 一个测试一个概念
**不好的：**
```
void testMakeMomentJSGreatAgain(){
    Date date;
    date = new MakeMomentJSGreatAgain("1/1/2015");
    date.addDays(30);
    Assert.equal(date.getString(),"1/31/2015").

    date = new MakeMomentJSGreatAgain("2/1/2016");
    date.addDays(28);
    Assert.equal(date.getString(),"02/29/2016");

    date = new MakeMomentJSGreatAgain("2/1/2015");
    date.addDays(28);
    Assert.equal(date.getString(),"03/01/2015");
}
```


**好的：**
```
void testThirtyDayMonths(){
    Date date = new MakeMomentJSGreatAgain("1/1/2015");
    date.addDays(30);
    Assert.equal(date.getString(),"1/31/2015");
}

void testLeapYear(){
    Date date = new MakeMomentJSGreatAgain("2/1/2016");
    date.addDays(28);
    Assert.equal(date.getString(),"02/29/2016");
}

void testNonLeapYear(){
    Date date = new MakeMomentJSGreatAgain("2/1/2015");
    date.addDays(28);
    Assert.equal(date.getString(),"03/01/2015");
}
```