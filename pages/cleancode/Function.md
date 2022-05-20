#### 函数参数(两个以下最理想)
限制函数参数的个数是非常重要的，因为这样将使你的函数容易进行测试。一旦超过三个参数将会导致组合爆炸，因为你不得不编写大量针对每个参数的测试用例。


没有参数是最理想的，一个或者两个参数也是可以的，三个参数应该避免，超过三个应该被重构。通常，如果你有一个超过两个函数的参数，那就意味着你的函数尝试做太多的事情。如果不是，多数情况下一个更高级对象可能会满足需求。


当你发现你自己需要大量的参数时，你可以使用一个对象。


**不好的：**
```
void createMenu(String title,String body,String buttonText,boolean cancellable){}
```


**好的：**
```
class MenuConfig{
    String title;
    String body;
    String buttonText;
    boolean cancellable;
}
void  createMenu(MenuConfig menuConfig){}
```


#### 函数应当只做一件事情


这是软件工程中最重要的一条规则，当函数需要做更多的事情时，它们将会更难进行编写、测试和推理。当你能将一个函数隔离到只有一个动作，他们将能够被容易的进行重构并且你的代码将会更容易阅读。如果你严格遵守本指南中的这一条，你将会领先于许多开发者。


**不好的：**
```
public void emailClients(List<Client> clients) {
    for (Client client : clients) {
        Client clientRecord = repository.findOne(client.getId());
        if (clientRecord.isActive()){
            email(client);
        }
    }
}
```


**好的：**
```
public void emailClients(List<Client> clients) {
    for (Client client : clients) {
        if (isActiveClient(client)) {
            email(client);
        }
    }
}
    private boolean isActiveClient(Client client) {
    Client clientRecord = repository.findOne(client.getId());
    return clientRecord.isActive();
}
```


#### 函数名称应该说明它要做什么？
**不好的：**
```
private void addToDate(Date date, int month){
    //..
}

Date date = new Date();

// It's hard to to tell from the method name what is added
addToDate(date, 1);
```


**好的：**
```
private void addMonthToDate(Date date, int month){
    //..
}

Date date = new Date();
addMonthToDate(1, date);
```


#### 函数应该只有一个抽象级别
当在你的函数中有多于一个抽象级别时，你的函数通常做了太多事情。拆分函数将会提升重用性和测试性。


**不好的：**
```
void parseBetterJSAlternative(String code){
    String[] REGECES={};
    String[] statements=code.split(" ");
    String[] tokens={};
    for(String regex: Arrays.asList(REGECES)){
        for(String statement:Arrays.asList(statements)){
            //...
        }
    }
    String[] ast={};
    for(String token:Arrays.asList(tokens)){
        //lex ...
    }
    for(String node:Arrays.asList(ast)){
        //parse ...
    }
}
```


**好的：**
```
String[] tokenize(String code){
    String[] REGECES={};
    String[] statements=code.split(" ");
    String[] tokens={};
    for(String regex: Arrays.asList(REGECES)){
        for(String statement:Arrays.asList(statements)){
            //tokens push
        }
    }
    return tokens;
}

String[] lexer(String[] tokens){
    String[] ast={};
    for(String token:Arrays.asList(tokens)){
        //ast push
    }
    return ast;
}

void parseBetterJSAlternative(String code){
    String[] tokens=tokenize(code);
    String[] ast=lexer(tokens);
    for(String node:Arrays.asList(ast)){
        //parse ...
    }
}
```


#### 移除冗余代码
竭尽你的全力去避免冗余代码。冗余代码是不好的，因为它意味着当你需要修改一些逻辑时会有多个地方需要修改。


想象一下你在经营一家餐馆，你需要记录所有的库存西红柿，洋葱，大蒜，各种香料等等。如果你有多个记录列表，当你用西红柿做一道菜时你得更新多个列表。如果你只有一个列表，就只有一个地方需要更新！


你有冗余代码通常是因为你有两个或多个稍微不同的东西，它们共享大部分，但是它们的不同之处迫使你使用两个或更多独立的函数来处理大部分相同的东西。移除冗余代码意味着创建一个可以处理这些不同之处的抽象的函数/模块/类。


让这个抽象正确是关键的，这是为什么要你遵循**Classes**那一章的SOLID的原因。不好的抽象比冗余代码更差，所以要谨慎行事。既然已经这么说了，如果你能够做出一个好的抽象，才去做。不要重复你自己，否则你会发现当你要修改一个东西时时刻需要修改多个地方。


**不好的：**
```
void showDeveloperList(List<Developer> developers){
    for(Developer developer:developers){
        render(new Data(developer.expectedSalary,developer.experience,developer.githubLink));
    }
}

void showManagerrList(List<Manager> managers){
    for(Manager manager:managers){
        render(new Data(manager.expectedSalary,manager.experience,manager.portfolio));
    }
}
```


**好的：**
```
void showList(List<Employee> employees){
    for(Employee employee:employees){
        Data data=new Data(employee.expectedSalary,employee.experience,employee.githubLink);
        String portfolio=employee.portfolio;
        if("manager".equals(employee)){
            portfolio=employee.portfolio;
        }
        data.portfolio=portfolio;
        render(data);
    }
}
```


#### 不要使用标记位做为函数参数
标记位是告诉你的用户这个函数做了不只一件事情。函数应该只做一件事情。如果你的函数因为一个布尔值出现不同的代码路径，请拆分它们。


**不好的：**
```
void createFile(String name,boolean temp){
    if(temp){
        new File("./temp"+name);
    }else{
        new File(name);
    }
}
```


**好的：**
```
void createFile(String name){
    new File(name);
}

void createTempFile(String name){
    new File("./temp"+name);
}
```


#### 避免副作用
如果一个函数做了除接受一个值然后返回一个值或多个值之外的任何事情，它将会产生副作用，它可能是写入一个文件，修改一个全局变量，或者意外的把你所有的钱连接到一个陌生人那里。


现在在你的程序中确实偶尔需要副作用，就像上面的代码，你也许需要写入到一个文件，你需要做的是集中化你要做的事情，不要让多个函数或者类写入一个特定的文件，用一个服务来实现它，一个并且只有一个。


重点是避免这些常见的易犯的错误，比如在对象之间共享状态而不使用任何结构，使用任何地方都可以写入的可变的数据类型，没有集中化导致副作用。如果你能做到这些，那么你将会比其它的码农大军更加幸福。


**不好的：**
```
String name="Ryan McDermott";
void splitIntoFirstAndLastName(){
    name=name.split(" ").toString();
}
splitIntoFirstAndLastName();
System.out.println(name);
```


**好的：**
```
String name="Ryan McDermott";
String splitIntoFirstAndLastName(){
    return name.split(" ").toString();
}
String newName=splitIntoFirstAndLastName();
System.out.println(name);
System.out.println(newName);
```


#### 函数式编程优于指令式编程
函数式语言更加简洁并且更容易进行测试，当你可以使用函数式编程风格时请尽情使用。


**不好的：**
```
List<Integer> programmerOutput=new ArrayList<>();
programmerOutput.add(500);
programmerOutput.add(1500);
programmerOutput.add(150);
programmerOutput.add(1000);
int totalOutput=0;
for(int i=0;i<programmerOutput.size();i++){
    totalOutput+=programmerOutput.get(i);
}
```


**好的：**
```
List<Integer> programmerOutput=new ArrayList<>();
programmerOutput.add(500);
programmerOutput.add(1500);
programmerOutput.add(150);
programmerOutput.add(1000);
int totalOutput= programmerOutput.stream().filter(programmer -> programmer > 500).mapToInt(programmer -> programmer).sum();
```


#### 封装条件语句

**不好的：**
```
if(fsm.state.equals("fetching")&&listNode.isEmpty(){
    //...
}
```


**好的：**
```
void shouldShowSpinner(Fsm fsm, String listNode) {
    return fsm.state.equals("fetching")&&listNode.isEmpty();
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
    // ...
}
```


#### 避免负面条件

**不好的：**
```
void isDOMNodeNotPresent(Node node) {
    // ...
}

if (!isDOMNodeNotPresent(node)) {
    // ...
}
```


**好的：**
```
void isDOMNodePresent(Node node) {
    // ...
}

if (isDOMNodePresent(node)) {
    // ...
}
```


#### 避免条件语句
这看起来似乎是一个不可能的任务。第一次听到这个时，多数人会说：“没有`if`语句还能期望我干啥呢”，答案是多数情况下你可以使用多态来完成同样的任务。第二个问题通常是 “好了 那么做很棒，但是我为什么想要那样做呢”，答案是我们学到的上一条代码整洁之道的理念：一个函数应当只做一件事情。当你有使用`if`语句的类/函数是，你在告诉你的用户你的函数做了不止一件事情。记住：只做一件事情。


**不好的：**
```
class Airplane{
int getCurisingAltitude(){
    switch(this.type){
        case "777":
            return this.getMaxAltitude()-this.getPassengerCount();
        case "Air Force One":
            return this.getMaxAltitude();
        case "Cessna":
            return this.getMaxAltitude() - this.getFuelExpenditure();
        }
    }
}
```


**好的：**
```
class Airplane {
    // ...
}

class Boeing777 extends Airplane {
    // ...
    int getCruisingAltitude() {
        return this.getMaxAltitude() - this.getPassengerCount();
    }
}

class AirForceOne extends Airplane {
    // ...
    int getCruisingAltitude() {
        return this.getMaxAltitude();
    }
}

class Cessna extends Airplane {
    // ...
    int getCruisingAltitude() {
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
}
```


#### 移除僵尸代码
僵死代码和冗余代码同样糟糕。没有理由在代码库中保存它。如果它不会被调用，就删掉它。当你需要它时，它依然保存在版本历史记录中。


**不好的：**
```
void  oldRequestModule(String url) {
    // ...
}

void  newRequestModule(String url) {
    // ...
}

String  req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```


**好的：**
```
void  newRequestModule(String url) {
    // ...
}

String  req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```