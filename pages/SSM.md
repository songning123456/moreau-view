#### 使用Spring的好处?
1. 降低了组件之间的耦合性 ，实现了软件各层之间的解耦。
2. 可以使用容易提供的众多服务，如事务管理，消息服务等。
3. 容器提供单例模式支持。
4. 容器提供了AOP技术，利用它很容易实现如权限拦截，运行期监控等功能。
5. 容器提供了众多的辅助类，能加快应用的开发。
6. Spring对于主流的应用框架提供了集成支持，如hibernate、JPA、Struts等。
7. Spring属于低侵入式设计，代码的污染极低。
8. 独立于各种应用服务器。
9. Spring的DI机制降低了业务对象替换的复杂性。
10. Spring的高度开放性，并不强制应用完全依赖于Spring，开发者可以自由选择Spring的部分或全部。


#### Spring中使用了哪些设计模式吗？
👉 [Spring中使用的设计模式](https://www.cnblogs.com/dengpengbo/p/10485924.html)


| 设计模式 | 解释 | 
| :----- | :----- | 
| 单例模式 | `<bean scope="singleton"></bean>` | 
| 原型模式 | `<bean scope="prototype"></bean>` | 
| 模板模式 | JdbcTemplate | 
| 观察者模式 | ApplicationListener | 
| 工厂模式-简单工厂模式 | getBean() | 
| 工厂模式-工厂方法模式 | `<bean factory-method="getInstance"></bean>` | 
| 适配器模式 | 在AOP实现中的Advice和interceptor之间的转换。 | 
| 装饰者模式 | 一种是类名中含有Wrapper，另一种是类名中含有Decorator。 | 
| 代理模式 | Spring AOP | 
| 策略模式 | 实例化对象 | 


#### SpringIOC的启动过程吗？
👉 [容器创建流程](https://zhuanlan.zhihu.com/p/76256312)


#### SpringBean的生命周期吗？
👉 [Spring中bean的生命周期详解](https://blog.csdn.net/knknknkn8023/article/details/107130806)


#### Spring中bean实例化有哪几种方式(依赖注入)?
**Set方法**
```java
public class SpringAction {  
    private SpringDao springDao;  
    public void setSpringDao(SpringDao springDao) {  
        this.springDao = springDao;
    }  
    public void doMethod(){  
        springDao.doMethod();  
    }  
}  

<bean name="springAction" class="xxx.SpringAction">  
    <property name="springDao" ref="springDao"></property>
</bean>  
<bean name="springDao" class="xxx.impl.SpringDaoImpl"></bean>  
```


**普通构造方法**
```java
public class SpringAction {  
    private SpringDao springDao;   
    public SpringAction(SpringDao springDao){  
        this.springDao = springDao;  
    }
    public void doMethod(){  
        springDao.doMethod();  
    }  
}  

 <bean name="springAction" class="xxx.SpringAction">  
    <constructor-arg index="0" ref="springDao"></constructor-arg>  
</bean>
<bean name="springDao" class="xxx.impl.SpringDaoImpl"></bean>  
```


**静态工厂**
```java
public class SpringAction {  
    private FactoryDao staticFactoryDao;   
    public void setStaticFactoryDao(FactoryDao staticFactoryDao) {  
        this.staticFactoryDao = staticFactoryDao;  
    }  
}  

public class DaoFactory {  
    public static final FactoryDao getStaticFactoryDaoImpl(){  
        return new StaticFacotryDaoImpl();  
    }  
} 

<bean id="bean2" class="xxx.StaticFactory" factory-method="getInstance"/>
<bean name="springAction" class="xxx.SpringAction" >  
    <property name="staticFactoryDao" ref="staticFactoryDao"></property>
</bean>  
<bean name="staticFactoryDao" class="xxx.DaoFactory" factory-method="getStaticFactoryDaoImpl"></bean>  
```


**实例工厂**
```java
public class SpringAction {  
    private FactoryDao factoryDao;  
    public void setFactoryDao(FactoryDao factoryDao) {  
        this.factoryDao = factoryDao;  
    }  
}  

public class DaoFactory {   
    public FactoryDao getFactoryDaoImpl(){  
        return new FactoryDaoImpl();  
    }  
}  

<bean name="springAction" class="xxx.SpringAction">   
    <property name="factoryDao" ref="factoryDao"></property>  
</bean>  
<bean name="daoFactory" class="xxx.DaoFactory"></bean>  
<bean name="factoryDao" factory-bean="daoFactory" factory-method="getFactoryDaoImpl"></bean>  
```


#### SpringBean有哪些作用域？是否是线程安全的？
| 类别 | 是否线程安全 | 作用域 | 
| :----- | :----- | 
| <div style="width: 150px">singleton(默认)</div> | <div style="width: 100px">不确定</div> | 单例模式，在整个Spring IoC容器中，使用singleton定义的Bean将只有一个实例。 | 
| <div style="width: 150px">prototype</div> | <div style="width: 100px">安全</div> | 原型模式，每次通过容器的getBean方法获取prototype定义的Bean时，都将产生一个新的Bean实例，一般来说下面几种作用域，在开发的时候一般都不会用，99.99%的时候都是用singleton单例作用域。 | 
| <div style="width: 150px">request</div> | <div style="width: 100px">不确定</div> | 对于每次HTTP请求，使用request定义的Bean都将产生一个新实例，即每次HTTP请求将会产生不同的Bean实例。只有在Web应用中使用Spring时，该作用域才有效，在请求完成以后，bean会失效并被垃圾回收器回收。 | 
| <div style="width: 150px">session</div> | <div style="width: 100px">不确定</div> | 对于每次HTTP Session，使用session定义的Bean豆浆产生一个新实例。同样只有在Web应用中使用Spring时，该作用域才有效，在session过期后，bean会随之失效。 | 
| <div style="width: 150px">globalSession</div> | <div style="width: 100px">不确定</div> | 每个全局的HTTP Session，使用session定义的Bean都将产生一个新实例。典型情况下，仅在使用portlet context的时候有效。同样只有在Web应用中使用Spring时，该作用域才有效。 | 


线程安全这个问题，要从单例与原型Bean分别进行说明。对于原型Bean，每次创建一个新对象，也就是线程之间并不存在Bean共享，自然是不会有线程安全的问题。对于单例Bean，所有线程都共享一个单例实例Bean，因此是存在资源的竞争。如果单例Bean，是一个无状态Bean，也就是线程中的操作不会对Bean的成员执行查询以外的操作，那么这个单例Bean是线程安全的。比如SpringMVC的Controller、Service、Dao等，这些Bean大多是无状态的，只关注于方法本身。对于有状态的Bean，Spring官方提供的Bean，一般提供了通过ThreadLocal去解决线程安全的方法，比如RequestContextHolder、TransactionSynchronizationManager、LocaleContextHolder等。


#### Spring IOC原理?
IOC(Inversion Of Control)是指容器控制程序对象之间的关系，而不是传统实现中，由程序代码直接操控。控制权由应用代码中转到了外部容器，控制权的转移是所谓反转。对于Spring而言，就是由Spring来控制对象的生命周期和对象之间的关系；IOC还有另外一个名字——“依赖注入(Dependency Injection)”。从名字上理解，所谓依赖注入，即组件之间的依赖关系由容器在运行期决定，即由容器动态地将某种依赖关系注入到组件之中。


所有的类都会在Spring容器中登记，告诉Spring这是个什么东西，你需要什么东西，然后Spring会在系统运行到适当的时候，把你要的东西主动给你，同时也把你交给其他需要你的东西。所有的类的创建、销毁都由Spring来控制，也就是说控制对象生存周期的不再是引用它的对象，而是Spring。对于某个具体的对象而言，以前是它控制其他对象，现在是所有对象都被Spring控制，所以这叫控制反转。


在系统运行中，动态的向某个对象提供它所需要的其他对象。


依赖注入的思想是通过反射机制实现的，在实例化一个类时，它通过反射调用类中set方法将事先保存在HashMap中的类属性注入到类中。


总而言之，在传统的对象创建方式中，通常由调用者来创建被调用者的实例，而在Spring中创建被调用者的工作由Spring来完成，然后注入调用者，即所谓的依赖注入or控制反转。


注入方式有两种：依赖注入和设置注入。


IOC的优点：降低了组件之间的耦合，降低了业务对象之间替换的复杂性，使之能够灵活的管理对象。


#### Spring AOP原理?
AOP面向方面编程基于IOC，是对OOP的有益补充。


AOP利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其名为“Aspect”，即方面。所谓“方面”，简单地说，就是将那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，比如日志记录，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。


AOP代表的是一个横向的关系，将“对象”比作一个空心的圆柱体，其中封装的是对象的属性和行为；则面向方面编程的方法，就是将这个圆柱体以切面形式剖开，选择性的提供业务逻辑。而剖开的切面，也就是所谓的“方面”了。然后它又以巧夺天功的妙手将这些剖开的切面复原，不留痕迹，但完成了效果。


实现AOP的技术，主要分为两大类：一是采用动态代理技术，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行；二是采用静态织入的方式，引入特定的语法创建“方面”，从而使得编译器可以在编译期间织入有关“方面”的代码。


| 代理方式 | 解释 | 
| :----- | :----- | 
| <div style="width: 100px">JDK动态代理</div> | 其代理对象必须是某个接口的实现，它是通过在运行期间创建一个接口的实现类来完成对目标对象的代理，其核心的两个类是InvocationHandler和Proxy | 
| <div style="width: 100px">CGLIB代理</div> | 实现原理类似于JDK动态代理，只是它在运行期间生成的代理对象是针对目标类扩展的子类。CGLIB是高效的代码生成包，底层是依靠ASM(开源的java字节码编辑类库)操作字节码实现的，性能比JDK强；需要引入包asm.jar和cglib.jar | 


#### Spring事务的传播行为？
👉 [Spring事务传播机制详解](https://blog.csdn.net/qq_26323323/article/details/81908955)


| 传播行为 | 解释 | 
| :----- | :----- | 
| <div style="width: 280px">PROPAGATION_REQUIRED</div> | 表示当前方法必须运行在事务中。如果当前事务存在，方法将会在该事务中运行。否则，会启动一个新的事务。 | 
| <div style="width: 280px">PROPAGATION_SUPPORTS</div> | 表示当前方法不需要事务上下文，但是如果存在当前事务的话，那么该方法会在这个事务中运行。 | 
| <div style="width: 280px">PROPAGATION_MANDATORY</div> | 表示该方法必须在事务中运行，如果当前事务不存在，则会抛出一个异常。 | 
| <div style="width: 280px">PROPAGATION_REQUIRED_NEW</div> | 表示当前方法必须运行在它自己的事务中。一个新的事务将被启动。如果存在当前事务，在该方法执行期间，当前事务会被挂起。如果使用JTATransactionManager的话，则需要访问TransactionManager。 | 
| <div style="width: 280px">PROPAGATION_NOT_SUPPORTED</div> | 表示该方法不应该运行在事务中。如果存在当前事务，在该方法运行期间，当前事务将被挂起。如果使用JTATransactionManager的话，则需要访问TransactionManager。 | 
| <div style="width: 280px">PROPAGATION_NEVER</div> | 表示当前方法不应该运行在事务上下文中。如果当前正有一个事务在运行，则会抛出异常。 | 
| <div style="width: 280px">PROPAGATION_NESTED</div> | 表示如果当前已经存在一个事务，那么该方法将会在嵌套事务中运行。嵌套的事务可以独立于当前事务进行单独地提交或回滚。如果当前事务不存在，那么其行为与PROPAGATION_REQUIRED一样。注意各厂商对这种传播行为的支持是有所差异的。可以参考资源管理器的文档来确认它们是否支持嵌套事务。 |


#### SpringMVC工作原理？
![SpringMVC](/images/SSM/SpringMVC.jpeg)


1. `步骤1`客户端发出一个http请求给web服务器，web服务器对http请求进行解析，如果匹配DispatcherServlet的请求映射路径(在web.xml中指定)，web容器将请求转交给DispatcherServlet。
2. `步骤2`DispatcherServlet接收到这个请求之后将根据请求的信息(包括URL、Http方法、请求报文头和请求参数Cookie等)以及HandlerMapping的配置找到处理请求的处理器(Handler)。
3. `步骤3、4`DispatcherServlet根据HandlerMapping找到对应的Handler,将处理权交给Handler(Handler将具体的处理进行封装)，再由具体的HandlerAdapter对Handler进行具体的调用。
4. `步骤5`Handler对数据处理完成以后将返回一个ModelAndView()对象给DispatcherServlet。
5. `步骤6`Handler返回的ModelAndView()只是一个逻辑视图并不是一个正式的视图，DispatcherServlet通过ViewResolver将逻辑视图转化为真正的视图View。
6. `步骤7`DispatcherServlet通过model解析出ModelAndView()中的参数进行解析最终展现出完整的view并返回给客户端。


#### SpringMVC常用的注解？
| 名称 | 解释 | 
| :----- | :----- | 
| @Controller | Controller控制器是通过服务接口定义的提供访问应用程序的一种行为，它解释用户的输入，将其转换成一个模型然后将试图呈献给用户。Spring MVC使用@Controller定义控制器，它还允许自动检测定义在类路径下的组件并自动注册。如想自动检测生效，需在XML头文件下引入spring-context。 | 
| @RequestMapping | 既可以作用在类级别，也可以作用在方法级别。 | 
| @PathVariable | @PathVariable中的参数可以是任意的简单类型，如int, long, Date等等。Spring会自动将其转换成合适的类型或者抛出TypeMismatchException异常。 | 
| @RequestParam | @RequestParam将请求的参数绑定到方法中的参数上。其实，即使不配置该参数，注解也会默认使用该参数。如果想自定义指定参数的话，如果将@RequestParam的required属性设置为false(如@RequestParam(value="id",required=false))。 | 
| @RequestBody | 方法参数应该被绑定到HTTP请求Body上。 | 
| @ResponseBody | 将返回类型直接输入到HTTP response body中。 | 
| @RestController | 创建REST类型的控制器与@Controller类型。 | 


#### SpringMVC单例，高并发情况下，如何保证线程安全？
Spring单例模式下用它来切换不同线程之间的参数。用**ThreadLocal**是为了保证线程安全，实际上ThreadLocal的key就是当前线程的Thread实例。单例模式下，Spring把每个线程可能存在线程安全问题的参数值放进了ThreadLocal。这样虽然是一个实例在操作，但是不同线程下的数据互相之间都是隔离的，因为运行时创建和销毁的bean大大减少了，所以大多数场景下这种方式对内存资源的消耗较少，而且并发越高优势越明显。总的来说就是，单例模式因为大大节省了实例的创建和销毁，有利于提高性能，而ThreadLocal用来保证线程安全性。


#### 如何在过滤器处理类似重复下单的问题？
通过设置token的形式。把当前用户信息和token设置到session里，token为当前时间戳的值，同一用户下每次提交的token不一样，才允许通过，如果是2次以上的请求，那么可以使用token.equals(session.getAttribute("token"))来判断是否在同一个时刻有重复提交，如果相等，那么给错误提示: 重复提交！如果没有，那么就把token设置到对应的Session里。
```jsp
<input type="hidden" name="token" value="<%=System.currentTimeMillis()%>" />
```
```
public void doFilter(ServletRequest request, ServletResponse response, FilterChain arg2) throws IOException, ServletException {
    HttpServletRequest servletrequest = (HttpServletRequest) request;
    HttpServletResponse servletresponse = (HttpServletResponse) response;
    // 获取页面token值
    String clientToken = servletrequest.getParameter("token"); 
    String uid=servletrequest.getParameter("uid");
    // 页面token值为空就不用过滤
    if(clientToken==null){
        arg2.doFilter(request, response);
    }else{
        HttpSession session = servletrequest.getSession();
        // 获取会话token值
        String sessionToken = (String) session.getAttribute("token"); 
        String sessionUid=(String) session.getAttribute("uid");
        // 判断页面token值是否等于会话token值，会话token值为空就是第一次提交，相等就是重复提交
        if (sessionToken != null && uid.equals(sessionUid) && clientToken.equals(sessionToken)) {
            servletresponse.setContentType("text/html");
            servletresponse.setCharacterEncoding("GBK");
            // 跳转到错误提示页面
            servletresponse.sendRedirect(servletrequest.getContextPath()+"/public/duplicataSubmitError.jsp");
        }else{
            session.setAttribute("uid",uid);
            // 把页面token值赋予会话token值
            session.setAttribute("token", clientToken); 
            arg2.doFilter(request, response);	
        }
    }
}
```


#### Restful的优势？
| 优势 | 解释 | 
| :----- | :----- | 
| <div style="width: 150px">客户-服务器</div> | 客户-服务器约束背后的原则是分离关注点。通过分离用户接口和数据存储这两个关注点，改善了用户接口跨多个平台的可移植性；同时通过简化服务器组件，改善了系统的可伸缩性 | 
| <div style="width: 150px">无状态</div> | 通信在本质上是无状态的，改善了可见性、可靠性、可伸缩性 | 
| <div style="width: 150px">缓存</div> | 改善了网络效率减少一系列交互的平均延迟时间，来提高效率、可伸缩性和用户可觉察的性能 | 
| <div style="width: 150px">统一接口</div> | REST架构风格区别于其他基于网络的架构风格的核心特征是，它强调组件之间要有一个统一的接口 | 


#### 什么是JDBC？解释下驱动(Driver)在JDBC中的角色？Class.forName()方法有什么作用？
![JDBC](/images/SSM/JDBC.jpg)


JDBC是允许用户在不同数据库之间做选择的一个抽象层。JDBC允许开发者用JAVA写数据库应用程序，而不需要关心底层特定数据库的细节。


JDBC驱动提供了特定厂商对JDBC API接口类的实现，驱动必须要提供java.sql包下面这些类的实现：Connection, Statement, PreparedStatement,CallableStatement, ResultSet和Driver。


Class.forName()用来载入跟数据库建立连接的驱动。


#### PreparedStatement比Statement有什么优势？
| Statement | PreparedStatement | 
| :----- | :----- | 
| 每次执行sql语句，相关数据库都要执行sql语句的编译。 | PreparedStatement是预编译的，对于批量处理可以大大提高效率，也叫JDBC存储过程。 | 
| 在对数据库只执行一次性存取的时侯，用Statement对象进行处理。 | 对象的开销比Statement大，对于一次性操作并不会带来额外的好处。 | 
| 是'+'字符串拼接，安全性较低。 | 用'?'传参,可以防止sql注入，具有安全性。 | 


#### 什么时候使用CallableStatement？用来准备CallableStatement的方法是什么？
CallableStatement用来执行存储过程。存储过程是由数据库存储和提供的。存储过程可以接受输入参数，也可以有返回结果。非常鼓励使用存储过程，因为它提供了安全性和模块化。准备一个CallableStatement的方法是：CallableStament.prepareCall()。


#### 数据库连接池的优点？
1. 限定数据库的个数，不会导致由于数据库连接过多导致系统运行缓慢或崩溃。
2. 数据库连接不需要每次都去创建或销毁，节约了资源。
3. 数据库连接不需要每次都去创建，响应时间更快。


#### JDBC连接数据库步骤？
1. `加载JDBC驱动程序`通过Class类的forName方法实现，并将驱动地址放进去成功加载后，会将Driver类的实例注册到DriverManager类中。
2. `提供JDBC连接的URL、创建数据库的连接`要连接数据库，需要向java.sql.DriverManager请求并获得Connection对象，该对象就代表一个数据库的连接。使用DriverManager的getConnection()方法传入指定的欲连接的数据库的路径、数据库的用户名和密码。
```
 Connection con = DriverManager.getConnection(url,username,password);
 "jdbc:mysql://localhost:3306/test?user=root&password=123&useUnicode=true&characterEncoding=utf-8"
```
3. `创建一个Statement`要执行SQL语句，必须获得java.sql.Statement实例；执行静态SQL语句，通常通过Statement实例实现；执行动态SQL语句，通常通过PreparedStatement实例实现。
```
String sql = "";
Statement st = con.createStatement();  
PreparedStatement pst = con.prepareStatement(sql);
```
4. `执行SQL语句`Statement接口提供了executeQuery、executeUpdate、execute三种方法。executeQuery执行select语句，返回ResultSet结果集；executeUpdate执行insert、update、delete语句。
```
ResultSet rst = pst.executeQuery();
```
5. `关闭JDBC对象`操作完成以后要把所有使用的JDBC对象全都关闭，以释放JDBC资源。


#### `${}`和`#{}`的区别是什么？
`${}`是Properties文件中的变量占位符，它可以用于标签属性值和sql内部，属于静态文本替换，比如${driver}会被静态替换为com.mysql.jdbc.Driver。


`#{}`是sql的参数占位符，Mybatis会将sql中的#{}替换为?号，在sql执行前会使用PreparedStatement的参数设置方法，按序给sql的?号占位符设置参数值，比如ps.setInt(0, parameterValue)，#{item.name}的取值方式为使用反射从参数对象中获取item对象的name属性值，相当于param.getItem().getName()。使用`#{}`可以有效的防止SQL注入，提高系统安全性。


#### MyBatis接口为啥就能执行SQL啊？
👉 [MyBatis你只写了接口为啥就能执行SQL啊？](https://www.cnblogs.com/williamjie/p/11188355.html)


#### 能画一张图并解释Spring Boot的核心架构吗？
![SpringBoot](/images/SSM/SpringBoot.png)


| 特征 | 解释 | 
| :----- | :----- | 
| <div style="width: 300px">独立运行Spring项目</div> | Spring Boot可以以jar包形式独立运行，运行一个Spring Boot项目只需要通过java -jar xx.jar来运行。 | 
| <div style="width: 300px">内嵌servlet容器</div> | Spring Boot可以选择内嵌Tomcat、jetty或者Undertow，这样我们无须以war包形式部署项目。 | 
| <div style="width: 300px">提供starter简化Maven配置</div> | Spring提供了一系列的start pom来简化Maven的依赖加载。例如，当你使用了spring-boot-starter-web，会自动加入依赖包。 | 
| <div style="width: 300px">自动装配Spring</div> | SpringBoot会根据在类路径中的jar包，类。为jar包里面的类自动配置Bean，这样会极大地减少我们要使用的配置。当然，SpringBoot只考虑大多数的开发场景，并不是所有的场景，若在实际开发中我们需要配置Bean，而SpringBoot没有提供支持，则可以自定义自动配置。 | 
| <div style="width: 300px">准生产的应用监控</div> | SpringBoot提供基于HTTP SSH Telnet对运行时的项目进行监控。 | 
| <div style="width: 300px">无代码生产和XML配置</div> | Spring Boot不是借助于代码生成来实现的，而是通过条件注解来实现的，这是Spring4.x提供的新特性。 | 


#### @SpringBootApplication包含哪些注解？
1. `@ComponentScan`扫描当前包及其子包下被@Component，@Controller，@Service，@Repository注解标记的类并纳入到Spring容器进行管理。
2. `@SpringBootConfiguration`继承自@Configuration，二者功能一致，标注当前类是配置类，并会将当前类内声明的一个或多个以@Bean注解标记的方法的实例纳入到Spring容器中，并且实例名就是方法名。
3. `@EnableAutoConfiguration`启动自动的配置，@EnableAutoConfiguration注解的意思就是SpringBoot根据你添加的jar包来配置你项目的默认配置。


#### SpringBoot连接池？
👉 [详解SpringBoot配置连接池](https://blog.csdn.net/qq_38714585/article/details/84069825)


#### springboot哪个注解实现返回json格式的数据？
```
@ResponseBody
```