### 使用方法链
这个模式在Java中是非常有用的，并且你可以在许多类库比如Glide和OkHttp中见到。它使你的代码变得富有表现力，并减少啰嗦。因为这个原因，我说，使用方法链然后再看看你的代码会变得多么简洁。在你的类／方法中，简单的在每个方法的最后返回**this**，然后你就能把这个类的其它方法链在一起。


**不好的：**
```
class Car{
    private String make;
    private String model;
    private String color;

    public void setMake(String make) {
        this.make = make;
    }

    public void setModel(String model) {
        this.model = model;
    }

    public void setColor(String color) {
        this.color = color;
    }

    public void save(){
        console.log(this.make, this.model, this.color);
    }
}

Car car=new Car();
car.setColor("pink");
car.setMake("Ford");
car.setModel("F-150");
car.save();
```

**好的：**
```
class Car{
    private String make;
    private String model;
    private String color;

    public Car setMake(String make) {
        this.make = make;
        return this;
    }

    public Car setModel(String model) {
        this.model = model;
        return this;
    }

    public Car setColor(String color) {
        this.color = color;
        return this;
    }

    public Car save() {
        console.log(this.make, this.model, this.color);
        return this;
    }
}

Car car=new Car().setColor("pink").setMake("Ford").setModel("F-150").save();
```


### 组合优先于继承
正如[*设计模式四人帮*](https://en.wikipedia.org/wiki/Design_Patterns)所述，如果可能，你应该优先使用组合而不是继承。有许多好的理由去使用继承，也有许多好的理由去使用组合。这个格言的重点是，如果你本能的观点是继承，那么请想一下组合能否更好的为你的问题建模。很多情况下它真的可以。


那么你也许会这样想，“我什么时候改使用继承？” 这取决于你手上的问题，不过这儿有一个像样的列表说明什么时候继承比组合更好用：
1. 你的继承表示“是一个”的关系而不是“有一个”的关系（人类->动物 vs 用户->用户详情）；
2. 你可以重用来自基类的代码（人可以像所有动物一样行动）；
3. 你想通过基类对子类进行全局的修改（改变所有动物行动时的热量消耗）。


**不好的：**
```
class Employee{
    private String name;
    private String email;
}
// 不好是因为雇员“有”税率数据， EmployeeTaxData 不是一个 Employee 类型。
class EmployeeTaxData extends Employee{
    private String ssn;
    private String salary;
}
```


**好的：**
```
class EmployeeTaxData{
    private String ssn;
    private String salary;

    public EmployeeTaxData(String ssn, String salary) {
        this.ssn = ssn;
        this.salary = salary;
    }
}
class Employee{
    private String name;
    private String email;
    private EmployeeTaxData taxData;
    void setTaxData(String ssn,String salary){
        this.taxData=new EmployeeTaxData(ssn,salary);
    }
}
```