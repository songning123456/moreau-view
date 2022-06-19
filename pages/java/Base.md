### 基础


#### Java的四个基本特性？
| 特性 | 解释 | 
| :----- | :----- | 
| <div style="width: 80px">抽象</div> | 就是把现实生活中的某一类东西提取出来，用程序代码表示，我们通常叫做类或者接口。抽象包括两个方面：一个是数据抽象，一个是过程抽象。数据抽象也就是对象的属性，过程抽象是对象的行为特征。 | 
| <div style="width: 80px">封装</div> | 把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行封装隐藏，封装分为属性的封装和方法的封装。 | 
| <div style="width: 80px">继承</div> | 是对有着共同特性的多类事物，进行再抽象成一个类。这个类就是多类事物的父类，父类的意义在于抽取多类事物的共性。 | 
| <div style="width: 80px">多态</div> | 允许不同类的对象对同一消息做出响应。方法的重载、类的覆盖正体现了多态。 | 


#### 面向对象和面向过程的区别？
| 类别 | 优点 | 缺点 | 
| :----- | :----- | :----- | 
| <div style="width: 80px">面向过程</div> | 性能比面向对象高，因为类调用时需要实例化，开销比较大，比较消耗资源。比如单片机、嵌入式开发、Linux/Unix等一般采用面向过程开发，性能是最重要的因素。 | 没有面向对象易维护、易复用、易扩展。 | 
| <div style="width: 80px">面向对象</div> | 易维护、易复用、易扩展。由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统更加灵活、更加易于维护。 | 性能比面向过程低。 |


#### Java创建对象的有几种方式？
**使用new关键字**


这是我们最常见的也是最简单的创建对象的方式，通过这种方式我们还可以调用任意的构造函数(无参的和有参的)。
```java
Student student = new Student();
```


**使用Class类的newInstance方法**


我们也可以使用Class类的newInstance方法创建对象，这个newInstance方法调用无参的构造器创建对象。
```java
Student student = (Student)Class.forName("xxx.xxx.Student").newInstance();
或者
Student student = Student.class.newInstance();
```


**使用Constructor类的newInstance方法**


本方法和Class类的newInstance方法很像，java.lang.reflect.Constructor类里也有一个newInstance方法可以创建对象。我们可以通过这个newInstance方法调用有参数的和私有的构造函数。
```java
Constructor<Student> constructor = Student.class.getInstance();
Student student = constructor.newInstance();
```


**使用Clone的方法**


无论何时我们调用一个对象的clone方法，JVM就会创建一个新的对象，将前面的对象的内容全部拷贝进去，用clone方法创建对象并不会调用任何构造函数。要使用clone方法，我们必须先实现Cloneable接口并实现其定义的clone方法。这也是原型模式的应用。
```java
Student student2 = <Student>student.clone();
```


**使用反序列化**


当我们序列化和反序列化一个对象，JVM会给我们创建一个单独的对象。在反序列化时，JVM创建对象并不会调用任何构造函数。为了反序列化一个对象，我们需要让我们的类实现Serializable接口。
```java
ObjectInputStream in = new ObjectInputStream(new FileInputStream("data.obj"));
Student student = (Student)in.readObject();
```


#### 变量命名规范？
1. 除第一个单词外，其他单词首字母大写。
2. 变量名不应该以下划线或美元符号开头。
3. 临时变量通常被取名为I、j、k、m和n，他们一般用于整型；c、d、e他们一般用于字符型。
4. 对不易清楚识别出该变量类型的变量应使用类型名或类型名的缩写作其后缀。


#### JDK常有哪些用的包？
| 包名 | 解释 | 
| :----- | :----- | 
| java.lang | 这个是系统的基础类，比如String、Math、Integer、System和Thread，提供常用功能。 | 
| java.io | 这里面是所有输入输出有关的类，比如文件操作等。 | 
| java.net | 这里面是与网络有关的类，比如URL、URLConnection等。 | 
| java.util | 这个是系统辅助类，特别是集合类Collection、List、Map等。 | 
| java.sql | 这个是数据库操作的类，Connection、Statememt、ResultSet等。 | 


#### Object中定义了哪些方法？
| 方法 | 解释 | 
| :----- | :----- | 
| <div style="width: 300px">public Boolean equals (Object obj)</div> | 比较当前对象与obj是否为同一对象。 | 
| <div style="width: 300px">public String toString()</div> | 返回当前对象的字符串表达形式。 | 
| <div style="width: 300px">public native int hashCode()</div> | 返回对象的Hash码。Hash码的标志对象的唯一值，Hash码相同的对象是同一对象。 | 
| <div style="width: 300px">protected void finalize() throws Throwable</div> | 对象销毁时被调用。 | 
| <div style="width: 300px">public final native void notify()</div> | 唤醒在此对象监视器上等待的单个线程。如果所有线程都在此对象上等待，则会选择唤醒其中一个线程。选择是任意性的，并在对实现做出决定时发生。线程通过调用其中一个wait方法，在对象的监视器上等待。 | 
| <div style="width: 300px">public final native void notifyAll()</div> | 唤醒在此对象监视器上等待的所有线程。线程通过调用其中一个wait方法，在对象的监视器上等待。 | 
| <div style="width: 300px">public final native void wait()</div> | 导致调用者线程等待，直到另一个线程调用notify()或者notifyAll()方法，或者指定的等待时间到达。 | 


#### ==和equals区别？以及hashCode和equals方法的关系？
| == | equals | 
| :----- | :----- | 
|1. 判断两个变量或实例是不是指向同一个内存空间；<br>2. 在JAVA中只是一个运算符合；<br>3. 运行速度快。|1. 判断两个变量或实例所指向的内存空间的值是不是相同；<br>2. 在JAVA中是一个方法；<br>3. 运行速度慢。|


equals相等，hashcode必相等；hashcode相等，equals可能不相等。


#### static关键字是什么意思？Java中是否可以覆盖(override)一个private或者是static的方法？是否可以在static环境中访问非static变量？
static关键字表明一个成员变量或者是成员方法可以在没有所属的类的实例变量的情况下被访问。


Java中static方法不能被覆盖，因为方法覆盖是基于运行时动态绑定的，而static方法是编译时静态绑定的。static方法跟类的任何实例都不相关，所以概念上不适用。


static变量在Java中是属于类的，它在所有的实例中的值是一样的。当类被Java虚拟机载入的时候，会对static变量进行初始化。如果你的代码尝试不用实例来访问非static的变量，编译器会报错，因为这些变量还没有被创建出来，还没有跟任何实例关联上。


#### Java支持的数据类型有哪些？什么是自动拆装箱？
| 数据类型 | 字节长度(byte) |
| :----: | :----: |
| byte | 1 |
| short | 2 |
| int | 4 |
| long | 8 |
| boolean | 1 |
| char | 2 |
| float | 4 |
| double | 8 |


自动装箱是Java编译器在基本数据类型和对应的对象包装类型之间做的一个转化。比如：把int转化成Integer，double转化成Double等等。反之就是自动拆箱。


#### String和StringBuffer、StringBuilder的区别？
| ———— | 可变性 | 线程安全性 | 性能 | 
| :----- | :----- | :----- | :----- | 
| String | 用字符数组保存字符串，private final char value[]，所以String对象是不可变的。 | 对象是不可变的，也就可以理解为常量，线程安全。 | 每次对String类型进行改变的时候，都会生成一个新的String对象，然后将指针指向新的String对象。 | 
| StringBuffer | 继承自AbstractStringBuilder类，在AbstractStringBuilder中也是使用字符数组保存字符串，char[] value，是可变的。 | 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。 | 本身进行操作，而不是生成新的对象并改变对象引用。 | 
| StringBuilder | 同StringBuffer。 | 并没有对方法进行加同步锁，所以是非线程安全的。 | StringBuilder相比使用StringBuffer仅能获得10%~15%左右的性能提升。 | 


#### Java中的方法重写(Override)和方法重载(Overload)是什么意思？
| 重写(Override) | 重载(Overload) | 
| :----- | :----- | 
| <div style='width: 270px'>1. 发生在父子类中；<br>2. 方法名、参数列表必须相同；<br>3. 返回值类型小于等于父类；<br>4. 抛出的异常小于等于父类；<br>5. 访问修饰符大于等于父类；<br>6. 如果父类方法访问修饰符为private，则子类中就不是重写。 </div>| 1. 发生在同一个类中；<br>2. 方法名必须相同；<br>3. 参数类型不同、个数不同、顺序不同；<br>4. 方法返回值和访问修饰符可以不同；<br>5. 发生在编译时。 | 


#### 访问控制符public、protected、private以及默认(default)的区别？
| 修饰符 | 当前类 | 同包 | 子类 | 其他包 | 
| :----- | :----- | :----- | :----- | :----- | 
| public | √ | √ | √ | √ | 
| protected | √ | √ | √ | × |  
| default | √ | √ | × | × | 
| private | √ | × | × | × |   


类的成员不写访问修饰时默认为default，默认对于同一个包中的其他类相当于公开(public)，对于不是同一个包中的其他类相当于私有(private)。受保护(protected)对子类相当于公开，对不是同一包中的没有父子关系的类相当于私有。Java中，外部类的修饰符只能是public或默认，类的成员(包括内部类)的修饰符可以是以上四种。


#### 接口和抽象类的区别是什么？
| 接口 | 抽象类 | 
| :----- | :----- | 
| 1. 不能有构造方法；<br>2. 可以有普通成员变量；<br>3. 所有的方法隐含的都是抽象的；<br>4. 声明的变量默认都是final的；<br>5. 类可以实现很多个接口；<br>6. 类如果要实现一个接口，它必须要实现接口声明的所有方法；<br>7. 成员函数默认是public的；<br>8. 绝对抽象的，不可以被实例化。 | 1. 可以有构造方法；<br>2. 可以同时包含抽象和非抽象的方法；<br>3. 可以包含非final的变量；<br>4. 类只能继承一个抽象类；<br>5. 类可以不实现抽象类声明的所有方法，当然，在这种情况下，类也必须得声明成是抽象的；<br>6. 成员函数可以是private、protected或者是public；<br>7. 不可以被实例化，但是如果它包含main方法的话是可以被调用的；<br>8. 可以在不提供接口方法实现的情况下实现接口。| 


#### Java中Exception和Error有什么区别？
![异常](/images/Base/Exception.jpeg)


| Exception | Error | 
| :----- | :----- | 
| 表示程序可以处理的异常，可以捕获且可能恢复。遇到这类异常，应该尽可能处理异常，使程序恢复运行，而不应该随意终止异常。 | Error类一般是指与虚拟机相关的问题，如系统崩溃、虚拟机错误、内存空间不足、方法调用栈溢等。对于这类错误的导致的应用程序中断，仅靠程序本身无法恢复和和预防，遇到这样的错误，建议让程序终止。 | 


#### Java中的两种异常类型是什么？他们有什么区别？
| 受检查的(checked)异常 | 不受检查的(unchecked)异常 | 
| :----- | :----- | 
| <div style='width: 400px'>1. 代表程序不能直接控制的无效外界情况(如用户输入、数据库问题、网络异常、文件丢失等)；<br>2. 除了Error和RuntimeException及其子类之外，如ClassNotFoundException、NamingException、ServletException、SQLException、IOException等；<br>3. 需要try catch处理或throws声明抛出异常。</div>  | 1. 指的是程序的瑕疵或逻辑错误，并且在运行时无法恢复；<br>2. 包括Error与RuntimeException及其子类，如OutOfMemoryError、UndeclaredThrowableException、IllegalArgumentException、IllegalMonitorStateException、NullPointerException、IllegalStateException、IndexOutOfBoundsException等；<br>3. 语法上不需要声明抛出异常。 | 


#### final关键字、finally代码块和finalize()方法有什么区别？
<table>
    <tr>
        <td>名词</td> 
        <td>作用域</td> 
        <td>解释</td> 
    </tr>
    <tr>
        <td rowspan="3">final</td> 
        <td style="width: 150px">类</td> 
        <td>一个类被final修饰，那么这个类就是最终类，不能派生出新的子类，不能作为父类被继承，该类中的所有方法都不能被重写，但是final类中的成员变量是可以改变的，要想final类中的成员变量的不可以改变，必须给成员变量添加final修饰。因此，一个类不能同时被final和abstract修饰，这两个关键字相互矛盾。</td>    
    </tr>
    <tr>
        <td style="width: 150px">方法</td> 
        <td>修饰方法，那么这个方法是最终方法，不允许任何子类重写该方法，但子类仍可以使用该方法，注意：final参数用来表示这个参数在这个函数内部不允许被修改。</td>       
    </tr>
    <tr>
        <td style="width: 150px">属性</td> 
        <td>被final修饰的变量不可变。这里的不可变有两重含义：引用不可变和对象不可变。final指的是引用不可变，即它只能指向初始化时指向的那个对象，而不关心指向对象内容的变化。因此，被final修饰的变量必须初始化，该变量其实就是常量。</td>      
    </tr>
    <tr>
        <td>finally</td> 
        <td style="width: 150px">try/catch语句块</td> 
        <td>finally代码块中的语句一定会被执行，经常被用来释放资源，如IO流和数据库资源的释放。</td>       
    </tr>
    <tr>
        <td>finalize</td> 
        <td style="width: 150px">Object类的一个方法 protected void finalize() throws Throwable{}</td> 
        <td>在垃圾回收器执行时会调用被回收对象的finalize()方法，可以覆盖此方法来实现对其资源的回收。注意：一旦垃圾回收器准备释放某个对象占用的空间，将首先调该对象的finalize()方法，并且在下一次垃圾回收动作发生时，才真正将该对象占用的内存回收。</td>      
    </tr>
</table>


#### 泛型是什么？有什么作用？
因为集合可以存储的对象类型是任意的，在取出进行向下转型时，容易发生ClassCastException。所以JDK1.5以后就有了解决这个问题的技术`泛型`。将运行时期的ClassCastException异常转移到编译时期通过编译失败体现，避免了强制转换的麻烦。


#### 自定义一个注解的过程？
1. 创建@Annotation类。
2. 指定该注解的作用目标。
```java
@Target(ElementType.TYPE)   // 接口 类 枚举 注解
@Target(ElementType.FIELD)  // 字段 枚举的常量
@Target(ElementType.METHOD) // 方法
@Target(ElementType.PARAMETER)  // 方法参数
@Target(ElementType.CONSTRUCTOR)    // 构造函数
@Target(ElementType.LOCAL_VARIABLE) // 局部变量
@Target(ElementType.ANNOTATION_TYPE)    // 注解
@Target(ElementType.PACKAGE)    // 包
```
3. 指定该注解的保留策略。
```java
@Retention(RetentionPolicy.SOURCE)  // 注解仅存在于源码中，在class字节码文件中不包含
@Retention(RetentionPolicy.CLASS)   // 默认的保存策略，注解会在class字节码文件中存在，但运行时无法获得
@Retention(RetentionPolicy.RUNTIME) // 注解会在class字节码文件中存在，在运行时可以通过反射获取到
```
4. 指定该注解是否可以被继承(@Inherited)。
