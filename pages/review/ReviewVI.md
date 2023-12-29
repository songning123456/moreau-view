## 题卷VI


![](/images/ReviewVI/ReviewVI.png)


### Java基础


#### JDK和JRE有什么区别？
|名称|解释|
| :----- | :----- |
|JDK|Java Development Kit的简称，java开发工具包，提供了java的开发环境和运行环境。|
|JRE|Java Runtime Environment的简称，java运行环境，为java的运行提供了所需环境。|


具体来说JDK其实包含了JRE，同时还包含了编译java源码的编译器javac，还包含了很多java程序调试和分析的工具。简单来说如果你需要运行java程序，只需安装JRE就可以了，如果你需要编写java程序，需要安装JDK。


#### ==和equals的区别是什么？
**==**


1. 基本类型: 比较的是值是否相同；
2. 引用类型: 比较的是引用是否相同。


```java
String x = "string";
String y = "string";
String z = new String("string");
System.out.println(x==y); // true
System.out.println(x==z); // false
System.out.println(x.equals(y)); // true
System.out.println(x.equals(z)); // true
```


因为x和y指向的是同一个引用，所以==也是true，而new String()方法则重写开辟了内存空间，所以==结果为 false，而equals比较的一直是值，所以结果都为true。


**equals**


equals本质上就是==，只不过String和Integer等重写了equals方法，把它变成了值比较。


```java
class Cat {

    private String name;
    
    public Cat(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

Cat c1 = new Cat("王磊");
Cat c2 = new Cat("王磊");
System.out.println(c1.equals(c2)); // false
```


```java
// equals本质上就是==
public boolean equals(Object obj) {
		return (this == obj);
}
```


**两个相同值的String对象，为什么返回的是true？**


```java
String s1 = new String("老王");
String s2 = new String("老王");
System.out.println(s1.equals(s2)); // true
```


```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```


String重写了Object的equals方法，把引用比较改成了值比较。


**总结**


==对于基本类型来说是值比较，对于引用类型来说是比较的是引用；而equals默认情况下是引用比较，只是很多类重写了equals方法，比如String、Integer等把它变成了值比较，所以一般情况下equals比较的是值是否相等。


#### 两个对象的hashCode()相同，则equals()也一定为true？


两个对象的hashCode()相同，equals()不一定true。


```java
String str1 = "通话";
String str2 = "重地";
System.out.println(String.format("str1: %d | str2: %d", str1.hashCode(), str2.hashCode()));
System.out.println(str1.equals(str2));
```


```java
str1: 1179395 | str2: 1179395

false
```


很显然“通话”和“重地”的hashCode()相同，然而equals()则为false，因为在散列表中，hashCode()相等即两个键值对的哈希值相等，然而哈希值相等并不一定能得出键值对相等。


#### final在Java中有什么作用？
|使用范围|解释|
| :----- | :----- |
|<div style='width: 80px'>修饰类</div>|被final修饰的类不允许被继承，表示此类设计的很完美，不需要被修改和扩展。|
|<div style='width: 80px'>修饰方法</div>|被final修饰的方法表示此方法提供的功能已经满足当前要求，不需要进行扩展，并且也不允许任何从此类继承的类来重写此方法。|
|<div style='width: 80px'>修饰变量</div>|当final修饰变量时，表示该属性一旦被初始化便不可以被修改。|
|<div style='width: 80px'>修饰参数</div>|当final修饰参数时，表示此参数在整个方法内不允许被修改。|


使用final修饰类可以防止被其他类继承，如JD代码中String类就是被final修饰的，从而防止被其他类继承，导致内部逻辑被破坏。


final是Java中常见的一个关键字，被它修饰的对象不允许修改、替换其原始值或定义。final有4种用法，可以用来修饰类、方法、变量或参数。


#### Math.round(-1.5)等于多少？
-1


Math.round()表示“四舍六入”，将原来的数字加上0.5后再向下取整。


```java
Math.round(x) => Math.floor(x + 0.5)
```


#### String属于基础的数据类型吗？
不属于，String属于对象。


| 基础数据类型 | 字节长度(byte) |
| :----: | :----: |
| byte | 1 |
| short | 2 |
| int | 4 |
| long | 8 |
| boolean | 1 |
| char | 2 |
| float | 4 |
| double | 8 |


#### Java中操作字符串都有哪些类？它们之间有什么区别？
| ———— | 可变性 | 线程安全性 | 性能 | 
| :----- | :----- | :----- | :----- | 
| String | 用字符数组保存字符串，private final char value[]，所以String对象是不可变的。 | 对象是不可变的，也就可以理解为常量，线程安全。 | 每次对String类型进行改变的时候，都会生成一个新的String对象，然后将指针指向新的String对象。 | 
| StringBuffer | 继承自AbstractStringBuilder类，在AbstractStringBuilder中也是使用字符数组保存字符串，char[] value，是可变的。 | 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。 | 本身进行操作，而不是生成新的对象并改变对象引用。 | 
| StringBuilder | 同StringBuffer。 | 并没有对方法进行加同步锁，所以是非线程安全的。 | StringBuilder相比使用StringBuffer仅能获得10%~15%左右的性能提升。 | 


#### String str="i"与String str=new String("i")一样吗？
不一样，因为内存的分配方式不一样。String str="i"的方式，Java虚拟机会将其分配到常量池中；而String str=new String("i")则会被分到堆内存中。


#### 如何将字符串反转？
使用StringBuilder或者StringBuffer的reverse()方法。


```java
// StringBuffer reverse
StringBuffer stringBuffer = new StringBuffer();
stringBuffer.append("abcdefg");
System.out.println(stringBuffer.reverse()); // gfedcba
// StringBuilder reverse
StringBuilder stringBuilder = new StringBuilder();
stringBuilder.append("abcdefg");
System.out.println(stringBuilder.reverse()); // gfedcba
```


#### String类的常用方法都有那些？
| 方法名称 | 解释 |
| :----: | :----: |
|indexOf()|返回指定字符的索引。|
|charAt()|返回指定索引处的字符。|
|replace()|字符串替换。|
|trim()|去除字符串两端空白。|
|split()|分割字符串，返回一个分割后的字符串数组。|
|getBytes()|返回字符串的byte类型数组。|
|length()|返回字符串长度。|
|toLowerCase()|将字符串转成小写字母。|
|toUpperCase()|将字符串转成大写字符。|
|substring()|截取字符串。|
|equals()|字符串比较。|


#### 抽象类必须要有抽象方法吗？
不需要，抽象类不一定非要有抽象方法。


```java
abstract class Cat {
    public static void sayHi() {
        System.out.println("hi~");
    }
}
```


上面代码，抽象类并没有抽象方法但完全可以正常运行。


#### 普通类和抽象类有哪些区别？
1. 普通类不能包含抽象方法，抽象类可以包含抽象方法。
2. 抽象类不能直接实例化，普通类可以直接实例化。


#### 抽象类能使用final修饰吗？
不能，定义抽象类就是让其他类继承的，如果定义为final该类就不能被继承，这样彼此就会产生矛盾，所以final不能修饰抽象类。


#### 接口和抽象类有什么区别？
|区别|接口|抽象类|
| :----- | :----- | :----- |
|实现|使用implements来实现接口。|子类使用extends来继承。|
|构造函数|不能有。|可以有构造函数。|
|实现数量|类可以实现很多个接口。|只能继承一个抽象类。|
|访问修饰符|方法默认使用public修饰。|可以是任意访问修饰符。|


#### Java中IO流分为几种？
1. 按功能来分: 输入流(input)、输出流(output)。
2. 按类型来分: 字节流和字符流。


字节流和字符流的区别是，字节流按8位传输以字节为单位输入输出数据，字符流按16位传输以字符为单位输入输出数据。


#### BIO、NIO、AIO有什么区别？
|类型|解释|
| :----- | :----- |
|<div style='width: 60px'>BIO</div>|Block IO同步阻塞式IO，就是我们平常使用的传统IO，它的特点是模式简单使用方便，并发处理能力低。|
|<div style='width: 60px'>NIO</div>|New IO(或Non Blocking IO)同步非阻塞IO，是传统IO的升级，客户端和服务器端通过Channel(通道)通讯，实现了多路复用。|
|<div style='width: 60px'>AIO</div>|Asynchronous IO是NIO的升级，也叫NIO2，实现了异步非堵塞IO，异步IO的操作基于事件和回调机制。|


#### Files的常用方法都有哪些？
|方法|解释|
| :----- | :----- |
|Files.exists()|检测文件路径是否存在。|
|Files.createFile()|创建文件。|
|Files.createDirectory()|创建文件夹。|
|Files.delete()|删除一个文件或目录。|
|Files.copy()|复制文件。|
|Files.move()|移动文件。|
|Files.size()|查看文件个数。|
|Files.read()|读取文件。|
|Files.write()|写入文件。|


### 容器


#### Java容器都有哪些？
![](/images/ReviewVI/Collection.png)


#### Collection和Collections有什么区别？
| 类 | 解释 |
| :----- | :----- | 
|Collection|是一个集合接口，它提供了对集合对象进行基本操作的通用接口方法，所有集合都是它的子类，比如List、Set等。|
|Collections|是一个包装类，包含了很多静态方法，不能被实例化，就像一个工具类，比如提供的排序方法Collections.sort(list)。|


#### List、Set、Map之间的区别是什么？
![](/images/ReviewVI/listmapset.png)


#### HashMap和Hashtable有什么区别？
// todo


#### 如何决定使用HashMap还是TreeMap？
// todo


#### 说一下HashMap的实现原理？
// todo


#### 说一下HashSet的实现原理？
// todo


#### ArrayList和LinkedList的区别是什么？
// todo


#### 如何实现数组和List之间的转换？
// todo


#### ArrayList和Vector的区别是什么？
// todo


#### Array和ArrayList有何区别？
// todo


#### 在Queue中poll()和remove()有什么区别？
// todo


#### 哪些集合类是线程安全的？
// todo


#### 迭代器Iterator是什么？
// todo


#### Iterator怎么使用？有什么特点？
// todo


#### Iterator和ListIterator有什么区别？
// todo


#### 怎么确保一个集合不能被修改？
// todo


### 多线程


#### 并行和并发有什么区别？
// todo


#### 线程和进程的区别？
// todo


#### 守护线程是什么？
// todo


#### 创建线程有哪几种方式？
// todo


#### 说一下runnable和callable有什么区别？
// todo


#### 线程有哪些状态？
// todo


#### sleep()和wait()有什么区别？
// todo


#### notify()和notifyAll()有什么区别？
// todo


#### 线程的run()和start()有什么区别？
// todo


#### 创建线程池有哪几种方式？
// todo


#### 线程池都有哪些状态？
// todo


#### 线程池中submit()和execute()方法有什么区别？
// todo


#### 在Java程序中怎么保证多线程的运行安全？
// todo


#### 多线程中synchronized锁升级的原理是什么？
在锁对象的对象头里面有一个threadid字段，在第一次访问的时候threadid为空，jvm让其持有偏向锁，并将threadid设置为其线程id，再次进入的时候会先判断threadid是否与其线程id一致，如果一致则可以直接使用此对象；如果不一致，则升级偏向锁为轻量级锁，通过自旋循环一定次数来获取锁，执行一定次数之后，如果还没有正常获取到要使用的对象，此时就会把锁从轻量级升级为重量级锁，此过程就构成了synchronized锁的升级。


锁升级是为了减低了锁带来的性能消耗。在Java 6之后优化synchronized的实现方式，使用了偏向锁升级为轻量级锁再升级到重量级锁的方式，从而减低了锁带来的性能消耗。


#### 什么是死锁？
// todo


#### 怎么防止死锁？
// todo


#### ThreadLocal是什么？有哪些使用场景？
// todo


#### 说一下synchronized底层实现原理？
// todo


#### synchronized和volatile的区别是什么？
// todo


#### synchronized和Lock有什么区别？
// todo


#### synchronized和ReentrantLock区别是什么？
// todo


#### 说一下atomic的原理？
通过CAS乐观锁保证原子性，通过自旋保证当次修改的最终修改成功，通过降低锁粒度(多段锁)增加并发性能。


### 反射


#### 什么是反射？
反射是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为Java语言的反射机制。


#### 什么是Java序列化？什么情况下需要序列化？
Java序列化是为了保存各种对象在内存中的状态，并且可以把保存的对象状态再读出来。


1. 想把的内存中的对象状态保存到一个文件中或者数据库中时候；
2. 想用套接字在网络上传送对象的时候；
3. 想通过RMI(远程方法调用)传输对象的时候。


#### 动态代理是什么？有哪些应用？
// todo


#### 怎么实现动态代理？
// todo


### 对象拷贝


#### 为什么要使用克隆？
// todo


#### 如何实现对象克隆？
// todo


#### 深拷贝和浅拷贝区别是什么？
// todo


### Java Web模块


#### JSP和servlet有什么区别？
// todo


#### JSP有哪些内置对象？作用分别是什么？
// todo


#### 说一下JSP的4种作用域？
// todo


#### session和cookie有什么区别？
|区别|解释|
| :----- | :----- |
|存储位置不同|session存储在服务器端；cookie存储在浏览器端。|
|安全性不同|cookie安全性一般，在浏览器存储，可以被伪造和修改。|
|容量和个数限制|cookie有容量限制，每个站点下的cookie也有个数限制。|
|存储的多样性|session可以存储在redis中、数据库中、应用程序中；而cookie只能存储在浏览器中。|


#### 说一下session的工作原理？
客户端登录完成之后，服务器会创建对应的session，session创建完之后，会把session的id发送给客户端，客户端再存储到浏览器中。这样客户端每次访问服务器时，都会带着sessionid，服务器拿到sessionid之后，在内存找到与之对应的session这样就可以正常工作了。


#### 如果客户端禁止cookie能实现session还能用吗？
可以用，session只是依赖cookie存储sessionid，如果cookie被禁用了，可以使用url中添加sessionid的方式保证session能正常使用。


#### SpringMVC和Struts的区别是什么？
// todo


#### 如何避免Sql注入？
1. 使用预处理PreparedStatement。
2. 使用正则表达式过滤掉字符中的特殊字符。


#### 什么是XSS攻击？如何避免？
跨站脚本攻击，它是web程序中常见的漏洞。原理是攻击者往web页面里插入恶意的脚本代码(css 代码、javascript代码等)，当用户浏览该页面时，嵌入其中的脚本代码会被执行，从而达到恶意攻击用户的目的，如盗取用户cookie、破坏页面结构、重定向到其他网站等。预防XSS的核心是必须对输入的数据做过滤处理。


#### 什么是CSRF攻击？如何避免？
Cross-Site Request Forgery(跨站请求伪造)，可以理解为攻击者盗用了你的身份，以你的名义发送恶意请求，比如以你名义发送邮件、发消息、购买商品，虚拟货币转账等。


1. 验证请求来源地址；
2. 关键操作添加验证码；
3. 在请求地址添加token并验证。


### 异常模块


#### throw和throws的区别？
// todo


#### final、finally、finalize有什么区别？
// todo


#### try-catch-finally中哪个部分可以省略？
// todo


#### try-catch-finally中，如果catch中return了，finally还会执行吗？
// todo


#### 常见的异常类有哪些？
// todo


### 网络模块


#### http响应码301和302代表的是什么？有什么区别？
// todo


#### forward和redirect的区别？
// todo


#### 简述TCP和UDP的区别？
// todo


#### TCP为什么要三次握手，两次不行吗？为什么？
// todo


#### 说一下TCP粘包是怎么产生的？
// todo


#### OSI的七层模型都有哪些？
// todo


#### get和post请求有哪些区别？
// todo


#### 如何实现跨域？
// todo


#### 说一下JSONP实现原理？
// todo


### 设计模式


#### 说一下你熟悉的设计模式？
|设计模式|解释|
| :----- | :----- |
|<div style="width: 120px">单例模式</div>|保证被创建一次，节省系统开销。|
|<div style="width: 120px">工厂模式</div>|解耦代码。|
|<div style="width: 120px">观察者模式</div>|定义了对象之间的一对多的依赖，这样一来，当一个对象改变时，它的所有的依赖者都会收到通知并自动更新。|
|<div style="width: 120px">外观模式</div>|提供一个统一的接口，用来访问子系统中的一群接口，外观定义了一个高层的接口，让子系统更容易使用。|
|<div style="width: 120px">模版方法模式</div>|定义了一个算法的骨架，而将一些步骤延迟到子类中，模版方法使得子类可以在不改变算法结构的情况下，重新定义算法的步骤。|
|<div style="width: 120px">状态模式</div>|允许对象在内部状态改变时改变它的行为，对象看起来好像修改了它的类。|


#### 简单工厂和抽象工厂有什么区别？
// todo


### Spring MVC


#### 为什么要使用Spring？
// todo


#### 解释一下什么是AOP？
// todo


#### 解释一下什么是IOC？
// todo


#### Spring有哪些主要模块？
// todo


#### Spring常用的注入方式有哪些？
// todo


#### Spring中的bean是线程安全的吗？
// todo


#### Spring支持几种bean的作用域？
// todo


#### Spring自动装配bean有哪些方式？
// todo


#### Spring事务实现方式有哪些？
// todo


#### 说一下Spring的事务隔离？
// todo


#### 说一下SpringMVC运行流程？
// todo


#### SpringMVC有哪些组件？
// todo


#### @RequestMapping的作用是什么？
// todo


#### @Autowired的作用是什么？
// todo


### Spring Boot/Spring Cloud


#### 什么是Spring Boot？
// todo


#### 为什么要用Spring Boot？
// todo


#### Spring Boot核心配置文件是什么？
// todo


#### Spring Boot配置文件有哪几种类型？它们有什么区别？
// todo


#### Spring Boot有哪些方式可以实现热部署？
// todo


#### JPA和Hibernate有什么区别？
// todo


#### 什么是Spring Cloud？
// todo


#### Spring Cloud断路器的作用是什么？
// todo


#### Spring Cloud的核心组件有哪些？
// todo


### Hibernate


#### 为什么要使用Hibernate？
// todo


#### 什么是ORM框架？
// todo


#### Hibernate中如何在控制台查看打印的sql语句？
// todo


#### Hibernate有几种查询方式？
// todo


#### Hibernate实体类可以被定义为final吗？
// todo


#### 在Hibernate中使用Integer和int做映射有什么区别？
// todo


#### Hibernate是如何工作的？
// todo


#### get()和load()的区别？
// todo


#### 说一下Hibernate的缓存机制？
// todo


#### Hibernate对象有哪些状态？
// todo


#### 在Hibernate中getCurrentSession和openSession的区别是什么？
// todo


#### Hibernate实体类必须要有无参构造函数吗？为什么？
// todo


### Mybatis


#### Mybatis中#{}和${}的区别是什么？
`#{}`是预编译处理，`${}`是字符替换。


在使用#{}时，Mybatis会将sql中的#{}替换成“?”，配合PreparedStatement的set方法赋值，这样可以有效的防止sql注入，保证程序的运行安全。


#### Mybatis有几种分页方式？
|分页方式|解释|
| :----- | :----- |
|<div style="width: 100px">逻辑分页</div>|使用Mybatis自带的RowBounds进行分页，它是一次性查询很多数据，然后在数据中再进行检索。|
|<div style="width: 100px">物理分页</div>|自己手写sql分页或使用分页插件PageHelper，去数据库查询指定条数的分页数据的形式。|


#### RowBounds是一次性查询全部结果吗？为什么？
RowBounds表面是在“所有”数据中检索数据，其实并非是一次性查询出所有数据，因为Mybatis是对jdbc的封装，在jdbc驱动中有一个Fetch Size的配置，它规定了每次最多从数据库查询多少条数据，假如你要查询更多数据，它会在你执行next()的时候，去查询更多的数据。就好比你去自动取款机取10000元，但取款机每次最多能取2500元，所以你要取4次才能把钱取完。只是对于jdbc来说，当你调用next()的时候会自动帮你完成查询工作。这样做的好处可以有效的防止内存溢出。


#### Mybatis逻辑分页和物理分页的区别是什么？
|分页|区别|
| :----- | :----- |
|<div style="width: 100px">逻辑分页</div>|一次性查询很多数据，然后再在结果中检索分页的数据。这样做弊端是需要消耗大量的内存、有内存溢出的风险、对数据库压力较大。|
|<div style="width: 100px">物理分页</div>|从数据库查询指定条数的数据，弥补了一次性全部查出的所有数据的种种缺点，比如需要大量的内存，对数据库查询压力较大等问题。|


#### Mybatis是否支持延迟加载？延迟加载的原理是什么？
```
 lazyLoadingEnabled=true
```


延迟加载的原理的是调用的时候触发加载，而不是在初始化的时候就加载信息。比如调用a.getB().getName()，这个时候发现a.getB()的值为null，此时会单独触发事先保存好的关联B对象的sql，先查询出来B，然后再调用a.setB(b)，而这时候再调用a.getB().getName()就有值了，这就是延迟加载的基本原理。


#### 说一下Mybatis的一级缓存和二级缓存？
|缓存|解释|
| :----- | :----- |
|<div style="width: 100px">一级缓存</div>|基于PerpetualCache的HashMap本地缓存，它的声明周期是和sqlSession一致的，有多个sqlSession或者分布式的环境中数据库操作，可能会出现脏数据。当Session flush或close之后，该Session中的所有Cache就将清空，默认一级缓存是开启的。|
|<div style="width: 100px">二级缓存</div>|也是基于PerpetualCache的HashMap本地缓存，不同在于其存储作用域为Mapper级别的，如果多个sqlSession之间需要共享缓存，则需要使用到二级缓存，并且二级缓存可自定义存储源，如Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态)。|


开启二级缓存数据查询流程: 二级缓存 -> 一级缓存 -> 数据库。


缓存更新机制: 当某一个作用域(一级缓存Session/二级缓存Mapper)进行了C/U/D操作后，默认该作用域下所有select中的缓存将被clear。


#### Mybatis和Hibernate的区别有哪些？
// todo


#### Mybatis有哪些执行器(Executor)？
|执行器|解释|
| :----- | :----- |
|<div style="width: 120px">SimpleExecutor</div>|每执行一次update或select就开启一个Statement对象，用完立刻关闭Statement对象。|
|<div style="width: 120px">ReuseExecutor</div>|执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后不关闭Statement对象，而是放置于Map内供下一次使用。简言之，就是重复使用Statement对象。|
|<div style="width: 120px">BatchExecutor</div>|执行update(没有select，jdbc批处理不支持select)，将所有sql都添加到批处理中(addBatch())，等待统一执行(executeBatch())，它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理，与jdbc批处理相同。|


#### Mybatis分页插件的实现原理是什么？
分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。


#### Mybatis如何编写一个自定义插件？
**自定义插件实现原理**


Mybatis自定义插件针对Mybatis四大对象(Executor、StatementHandler、ParameterHandler、ResultSetHandler)进行拦截:


|Mybatis对象|解释|
| :----- | :----- |
|<div style="width: 140px">Executor</div>|拦截内部执行器，它负责调用StatementHandler操作数据库，并把结果集通过ResultSetHandler进行自动映射，另外它还处理了二级缓存的操作。|
|<div style="width: 140px">StatementHandler</div>|拦截sql语法构建的处理，它是Mybatis直接和数据库执行sql脚本的对象，另外它也实现了Mybatis的一级缓存。|
|<div style="width: 140px">ParameterHandler</div>|拦截参数的处理。|
|<div style="width: 140px">ResultSetHandler</div>|拦截结果集的处理。|


**自定义插件实现关键**


Mybatis插件要实现Interceptor接口，接口包含的方法，如下: 


```java
public interface Interceptor {   
   Object intercept(Invocation invocation) throws Throwable;       
   Object plugin(Object target);    
   void setProperties(Properties properties);
}
```


|接口方法|解释|
| :----- | :----- |
|<div style="width: 120px">setProperties()</div>|在Mybatis进行配置插件的时候可以配置自定义相关属性，即接口实现对象的参数配置。|
|<div style="width: 120px">plugin()</div>|插件用于封装目标对象的，通过该方法我们可以返回目标对象本身，也可以返回一个它的代理，可以决定是否要进行拦截进而决定要返回一个什么样的目标对象，官方提供了示例`return Plugin.wrap(target, this)`。|
|<div style="width: 120px">intercept()</div>|要进行拦截的时候要执行的方法。|


**自定义插件实现示例**


```java
@Intercepts({@Signature(type = Executor.class, method = "query",
        args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class})})
public class TestInterceptor implements Interceptor {
    public Object intercept(Invocation invocation) throws Throwable {
        Object target = invocation.getTarget(); //被代理对象
        Method method = invocation.getMethod(); //代理方法
        Object[] args = invocation.getArgs(); //方法参数
        // do something ...... 方法拦截前执行代码块
        Object result = invocation.proceed();
        // do something .......方法拦截后执行代码块
        return result;
    }
    public Object plugin(Object target) {
        return Plugin.wrap(target, this);
    }
}
```


### RabbitMQ


#### RabbitMQ的使用场景有哪些？
// todo


#### RabbitMQ有哪些重要的角色？
// todo


#### RabbitMQ有哪些重要的组件？
// todo


#### RabbitMQ中vhost的作用是什么？
// todo


#### RabbitMQ的消息是怎么发送的？
// todo


#### RabbitMQ怎么保证消息的稳定性？
// todo


#### RabbitMQ怎么避免消息丢失？
// todo


#### 要保证消息持久化成功的条件有哪些？
// todo


#### RabbitMQ持久化有什么缺点？
// todo


#### RabbitMQ有几种广播类型？
// todo


#### RabbitMQ怎么实现延迟消息队列？
// todo


#### RabbitMQ集群有什么用？
// todo


#### RabbitMQ节点的类型有哪些？
// todo


#### RabbitMQ集群搭建需要注意哪些问题？
// todo


#### RabbitMQ每个节点是其他节点的完整拷贝吗？为什么？
// todo


#### RabbitMQ集群中唯一一个磁盘节点崩溃了会发生什么情况？
// todo


#### RabbitMQ对集群节点停止顺序有要求吗？
// todo


### Kafka


#### Kafka可以脱离zookeeper单独使用吗？为什么？
kafka不能脱离zookeeper单独使用，因为kafka使用zookeeper管理和协调kafka的节点服务器。


#### Kafka有几种数据保留的策略？
1. 过期时间保留
2. 按照存储的消息大小


#### Kafka同时设置了7天和10G清除数据，到第五天的时候消息达到了10G，这个时候Kafka将如何处理？
Kafka会执行数据清除工作，时间和大小不论那个满足条件，都会清空数据。


#### 什么情况会导致Kafka运行变慢？
1. CPU性能瓶颈
2. 磁盘读写瓶颈
3. 网络瓶颈


#### 使用Kafka集群需要注意什么？
1. 集群的数量不是越多越好，最好不要超过7个，因为节点越多，消息复制需要的时间就越长，整个群组的吞吐量就越低。
2. 集群数量最好是单数，因为超过一半故障集群就不能用了，设置为单数容错率更高。


### Zookeeper


#### Zookeeper是什么？
Zookeeper是一个分布式的，开放源码的分布式应用程序协调服务，是google chubby的开源实现，是hadoop和hbase的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括配置维护、域名服务、分布式同步、组服务等。


#### Zookeeper都有哪些功能？
|功能|解释|
| :----- | :----- |
|<div style='width: 100px'>集群管理</div>|监控节点存活状态、运行请求等。|
|<div style='width: 100px'>主节点选举</div>|主节点挂掉了之后可以从备用的节点开始新一轮选主，主节点选举说的就是这个选举的过程，使用zookeeper可以协助完成这个过程。|
|<div style='width: 100px'>分布式锁</div>|zookeeper提供两种锁独占锁、共享锁。独占锁即一次只能有一个线程使用资源，共享锁是读锁共享、读写互斥，即可以有多线程同时读同一个资源，如果要使用写锁也只能有一个线程使用。zookeeper可以对分布式锁进行控制。|
|<div style='width: 100px'>命名服务</div>|在分布式系统中，通过使用命名服务，客户端应用能够根据指定名字来获取资源或服务的地址、提供者等信息。|


#### Zookeeper有几种部署模式？
|部署模式|解释|
| :----- | :----- |
|单机部署|一台集群上运行。|
|集群部署|多台集群运行。|
|伪集群部署|一台集群启动多个zookeeper实例运行。|


#### Zookeeper怎么保证主从节点的状态同步？
zookeeper的核心是原子广播，这个机制保证了各个server之间的同步。实现这个机制的协议叫做zab协议。 zab协议有两种模式，分别是恢复模式(选主)和广播模式(同步)。当服务启动或者在领导者崩溃后，zab就进入了恢复模式，当领导者被选举出来，且大多数server完成了和leader的状态同步以后，恢复模式就结束了。状态同步保证了leader和server具有相同的系统状态。


#### 集群中为什么要有主节点？
在分布式环境中，有些业务逻辑只需要集群中的某一台机器进行执行，其他的机器可以共享这个结果，这样可以大大减少重复计算，提高性能，所以就需要主节点。


#### 集群中有3台服务器，其中一个节点宕机，这个时候Zookeeper还可以使用吗？
可以继续使用，单数服务器只要没超过一半的服务器宕机就可以继续使用。


#### 说一下Zookeeper的通知机制？
客户端端会对某个znode建立一个watcher事件，当该znode发生变化时，这些客户端会收到zookeeper的通知，然后客户端可以根据znode变化来做出业务上的改变。


### Mysql


#### 数据库的三范式是什么？
|范式|解释|
| :----- | :----- |
|<div style='width: 100px'>第一范式</div>|强调的是列的原子性，即数据库表的每一列都是不可分割的原子数据项。|
|<div style='width: 100px'>第二范式</div>|要求实体的属性完全依赖于主关键字。所谓完全依赖是指不能存在仅依赖主关键字一部分的属性。|
|<div style='width: 100px'>第三范式</div>|任何非主属性不依赖于其它非主属性。|


#### 一张自增表里面总共有7条数据，删除了最后2条数据，重启Mysql数据库，又插入了一条数据，此时ID是多少？
表类型如果是MyISAM，那ID就是8。表类型如果是InnoDB，那ID就是6。InnoDB表只会把自增主键的最大ID记录在内存中，所以重启之后会导致最大ID丢失。


#### 如何获取当前数据库版本？
```
select version();
```


#### 说一下ACID是什么？
|ACID|解释|
| :----- | :----- |
|<div style='width: 150px'>Atomicity(原子性)</div>|一个事务(transaction)中的所有操作，或者全部完成、或者全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被恢复(Rollback)到事务开始前的状态，就像这个事务从来没有执行过一样。即事务不可分割、不可约简。|
|<div style='width: 150px'>Consistency(一致性)</div>|在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设约束、触发器、级联回滚等。|
|<div style='width: 150px'>Isolation(隔离性)</div>|数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交(Read Uncommitted)、读提交(Read Committed)、可重复读(Repeatable Read)和串行化(Serializable)。|
|<div style='width: 150px'>Durability(持久性)</div>|事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。|


#### char和varchar的区别是什么？
|名称|解释|优点|缺点|适用场景|
| :----- | :----- | :----- | :----- | :----- |
|char(n)|固定长度类型，比如订阅char(10)，当你输入"abc"三个字符的时候，它们占的空间还是10个字节，其他7个是空字节。|效率高。|占用空间。|存储密码的md5值，固定长度的，使用char非常合适。|
|varchar(n)|可变长度，存储的值是每个值占用的字节再加上一个用来记录其长度的字节的长度。|--|--|--|


#### float和double的区别是什么？
|名称|解释|
| :----- | :----- |
|float|最多可以存储8位的十进制数，并在内存中占4字节。|
|double|最可可以存储16位的十进制数，并在内存中占8字节。|


#### Mysql的内连接、左连接、右连接有什么区别？
|名称|解释|
| :----- | :----- |
|内连接(inner join)|把匹配的关联数据显示出来。|
|左连接(left join)|左边的表全部显示出来，右边的表显示出符合条件的数据。|
|右连接(right join)|右边的表全部显示出来，左边的表显示出符合条件的数据。|


#### Mysql索引是怎么实现的？
索引是满足某种特定查找算法的数据结构，而这些数据结构会以某种方式指向数据，从而实现高效查找数据。


具体来说Mysql中的索引，不同的数据引擎实现有所不同，但目前主流的数据库引擎的索引都是B+树实现的，B+树的搜索效率，可以到达二分法的性能，找到数据区域之后就找到了完整的数据结构了，所有索引的性能也是更好的。


#### 怎么验证Mysql的索引是否满足需求？
使用explain查看sql是如何执行查询语句的，从而分析你的索引是否满足需求。


explain语法:


```
explain select * from ${table_name} where ${column_name} = 'xxx';
```


#### 说一下数据库的事务隔离？
Mysql的事务隔离是在mysql.ini配置文件里添加的，在文件的最后添加:


```
transaction-isolation = REPEATABLE-READ
```


可用的配置值: READ-UNCOMMITTED、READ-COMMITTED、REPEATABLE-READ、SERIALIZABLE。


|事务隔离级别|解释|
| :----- | :----- |
|<div style='width: 200px'>READ-UNCOMMITTED</div>|未提交读，最低隔离级别、事务未提交前，就可被其他事务读取(会出现幻读、脏读、不可重复读)。|
|<div style='width: 200px'>READ-COMMITTED</div>|提交读，一个事务提交后才能被其他事务读取到(会造成幻读、不可重复读)。|
|<div style='width: 200px'>REPEATABLE-READ</div>|可重复读，默认级别，保证多次读取同一个数据时，其值都和事务开始时候的内容是一致，禁止读取到别的事务未提交的数据(会造成幻读)。|
|<div style='width: 200px'>SERIALIZABLE</div>|序列化，代价最高、最可靠的隔离级别，该隔离级别能防止脏读、不可重复读、幻读。|


|事务隔离异常|解释|
| :----- | :----- |
|<div style='width: 100px'>脏读</div>|表示一个事务能够读取另一个事务中还未提交的数据。比如某个事务尝试插入记录A，此时该事务还未提交，然后另一个事务尝试读取到了记录A。|
|<div style='width: 100px'>不可重复读</div>|是指在一个事务内，多次读同一数据。|
|<div style='width: 100px'>幻读</div>|指同一个事务内多次查询返回的结果集不一样。比如同一个事务A第一次查询时候有n条记录，但是第二次同等条件下查询却有n+1条记录，这就好像产生了幻觉。发生幻读的原因也是另外一个事务新增或者删除或者修改了第一个事务结果集里面的数据，同一个记录的数据内容被修改了，所有数据行的记录就变多或者变少了。|


#### 说一下Mysql常用的引擎？
|引擎|解释|
| :----- | :----- |
|<div style='width: 100px'>InnoDB引擎</div>|Mysql 5.1之后默认引擎，提供了对数据库ACID事务的支持，并且还提供了行级锁和外键的约束，它的设计的目标就是处理大数据容量的数据库系统。Mysql运行的时候，InnoDB会在内存中建立缓冲池，用于缓冲数据和索引。但是该引擎是不支持全文搜索，同时启动也比较的慢，它是不会保存表的行数的，所以当进行`select count(*) from table`指令的时候，需要进行扫描全表。由于锁的粒度小，写操作是不会锁定全表的,所以在并发度较高的场景下使用会提升效率的。|
|<div style='width: 100px'>MyISAM引擎</div>|不提供事务的支持，也不支持行级锁和外键。因此当执行插入和更新语句时，即执行写操作的时候需要锁定这个表，所以会导致效率会降低。不过和InnoDB不同的是，MyISAM引擎是保存了表的行数，于是当进行`select count(*) from table`语句时，可以直接的读取已经保存的值而不需要进行扫描全表。所以，如果表的读操作远远多于写操作时，并且不需要事务的支持的，可以将MyISAM作为数据库引擎的首选。|


#### 说一下Mysql的行锁和表锁？
MyISAM只支持表锁，InnoDB支持表锁和行锁，默认为行锁。


|锁级别|解释|
| :----- | :----- |
|行锁|开销大、加锁慢、会出现死锁。锁力度小，发生锁冲突的概率小，并发度最高。|
|表锁|开销小、加锁快，不会出现死锁。锁定粒度大，发生锁冲突的概率最高，并发量最低。|


#### 说一下乐观锁和悲观锁？
|锁类别|解释|
| :----- | :----- |
|<div style='width: 100px'>乐观锁</div>|每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在提交更新的时候会判断一下在此期间别人有没有去更新这个数据。|
|<div style='width: 100px'>悲观锁</div>|每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻止，直到这个锁被释放。|


数据库的乐观锁需要自己实现，在表里面添加一个version字段，每次修改成功值加1，这样每次修改的时候先对比一下，自己拥有的version和数据库现在的version是否一致，如果不一致就不修改，这样就实现了乐观锁。


#### Mysql问题排查都有哪些手段？
1. 使用`show processlist`命令查看当前所有连接信息。
2. 使用explain命令查询sql语句执行计划。
3. 开启慢查询日志，查看慢查询的sql。


#### 如何做Mysql的性能优化？
1. 为搜索字段创建索引。
2. 避免使用select *，列出需要查询的字段。
3. 垂直分割分表。
4. 选择正确的存储引擎。


### Redis


#### Redis是什么？都有哪些使用场景？
Redis是一个使用C语言开发的高速缓存数据库。


1. 记录帖子点赞数、点击数、评论数；
2. 缓存近期热帖；
3. 缓存文章详情信息；
4. 记录用户会话信息。


#### Redis有哪些功能？
1. 数据缓存功能；
2. 分布式锁的功能；
3. 支持数据持久化；
4. 支持事务；
5. 支持消息队列。


#### Redis和Memcache有什么区别？
|区别|解释|
| :----- | :----- |
|<div style="width: 130px">存储方式不同</div>|Memcache把数据全部存在内存之中，断电后会挂掉，数据不能超过内存大小；Redis有部份存在硬盘上，这样能保证数据的持久性。|
|<div style="width: 130px">数据支持类型</div>|Memcache对数据类型支持相对简单；Redis有复杂的数据类型。|
|<div style="width: 130px">使用底层模型不同</div>|它们之间底层实现方式，以及与客户端之间通信的应用协议不一样，Redis自己构建了vm机制，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求。|
|<div style="width: 130px">value值大小不同</div>|Redis最大可以达到512MB；Memcache只有1MB。|


#### Redis为什么是单线程的？
因为CPU不是Redis的瓶颈，Redis的瓶颈最有可能是机器内存或者网络带宽。既然单线程容易实现，而且CPU又不会成为瓶颈，那就顺理成章地采用单线程的方案了。


关于Redis的性能，官方网站也有，普通笔记本轻松处理每秒几十万的请求。而且单线程并不代表就慢Nginx和Nodejs也都是高性能单线程的代表。


#### 什么是缓存穿透？怎么解决？
指查询一个一定不存在的数据，由于缓存是不命中时需要从数据库查询，查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到数据库去查询，造成缓存穿透。


最简单粗暴的方法如果一个查询返回的数据为空(不管是数据不存在，还是系统故障)，我们就把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。


#### Redis支持的数据类型有哪些？
1. string(字符串)；
2. list(列表)；
3. hash(字典)；
4. set(集合)；
5. zset(有序集合)。


#### Redis支持的Java客户端都有哪些？
1. redisson；
2. jedis；
3. lettuce。


#### jedis和redisson有哪些区别？
|名称|区别|
| :----- | :----- |
|jedis|提供了比较全面的Redis命令的支持。|
|redisson|实现了分布式和可扩展的Java数据结构，与jedis相比redisson的功能相对简单，不支持排序、事务、管道、分区等Redis特性。|


#### 怎么保证缓存和数据库数据的一致性？
1. 合理设置缓存的过期时间。
2. 新增、更改、删除数据库操作时同步更新Redis，可以使用事物机制来保证数据的一致性。


#### Redis持久化有几种方式？
|方式|解释|
| :----- | :----- |
|RDB(Redis Database)|指定的时间间隔能对你的数据进行快照存储。|
|AOF(Append Only File)|每一个收到的写命令都通过write函数追加到文件中。|


#### Redis怎么实现分布式锁？
Redis分布式锁其实就是在系统里面占一个“坑”，其他程序也要占“坑”的时候，占用成功了就可以继续执行，失败了就只能放弃或稍后重试。


占坑一般使用`setnx(set if not exists)`指令，只允许被一个程序占有，使用完调用`del`释放锁。


#### Redis分布式锁有什么缺陷？
Redis分布式锁不能解决超时的问题，分布式锁有一个超时时间，程序的执行如果超出了锁的超时时间就会出现问题。


#### Redis如何做内存优化？
尽量使用Redis的散列表，把相关的信息放到散列表里面存储，而不是把每个字段单独存储，这样可以有效的减少内存使用。比如将web系统的用户对象，应该放到散列表里面再整体存储到Redis，而不是把用户的姓名、年龄、密码、邮箱等字段分别设置key进行存储。


#### Redis淘汰策略有哪些？
|淘汰策略|解释|
| :----- | :----- |
|volatile-lru|从已设置过期时间的数据集(server.db[i].expires)中挑选最近最少使用的数据淘汰。|
|volatile-ttl|从已设置过期时间的数据集(server.db[i].expires)中挑选将要过期的数据淘汰。|
|volatile-random|从已设置过期时间的数据集(server.db[i].expires)中任意选择数据淘汰。|
|allkeys-lru|从数据集(server.db[i].dict)中挑选最近最少使用的数据淘汰。|
|allkeys-random|从数据集(server.db[i].dict)中任意选择数据淘汰。|
|no-enviction|禁止驱逐数据。|


#### Redis常见的性能问题有哪些？该如何解决？
1. 主服务器写内存快照，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务，所以主服务器最好不要写内存快照。
2. Redis主从复制的性能问题，为了主从复制的速度和连接的稳定性，主从库最好在同一个局域网内。


### JVM


#### 说一下JVM的主要组成部分？及其作用？
1. 类加载器(ClassLoader)
2. 运行时数据区(Runtime Data Area)
3. 执行引擎(Execution Engine)
4. 本地库接口(Native Interface)


首先通过类加载器(ClassLoader)会把Java代码转换成字节码，运行时数据区(Runtime Data Area)再把字节码加载到内存中，而字节码文件只是JVM的一套指令集规范，并不能直接交给底层操作系统去执行，因此需要特定的命令解析器执行引擎(Execution Engine)，将字节码翻译成底层系统指令，再交由CPU去执行，而这个过程中需要调用其他语言的本地库接口(Native Interface)来实现整个程序的功能。


#### 说一下JVM运行时数据区？
![](/images/ReviewVI/JVM.png)


|运行时数据区|解释|
| :----- | :----- |
|程序计数器(Program Counter Register)|当前线程所执行的字节码的行号指示器，字节码解析器的工作是通过改变这个计数器的值，来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能，都需要依赖这个计数器来完成。|
|Java虚拟机栈(Java Virtual Machine Stacks)|用于存储局部变量表、操作数栈、动态链接、方法出口等信息。|
|本地方法栈(Native Method Stack)|与虚拟机栈的作用是一样的，只不过虚拟机栈是服务Java方法的，而本地方法栈是为虚拟机调用Native方法服务的。|
|Java堆(Java Heap)|Java虚拟机中内存最大的一块，是被所有线程共享的，几乎所有的对象实例都在这里分配内存。|
|方法区(Method Area)|用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据。|


#### 说一下堆栈的区别？
|区别|解释|
| :----- | :----- |
|功能方面|堆是用来存放对象的，栈是用来执行程序的。|
|共享性|堆是线程共享的，栈是线程私有的。|
|空间大小|堆大小远远大于栈。|


#### 队列和栈是什么？有什么区别？
1. 队列和栈都是被用来预存储数据的。
2. 队列允许先进先出检索元素，但也有例外的情况，Deque接口允许从两端检索元素。
3. 栈和队列很相似，但它运行对元素进行后进先出进行检索。


#### 什么是双亲委派模型？
在介绍双亲委派模型之前先说下类加载器。对于任意一个类，都需要由加载它的类加载器和这个类本身一同确立在JVM中的唯一性，每一个类加载器，都有一个独立的类名称空间。类加载器就是根据指定全限定名称将Class文件加载到JVM内存，然后再转化为Class对象。


|类加载器|解释|
| :----- | :----- |
|启动类加载器(Bootstrap ClassLoader)|是虚拟机自身的一部分，用来加载JAVA_HOME/lib/目录中的，或者被-Xbootclasspath参数所指定的路径中并且被虚拟机识别的类库。|
|扩展类加载器(Extension ClassLoader)|负责加载<JAVA_HOME>\lib\ext目录或java.ext.dirs系统变量指定的路径中的所有类库。|
|应用程序类加载器(Application ClassLoader)|负责加载用户类路径(classpath)上的指定类库，我们可以直接使用这个类加载器。一般情况，如果我们没有自定义类加载器默认就是用这个加载器。|


如果一个类加载器收到了类加载的请求，它首先不会自己去加载这个类，而是把这个请求委派给父类加载器去完成，每一层的类加载器都是如此，这样所有的加载请求都会被传送到顶层的启动类加载器中，只有当父加载无法完成加载请求(它的搜索范围中没找到所需的类)时，子加载器才会尝试去加载类。


#### 说一下类装载的执行过程？
|执行过程|解释|
| :----- | :----- |
|<div style='width: 80px'>加载</div>|根据查找路径找到相应的class文件然后导入。|
|<div style='width: 80px'>检查</div>|检查加载的class文件的正确性。|
|<div style='width: 80px'>准备</div>|给类中的静态变量分配内存空间。|
|<div style='width: 80px'>解析</div>|虚拟机将常量池中的符号引用替换成直接引用的过程。符号引用就理解为一个标识，而在直接引用直接指向内存中的地址。|
|<div style='width: 80px'>初始化</div>|对静态变量和静态代码块执行初始化工作。|


#### 怎么判断对象是否可以被回收？
|回收方法|解释|
| :----- | :----- |
|<div style='width: 100px'>引用计数器</div>|为每个对象创建一个引用计数，有对象引用时计数器+1，引用被释放时计数-1，当计数器为0时就可以被回收。它有一个缺点不能解决循环引用的问题。|
|<div style='width: 100px'>可达性分析</div>|从GC Roots开始向下搜索，搜索所走过的路径称为引用链。当一个对象到GC Roots没有任何引用链相连时，则证明此对象是可以被回收的。|


#### Java中都有哪些引用类型？
|引用类型|解释|
| :----- | :----- |
|<div style='width: 100px'>强引用</div>|发生GC的时候不会被回收。|
|<div style='width: 100px'>软引用</div>|有用但不是必须的对象，在发生内存溢出之前会被回收。|
|<div style='width: 100px'>弱引用</div>|有用但不是必须的对象，在下一次GC时会被回收。|
|<div style='width: 100px'>虚引用</div>|无法通过虚引用获得对象，用PhantomReference实现虚引用，虚引用的用途是在GC时返回一个通知。|


#### 说一下JVM有哪些垃圾回收算法？
|垃圾回收算法|解释|
| :----- | :----- |
|<div style='width: 150px'>标记-清除算法</div>|标记无用对象，然后进行清除回收。缺点效率不高，无法清除垃圾碎片。|
|<div style='width: 150px'>标记-整理算法</div>|标记无用对象，让所有存活的对象都向一端移动，然后直接清除掉端边界以外的内存。|
|<div style='width: 150px'>复制算法</div>|按照容量划分二个大小相等的内存区域，当一块用完的时候将活着的对象复制到另一块上，然后再把已使用的内存空间一次清理掉。缺点内存使用率不高，只有原来的一半。|
|<div style='width: 150px'>分代算法</div>|根据对象存活周期的不同将内存划分为几块，一般是新生代和老年代，新生代基本采用复制算法，老年代采用标记整理算法。|


#### 说一下JVM有哪些垃圾回收器？
|垃圾回收器|解释|
| :----- | :----- |
|Serial|最早的单线程串行垃圾回收器。|
|Serial Old|Serial垃圾回收器的老年版本，同样也是单线程的，可以作为CMS垃圾回收器的备选预案。|
|ParNew|是Serial的多线程版本。|
|Parallel|和ParNew收集器类似是多线程的，但Parallel是吞吐量优先的收集器，可以牺牲等待时间换取系统的吞吐量。|
|Parallel Old|是Parallel老生代版本，Parallel使用的是复制的内存回收算法，Parallel Old使用的是标记-整理的内存回收算法。|
|CMS|一种以获得最短停顿时间为目标的收集器，非常适用B/S系统。|
|G1|一种兼顾吞吐量和停顿时间的GC实现，是JDK 9以后的默认GC选项。|


#### 详细介绍一下CMS垃圾回收器？
CMS是英文Concurrent Mark-Sweep的简称，是以牺牲吞吐量为代价来获得最短回收停顿时间的垃圾回收器。对于要求服务器响应速度的应用上，这种垃圾回收器非常适合。在启动JVM的参数加上`-XX:+UseConcMarkSweepGC`来指定使用CMS垃圾回收器。


CMS使用的是标记-清除的算法实现的，所以在GC的时候回产生大量的内存碎片，当剩余内存不能满足程序运行要求时，系统将会出现Concurrent Mode Failure，临时CMS会采用Serial Old回收器进行垃圾清除，此时的性能将会被降低。


#### 新生代垃圾回收器和老生代垃圾回收器都有哪些？有什么区别？
|垃圾回收器|--| 解释| 
| :----- | :----- | :----- |
|<div style='width: 150px'>新生代回收器</div>|Serial、ParNew、Parallel Scavenge|新生代垃圾回收器一般采用的是复制算法，复制算法的优点是效率高，缺点是内存利用率低。|
|<div style='width: 150px'>老年代回收器</div>|Serial Old、Parallel Old、CMS|老年代回收器一般采用的是标记-整理的算法进行垃圾回收。|
|<div style='width: 150px'>整堆回收器</div>|G1|--|


#### 简述分代垃圾回收器是怎么工作的？
分代回收器有两个分区老生代和新生代，新生代默认的空间占比总空间的1/3，老生代的默认占比是2/3。


新生代使用的是复制算法，新生代里有3个分区Eden、To Survivor、From Survivor，它们的默认占比是8:1:1，它的执行流程如下:


1. 把Eden + From Survivor存活的对象放入To Survivor区。
2. 清空Eden和From Survivor分区。
3. From Survivor和To Survivor分区交换，From Survivor变To Survivor，To Survivor变From Survivor。


每次在From Survivor到To Survivor移动时都存活的对象，年龄就+1，当年龄到达15(默认配置是15)时，升级为老生代。大对象也会直接进入老生代。老生代当空间占用到达某个值之后就会触发全局垃圾收回，一般使用标记整理的执行算法。以上这些循环往复就构成了整个分代垃圾回收的整体执行流程。


#### 说一下JVM调优的工具？
JDK自带了很多监控工具，都位于jdk的bin目录下，其中最常用的是`jconsole`和`jvisualvm`这两款视图监控工具。


|调优工具|解释|
| :----- | :----- |
|jconsole|用于对JVM中的内存、线程和类等进行监控。|
|jvisualvm|JDK自带的全能分析工具，可以分析内存快照、线程快照、程序死锁、监控内存的变化、GC变化等。|


#### 常用的JVM调优的参数都有哪些？
|调优参数|解释|
| :----- | :----- |
|<div style='width: 150px'>Xms2g</div>|初始化推大小为2g。| 
|<div style='width: 150px'>Xmx2g</div>|堆最大内存为2g。| 
|<div style='width: 150px'>XX:NewRatio=4</div>|设置年轻的和老年代的内存比例为1:4。| 
|<div style='width: 150px'>XX:SurvivorRatio=8</div>|设置新生代Eden和Survivor比例为8:2。| 
|<div style='width: 150px'>XX:+UseParNewGC</div>|指定使用ParNew+Serial Old垃圾回收器组合。| 
|<div style='width: 150px'>XX:+UseParallelOldGC</div>|指定使用ParNew+ParNew Old垃圾回收器组合。| 
|<div style='width: 150px'>XX:+UseConcMarkSweepGC</div>|指定使用CMS+Serial Old垃圾回收器组合。| 
|<div style='width: 150px'>XX:+PrintGC</div>|开启打印GC信息。| 
|<div style='width: 150px'>XX:+PrintGCDetails</div>|打印GC详细信息。| 
