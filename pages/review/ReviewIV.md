## 题卷IV


### BIGO


#### 内存泄露怎么分析？怎么知道整条内存泄露的链路？
内存泄露或内存持续使用较高时，通常通过堆的情况来排查。首先可以通过`jmap -histo:live PID|less`命令查看堆内对象使用情况。此时如果内存泄露，一般都是会某个基本类型对象过多，然后可以与正常的服务作对比，看哪个对象的数量异常的多，此时如果可以判断出来，也没必要dump了。如果通过jmap无法断定，则可以使用`jmap -dump:live,format=b,file=`命令生成dump文件。将dump文件通过java原生的软件或者eclipse的mat工具，就可以看到哪些对象占用过多，此时你应该关注的是非基本类型对象的其他对象，因为一般来说都是基本类型的数量和大小最多。


一般来说，你会看到以下现象:


1. 某个map的Node十分多，有几十万个。
2. 某个框架的某个对象十分多。
3. char数据，也就是C[]，占用十分多，因为有很多大字符串。


#### 用的什么垃圾收集器？GC一次多久？线上多久一次Full GC？
G1


| GC类型 | 执行时间(建议值) | 执行频率(建议值) | 
| :----- | :----- | :----- | 
| Yong GC | <= 50ms | >= 10s/次 |
| Full GC | <= 1s | >= 10/min |


```
// 每秒一次, 连续输出10次 GC信息
jstat -gc PID 1000 10
```


```
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT   
 0.0   4096.0  0.0   4096.0 2637824.0 1824768.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
 0.0   4096.0  0.0   4096.0 2637824.0 1837056.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
 0.0   4096.0  0.0   4096.0 2637824.0 1853440.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
 0.0   4096.0  0.0   4096.0 2637824.0 1902592.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
 0.0   4096.0  0.0   4096.0 2637824.0 1910784.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
 0.0   4096.0  0.0   4096.0 2637824.0 1957888.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
 0.0   4096.0  0.0   4096.0 2637824.0 1968128.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
 0.0   4096.0  0.0   4096.0 2637824.0 1974272.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
 0.0   4096.0  0.0   4096.0 2637824.0 2023424.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
 0.0   4096.0  0.0   4096.0 2637824.0 2033664.0 1552384.0   316716.7  216248.0 193369.5 25600.0 22631.0    162    2.778   0      0.000    2.778
```


| 参数 | 解释 |
| :----- | :----- |
|S0C|第一个幸存区的大小|
|S1C|第二个幸存区的大小|
|S0U|第一个幸存区的使用大小|
|S1U|第二个幸存区的使用大小|
|EC|伊甸园区的大小|
|EU|伊甸园区的使用大小|
|OC|老年代大小|
|OU|老年代使用大小|
|MC|方法区大小|
|MU|方法区使用大小|
|CCSC|压缩类空间大小|
|CCSU|压缩类空间使用大小|
|YGC|年轻代垃圾回收次数|
|YGCT|年轻代垃圾回收消耗时间|
|FGC|老年代垃圾回收次数|
|FGCT|老年代垃圾回收消耗时间|
|GCT|垃圾回收消耗总时间|


PS: 之前的PC、PU被MC、MU取代了。(永久代)


#### 怎么进行JVM调优？何时需要做JVM调优？JVM调优量化目标？
** 怎么进行JVM调优 ** 


| 调优参数 | 解释 |
| :----- | :----- |
|<div style='width:100px'>-Xmx4g</div>|堆内存最大值为4GB。|
|<div style='width:100px'>-Xms4g</div>|初始化堆内存大小为4GB。|
|<div style='width:100px'>-Xss512k</div>|设置每个线程的堆栈大小。JDK5.0以后每个线程堆栈大小为1MB，以前每个线程堆栈大小为256K。应根据应用线程所需内存大小进行调整。在相同物理内存下，减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。|
|...|...|


**何时需要做JVM调优**


1. heap内存(老年代)持续上涨达到设置的最大内存值；
2. Full GC次数频繁；
3. GC停顿时间过长(超过1秒)；
4. 应用出现OutOfMemory等内存异常；
5. 应用中有使用本地缓存且占用大量内存空间；
6. 系统吞吐量与响应性能不高或下降。


**JVM调优量化目标**


1. Heap内存使用率 <= 70%；
2. Old generation内存使用率 <= 70%；
3. avg pause <= 1秒；
4. Full gc次数0 或 avg pause interval >= 24小时。


#### 项目里有用过ConcurrentHashMap吗？ConcurrentHashMap底层结构有了解吗？
**JDK1.7**


底层数据结构: `Segments数组 + HashEntry数组 + 链表`，采用分段锁保证安全性。


容器中有多把锁，每一把锁锁一段数据，这样在多线程访问时不同段的数据时，就不会存在锁竞争了，这样便可以有效地提高并发效率。这就是ConcurrentHashMap所采用的”分段锁”思想。


![](/images/ReviewIV/ConcurrentHashMap7.png)


1. 一个ConcurrentHashMap中有一个Segments数组，一个Segments中存储一个HashEntry数组，每个HashEntry是一个链表结构的元素。
2. segment继承自ReentrantLock锁。首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一段数据时，其他段的数据也能被其他线程访问，实现了真正的并发访问。
3. 可以通过构造函数指定，数组扩容不会影响其他的segment，get无需加锁，volatile保证内存可见性。


| 操作 | 解释 |
| :----- | :----- |
|get()|HashEntry中的value属性和next指针是用volatile修饰的，保证了可见性，所以每次获取的都是最新值，get过程不需要加锁。<br>1. 将key传入get方法中，先根据key的hashcode的值找到对应的segment段。<br>2. 再根据segment中的get方法再次hash，找到HashEntry数组中的位置。<br>3. 最后在链表中根据hash值和equals方法进行查找。<br>ConcurrentHashMap的get操作跟HashMap类似，只是ConcurrentHashMap第一次需要经过一次hash定位到Segment的位置，然后再hash定位到指定的HashEntry，遍历该HashEntry下的链表进行对比，成功就返回，不成功就返回null。|
|put()|1. 将key传入put方法中，先根据key的hashcode的值找到对应的segment段。<br>2. 再根据segment中的put方法，加锁lock()。<br>3. 再次hash确定存放的hashEntry数组中的位置。<br>4. 在链表中根据hash值和equals方法进行比较，如果相同就直接覆盖，如果不同就插入在链表中。|


**JDK1.8**


底层数据结构: `Synchronized + CAS + Node + 红黑树`。Node的val和next都用volatile保证，保证可见性，查找、替换、赋值操作都使用CAS。


```
为什么在有Synchronized的情况下还要使用CAS？

因为CAS是乐观锁，在一些场景中(并发不激烈的情况下)它比Synchronized和ReentrentLock的效率要高，
当CAS保障不了线程安全的情况下(扩容或者hash冲突的情况下)转成Synchronized来保证线程安全，大大
提高了低并发下的性能。
```


锁是锁的链表的head的节点，不影响其他元素的读写，锁粒度更细，效率更高，扩容时，阻塞所有的读写操作(因为扩容的时候使用的是Synchronized锁，锁全表)，并发扩容。


读操作无锁，Node的val和next使用volatile修饰，读写线程对该变量互相可见；数组用volatile修饰，保证扩容时被读线程感知。


| 操作 | 解释 |
| :----- | :----- |
|get()|get操作全程无锁。get操作可以无锁是由于Node元素的val和指针next是用volatile修饰的。 在多线程环境下线程A修改节点的val或者新增节点的时候是对线程B可见的。<br>1. 计算hash值，定位到Node数组中的位置。<br>2. 如果该位置为null，则直接返回null。<br>3. 如果该位置不为null，再判断该节点是红黑树节点还是链表节点；如果是红黑树节点，使用红黑树的查找方式来进行查找；如果是链表节点，遍历链表进行查找。|
|put()|1. 先判断Node数组有没有初始化，如果没有初始化先初始化initTable()。 <br>2. 根据key的进行hash操作，找到Node数组中的位置，如果不存在hash冲突，即该位置是null，直接用CAS插入。<br>3. 如果存在hash冲突，就先对链表的头节点或者红黑树的头节点加synchronized锁。<br>4. 如果是链表，就遍历链表，如果key相同就执行覆盖操作，如果不同就将元素插入到链表的尾部， 并且在链表长度大于8， Node数组的长度超过64时，会将链表的转化为红黑树。<br>5. 如果是红黑树，就按照红黑树的结构进行插入。|


👉 [ConcurrentHashMap底层结构和原理详解](https://blog.csdn.net/qq_45408390/article/details/122189726)


#### 你知道JDK7和JDK8之间的区别吗？
| 区别 | 解释 |
| :----- | :----- |
|接口中的default方法|一般来说接口中的方法都是不实现的，基本通过实现类来实现方法。但是jdk8中提供了一种被default修饰的方法，可以直接在接口中进行实现。|
|lambda表达式|jdk8引入了lambda表达式，也可称为closure(闭包)，通常是在需要一个函数，但又不想费神去命名一个函数的场合下使用，也就是指匿名函数。lambda允许把函数作为一个方法的参数(函数作为参数传递进方法中)。由于其几乎被所有主流开发语言支持。是java8新引入的一种语法，是一种紧凑的传递代码的方式。使用lambda表达式可以使代码变的更加简洁紧凑。|
|函数式接口|函数式接口的引入是为了函数式编程的，函数式编程往往依赖于lambda表达式。定义函数式接口有两个步骤:<br>1. 确保接口中有且仅有一个抽象方法；<br>2. 使用注解@FunctionalInterface修饰接口。|
|StreamAPI|JDK8中引入了StreamAPI，对于学过Flink或Spark的学者应该对此比较熟悉，其实就是类似于一种流式的数据处理，从dataSource到中间的operator操作，再到最后的dataSink。Stream的中间操作有map、filter等操作。而流式的数据处理往往就是通过函数式编程实现的。|
|移出永久代，元空间替换|JDK7使用永久代存储类的元数据，JDK8使用元空间存储类的元数据。元空间和永久代都是用来存储class相关信息，包括class对象的Method，Field等，元空间和永久代其实都是方法区的实现，只是实现有所不同，所以说方法区其实只是一种JVM的规范。两者最大的区别是元空间使用本地内存，而永久代使用的是JVM的内存。元空间优势就是元数据分配只受本地内存大小的限制。本地内存剩余多少理论上metaspace就可以有多大，这解决了空间不足的问题，不过也不可能任其无限壮大，JVM默认在运行时会根据需要动态的设置其大小。|
|HashMap中的优化|1. 增加红黑树。<br>2. 解决jdk1.7多线程下的死循环。扩容使用尾插法，抛弃头插法。<br>3. rehash的优化。jdk7扩容的rehash是重新计算桶的位置，而jdk8则进行了优化，不需要进行重新计算。|
|CurrentHashMap的优化|jdk7是使用分段锁实现线程安全，jdk8则使用synchronized关键字和大量的CAS操作实现。|


![](/images/ReviewIV/Stream.png)


#### 用过Stream吗，讲讲？
就根据平时使用说就好了，比较简单。


#### sql优化的经历？
1. 正确创建索引
2. 建表时合理选择数据类型
3. 不滥用事务
4. 使用定时作业去优化结构
5. 避免不必要的数据库连接
6. 学会使用sql分析工具(profiler)


👉 [sql数据库的优化](https://blog.csdn.net/qq_41510985/article/details/117778163)


#### 讲讲ES，ES文档数据太多了怎么办？
1. filesystem cache
2. 数据预热
3. 冷热分离
4. document模型设计
5. 分页性能优化
    1. 不允许深度分页/默认深度分页性能很惨；
    2. 类似于app里的推荐商品不断下拉出来一页一页的。
    
    
👉 [ES在数据量很大的情况下如何提高查询效率？](https://blog.csdn.net/lukabruce/article/details/123520323)


#### RocketMQ集群的原理，消息堆积怎么办，推拉模式优劣？
| 模式 | 优点 | 缺点 |
| :----- | :----- | :----- |
|推模式|1. 实时性强，有消息立马推送给客户端。<br>2. 客户端实现简单，只需要监听服务端的推送即可。|1. 容易导致客户端发生消息堆积的情况，因为每个客户端的消费能力是不同的，如果简单粗暴的有消息就推送，就会会出现堆积情况。<br>2. 服务端逻辑复杂，因为简单的推送会导致客户端出现堆积问题，所以服务端需要进行优化。记录给每个客户端的推送数据，然后根据每个客户端的消费能力去平衡数据推送的速度。|
|拉模式|1. 不会造成客户端消息积压，消费完了再去拉取，主动权在自己手中。<br>2. 长轮询实现的拉模式实时性也能够保证。|1. 客户端的逻辑实现相对复杂点，简化了服务端的逻辑。<br>|


👉 [消息队列，推拉模式的区别在哪？](https://blog.csdn.net/jiagoubaiduren/article/details/124829036)


#### 说下Raft协议？
Raft是一种实现分布式共识(一致性)的协议，也就是多个节点达成一致的协议。Raft算法想解决的核心问题是分布式共识问题。


分布式存储系统的核心问题之一: 维护多个副本的数据一致性。


Raft将一致性算法分为了几个部分，包括：领导选取(leader selection)、日志复制(log replication)、安全性(safety)、成员变更。


👉 [Raft协议简介](https://blog.csdn.net/chongbin007/article/details/117330501)


#### 分布式ID的设计方案？
1. 使用UUID生成ID
2. 使用数据库单机自增生成
3. 使用数据库集群模式自增生成
4. 使用数据库号段模式自增生成
5. 使用Redis单节点实现分布式ID
6. 使用Redis集群实现分布式
7. 利用Snowflake算法实现ID
8. 使用Zookeeper生成ID
9. 使用MongoDB创建ObjectID


👉 [Java中分布式ID的设计方案](https://baijiahao.baidu.com/s?id=1725596058860143229&wfr=spider&for=pc)


#### Redis集群的特性，分布式锁的设计？
1. Redis主从同步
2. 哨兵模式
3. cluster集群模式


👉 [redis集群、分布式锁](https://blog.csdn.net/weixin_35821291/article/details/123112125)


### 腾讯


#### HTTP/HTTPS，网络安全问题？
👉 [HTTPS如何解决HTTP存在的安全性问题](https://blog.csdn.net/qq_44159028/article/details/121060709)


#### volatile和synchronized的区别？
一旦一个共享变量(类的成员变量、类的静态成员变量)被volatile修饰之后，那么就具备了两层语义:


1. 保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。
2. 禁止进行指令重排序。


| 参数 | 区别 |
| :----- | :----- |
|volatile|1. 本质是在告诉jvm当前变量在寄存器(工作内存)中的值是不确定的，需要从主存中读取。<br>2. 仅能使用在变量级别。<br>3. 仅能实现变量的修改可见性，并不能保证原子性。<br>4. 不会造成线程的阻塞。<br>5. 标记的变量不会被编译器优化。|
|synchronized|1. 锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞。<br>2. 可以使用在变量、方法、类级别的。<br>3. 可以保证变量的修改可见性和原子性。<br>4. 可能会造成线程的阻塞。<br>5. 标记的变量可以被编译器优化。|


👉 [synchronized和volatile的区别](https://www.cnblogs.com/gaokeji/p/16058072.html)


#### JAVA内存模型？
JVM中试图定义一种JMM来屏蔽各种硬件和操作系统的内存访问差异，以实现让Java程序在各种平台下都能达到一致的内存访问效果。


![](/images/ReviewIV/JMM.png)


JMM定义了8个操作来完成主内存和工作内存之间的交互操作。JVM实现时必须保证下面介绍的每种操作都是原子的(对于double和long型的变量来说，load、store、read和write操作在某些平台上允许有例外)。


| 操作 | 解释 |
| :----- | :----- |
|<div style='width: 100px'>lock(锁定)</div>|作用于主内存的变量，它把一个变量标识为一条线程独占的状态。|
|<div style='width: 100px'>unlock(解锁)</div>|作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。|
|<div style='width: 100px'>read(读取)</div>|作用于主内存的变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用。|
|<div style='width: 100px'>write(写入)</div>|作用于主内存的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中。|
|<div style='width: 100px'>load(载入)</div>|作用于工作内存的变量，它把read操作从主内存中得到的变量值放入工作内存的变量副本中。|
|<div style='width: 100px'>use(使用)</div>|作用于工作内存的变量，它把工作内存中一个变量的值传递给执行引擎，每当虚拟机遇到一个需要使用到变量的值得字节码指令时就会执行这个操作。|
|<div style='width: 100px'>assign(赋值)</div>|作用于工作内存的变量，它把一个从执行引擎接收到的值赋给工作内存的变量，每当虚拟机遇到一个给变量赋值的字节码指令时执行这个操作。|
|<div style='width: 100px'>store(存储)</div>|作用于工作内存的变量，它把工作内存中一个变量的值传送到主内存中，以便随后write操作使用。|


如果要把一个变量从主内存中复制到工作内存，就需要按序执行read和load操作；如果把变量从工作内存中同步回主内存中，就需要按序执行store和write操作。但Java内存模型只要求上述操作必须按顺序执行，而没有保证必须是连续执行。


JMM还规定了上述8种基本操作，需要满足以下规则:


1. read和load必须成对出现；store和write必须成对出现。即不允许一个变量从主内存读取了但工作内存不接受，或从工作内存发起回写了但主内存不接受的情况出现。
2. 不允许一个线程丢弃它的最近assign的操作，即变量在工作内存中改变了之后必须把变化同步到主内存中。
3. 不允许一个线程无原因的(没有发生过任何assign操作)把数据从工作内存同步回主内存中。
4. 一个新的变量只能在主内存中诞生，不允许在工作内存中直接使用一个未被初始化(load或assign)的变量。换句话说，就是对一个变量实施use和store操作之前，必须先执行过了load或assign操作。
5. 一个变量在同一个时刻只允许一条线程对其进行lock操作，但lock操作可以被同一条线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。所以lock和unlock必须成对出现。
6. 如果对一个变量执行lock操作，将会清空工作内存中此变量的值，在执行引擎使用这个变量前，需要重新执行load或assign操作初始化变量的值。
7. 如果一个变量事先没有被lock操作锁定，则不允许对它执行unlock操作，也不允许去unlock一个被其他线程锁定的变量。
8. 对一个变量执行unlock操作之前，必须先把此变量同步到主内存中(执行store和write操作)。


👉 [Java内存模型](https://blog.csdn.net/m0_50414588/article/details/123912057)


👉 [Java的内存模型是什么](https://blog.csdn.net/qq_41848006/article/details/121542511)


#### Redis实现分布式锁与Zookeeper实现分布式锁的异同点？
| Redis | Zookeeper |
| :----- | :----- |
|1. 多个客户端(JVM)，会在Redis使用setnx命令创建相同的一个key，因为Redis的key保证唯一，不允许出现重复，只要谁能够先创建成功，谁能够获取到锁。<br>2. 在释放锁的时候，为了确保是锁的一致性问题，在删除的redis的key时候，需要判断同一个锁的id，才可以删除。<br>3. 对key设置有效期解决死锁现象。|1. 多个客户端(JVM)，会在Zookeeper上创建同一个临时节点，因为Zookeeper节点命名路径保证唯一，不允许出现重复，只要谁能够先创建成功，谁能够获取到锁。<br>2. 使用直接关闭临时节点session会话连接，因为临时节点生命周期与session会话绑定在一块，如果session会话连接关闭的话，该临时节点也会被删除。这时候客户端使用事件监听，如果该临时节点被删除的话，重新进入盗获取锁的步骤。<br>3. 使用会话有效期方式解决死锁现象。|


#### Innodb讲讲？
把知道的都说出来就好了。行锁、MVCC、外键、一致性读......


#### ZAB讲讲？
1. ZAB协议全称: Zookeeper Atomic Broadcast(Zookeeper原子广播协议)。
2. Zookeeper是一个为分布式应用提供高效且可靠的分布式协调服务。在解决分布式一致性方面，Zookeeper并没有使用Paxos，而是采用了ZAB协议。
3. ZAB协议定义: ZAB协议是为分布式协调服务。Zookeeper专门设计的一种支持崩溃恢复和原子广播协议。
4. 基于该协议，Zookeeper实现了一种主备模式的系统架构来保持集群中各个副本之间数据一致性。


ZAB协议和我们之前看的Raft协议实际上是有相似之处的，比如都有一个Leader，用来保证一致性(Paxos并没有使用Leader机制保证一致性)。再有采取过半即成功的机制保证服务可用(实际上Paxos和Raft都是这么做的)。ZAB让整个Zookeeper集群在两个模式之间转换，消息广播和崩溃恢复，消息广播可以说是一个简化版本的2PC，通过崩溃恢复解决了2PC的单点问题，通过队列解决了2PC的同步阻塞问题。而支持崩溃恢复后数据准确性的就是数据同步了，数据同步基于事务的ZXID的唯一性来保证。通过+1操作可以辨别事务的先后顺序。


👉 [ZAB算法详解](https://blog.csdn.net/yizhiniu_xuyw/article/details/114870828)


#### 怎么分库分表？
如果一个网站业务快速发展，那这个网站流量也会增加，数据的压力也会随之而来，比如电商系统来说双十一大促对订单数据压力很大，TPS十几万并发量，如果传统的架构(一主多从)，主库容量肯定无法满足这么高的TPS，业务越来越大，单表数据超出了数据库支持的容量，持久化磁盘IO，传统的数据库性能瓶颈，产品经理业务必须做，改变程序，数据库刀子切分优化。数据库连接数不够需要分库，表的数据量大，优化后查询性能还是很低，需要分。


👉 [MySQL数据库之分库分表方案](https://blog.csdn.net/weixin_46217160/article/details/120464899)


#### 怎么自己实现IOC？
1. 设计组件
2. 设计接口
3. 如何实现


👉 [自己动手实现一个简单的IOC](https://www.jianshu.com/p/6e25dc62e3a1)


#### 用过哪些设计模式，讲讲？
1. 单例(双重校验锁)模式
2. 动态代理模式
3. 简单工厂模式
4. 模板方法模式
5. 建造者模式


#### 怎么判断一个链表是不是有环？
| 方法 | 解释 |
| :----- | :----- |
|<div style='width:100px'>暴力双重循环</div>|直接使用双重循环，没什么好讲的。|
|<div style='width:100px'>使用HashSet</div>|在方法一的基础上进行优化降低复杂度，使用hashSet作为额外缓存，可以减少一层循环，具体思路如下:<br>首先创建一个以节点ID为Key的HashSet集合，用来存储曾经遍历过的节点。然后同样从头节点开始，依次遍历单链表中的每一个节点。每遍历一个新节点，都用新节点和HashSet集合中存储的节点进行比较，如果发现HashSet中存在与之相同的节点ID，则说明链表有环，如果HashSet中不存在与新节点相同的节点ID，就把这个新节点ID存入HashSet中，之后进入下一节点，继续重复刚才的操作。使用HashSet将算法的时间复杂度降为了O(n)。|
|<div style='width:100px'>利用两个指针</div>|首先创建两个指针p1和p2(在Java里就是两个对象引用)，让它们同时指向这个链表的头节点。然后开始一个大循环，在循环体中，让指针p1每次向后移动1个节点，让指针p2每次向后移动2个节点，然后比较两个指针指向的节点是否相同。如果相同，则可以判断出链表有环，如果不同，则继续下一次循环。<br>这种方法的时间复杂度同样是o(n)，但是双指针之外，没有额外使用存储空间，降低了空间复杂度。|


```java
public static boolean isLinkCycle(Node head) {
	Node p1 = head;
	Node p2 = head;
	while(p2 != null && p2.next != null) {
		p1 = p1.next;
		p2 = p2.next.next;
		if(p1 == p2) {
			return true;
		}
		return false;
}
```


#### 自我介绍？
自我介绍要准备好，不要太长也不要太短，几句话说明自己的职业生涯的情况，重点的项目，用到的技能点概括进去就行。


#### ES讲了个遍，包括基础原理和优化？
// todo


#### 分布式ID的生成方式？
// todo


#### 分布式事务相关知识，保证数据一致性？
1. 两阶段提交
2. 三阶段提交


👉 [分布式环境下保证数据一致性的几种实现方式](https://blog.csdn.net/qianshangding0708/article/details/104681548/)


#### 为什么要用分布式架构？又为什么要用微服务？
| 架构 | 优点 | 缺点 |
| :----- | :----- | :----- |
|单体架构|1. 易于开发，开发的方式简单，方便运行也容易调试。<br>2. 易于测试。<br>3. 易于部署。<br>|1. 项目过于臃肿，维护成本大，出现bug难定位。<br>2. 资源无法隔离，共享一个数据库，或者一块内存。如果一个功能模块突然访问量过大，可能影响整个系统的性能。<br>3. 无法灵活扩展，单体系统也可以集群部署，但是不够灵活，我明明只是订单系统遇到了瓶颈，只需要将订单模块水平扩展就行，但现在要将整个系统水平扩展。不灵活！<br>4. 交付周期长，所有功能得一起上线、一起构建、一起部署。任何一个环节出错，都可能影响交付。|
|分布式架构|......|1. 分布式系统是跨进程，跨网络的，性能很收网络延迟和带宽的影响。<br>2. 由于高度依赖网络状况，任何一次远程调用都可以失败。随着服务的增多，还会出现更多的潜在故障点。<br>3. 引入各种中间件，异步通信大大增加了功能实现的复杂度。<br>4. 分布式系统必然会有分布式事务的出现，这时对数据的一致性，需要在CAP中做出选择。<br>5. 一个系统拆成了多个服务，每个服务都得配置、部署、监控、日志处理。|


### 快手


#### 数据库连接不上了，怎么排查？
1. 网络是否正常。
2. 数据库服务是否正常。
3. 数据库权限。
4. 检查URL。
5. 检查白名单。
6. 检查防火墙。


#### 双亲委派模型，有什么好处？
![](/images/ReviewIV/ParentsDelegate.png)


**是什么**


如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行，如果父类加载器还存在其父类加载器，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器，如果父类加载器可以完成类加载任务，就成功返回，倘若父类加载器无法完成此加载任务，子加载器才会尝试自己去加载，这就是双亲委派模式。


简单来说就是首先从底向上检查类是否已经加载过，如果都没有加载过的话，那么就自顶向下的尝试加载该类。


**为什么**


| 原因 | 解释 |
| :----- | :----- |
|避免字节码重复加载|采用双亲委派模式的是好处是Java类随着它的类加载器一起具备了一种带有优先级的层次关系，通过这种层级关可以避免类的重复加载，当父亲已经加载了该类时，就没有必要子ClassLoader再加载一次。|
|程序更加安全，核心API不会被替换|假设通过网络传递一个名为java.lang.Integer的类，通过双亲委托模式传递到启动类加载器，而启动类加载器在核心Java API发现这个名字的类，发现该类已被加载，并不会重新加载网络传递的过来的java.lang.Integer，而直接返回已加载过的Integer.class，这样便可以防止核心API库被随意篡改。相同的class文件被不同的classloader加载就是不同的两个类。|


#### ThreadLocal讲讲？
![](/images/ReviewIV/ThreadLocal.png)


ThreadLocal是作为当前线程中属性ThreadLocalMap集合中的某一个Entry的key值Entry(ThreadLocal，value)，虽然不同的线程之间ThreadLocal这个key值是一样，但是不同的线程所拥有的ThreadLocalMap是独一无二的，也就是不同的线程间同一个ThreadLocal(key)对应存储的值(value)不一样，从而到达了线程间变量隔离的目的，但是在同一个线程中这个value变量地址是一样的。


**ThreadLocal如何解决Hash冲突**


与HashMap不同，ThreadLocalMap结构非常简单，没有next引用，也就是说ThreadLocalMap中解决Hash冲突的方式并非链表的方式，而是采用线性探测的方式。所谓线性探测，就是根据初始key的hashcode值确定元素在table数组中的位置，如果发现这个位置上已经被其他的key值占用，则利用固定的算法寻找一定步长的下个位置，依次判断，直至找到能够存放的位置。


```java
/
 * Increment i modulo len.
 */
private static int nextIndex(int i, int len) {
    return ((i + 1 < len) ? i + 1 : 0);
}

/
 * Decrement i modulo len.
 */
private static int prevIndex(int i, int len) {
    return ((i - 1 >= 0) ? i - 1 : len - 1);
}
```


**为什么ThreadLocalMap的key是弱引用**


key使用强引用这样会导致一个问题，引用的ThreadLocal的对象被回收了，但是ThreadLocalMap还持有ThreadLocal的强引用，如果没有手动删除，ThreadLocal不会被回收，则会导致内存泄漏。


key使用弱引用，引用的ThreadLocal的对象被回收了，由于ThreadLocalMap持有ThreadLocal的弱引用，即使没有手动删除，ThreadLocal也会被回收。value在下一次ThreadLocalMap调用set()、get()、remove()的时候会被清除。


#### 一次接口调用，在日志文件里打印“kuaishou ” + 耗时，比如“kuaishou 20ms”......有10w+条，用Linux的命令怎么查出来耗时最短的十条？耗时最长的呢？
```
test.log文件内容

kuaishou 50ms
kuaishou 25ms
kuaishou 100ms
kuaishou 200ms
kuaishou 13ms
log info 1......
log info 2......
kuaishou 499ms
log info 3......
kuaishou 1234ms
kuaishou 123ms
log info 4......
log info 5......
kuaishou 34ms
kuaishou 45ms
kuaishou 34ms
kuaishou 55ms
log info 6......
kuaishou 1234ms
log info 7......
```


```
# 耗时最短的十条

grep kuaishou test.log|sort -g -k2|head -n 10
```


```
# 耗时最长的十条

grep kuaishou test.log|sort -r -g -k2|head -n 10
或者
grep kuaishou test.log|sort -g -k2|tail -n 10
```


#### 安装了一个软件，怎么在Linux找到他的路径？
* find


通过find查找某个关键字，可以得到结果，结果是否精准完全取决于你的关键字。


```shell
[root@sit1 ~]# find / -name mysql
```


* whereis(推荐)


whereis除了可以找软件位置，还可以找到命令的二进制文件，源文件和手动页文件。


```shell
[root@sit1 ~]# 】whereis mysql
```


* which


准确的来说，which并不能找软件安装位置，只能查询软件命令的运行文件所在路径。


```shell
[root@sit1 ~]# which mysql
```


* locate


locate命令其实是“find -name”的另一种写法，但是要比后者快得多，原因在于它不搜索具体目录，而是搜索一个数据库(/var/lib/locatedb)，这个数据库中含有本地所有文件信息。Linux系统自动创建这个数据库，并且每天自动更新一次，所以使用locate命令查不到最新变动过的文件。为了避免这种情况，可以在使用locate之前，先使用updatedb命令，手动更新数据库。


#### 怎么查看JVM里线程状态？JVM使用情况？
```
# 线程使用情况
jstack PID
```


```
# JVM使用情况
jmap -heap PID
```


#### CountDownLatch和CyclicBarrier有什么区别？
| CountDownLatch | CyclicBarrier |
| :----- | :----- |
|1. CountDownLatch是不可重置的，所以无法重用。<br>2. CountDownLatch的基本操作组合是countDown/await。调用await的线程阻塞等待countDown足够的次数，不管你是在一个线程还是多个线程里countDown，只要次数足够即可。所以说CountDownLatch操作的是事件。|1. CyclicBarrier则没有这种限制，可以重用。<br>2. CyclicBarrier的基本操作组合，则就是await。当所有的伙伴(parties)都调用了await，才会继续进行任务，并自动进行重置。注意，正常情况下，CyclicBarrier的重置都是自动发生的，如果我们调用reset方法，但还有线程在等待，就会导致等待线程被打扰，抛出BrokenBarrierException异常。CyclicBarrier侧重点是线程，而不是调用事件，它的典型应用场景是用来等待并发线程结束。|


#### jps -m、jps -l用过吗？
jps [options] [hostid]


[options]选项

-q: 仅输出VM标识符，不包括classname、jar name、arguments in main method
-m: 输出main method的参数
-l: 输出完全的包名、应用主类名，jar的完全路径名
-v: 输出jvm参数


#### 讲一下Spring事务底层是怎么实现的？
事务是基于AOP的机制进行实现的！


👉 [Spring事务底层的实现流程](https://blog.csdn.net/qq_35436158/article/details/123400990)


### 菜鸟&嘀嘀


#### JVM内存溢出排查？
内存溢出一般可能存在的情况:


1. 实例化对象太大，超出空闲内存。
2. 读取文件等操作，一次加载全部，全加载到内存，超出内存范围。
3. 静态域做缓存，静态变量不会被GC机制处理，即使内存溢出。
4. 大量无效强引用对象，GC不会处理强引用对象，可达性算法也不会标记活跃对象。


```
取样查看内存使用情况
jstat -gcutil 28506(PID) 1000 5 间隔一秒，取五次

jmap查看是否有jmap环境，生成jvm快照
jmap -dump:live,format=b,file=xx.hprof 28506(PID) 

报以下错误，在语句中加-F
Unable to open socket file :target process not responding or hotspot vm not loaded 
the -F  option can be used when the target process is not responding

jmap -F -dump:live,format=b,file=xx.hprof 28506(PID) 

在内存溢出前生成JVM快照配置，启动脚本中加
export JAVA_OPTIONS="-XX:+HeapDumpOnOutOfMemoryError  -XX:HeapDumpPath=/usr/local/app/oom";
nohup $JAVA_OPTIONS -jar xxxx &

java -XX:+HeapDumpOnOutOfMemoryError  -XX:HeapDumpPath=/usr/local/app/oom -jar xxx
```


#### 网络编程相关的提问
// todo


#### 并发包相关问题
JUC下的那些常⻅问题，ConcurrentHashMap、CountDownLatch等等这些都应该要熟悉掌握，面试必考点。


#### 线程池原理
// todo


#### Mysql索引、锁机制、隔离级别
// todo


#### Redis连环炮
Redis连环炮，数据类型、缓存击穿、雪崩、穿透、热key、大key，哨兵、集群、同步机制......都应该了然于心。


#### Tomcat起两个war包，怎么识别哪个请求要给到哪个进程？
👉 [多war包部署在一个tomcat中](https://blog.csdn.net/u012832579/article/details/83651469)


#### linux的命令，比如怎么查看给文件按大小排序，主要是查看日志相关的技巧命令？
```
[cloud@sit4 apps]$ du -s *|sort -g -k1
```


#### volatile内存屏障具体是怎么实现？
在每个volatile写操作前插入StoreStore屏障，在写操作后插入StoreLoad屏障；在每个volatile读操作前插入LoadLoad屏障，在读操作后插入LoadStore屏障。


JVM这么插入是基于volatile关键字的特性: 


1. 保证volatile全局可见；
2. 保障volatile的操作不会被重排序。


#### JVM启动参数有哪些？怎么调优？
// todo


#### TLAB是什么？
TLAB的全称是Thread Local Allocation Buffer，翻译过来就是线程本地分配缓存。


首先从Thread Local这两个单词能够联想到一个本地线程变量类ThreadLocal，该类可以用来维护线程私有变量，而TLAB则是一个线程专用的内存分配区域，也是线程私有的。


在日常的业务过程中，Java对象会不断的被新建和不断的被回收，这就涉及到对象的分配了，而新建的对象一般都是分配在堆上，而堆却是线程共享的。所以如果同一时间，有多个线程要在堆上申请空间，这里可以类比多线程访问共享变量的操作，要保证共享变量的线程安全，就得采取线程安全的手段。所以每一次对象分配都要做同步，而越多的线程要在堆上申请空间，竞争就会越激烈，效率就会降低。因此Java虚拟机采用了TLAB这种线程专属的区域来避免出现多线程冲突，提高对象分配的效率。TLAB是默认启动的，在该情况下，JAVA虚拟机会为每一个线程都分配一个TLAB区域。



TLAB分配在eden区，因为eden区一般是新建对象所在的区域(这里去除大对象，因为大对象会直接进入老年代)。


```
# 以server模式运行，支持逃逸分析参数DoEscapeAnalysis
-server
# 关闭逃逸分析，避免出现栈上分配影响效果
-XX:-DoEscapeAnalysis
# 禁止后台编译
-XX:-BackgroundCompilation
# 禁用TLAB
-XX:-UseTLAB
```


👉 [什么是Java中的TLAB](https://blog.csdn.net/hfer/article/details/106077631)


#### Java内存逃逸
👉 [对象和数组并不是都在堆上分配内存的](http://www.hollischuang.com/archives/2398)


👉 [深入理解Java中的逃逸分析](https://www.cnblogs.com/cy0628/p/15214024.html)


#### 阻塞队列对比和选择？
![](/images/ReviewIV/BlockingQueue.png)


[如何选择适合自己的阻塞队列？](https://blog.csdn.net/vincent_wen0766/article/details/108593587)


#### DDD的理解？
DDD是一种设计思想，通过敏捷演变而来，主要解决使系统减少重构风险，并且清晰规划业务架构、系统架构、技术架构，使系统在快速发展过程避免重构推倒重来。


1. 解决系统架构不清晰、内聚低、耦合高；
2. 减少重构风险；
3. 使各业务边界清晰；
4. 可以随业务发展可很好拓展。


👉 [领域驱动设计在互联网业务开发中的实践](https://tech.meituan.com/2017/12/22/ddd-in-practice.html)


#### Reactor模型？
Reactor模型是应对高并发网络I/O请求的一种技术处理方案，主要用于处理客户端和服务器端的交互过程。


在Web服务中，处理Web请求时一般有两种体系，一种是多线程并发模式，服务器端每接收客户端的一个请求，就开启一个独立的线程来服务；还有一种是基于事件驱动，定义一系列事件与其对应的响应函数，并将服务器端接受连接与对事件的处理分离。而Reactor模型和Proactor模型便是事件驱动模型的两种实现方式，其中Reactor主要应用于同步I/O模式，而Proactor主要用于异步I/O模式。通过使用这样的模型，能够提高服务器处理I/O的性能。


👉 [Reactor线程的三种模型](https://www.jianshu.com/p/9a4624447f1b)


👉 [Reactor模型](https://www.cnblogs.com/wyjKeepMoveOn/articles/15449863.html)


### 蚂蚁金服


#### CHM结构，线程安全保证，加锁实现细节？
// todo


#### JVM内存结构，垃圾回收原理，GC配合策略，排查过程细节和调优方案？
// todo


#### 线程安全的本质？
1. 原子性
2. 有序性
3. 可见性


#### 并发锁，AQS原理
AQS(AbstractQueuedSynchronizer)在ReentrantLock、ReentrantReadWriteLock、Semaphore、CountDownLatch、ThreadPoolExecutor的Worker中都有运用(JDK 1.8)，AQS是这些类的底层原理。


#### 为什么AQS使用双向链表
1. 中断
2. 唤醒


在队列同步器中，头节点是成功获取到同步状态的节点，而头节点的线程释放了同步状态后将会唤醒其他后续节点，后继节点的线程被唤醒后需要检查自己的前驱节点是否是头节点，如果是则尝试获取同步状态。所以为了能让后继节点获取到其前驱节点，同步队列便设置为双向链表，而等待队列没有这样的需求，就为单链表。


👉 [为什么AQS使用双向链表](https://blog.csdn.net/qq_41490274/article/details/124535355)


#### ThreadLocal源码
// todo


#### Mysql幻读，怎么加锁
👉 [MySQL如何使用锁解决幻读](http://t.zoukankan.com/reecelin-p-13469969.html)


#### 秒杀设计
秒杀从流量过滤、缓存、异步队列、限流、降级这些方面说说就可以了。


### 淘宝


#### 网络模型，NIO、Netty
// todo


#### JVM，垃圾回收，调优，排查思路
// todo


#### Redis为什么快？使用场景？过期策略？
// todo


#### RocketMQ场景，如何保证不丢消息？
// todo


#### Spring Bean生命周期
1. 实例化Bean
2. Bean属性填充
3. 初始化Bean
4. 销毁Bean


![](/images/ReviewIV/SpringBean.png)


👉 [Spring中bean的生命周期](https://blog.csdn.net/kykangyuky/article/details/123114227)


#### 线程池参数，执行顺序，场景？
// todo


#### Mysql存储引擎，索引？
基本上就说Innodb和Myisam就行了，区别说清楚。


### 饿了么


#### CHM和HashTable
// todo


#### JVM垃圾回收的原理，GC排查步骤
// todo


#### volatile，synchronized
// todo


#### SQL优化，事务隔离级别
// todo


#### MQ的区别以及选型
| 特性 | ActiveMQ | RabbitMQ | RocketMQ | Kafka |
| :----- | :----- | :----- | :----- | :----- |
|单机吞吐量|万级，比RocketMQ、Kafka低一个数量级|同ActiveMQ|10万级，支撑高吞吐|10万级，高吞吐，一般配合大数据类的系统来进行实时数据计算、日志采集等场景。|
|topic数量对吞吐量的影响|----|----|topic可以达到几百/几千的级别，吞吐量会有较小幅度的下降，这是RocketMQ的一大优势，在同等机器下，可以支撑大量的topic。|topic从几十到几百个时候，吞吐量会大幅度下降，在同等机器下，Kafka尽量保证topic数量不要过多，如果要支撑大规模的topic，需要增加更多的机器资源。|
|时效性|ms级|微秒级，这是RabbitMQ的一大特点，延迟最低|ms级|延迟在ms级以内。|
|可用性|高，基于主从架构实现高可用|同ActiveMQ|非常高，分布式架构|非常高，分布式，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用。|
|消息可靠性|有较低的概率丢失数据|基本不丢|经过参数优化配置，可以做到0丢失|同RocketMQ|
|功能支持|MQ领域的功能极其完备|基于erlang开发，并发能力很强，性能极好，延时很低|MQ功能较为完善，还是分布式的，扩展性好|功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用。|


综上，各种对比之后，有如下建议:


一般的业务系统要引入MQ，最早大家都用ActiveMQ，但是现在确实大家用的不多了，没经过大规模吞吐量场景的验证，社区也不是很活跃，所以大家还是算了吧，我个人不推荐用这个了。后来大家开始用RabbitMQ，但是确实erlang语言阻止了大量的Java工程师去深入研究和掌控它，对公司而言，几乎处于不可控的状态，但是确实人家是开源的，比较稳定的支持，活跃度也高。不过现在确实越来越多的公司会去用RocketMQ，确实很不错，毕竟是阿里出品，但社区可能有突然黄掉的风险(目前RocketMQ已捐给Apache，但GitHub上的活跃度其实不算高)对自己公司技术实力有绝对自信的，推荐用RocketMQ，否则回去老老实实用RabbitMQ 吧，人家有活跃的开源社区，绝对不会黄。所以中小型公司，技术实力较为一般，技术挑战不是特别高，用RabbitMQ是不错的选择；大型公司，基础架构研发实力较强，用RocketMQ是很好的选择。如果是大数据领域的实时计算、日志采集等场景，用Kafka是业内标准的，绝对没问题，社区活跃度很高，绝对不会黄，何况几乎是全世界这个领域的事实性规范。


#### Redis分布式锁
// todo


#### 业务幂等实现
| 实现方式 | 解释 |
| :----- | :----- |
|利用类似Mysql这种具有唯一性约束的数据库机制。|需要落库，比较重。|
|消费消息的时候，先根据唯一key值做一次select，有则不用消费，没有则消费。|select不一定是从关系型数据库中获取。|
|利用集合的contains方法。|分布式环境下可以借助redis来实现，存在网络消耗，且集合的大小需要确定一个固定大小，或者设计成一个队列，分布式网络环境下，如何本地缓存实现去重？|
|利用分布式缓存(如redis)。|做已处理标识位，每次处理消息的时候都去check一下。|


#### 接口幂等实现
**接口未做幂等带来的问题**


1. 填写某些form表单时，保存快速点了两次，数据库中新增两条相同的数据；
2. 微服务调用超时重试；
3. mq消费时读取到重复消息。


接口幂等性是指用户对于同一操作发起的一次请求或者多次请求的结果是一致的，不会因为多次点击而产生了副作用，以上操作未做接口幂等的话都可能产生重复数据。


**主要实现方式**


| 过程 | 解释 |
| :----- | :----- |
|<div style='width: 90px'>悲观锁</div>|select * from oder where order id = #{orderId} for update;<br>对需要修改的数据加锁处理，判断业务是否已执行，已执行的话则返回错误；条件语句中需要时主键或唯一索引。|
|<div style='width: 90px'>乐观锁</div>|update stock set num=num-1 ,version=version+1 where id = #{id} and version = #{version}<br>判断操作影响行数，如果影响1行，则说明是一次请求，可以做其他数据操作,否则返回错误信息。|
|<div style='width: 90px'>加唯一索引</div>|alter table order add UNIQUE KEY un_code (code);<br>通过是否抛出DuplicateKeyException判断请求是否插入成功。|
|<div style='width: 90px'>状态机</div>|业务是否具体一定顺序的状态。下单=>待支付=>已支付=>待发货=>完成。做业务操作时，更新条件中加上上一步的状态。|
|<div style='width: 90px'>分布式锁</div>|做业务操作时先获取到分布式锁才能继续业务操作，拿到锁后同时也需要判断业务是否已经操作完成。|


#### 分库分表，MyCat底层实现原理
// todo


#### 监控指标、业务分析、业务指标
根据自己使用的监控系统来说，哪些监控项，为什么？告警策略是什么，指标，阈值。


#### 亮点业务，好的项目
// todo


