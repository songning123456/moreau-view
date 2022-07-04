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


### 多线程


### 反射


### 对象拷贝


### Java Web模块


### 异常模块


### 网络模块


### 设计模式


### Spring MVC


### Spring Boot


### Spring Cloud


### Hibernate


### Mybatis


### RabbitMQ


### Kafka


### Zookeeper


### Mysql


### Redis


### JVM
