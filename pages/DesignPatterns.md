### 设计模式


#### 23种设计模式简介？
<table>
    <tr>
        <td>类型</td>
        <td>名称</td>
        <td>意图</td>
        <td>适用性</td>
    </tr>
    <tr>
        <td rowspan="5">创建型</td>
        <td>Factory Method(工厂方法)</td>
        <td>定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method使一个类的实例化延迟到其子类。</td>
        <td>当一个类不知道它所必须创建的对象的类的时候；当一个类希望由它的子类来指定它所创建的对象的时候；当类将创建对象的职责委托给多个帮助子类中的某一个，并且你希望将哪一个帮助子类是代理者这一信息局部化的时候。</td>
    </tr>
    <tr>
        <td>Abstract Factory(抽象工厂)</td>
        <td>提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。</td>
        <td>一个系统要独立于它的产品的创建、组合和表示时；一个系统要由多个产品系列中的一个来配置时；当你要强调一系列相关的产品对象的设计以便进行联合使用时；当你提供一个产品类库，而只想显示它们的接口而不是实现时。</td>
    </tr>
    <tr>
        <td>Builder(建造者)</td>
        <td>将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。</td>
        <td>当创建复杂对象的算法应该独立于该对象的组成部分以及它们的装配方式时；当构造过程必须允许被构造的对象有不同的表示时。</td>
    </tr>
    <tr>
        <td>Prototype(原型)</td>
        <td>用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。</td>
        <td>当要实例化的类是在运行时刻指定时，例如通过动态装载；或者为了避免创建一个与产品类层次平行的工厂类层次时；或者当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。</td>
    </tr>
    <tr>
        <td style="color: red">Singleton(单例)</td>
        <td>保证一个类仅有一个实例，并提供一个访问它的全局访问点。</td>
        <td>当类只能有一个实例而且客户可以从一个众所周知的访问点访问它时；当这个唯一实例应该是通过子类化可扩展的，并且客户应该无需更改代码就能使用一个扩展的实例时。</td>
    </tr>
    <tr>
        <td rowspan="7">结构型</td>
        <td>Adapter Class/Object(适配器)</td>
        <td>将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。</td>
        <td>你想使用一个已经存在的类，而它的接口不符合你的需求；你想创建一个可以复用的类，该类可以与其他不相关的类或不可预见的类(即那些接口可能不一定兼容的类)协同工作；(仅适用于对象Adapter)你想使用一些已经存在的子类，但是不可能对每一个都进行子类化以匹配它们的接口。对象适配器可以适配它的父类接口。</td>
    </tr>
    <tr>
        <td>Bridge(桥接)</td>
        <td>将抽象部分与它的实现部分分离，使它们都可以独立地变化。</td>
        <td>你不希望在抽象和它的实现部分之间有一个固定的绑定关系。例如这种情况可能是因为，在程序运行时刻实现部分应可以被选择或者切换。</td>
    </tr>
    <tr>
        <td>Composite(组合)</td>
        <td>将对象组合成树形结构以表示“部分-整体”的层次结构。Composite使得用户对单个对象和组合对象的使用具有一致性。</td>
        <td>你想表示对象的部分-整体层次结构；你希望用户忽略组合对象与单个对象的不同，用户将统一地使用组合结构中的所有对象。</td>
    </tr>
    <tr>
        <td>Decorator(装饰)</td>
        <td>动态地给一个对象添加一些额外的职责。就增加功能来说，Decorator模式相比生成子类更为灵活。</td>
        <td>在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责，处理那些可以撤消的职责。当不能采用生成子类的方法进行扩充时。一种情况是，可能有大量独立的扩展，为支持每一种组合将产生大量的子类，使得子类数目呈爆炸性增长。另一种情况可能是因为类定义被隐藏，或类定义不能用于生成子类。</td>
    </tr>
    <tr>
        <td>Facade(外观)</td>
        <td>为子系统中的一组接口提供一个一致的界面，Facade模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。</td>
        <td>提供一个简单的缺省视图，这一视图对大多数用户来说已经足够，而那些需要更多的可定制性的用户可以越过facade层。</td>
    </tr>
    <tr>
        <td>Flyweight(享元)</td>
        <td>运用共享技术有效地支持大量细粒度的对象。</td>
        <td>一个应用程序使用了大量的对象；完全由于使用大量的对象，造成很大的存储开销；对象的大多数状态都可变为外部状态；如果删除对象的外部状态，那么可以用相对较少的共享对象取代很多组对象；应用程序不依赖于对象标识；由于Flyweight对象可以被共享，对于概念上明显有别的对象，标识测试将返回真值。</td>
    </tr>
    <tr>
        <td style="color: red">Proxy(代理)</td>
        <td>为其他对象提供一种代理以控制对这个对象的访问。</td>
        <td>在需要用比较通用和复杂的对象指针代替简单的指针的时候，使用Proxy模式。</td>
    </tr>
    <tr>
        <td rowspan="11">行为型</td>
        <td>Interpreter(解释器)</td>
        <td>给定一个语言，定义它的文法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子。</td>
        <td>当有一个语言需要解释执行, 并且你可将该语言中的句子表示为一个抽象语法树时，可使用解释器模式。</td>
    </tr>
    <tr>
        <td>Template Method(模板方法)</td>
        <td>定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。TemplateMethod使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。</td>
        <td>一次性实现一个算法的不变的部分，并将可变的行为留给子类来实现。</td>
    </tr>
    <tr>
        <td>Chain of Responsibility(责任链)</td>
        <td>使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。</td>
        <td>有多个的对象可以处理一个请求，哪个对象处理该请求运行时刻自动确定；你想在不明确指定接收者的情况下，向多个对象中的一个提交一个请求；可处理一个请求的对象集合应被动态指定。</td>
    </tr>
    <tr>
        <td>Command(命令)</td>
        <td>将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可撤消的操作。</td>
        <td>抽象出待执行的动作以参数化某对象，你可用过程语言中的回调(call back)函数表达这种参数化机制。所谓回调函数是指函数先在某处注册，而它将在稍后某个需要的时候被调用。</td>
    </tr>
    <tr>
        <td>Iterator(迭代器)</td>
        <td>提供一种方法顺序访问一个聚合对象中各个元素, 而又不需暴露该对象的内部表示。</td>
        <td>访问一个聚合对象的内容而无需暴露它的内部表示；支持对聚合对象的多种遍历；为遍历不同的聚合结构提供一个统一的接口(即支持多态迭代)。</td>
    </tr>
    <tr>
        <td>Mediator(中介者)</td>
        <td>用一个中介对象来封装一系列的对象交互。中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。</td>
        <td>一组对象以定义良好但是复杂的方式进行通信。产生的相互依赖关系结构混乱且难以理解；一个对象引用其他很多对象并且直接与这些对象通信,导致难以复用该对象；想定制一个分布在多个类中的行为，而又不想生成太多的子类。</td>
    </tr>
    <tr>
        <td>Memento(备忘录)</td>
        <td>在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态。</td>
        <td>必须保存一个对象在某一个时刻的(部分)状态, 这样以后需要时它才能恢复到先前的状态；如果一个用接口来让其它对象直接得到这些状态，将会暴露对象的实现细节并破坏对象的封装性。</td>
    </tr>
    <tr>
        <td>Observer(观察者)</td>
        <td>定义对象间的一种一对多的依赖关系,当一个对象的状态发生改变时, 所有依赖于它的对象都得到通知并被自动更新。</td>
        <td>当一个抽象模型有两个方面, 其中一个方面依赖于另一方面。将这二者封装在独立的对象中以使它们可以各自独立地改变和复用；当对一个对象的改变需要同时改变其它对象, 而不知道具体有多少对象有待改变；当一个对象必须通知其它对象，而它又不能假定其它对象是谁。换言之, 你不希望这些对象是紧密耦合的。</td>
    </tr>
    <tr>
        <td>State(状态)</td>
        <td>允许一个对象在其内部状态改变时改变它的行为。对象看起来似乎修改了它的类。</td>
        <td>一个对象的行为取决于它的状态, 并且它必须在运行时刻根据状态改变它的行为；一个操作中含有庞大的多分支的条件语句，且这些分支依赖于该对象的状态。这个状态通常用一个或多个枚举常量表示。通常, 有多个操作包含这一相同的条件结构。State模式将每一个条件分支放入一个独立的类中。这使得你可以根据对象自身的情况将对象的状态作为一个对象，这一对象可以不依赖于其他对象而独立变化。</td>
    </tr>
    <tr>
        <td style="color: red">Strategy(策略)</td>
        <td>定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。本模式使得算法可独立于使用它的客户而变化。</td>
        <td>许多相关的类仅仅是行为有异。“策略”提供了一种用多个行为中的一个行为来配置一个类的方法。</td>
    </tr>
    <tr>
        <td>Visitor(访问者)</td>
        <td>定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。TemplateMethod使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。</td>
        <td>一次性实现一个算法的不变的部分，并将可变的行为留给子类来实现。</td>
    </tr>
</table>


#### 设计模式的六大原则？
👉 [设计模式六大原则](http://www.uml.org.cn/sjms/201211023.asp)


1. 单一职责原则
2. 里氏替换原则
3. 依赖倒置原则
4. 接口隔离原则
5. 迪米特法则
6. 开闭原则


#### 单例模式的优缺点及其实现的方式？
| 优点 | 缺点 | 
| :----- | :----- | 
| <div style="width: 400px">由于单例模式在内存中只有一个实例，减少内存开支，特别是一个对象需要频繁地创建销毁时，而且创建或销毁时性能又无法优化,单例模式就非常明显了。</div> | 单例模式一般没有接口，扩展很困难，若要扩展，除了修改代码基本上没有第二种途径可以实现。 | 
| <div style="width: 400px">由于单例模式只生成一个实例，所以，减少系统的性能开销，当一个对象产生需要比较多的资源时，如读取配置，产生其他依赖对象时，则可以通过在应用启动时直接产生一个单例对象，然后永久驻留内存的方式来解决。</div> | 单例对象如果持有Context，那么很容易引发内存泄漏，此时需要注意传递给单例对象的Context最好是Application Context。 | 
| <div style="width: 400px">单例模式可以避免对资源的多重占用，例如一个写文件操作，由于只有一个实例存在内存中，避免对同一个资源文件的同时写操作。</div> | ———— | 
| <div style="width: 400px">单例模式可以在系统设置全局的访问点，优化和共享资源访问，例如，可以设计一个单例类，负责所有数据表的映射处理。</div> | ———— | 


**饿汉式**
```java
public class Singleton {
    private Singleton() {};
    private static Singleton single = new Singleton();
    public static Singleton getInstance() {
        return single;
    }
}
```


**懒汉式**
```java
public class Singleton {
    private Singleton() {}
    private static Singleton single=null;
    public tatic Singleton getInstance() {
        if (single == null) {  
            single = new Singleton();
        }  
        return single;
    }
}
```


**线程安全**
```java
public class Singleton {
    private Singleton() {}
    private static Singleton single;
    public static Singleton getInstance() {
        if(null == single){
            synchronized(single){
                if(null == single){
                    single = new Singleton();
                }
            }
        }
        return single;
    }
}
```


#### 动态代理的实现方式？
**基于JDK的动态代理**
```java
public interface IJdkSubject {
    void jdkProxyMethod(String param);
}

public class JdkSubjectImpl implements IJdkSubject {
    @Override
    public void jdkProxyMethod(String param) {
        System.out.println("JDK Proxy " + param);
    }
}

public class JdkSubjectProxy implements InvocationHandler {
    private IJdkSubject iJdkSubject;
    public JdkSubjectProxy(IJdkSubject jdkSubject) {
        this.iJdkSubject = jdkSubject;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("--------------begin--------------");
        // invoke => 方法的返回值；如果没有，返回null
        Object invoke = method.invoke(iJdkSubject, args);
        System.out.println("--------------end--------------");
        return invoke;
    }
}

public class JdkProxyMain {
    public static void main(String[] args) {
        IJdkSubject iJdkSubject = new JdkSubjectImpl();
        InvocationHandler jdkSubjectProxy = new JdkSubjectProxy(iJdkSubject);
        // jdkSubjectProxy.getClass().getClassLoader() => 代理类的类加载器
        // iJdkSubject.getClass().getInterfaces() => 被代理类的接口，如果有多个就是数组的形式传入
        // jdkSubjectProxy => 代理类实例
        IJdkSubject proxyInstance = (IJdkSubject) Proxy.newProxyInstance(jdkSubjectProxy.getClass().getClassLoader(), iJdkSubject.getClass().getInterfaces(), jdkSubjectProxy);
        proxyInstance.jdkProxyMethod("success");
    }
}
```
```
// 运行结果
--------------begin--------------
JDK Proxy success
--------------end--------------
```


**基于CGLIB的动态代理**
```java
public class CglibSubject {
    public void cglibProxyMethod() {
        System.out.println("cglib Proxy success");
    }
}

public class CglibInterceptor implements MethodInterceptor {
    @Override
    public Object intercept(Object object, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println("--------------begin--------------");
        // MethodProxy => 代理方法
        // object => 被代理对象的实例
        // invokeSuper => 调用被拦截的方法，不要使用invoke，会出现OOM的情况
        Object obj = methodProxy.invokeSuper(object, objects);
        System.out.println("--------------end--------------");
        return obj;
    }
}

public class CglibProxyMain {
    public static void main(String[] args) {
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(CglibSubject.class);
        enhancer.setCallback(new CglibInterceptor());
        CglibSubject cglibSubject = (CglibSubject) enhancer.create();
        cglibSubject.cglibProxyMethod();
    }
}
```
```
// 运行结果
--------------begin--------------
cglib Proxy success
--------------end--------------
```


