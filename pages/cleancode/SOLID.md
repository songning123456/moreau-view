#### 单一职责原则(SRP)
正如代码整洁之道所述，“永远不要有超过一个理由来修改一个类”。给一个类塞满许多功能，就像你在航班上只能带一个行李箱一样，这样做的问题你的类不会有理想的内聚性，将会有太多的理由来对它进行修改。最小化需要修改一个类的次数时很重要的，因为如果一个类拥有太多的功能，一旦你修改它的一小部分，将会很难弄清楚会对代码库中的其它模块造成什么影响。


**不好的：**
```
class UserSettings {
    User user;

    void changeSettings(UserSettings settings) {
        if (this.verifyCredentials()) {
        // ...
        }
    }

    void verifyCredentials() {
        // ...
    }
}
```


**好的：**
```
User user;
UserAuth auth;

public UserSettings(User user) {
    this.user = user;
    this.auth = new UserAuth(user);
}

void changeSettings(UserSettings settings) {
    if (this.auth.verifyCredentials()) {
        // ...
    }
}

```


#### 开闭原则(OCP)
Bertrand Meyer说过，“软件实体(类，模块，函数等)应该为扩展开放，但是为修改关闭。” 这是什么意思呢？这个原则基本上说明了你应该允许用户添加功能而不必修改现有的代码。


**不好的：**
```
class AjaxAdapter extends Adapter {
    private String name;
    public  AjaxAdapter() {
        this.name = "ajaxAdapter";
    }
}

class NodeAdapter extends Adapter {
    private String name;
    public NodeAdapter() {
        this.name = "nodeAdapter";
    }
}

class HttpRequester {
    public HttpRequester(Adapter adapter) {
        this.adapter = adapter;
    }

    void fetch(String url) {
        if ("ajaxAdapter".equals(this.adapter.name)) {
            makeAjaxCall(url);
        } else if ("httpNodeAdapter".equals(this.adapter.name)) {
            makeHttpCall(url);
        }
    }
}

void  makeAjaxCall(String url) {
    // request and return promise
}

void  makeHttpCall(String url) {
    // request and return promise
}
```

**好的：**
```
class AjaxAdapter extends Adapter {
    private String name;
    public  AjaxAdapter() {
        this.name = "ajaxAdapter";
    }
    void request(String url){
        
    }
}

class NodeAdapter extends Adapter {
    private String name;
    public NodeAdapter() {
        this.name = "nodeAdapter";
    }
    void request(String url){

    }
}

class HttpRequester {
    public  HttpRequester(Adapter adapter) {
        this.adapter = adapter;
    }

    void fetch(String url) {
        this.adapter.request(url);
    }
}
```


#### 里氏代换原则(LSP)

这是针对一个非常简单的里面的一个恐怖意图，它的正式定义是：“如果S是T的一个子类型，那么类型为T的对象可以被类型为S的对象替换（例如，类型为S的对象可作为类型为T的替代品儿不需要修改目标程序的期望性质（正确性、任务执行性等）。” 这甚至是个恐怖的定义。


最好的解释是，如果你又一个基类和一个子类，那个基类和字类可以互换而不会产生不正确的结果。这可能还有有些疑惑，让我们来看一下这个经典的正方形与矩形的例子。从数学上说，一个正方形是一个矩形，但是你用“is-a”的关系用继承来实现，你将很快遇到麻烦。


**不好的：**
```
class Rectangle {
    protected int width;
    protected int height;
    public Rectangle() {
        this.width = 0;
        this.height = 0;
    }

    void setColor(String color) {
       // ...
    }

    void render(int  area) {
        // ...
    }

    void setWidth(int width) {
        this.width = width;
    }

    void setHeight(int height) {
        this.height = height;
    }

    int getArea() {
        return this.width * this.height;
    }
}

class Square extends Rectangle {
    void setWidth(int width) {
        this.width = width;
        this.height = width;
    }

    void setHeight(int height) {
        this.width = height;
        this.height = height;
    }
}

void  renderLargeRectangles(List<Rectangle> rectangles) {
    for(Rectangle rectangle:rectangles) {
        rectangle.setWidth(4);
        rectangle.setHeight(5);
        int area = rectangle.getArea(); // BAD: Will return 25 for Square. Should be 20.
        rectangle.render(area);
    }
}

List<Rectangle> rectangles=new ArrayList<>();
rectangles.add(new Rectangle());
rectangles.add(new Rectangle());
rectangles.add(new Square());
renderLargeRectangles(rectangles);
```

**好的：**
```
class Shape {
    void setColor(String color) {
        // ...
    }

    int getArea() {
    }

    void render(int area) {
        // ...
    }
}

class Rectangle extends Shape {
    private int width;
    private int height;
    public Rectangle(int width, int height) {
        super();
        this.width = width;
        this.height = height;
    }

    int getArea() {
        return this.width * this.height;
    }
}

class Square extends Shape {
    private int length;
    public Square(int length) {
        super();
        this.length = length;
    }

    int getArea() {
        return this.length * this.length;
    }
}

void  renderLargeShapes(List<Shape> shapes) {
    for(Shape shape:shapes) {
        int area = shape.getArea();
        shape.render(area);
    }
}

List<Shape> shapes=new ArrayList<>();
shapes.add(new Rectangle(4,5));
shapes.add(new Rectangle(4,5));
shapes.add(new Square(5));
renderLargeShapes(shapes);
```


#### 接口隔离原则 (ISP)
接口隔离原则说的是 “客户端不应该强制依赖他们不需要的接口。”


**不好的：**
```
interface I {
    public void method1();
    public void method2();
    public void method3();
    public void method4();
    public void method5();
}

class A{
    public void depend1(I i){
        i.method1();
    }
    public void depend2(I i){
        i.method2();
    }
    public void depend3(I i){
        i.method3();
    }
}

class B{
    public void depend1(I i){
        i.method1();
    }
    public void depend2(I i){
        i.method4();
    }
    public void depend3(I i){
        i.method5();
    }
}

class C implements I{
    public void method1() {
        System.out.println("类B实现接口I的方法1");
    }
    public void method2() {
        System.out.println("类B实现接口I的方法2");
    }
    public void method3() {
        System.out.println("类B实现接口I的方法3");
    }
    // 对于类B来说，method4和method5不是必需的，但是由于接口A中有这两个方法，
    // 所以在实现过程中即使这两个方法的方法体为空，也要将这两个没有作用的方法进行实现。
    public void method4() {}
    public void method5() {}
}

class D implements I{
    public void method1() {
     System.out.println("类D实现接口I的方法1");
    }
    // 对于类D来说，method2和method3不是必需的，但是由于接口A中有这两个方法，
    // 所以在实现过程中即使这两个方法的方法体为空，也要将这两个没有作用的方法进行实现。
    public void method2() {}
    public void method3() {}

    public void method4() {
        System.out.println("类D实现接口I的方法4");
    }
    public void method5() {
        System.out.println("类D实现接口I的方法5");
    }
}
```

**好的：**
```
   interface I1 {
            public void method1();
        }

        interface I2 {
            public void method2();
            public void method3();
        }

        interface I3 {
            public void method4();
            public void method5();
        }

        class A{
            public void depend1(I1 i){
                i.method1();
            }
            public void depend2(I2 i){
                i.method2();
            }
            public void depend3(I2 i){
                i.method3();
            }
        }

        class B{
            public void depend1(I1 i){
                i.method1();
            }
            public void depend2(I3 i){
                i.method4();
            }
            public void depend3(I3 i){
                i.method5();
            }
        }


        class C implements I1, I2{
            public void method1() {
                System.out.println("类B实现接口I1的方法1");
            }
            public void method2() {
                System.out.println("类B实现接口I2的方法2");
            }
            public void method3() {
                System.out.println("类B实现接口I2的方法3");
            }
        }


        class D implements I1, I3{
            public void method1() {
                System.out.println("类D实现接口I1的方法1");
            }
            public void method4() {
                System.out.println("类D实现接口I3的方法4");
            }
            public void method5() {
                System.out.println("类D实现接口I3的方法5");
            }
```


#### 依赖反转原则(DIP)
这个原则阐述了两个重要的事情：

1. 高级模块不应该依赖于低级模块，两者都应该依赖与抽象；
2. 抽象不应当依赖于具体实现，具体实现应当依赖于抽象。


这个一开始会很难理解，但是如果你使用过Angular.js，你应该已经看到过通过依赖注入来实现的这个原则，虽然他们不是相同的概念，依赖反转原则让高级模块远离低级模块的细节和创建，可以通过DI来实现。这样做的巨大益处是降低模块间的耦合。耦合是一个非常糟糕的开发模式，因为会导致代码难于重构。


如上所述，JavaScript没有接口，所以被依赖的抽象是隐式契约。也就是说，一个对象/类的方法和属性直接暴露给另外一个对象/类。在下面的例子中，任何一个Request模块的隐式契约`InventoryTracker`将有一个`requestItems`方法。


**不好的：**
```
class InventoryRequester {
    private String REQ_METHODS;
    public InventoryRequester() {
        this.REQ_METHODS = "HTTP";
    }

    void requestItem(String item) {
        // ...
    }
}

class InventoryTracker {
    private List<String> items;
    private InventoryRequester requester;
    public InventoryTracker(List<String> items) {
        this.items = items;
    
        // 不好的： 我们已经创建了一个对请求的具体实现的依赖， 我们只有一个 requestItems 方法依
        // 赖一个请求方法 'request'
        this.requester = new InventoryRequester();
    }

    void requestItems() {
        this.items.stream().forEach(item->this.requester.requestItem(item));
    }
}

List<String> items=new ArrayList<>();
items.add("apples");
items.add("bananas");
InventoryTracker inventoryTracker = new InventoryTracker(items);
inventoryTracker.requestItems();
```


**好的：**
```
interface Requester{
        void requestItem(String item);
    }


class InventoryTracker {
    List<String> items;
    Requester requester;
    public InventoryTracker(List<String> items, Requester requester) {
        this.items = items;
        this.requester = requester;
    }

    void requestItems() {
        this.items.stream().forEach(item->requester.requestItem(item));
    }
}

class InventoryRequesterV1 implements Requester{
    String REQ_METHODS;
    public InventoryRequesterV1() {
        this.REQ_METHODS="HTTP";
    }

    @Override
    public void requestItem(String item) {
        // ...
    }
}

class InventoryRequesterV2 implements Requester{
    String REQ_METHODS;
    public InventoryRequesterV2() {
        this.REQ_METHODS="WS";
    }

    @Override
    public void requestItem(String item) {
        // ...
    }
}
// 通过外部创建依赖项并将它们注入， 我们可以轻松的用一个崭新的使用 WebSockets 的请求模块进行替换。
List<String> items=new ArrayList<>();
items.add("apples");
items.add("bananas");
InventoryTracker inventoryTracker = new InventoryTracker(items, new InventoryRequesterV2());
inventoryTracker.requestItems();
```