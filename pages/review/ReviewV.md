## 题卷V


### 阿里巴巴


#### 对象如何进行深拷贝，除了clone？
1. 构造函数
2. Apache Commons Lang序列化
3. Gson序列化


#### happen-before原则？
|原则|解释|
| :----- | :----- |
|<div style='width: 80px'>单线程</div>|在同一个线程中，书写在前面的操作happen-before后面的操作。|
|<div style='width: 80px'>锁</div>|同一个锁的unlock操作happen-before此锁的lock操作。|
|<div style='width: 80px'>volatile</div>|对一个volatile变量的写操作happen-before对此变量的任意操作(当然也包括写操作了)。|
|<div style='width: 80px'>传递性</div>|如果A操作happen-before B操作，B操作happen-before C操作，那么A操作happen-before C操作。|
|<div style='width: 80px'>线程启动</div>|同一个线程的start方法happen-before此线程的其它方法。|
|<div style='width: 80px'>线程中断</div>|对线程interrupt方法的调用happen-before被中断线程的检测到中断发送的代码。|
|<div style='width: 80px'>线程终结</div>|线程中的所有操作都happen-before线程的终止检测。|
|<div style='width: 80px'>对象创建</div>|一个对象的初始化完成先于他的finalize方法调用。|


#### JVM调优的实践？
1. 通过设置JVM启动参数
    1. 增大堆内存大小；
    2. 增大新生代大小(减少minor GC的同时，也会增加GC时长)；
    3. 提高年轻代晋升老年代的阈值。
2. 针对每次业务处理使用的内存数量
    1. 代码上减少大对象的使用，或者是拆分大对象；
    2. 代码上减少对象的创建。


#### 单例对象会被JVM的GC时回收吗？
<span style="color: red">不会</span>


**判断是否可回收**


以主流的JVM HotSpot来说，使用的是可达性分析，通过从GC Roots的引用作为起点，来判断对象可达性，从而决定是否回收该对象。那什么可以作为GC Roots呢？


1. 虚拟机栈(栈桢中的本地变量表)中的引用的对象。
2. 方法区中的类静态属性引用的对象。
3. 方法区中的常量引用的对象。
4. 本地方法栈中JNI的引用的对象。


从单例模式创建的对象来看，可以判断其符合方法区中的类静态属性引用的对象这条定义。方法区中的类中的静态属性必然引用堆中的对象。那如此看，如果没办法解决方法区中的类的话，可以认为单例对象不会被回收。类会放在方法区，对象会放在堆中。


**判断方法区中的类**


再想深一层，什么情况下有可能触发方法区(JDK8-)或元数据区(JDK8+)中单例类消亡？这样的话，堆中的对象就不可达，从而会被回收。如果这个假想不现实，则可认为单例模式实现的对象不可回收。符合下列三点则可认为类卸载:


1. 该类所有的实例都已经被回收，也就是Java堆中不存在该类的任何实例。
2. 加载该类的Class Loader已经被回收。
3. 该类对应的java.lang.Class对象没有任何地方被引用，无法在任何地方通过反射访问该类的方法。


要求相当苛刻，所以一般情况下可以认定不可回收了。


#### Redis如果list较大，怎么优化？
redis list分片，当redis的list数据量比较大时采用分片处理。


#### Redis优化？
Redis都是基于内存的操作，CPU不是Redis的性能瓶颈，服务器的内存利用率和网络IO才是Redis的性能瓶颈，Redis优化主要是从这2个维度做优化。


1. 数据存储优化:
    1. 控制key的长度:<br>在保证key在简单、清晰的前提下，尽可能把key定义得短一些来控制key的长度，如uerOrder:0001缩写为uo:0001。做好key规范管理的文档，定义好每个key缩写的含义。如按以上规范来做当Redis中有大量的key时也会节约Redis大量的内存空间，使其性能更高。
    2. 控制元素的大小:<br>在决定使用Redis建议强制规定元素的大小，推荐如下规范: String类型数据的值控制在10K以下；List/Hash/Set/ZSet数据类型的元素要控制在1W以内。以上的是杜绝big key有效措施，在big key对Redis的性能影响是最为致命的。
    3. 存储合适数据类型:<br>除非业务的强要求，建议选择合适的数据类型来优化内存。String/Set尽可能存储int类型数据；Hash/ZSet存储的元素数量控制在转换阈值之下，以压缩列表存储，节约内存。
    4. 设置过期key:<br>强制要求所有的key必须设置过期时间，以优化Redis内存。
    5. 冷热分离:<br>热key需要单独存放并分配合理的资源，防止大流量下直接冲垮整个缓存系统。
    6. 数据压缩:<br>可以采用snappy/gzip等压缩算法来先将数据压缩后再存入缓存中来节约Redis的内存空间，但这种方法会使客户端在读取时还需要解压缩，在这期间会消耗更多CPU资源，你需要根据实际情况进行权衡。建议只是在Redis匮乏时的一种方案。
2. 内存淘汰优化:<br>杜绝使用默认的内存淘汰策略，避免在业务扩展下Redis的内存持续膨胀，需要根据你的业务设置对应内存淘汰策略。
3. 过期策略优化:<br>由于Redis采用的是定期删除+懒加载删除策略，且这个过程在Redis 6.0之前是在主线程上执行的，建议所有key的过期时间用随机数打散，杜绝大批量的数据同时过期，拉垮Redis的性能和造成缓存雪崩。
4. 持久化优化:<br>除非业务的强要求，否则不要开启AOF，避免写磁盘拖垮Redis的性能。如果有业务要求开启AOF，建议配置为appendfsync everysec把数据持久化的刷盘操作，放到后台线程中去执行，尽量降低Redis写磁盘对性能的影响。
5. 部署优化:
    1. 物理机部署:<br>Redis在做数据持久化时，采用创建子进程的方式进行(会调用操作系统的fork系统)，而虚拟机环境执行fork的耗时要比物理机慢得多。
    2. 内网部署:<br>Redis集群有做副本数据同步，为了解决网络问题建议整个集群都部署在同一个局域网中。
6. 集群优化:
    1. 只使用db0:<br>在一个连接上操作多个db数据时，每次都会先执行查询有额外开销；建议不同业务数据尽量存储到不同的实例中而不是存放在不同db中；Redis cluster只支持db0。
    2. 架构优化:<br>读写分离能最大限度提高Redis的性能，其中主库负责数据写入，从库负责数据读取；分片集群是解决超大量数据导致性能瓶颈方案，如redis cluster。以上是在大流量下提高Redis性能在架构上的优化。
    3. 实例内存优化:<br>Redis集群有做副本数据同步，如果Redis的内存过大，那么在做持久化或数据同步时，磁盘IO和网络IO会拉垮Redis性能导致卡顿。建议Redis每一个实例都不要超过8G，要从业务上解耦，不同的业务用不同的Redis实例，杜绝所有业务都使用同一个Redis实例。
7. 其他优化:
    1. lazy-free机制:<br>在Redis4.0+中支持，开启lazy-free机制后，由主线程删除big key，而较耗时的内存释放会在后台线程中执行，不会影响到主线程。
    2. 批量命令:<br>尽量减少客户端和服务端之间来回的IO次数，建议string/hash类型使用mget/mset代替get/set，hmget/hmset代替hget/hset；其他数据类型使用pipeline。
    3. 避免复杂度过高命令:<br>Redis是单线程模型处理请求，在执行复杂度过高的命令(消耗更多CPU资源)时后面的请求会排队导致延迟，如SORT/SINTER/SINTERSTORE/ZUNIONSTORE/ZINTERSTORE等聚合类命令。
    4. 注意容器类型数据:<br>`容器类型数据查询`list、hash、set、zset类型数据在查询时要先确认元素的数量，元素非常多时需要分批查询(LRANGE/HASCAN/SSCAN/ZSCAN)，否则大量数据会导致延迟(如网络传输问题等)。`容器类型数据删除`建议分批删除，如list执行多次lpop/rpop；hash/set/zset先执行hscan/sscan/scan查询元素，再执行hdel/srem/zrem,不关注元素的数量直接del会有大量内存释放，拖垮主线程性能。
    

👉 [Redis优化篇](https://blog.csdn.net/qq_32979219/article/details/122759925)


#### TCP的粘包与半包？
TCP是面向连接的传输协议，TCP传输的数据是以流的形式 ，而流数据是没有明确的开始结尾边界，所以TCP也没办法判断哪一段流属于一个消息。


1. 发送方和接收方规定固定大小的缓冲区，也就是发送和接收都使用固定大小的byte[]数组长度，当字符长度不够时使用空字符弥补；
2. 在TCP协议的基础上封装一层数据请求协议，既将数据包封装成`数据头(存储数据正文大小)+数据正文`的形式，这样在服务端就可以知道每个数据包的具体长度了，知道了发送数据的具体边界之后，就可以解决半包和粘包的问题了；
3. 以特殊的字符结尾，比如以“\n”结尾，这样我们就知道结束字符，从而避免了半包和粘包问题(推荐解决方案)。


👉 [TCP粘包和半包问题及解决](https://blog.csdn.net/qq_45915957/article/details/116001523)


#### Socket编程相关的一些API和用法？
| API | 解释 |
| :----- | :----- |
|socket()|创建socket|
|bind()|绑定socket到本地地址和端⼝，通常由服务端调⽤|
|listen()|TCP专⽤，开启监听模式|
|accept()|TCP专⽤，服务器等待客户端连接，⼀般是阻塞态|
|connect()|TCP专⽤，客户端主动连接服务器|
|send()|TCP专⽤，发送数据|
|recv()|TCP专⽤，接收数据|
|sendto()|UDP专⽤，发送数据到指定的IP地址和端⼝|
|recvfrom()|UDP专⽤，接收数据，返回数据远端的IP地址和端⼝|
|closesocket()|关闭socket|


#### 建立和处理连接的是同一个Socket吗？Socket中两个队列分别是啥？
TCP三次握手建立连接的过程中，内核通常会为每一个LISTEN状态的Socket维护两个队列:


1. SYN队列(半连接队列): 这些连接已经接到客户端SYN；
2. ACCEPT队列(全连接队列): 这些连接已经接到客户端的ACK，完成了三次握手，等待被accept系统调用取走。


👉 [计算机网络](https://blog.csdn.net/weixin_44478659/article/details/121035192)


####  项目中有使用过Netty吗？
![](/images/ReviewV/Netty2.png)


Netty是一个基于JAVA NIO类库的异步通信框架，它的架构特点是异步非阻塞、基于事件驱动、高性能、高可靠性和高可定制性。Netty是基于Java NIO client-server的网络应用框架，使用Netty可以快速开发网络应用，例如服务器和客户端协议。Netty提供了一种新的方式来开发网络应用程序，这种新的方式使它很容易使用和具有很强的扩展性。Netty的内部实现是很复杂的，但是Netty提供了简单易用的API从网络处理代码中解耦业务逻辑。Netty是完全基于NIO实现的，所以整个Netty都是异步的。


分布式开源框架中Dubbo、Zookeeper，RocketMQ底层RPC通讯使用就是Netty。


#### TSL1.3新特性？
👉 [TLS 1.3科普](https://blog.csdn.net/andylau00j/article/details/79269499)


#### AES算法原理
![](/images/ReviewIV/AES.png)


#### Redis集群的使用
| 集群模式 | 特点 | 工作机制 | 
| :----- | :----- | :----- | 
|主从|master进行读写操作，当读写操作导致数据变化时会自动将数据同步给slave。slave一般都是只读的，并且接收master同步过来的数据。一个master可以拥有多个slave，但是一个slave只能对应一个master。slave挂了不影响其他从数据库的读和master的读和写，重新启动后会将数据从master同步过来。master挂了以后，不会在slave节点中重新选一个master。|当slave启动之后，开始向master发送SYNC命令。master接收到SYNC命令后在后台保存快照(RDB持久化)和缓存保存快照这段的命令，然后将保存的快照文件和缓存的命令发送给slave。slave接收到快照文件和命令后加载快照文件和缓存的执行命令。复制初始化后，master每次接收到的写命令都会同步发送给slave，保证主从数据一致性。|
|Sentinel|当master挂了以后，sentinel会在slave中选择一个做为master，并修改它们的配置文件，其他slave的配置文件也会被修改，比如slaveof属性会指向新的master。当master重新启动后，它将不再是master而是做为slave接收新的master的同步数据。sentinel因为也是一个进程有挂掉的可能，所以sentinel也会启动多个形成一个sentinel集群。多个sentinel配置的时候，sentinel之间也会自动监控。Sentinel不能和Redis部署在同一台机器，否则Redis的服务器一挂，sentinel也挂了。|sentinel以每秒钟一次的频率向它所知的master，slave以及其他sentinel实例发送一个PING命令 。如果一个实例距离最后一次有效回复PING命令的时间超过down-after-milliseconds选项所指定的值，则这个实例会被sentinel标记为主观下线。如果一个master被标记为主观下线，则正在监视这个master的所有sentinel要以每秒一次的频率确认master的确进入了主观下线状态。在一般情况下，每个sentinel会以每10秒一次的频率向它已知的所有master，slave发送INFO命令。当master被sentinel标记为客观下线时，sentinel向下线的master的所有slave发送INFO命令的频率会从10秒一次改为1秒一次。|
|Cluster|多个redis节点网络互联，数据共享所有的节点都是一主一从(也可以是一主多从)，其中从不提供服务，仅作为备用。不支持同时处理多个key(如MSET/MGET)，因为redis需要把key均匀分布在各个节点上、并发量很高的情况下同时创建key-value会降低性能并导致不可预测的行为。支持在线增加、删除节点客户端可以连接任何一个主节点进行读写。|----|


#### mysql与mogo对比
// todo


#### 场景题: 设计一个im系统包括群聊单聊
// todo


#### 场景题: 设计数据库连接池
![](/images/ReviewV/ConnectionPool.png)


👉 [如何设计数据库连接池](https://blog.csdn.net/buffeer/article/details/117186123)


#### 场景题: 秒杀场景的设计
// todo


### 美团


#### 项目详细信息，涉及一些AIOT交互处理，怎么实现大量的不同设备的指令编解码和指令转化，服务器的架构，自己责任模块
// todo


#### OOM的故障处理
![](/images/ReviewV/OOMError.png)


| 故障类型 | 出现场景 | 原因分析 | 解决方案 |
| :----- | :----- | :----- | :----- |
|Java heap space|当堆内存(Heap Space)没有足够空间存放新创建的对象时，就会抛出java.lang.OutOfMemoryError:Java heap space错误(根据实际生产经验，可以对程序日志中的OutOfMemoryError配置关键字告警，一经发现，立即处理)。|1. 请求创建一个超大对象，通常是一个大数组。<br>2. 超出预期的访问量/数据量，通常是上游系统请求流量飙升，常见于各类促销/秒杀活动，可以结合业务流量指标排查是否有尖状峰值。<br>3. 过度使用终结器(Finalizer)，该对象没有立即被GC。<br>4. 内存泄漏(Memory Leak)，大量对象引用没有释放，JVM无法对其自动回收，常见于使用了File等资源没有回收。|1. 通过-Xmx参数调高JVM堆内存空间。<br>2. 如果是超大对象，可以检查其合理性，比如是否一次性查询了数据库全部结果而没有做结果数限制。<br>3. 如果是业务峰值压力，可以考虑添加机器资源或者做限流降级。<br>4. 如果是内存泄漏，需要找到持有的对象，修改代码设计，比如关闭没有释放的连接。|
|GC overhead limit exceeded|当Java进程花费98%以上的时间执行GC，但只恢复了不到2%的内存，且该动作连续重复了5次，就会抛出java.lang.OutOfMemoryError:GC overhead limit exceeded错误。简单地说就是应用程序已经基本耗尽了所有可用内存，GC也无法回收。|同Java heap space|同Java heap space|
|Permgen space|该错误表示永久代(Permanent Generation)已用满，通常是因为加载的class数目太多或体积太大。|永久代存储对象主要包括以下几类: <br>1. 加载/缓存到内存中的Class定义，包括类的名称、字段、方法和字节码；<br>2. 常量池；<br>3. 对象数组/类型数组所关联的class；<br>4. JIT编译器优化后的class信息。<br>PermGen的使用量与加载到内存的Class的数量/大小正相关。|1. 程序启动报错，修改-XX:MaxPermSize启动参数，调大永久代空间。<br>2. 应用重新部署时报错，很可能是没有应用没有重启，导致加载了多份class信息，只需重启JVM即可解决。<br>3. 运行时报错，应用程序可能会动态创建大量class，而这些class的生命周期很短暂，但是JVM默认不会卸载class，可以设置-XX:+CMSClassUnloadingEnabled和-XX:+UseConcMarkSweepGC这两个参数允许JVM卸载class。|
|Metaspace|JDK1.8使用Metaspace替换了永久代(Permanent Generation)，该错误表示Metaspace已被用满，通常是因为加载的class数目太多或体积太大。|同Permgen space|同Permgen space。注意的是调整Metaspace空间大小的启动参数为-XX:MaxMetaspaceSize。|
|Unable to create new native thread|每个Java线程都需要占用一定的内存空间，当JVM向底层操作系统请求创建一个新的native线程时，如果没有足够的资源分配就会报此类错误。|JVM向OS请求创建native线程失败，就会抛出Unable to create new native thread，常见的原因包括以下几类:<br>1. 线程数超过操作系统最大线程数ulimit限制；<br>2. 线程数超过kernel.pid_max(只能重启)；<br>3. native内存不足。|1. JVM内部的应用程序请求创建⼀个新的Java线程；<br>2. JVM native方法代理了该次请求，并向操作系统请求创建一个native线程；<br>3. 操作系统尝试创建一个新的native线程，并为其分配内存；<br>4. 如果操作系统的虚拟内存已耗尽或是受到32位进程的地址空间限制，操作系统就会拒绝本次native内存分配；<br>5. JVM将抛出java.lang.OutOfMemoryError: Unable to create new native thread错误。|
|Out of swap space|该错误表示所有可用的虚拟内存已被耗尽。虚拟内存(Virtual Memory)由物理内存(Physical Memory)和交换空间(Swap Space)两部分组成。当运⾏时程序请求的虚拟内存溢出时就会报Out of swap space错误。|1. 地址空间不⾜；<br>2. 物理内存已耗光；<br>3. 应用程序的本地内存泄漏(native leak)，例如不断申请本地内存，却不释放。<br>4. 执行jmap-histo:live<pid>命令，强制执行Full GC；如果几次执行后内存明显下降，则基本确认为Direct ByteBuffer问题。|1. 升级地址空间为64bit。<br>2. 使用Arthas检查是否为Inflater/Deflater解压缩问题，如果是则显式调用end方法。<br>3. Direct ByteBuffer问题可以通过启动参数-XX:MaxDirectMemorySize调低阈值。<br>4. 升级服务器配置/隔离部署，避免争用。|
|Kill process or sacrifice child|有一种内核作业(Kernel Job)名为Out of Memory Killer，它会在可用内存极低的情况下杀死(kill)某些进程。OOM Killer会对所有进程进行打分，然后将评分较低的进程“杀死”，具体的评分规则可以参考Surviving the Linux OOM Killer。不同于其他的OOM错误，Kill processor sacrifice child错误不是由JVM层面触发的，而是由操作系统层面触发的。|默认情况下，Linux内核允许进程申请的内存总量大于系统可用内存，通过这种“错峰复用”的方式可以更有效的利用系统资源。然而这种方式也会无可避免地带来一定的“超卖”风险。例如某些进程持续占用系统内存，然后导致其他进程没有可用内存。此时系统将自动激活OOM Killer，寻找评分低的进程，并将其“杀死”，释放内存资源。|1. 升级服务器配置/隔离部署，避免争用。<br>2. OOM Killer调优。|
|Requested array size exceeds VM limit|JVM限制了数组的最大长度，该错误表示程序请求创建的数组超过最大长度限制。JVM在为数组分配内存前，会检查要分配的数据结构在系统中是否可寻址，通常为Integer.MAX_VALUE-2。此类问题比较罕见，通常需要检查代码，确认业务是否需要创建如此大的数组，是否可以拆分为多个块，分批执行。|----|----|
|Direct buffer memory|Java允许应用程序通过Direct ByteBuffer直接访问堆外内存，许多高性能程序通过Direct ByteBuffer结合内存映射文件(MemoryMapped File)实现高速IO。|Direct ByteBuffer的默认大小为64MB，一旦使用超出限制就会抛出Direct buffer memory错误。|1. Java只能通过ByteBuffer.allocateDirect方法使用Direct ByteBuffer，因此可以通过Arthas等在线诊断工具拦截该方法进行排查。<br>2. 检查是否直接或间接使用了NIO，如netty、jetty等。<br>3. 通过启动参数-XX:MaxDirectMemorySize调整Direct ByteBuffer的上限值。<br>4. 检查JVM参数是否有-XX:+DisableExplicitGC选项，如果有就去掉，因为该参数会使System.gc()失效。<br>5. 检查堆外内存使用代码，确认是否存在内存泄漏；或者通过反射调用sun.misc.Cleaner的clean()方法来主动释放被Direct ByteBuffer持有的内存空间。<br>6. 内存容量确实不足，升级配置。|


#### 有没有用过分布式锁，怎么实现的，讲讲原理
1. 数据库
2. zookeeper
3. redis


👉 [分布式锁原理及实现](https://blog.csdn.net/A_BlackMoon/article/details/116519674)


#### Redis的跳表用在哪里，为什么用跳表？
跳跃表以有序的方式在层次化的链表中保存元素，在大多数情况下，跳跃表的效率可以和平衡树媲美，查找、删除、添加等操作都可以在对数期望时间下完成，并且比起平衡树来说，跳跃表的实现要简单直观得多。所以在Redis中没有使用平衡树，而是使用了跳跃表。


跳跃表的结构是多层的，通过从最高维度的表进行检索再逐渐降低维度从而达到对任何元素的检索接近线性时间的目的O(logn)。理想的跳表是每一层是下一层元素的1/2，即每个元素跳过2个元素，这样共有log2N层。但是这样插入删除元素就会很复杂，ex插入一个元素需要更新所有层相关的节点。所以通常的做法：没次向跳表加入一个元素时，用扔硬币的方式决定要不要向上增长一层。


![](/images/ReviewIV/skiplist.png)


👉 [5分钟了解Redis的内部实现跳跃表](https://blog.csdn.net/sinat_19594515/article/details/118864717)


#### Mysql优化的实践经验
**优化目标**


1. 减少IO次数，IO永远是数据库最容易瓶颈的地方，这是由数据库的职责所决定的，大部分数据库操作中超过90%的时间都是IO操作所占用的，减少IO次数是SQL优化中需要第一优先考虑，当然也是收效最明显的优化手段。
2. 降低CPU计算，除了IO瓶颈之外，SQL优化中需要考虑的就是CPU运算量的优化了。order by、group by、distinct等等都是消耗CPU的大户(这些操作基本上都是CPU处理内存中的数据比较运算)。当我们的IO优化做到一定阶段之后，降低CPU计算也就成为了我们SQL优化的重要目标。
    1. 少join
    2. 少排序
    3. 避免select *
    4. 用join代替子查询
    5. 少or
    6. 用union all代替union
    7. 早过滤
    8. 避免类型转换
    
    
👉 [MySQL性能调优最佳实践经验](https://tech.it168.com/a2012/0414/1337/000001337422.shtml)


#### HashMap的1.8与1.7区别？
![](/images/ReviewV/HashMap.png)


| 版本号 | 结构 | 
| :----- | :----- |  
|<div style='width: 80px'>1.7</div>|数组+链表|
|<div style='width: 80px'>1.8</div>|数组+链表+红黑树(当链表长度>8且数组长度>=64时链表会转成红黑树，当长度<6时红黑树又会转成链表。)|


#### Netty的原理和使用
![](/images/ReviewV/Netty.png)


#### TCP的连接过程
![](/images/ReviewV/TCP.png)


| 过程 | 解释 |
| :----- | :----- |
|LISTEN|侦听来自远方的TCP端口的连接请求。|
|SYN-SENT|再发送连接请求后等待匹配的连接请求。|
|SYN-RECEIVED|再收到和发送。|


1. 第一次握手。建立连接后，客户端向服务器发送syn包(syn=j)，进入SYN_SEND状态，等待服务器确认。
2. 第二次握手。当服务器收到syn包时，必须确认客户端的syn(ack=j+1)并发送一个syn包(syn=k)，即syn+ack包。此时服务器进入SYN_RECV状态。
3. 第三次握手。SYN+ACK包，客户端收到服务器端发来的确认包ACK(ACK=k+1)，来发送这个包来发送，客户端和服务器端进入建立状态，完成三次握手。


#### Socket有几个队列
LISTEN状态的Socket维护的两个队列。


1. SYN队列(半连接队列)，这些连接已经接到客户端SYN。
2. ACCEPT队列(全连接队列)，这些连接已经接到客户端的ACK，完成了三次握手，等待被accept系统调用取走。


#### 一台服务器能支持多少连接，为什么？


实际情况下，每创建一个链接需要消耗一定的内存，大概是4-10kb，所以链接数也受限于机器的总内存。(链接发起端，活力全开才64000左右链接，内存最多才占用640M，一般客户端都能满足；内存限制主要还是考虑服务器端。)


👉 [单台服务器最大支持多少连接数](https://blog.csdn.net/wangpeng322/article/details/99842126)


👉 [一台服务器能支撑多少个TCP连接](http://t.zoukankan.com/lizexiong-p-14528874.html)


#### TCP各个参数怎么设置
👉 [TCP参数设置](http://t.zoukankan.com/DengGao-p-tcp_parameter.html)


#### Redis底层基本数据类型，redis集群原理，cluster集群的使用
// todo


#### Mysql存储引擎类型、索引类型，Innodb数据存储方式
// todo


#### 线程池的参数说明，rejectHandler说明
```java
public ThreadPoolExecutor(int corePoolSize, 
                          int maximumPoolSize,
                          long keepAliveTime, TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler)
```


👉 [线程池参数及使用说明](https://blog.csdn.net/changlina_1989/article/details/110630674)


#### volatile的原理
1. 可见性
2. 有序性


👉 [深入理解volatile底层原理](https://blog.csdn.net/weixin_41951205/article/details/123193064)


#### JVM有哪几种垃圾回收器，各自的应用场景
JVM垃圾回收器是GC算法的具体实现。常用的垃圾回收器有以下几种，并且分别针对的是堆内存中的新生代、老年代。值得一提的是方法区(有的文章也将之称为永久代，主要存放类信息、常量池、静态类变量等)也会触发GC，主要是回收不可存活的常量对象以及无用类(当该类在程序中不再存在任何实例、对应的classloader被卸载、该类的类对象class不被任何地方引用，此时可以被回收)。


|JVM垃圾回收器|特性|
| :----- | :----- |
|Serial垃圾回收器|简要概括下该回收器的特点: <br>单线程，基于复制算法，JVM运行在Client时默认的新生代垃圾收集器。可以说Serial是最基本的垃圾回收器，使用的是复制算法，回收效率高，不容易产生不连续的内存碎片。并且也是单线程的垃圾回收器，只使用一个线程完成垃圾回收工作，并且此时会暂停其他工作线程。|
|ParNew垃圾回收器(Serial+多线程版本)|简要概括下该回收器的特点: <br>Serial的多线程实现，基于复制算法，JVM运行在Server时默认的新生代垃圾收集器。该垃圾回收器是Serial的多线程版本，采用的也是复制算法(毕竟都是作用于新生代嘛)，也是在执行垃圾回收收集时需要暂停其他工作线程。默认开启与CPU相同数目的线程数，可以通过参数-XX：ParallerGCThreads来修改默认开启的线程数量。|
|Parallel Scavenge垃圾回收器(多线程复制算法)|简要概括下该回收器的特点: <br>多线程，基于复制算法，以吞吐量最大化为目标，允许较长时间的STW换取吞吐量。该回收器也是新生代的GC回收器，同样采用复制算法，多线程的策略。主要关注的吞吐量(即CPU运行用户代码的时间/CPU总消耗的时间，吞吐量=运行用户代码时间/(运行用户代码时间+垃圾收集时间))。|
|Serial Old 垃圾回收器(单线程标记整理算法)|简单概括下该回收器的特点: <br>单线程，基于标记整理算法，是JVM运行在Client模式下默认的老年代垃圾回收器，可和Serial搭配使用。Serial Old回收器时Serial回收器的老年代版本，也是单线程，但采用的标记整理的算法。这里注意最重要的特点就是这两种回收器都会使得工作线程被停止，并且均是单线程模式。与两个Serial不同的是，Scavenge/ParNew采用的是多线程版本，但是此时也会暂停其他工作线程。|
|Parallel Old垃圾回收器(多线程标记整理算法)|简单概括下该垃圾回收器的特点: <br>多线程，基于标记整理算法，优先考虑系统的吞吐量。该垃圾回收器是Paraller Scavenge的老年代版本，多线程、标记整理。在JDK1.6之前，新生代使用ParallelScavenge收集器只能搭配年老代的Serial Old收集器，只能保证新生代的吞吐量优先，无法保证整体的吞吐量，Parallel Old正是为了在年老代同样提供吞吐量优先的垃圾收集器，如果系统对吞吐量要求比较高，可以优先考虑新生代Parallel Scavenge和年老代Parallel Old收集器的搭配策略。|
|CMS垃圾回收器(多线程标记清楚算法)|简单概括下该垃圾回收器的特点：<br>多线程，基于标记清除算法，为老年代设计，追求最短停顿时间。主要有四个步骤：初始标记、并发标记、重新标记、并发清除。不会暂停用户工作线程。全称为Concurrent mark sweep，该回收器是专门为老年代设计的，主要追求的是最短的停顿时间，采用的标记清楚算法。四个主要工作阶段的内容为: <br>1. 初始标记，标记一下GC Roots能直接关联的对象，速度很快，仍然需要暂停所有的工作线程。(这里简要回顾下哪些对象可以作为GC Roots: java虚拟机栈中的引用对象；方法区中的常量引用对象；方法区中的静态变量引用的对象；本地方法栈中一般Native方法引用的对象。)<br>2. 并发标记，进行GC Roots 跟踪的过程，和用户线程一起工作，不需要暂停工作线程。<br>3. 重新标记，为了修正在并发标记期间，因用户程序继续运行而导致标记产生变动的那一部分对象的标记记录，仍然需要暂停所有的工作线程。<br>4. 并发清除，清除GC Roots不可达对象，和用户线程一起工作，不需要暂停工作线程。由于耗时最长的并发标记和并发清除过程中，垃圾收集线程可以和用户现在一起并发工作，所以总体上来看CMS 收集器的内存回收和用户线程是一起并发地执行。|
|G1垃圾回收器|----|


#### G1回收器的特征
简要概括下该垃圾回收器的主要特点: 将堆内存分为几个大小固定的独立区域，在后台维护了一个优先列表，根据允许的收集时间回收垃圾收集价值最大的区域。相比CMS不会产生内存碎片，并且可精确控制停顿时间。分为四个阶段: 初始标记、并发标记、最终标记、筛选回收。


该垃圾回收器看似和CMS工作流程差不多。采用的却是标记清楚算法，但是本质上还是存在差别，尤其是讲堆内存分为大小固定的几个区域，并且维持了一个优先列表，选取其中最有价值的回收垃圾。


Garbage first垃圾收集器是目前垃圾收集器理论发展的最前沿成果，相比与CMS收集器，G1收集器两个最突出的改进是:


1. 基于标记-整理算法，不产生内存碎片。
2. 可以非常精确控制停顿时间，在不牺牲吞吐量前提下，实现低停顿垃圾回收。G1收集器避免全区域垃圾收集，它把堆内存划分为大小固定的几个独立区域，并且跟踪这些区域的垃圾收集进度，同时在后台维护一个优先级列表，每次根据所允许的收集时间，优先回收垃圾 最多的区域。区域划分和优先级区域回收机制，确保G1收集器可以在有限时间获得最高的垃圾收集效率。


#### JVM结构
![](/images/ReviewV/JVM.png)


👉 [JVM内存结构](https://blog.csdn.net/weixin_41812379/article/details/123999872)


#### 负载均衡器的四层和七层负载均衡原理
![](/images/ReviewV/LVSNginx.jpg)


|负载均衡|解释|
| :----- | :----- |
|<div style='width: 100px'>四层负载均衡</div>|四层的负载均衡就是基于IP+端口的负载均衡。<br>在三层负载均衡的基础上，通过发布三层的IP地址(VIP)，然后加四层的端口号，来决定哪些流量需要做负载均衡，对需要处理的流量进行NAT处理，转发至后台服务器，并记录下这个TCP或者UDP的流量是由哪台服务器处理的，后续这个连接的所有流量都同样转发到同一台服务器处理。对应的负载均衡器称为四层交换机(L4 switch)，主要分析IP层及TCP/UDP层，实现四层负载均衡。此种负载均衡器不理解应用协议(如HTTP/FTP/MySQL等等)，常见例子有LVS、F5。|
|<div style='width: 100px'>七层负载均衡</div>|七层的负载均衡就是基于虚拟的URL或主机IP的负载均衡。<br>在四层负载均衡的基础上(没有四层是绝对不可能有七层的)，再考虑应用层的特征，比如同一个Web服务器的负载均衡，除了根据VIP加80端口辨别是否需要处理的流量，还可根据七层的URL、浏览器类别、语言来决定是否要进行负载均衡。举个例子，如果你的Web服务器分成两组，一组是中文语言的，一组是英文语言的，那么七层负载均衡就可以当用户来访问你的域名时，自动辨别用户语言，然后选择对应的语言服务器组进行负载均衡处理。|


👉 [负载均衡之四层与七层](https://blog.csdn.net/w2009211777/article/details/123984559)


#### 场景题: 设计一个高可用高并发的电商系统
// todo


### 腾讯


#### Kafka生产端怎么实现幂等的
![](/images/ReviewV/KafkaEqual.png)


```
# 配置幂等性
props.put("enable.idempotence", true);
```


为了实现生产者的幂等性，Kafka引入了Producer ID(PID)和Sequence Number的概念。


1. 每个Producer在初始化时，都会分配一个唯一的PID，这个PID对用户来说是透明的。
2. 针对每个生产者(对应PID)发送到指定主题分区的消息都对应一个从0开始递增的Sequence Number。


👉 [Kafka如何保证幂等性](https://blog.csdn.net/bookssea/article/details/124562291)


#### Kafka如何实现分布式消息
![](/images/ReviewV/Kafka.png)


#### Kafka的slave的同步机制
1. producer先从zookeeper的"/brokers/.../state"节点找到该partition的leader；
2. producer将消息发送给该leader；
3. leader将消息写入本地log；
4. followers从leader pull消息，写入本地log后leader发送ACK；
5. leader收到所有ISR中的replica的ACK后，增加HW(high watermark，最后commit的offset)并向producer发送ACK。


👉 [kafka同步机制](https://blog.csdn.net/zxczb/article/details/107923247)


👉 [kafka消息与同步机制](https://blog.csdn.net/u010285974/article/details/83308554)


#### Kafka怎么进行消息写入的ack
Kafka的ack机制，指的是producer的消息发送确认机制，这直接影响到Kafka集群的吞吐量和消息可靠性。而吞吐量和可靠性就像硬币的两面，两者不可兼得，只能平衡。


|ack值|解释|
| :----- | :----- |
|ack=1|简单来说就是，producer只要收到一个分区副本成功写入的通知就认为推送消息成功了。这里有一个地方需要注意，这个副本必须是leader副本。只有leader副本成功写入了，producer才会认为消息发送成功。|
|ack=0|简单来说就是，producer发送一次就不再发送了，不管是否发送成功。|
|ack=-1|简单来说就是，producer只有收到分区内所有副本的成功写入的通知才认为推送消息成功了。|


#### 为什么实现equals()必须先实现hash()
对于对象集合的判重，如果一个集合含有100个对象实例，仅仅使用equals()方法的话，那么对于一个对象判重就需要比较4950次，随着集合规模的增大，时间开销是很大的。但是同时使用哈希表的话，就能快速定位到对象的大概存储位置，并且在定位到大概存储位置后，后续比较过程中，如果两个对象的hashCode不相同，也不再需要调用equals()方法，从而大大减少了equals()比较次数。所以从程序实现原理上来讲的话，既需要equals()方法，也需要hashCode()方法。那么既然重写了equals()，那么也要重写hashCode()方法，以保证两者之间的配合关系。


#### 一个对象new出来后的结构，怎么保存的
1. 堆内存是用来存放由new创建的对象和数组，即动态申请的内存都存放在堆内存；
2. 栈内存是用来存放在函数中定义的一些基本类型的变量和对象的引用变量。


例子: 局部变量存放在栈；new函数和malloc函数申请的内存在堆；函数调用参数，函数返回值，函数返回地址存放在栈。


|内存类型|解释|
| :----- | :----- |
|栈区(stack)|由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。|
|堆区(heap)|一般由程序员分配释放，若程序员不释放，程序结束时可能由OS回收。注意它与数据结构中的堆是两回事，分配方式倒是类似于链表。|


#### 一个Java对象被new出来的全过程
1. 检查常量池中有没有一个符号代表这个类。
2. 检查这个类有没有被加载。
3. 在堆上或者TLAB上为对象分配内存空间。
4. 为对象的实例属性赋领值。
5. 设置对象头(Object_header)信息。比如对象是那个类的实例、对象的Hash码、GC分代年龄、锁状态标志、是否启用偏向锁等等。
6. 对象引用入栈。

    
#### 讲一讲类加载的过程
![](/images/ReviewV/ClassLoad.png)


👉 [简述类加载过程详解](https://blog.csdn.net/Y_eatMeat/article/details/122954134)


#### Redis的hash数据结构和如何扩容
**渐进式rehash**


在扩容和收缩的时候，如果哈希字典中有很多元素，一次性将这些键全部rehash到ht[1]的话，可能会导致服务器在一段时间内停止服务。所以，采用渐进式rehash的方式，详细步骤如下:


为ht[1]分配空间，让字典同时持有ht[0]和ht[1]两个哈希表。将rehashindex的值设置为0，表示rehash工作正式开始。在rehash期间，每次对字典执行增删改查操作是，程序除了执行指定的操作以外，还会顺带将ht[0]哈希表在rehashindex索引上的所有键值对rehash到ht[1]，当rehash工作完成以后，rehashindex的值+1。随着字典操作的不断执行，最终会在某一时间段上ht[0]的所有键值对都会被rehash到ht[1]，这时将rehashindex的值设置为-1，表示rehash操作结束。渐进式rehash采用的是一种分而治之的方式，将rehash的操作分摊在每一个的访问中，避免集中式rehash而带来的庞大计算量。需要注意的是在渐进式rehash的过程，如果有增删改查操作时，如果index大于rehashindex，访问ht[0]，否则访问ht[1]。


👉 [redis中hash扩容过程](https://blog.csdn.net/weixin_38004638/article/details/118206845)


#### Mysql快照读怎么实现的
Innodb存储引擎的快照读是基于多版本并发控制MVCC和undo log实现，通过MVCC机制提高系统读写并发性能，快照读只发生于select操作，但不包括select ... lock in share mode，select ... for update。


#### Mysql的事务隔离级别，不可重复读和幻读区别
| 事务隔离级别 | 解释 | 
| :----- | :----- | 
|脏读|一个事务读取到另一个事务还没有提交的数据。|
|不可重复读(修改)|在一个事务中多次读取同一个数据时，结果出现不一致。|
|幻读(插入/删除)|在一个事务中，使用相同的SQL两次读取，第二次读取到其他事务新插入的行。|


👉 [一篇文章读懂 MySQL 事务中的四种隔离级别](https://blog.csdn.net/weixin_50983264/article/details/125380783)


### YY


#### JVM调优思路
// todo


#### Redis cluster集群扩容怎么数据平滑过度(从客户端设计)


👉 [Redis cluster集群扩容缩容原理](https://blog.csdn.net/hellozhxy/article/details/120855704)


#### 两个Redis集群，如何平滑数据迁移
1. 基于Redis自身的RDB/AOF备份机制。
2. 基于redis-dump导入导出json备份。
3. 基于redis-shake实现redis-cluster迁移。


👉 [面试官: 两个Redis集群，如何平滑数据迁移？](https://zhuanlan.zhihu.com/p/90445769)


#### Mysql的sql本身没问题的情况下，没走索引原因？(反复强调sql没问题，不需要从sql⻆度考虑)
当表的索引被查询，会使用最好的索引，除非优化器使用全表扫描更有效。优化器优化成全表扫描取决与使用最好索引查出来的数据是否超过表的30%的数据。


👉 [为什么mysql没走索引？](https://blog.csdn.net/llchopin/article/details/118675176)


#### Kafka如何确保消息不丢失
// todo


#### 分库分表如何进行跨库联合查询
可以使用第三方中间件来实现，比如mycat、shading-jdbc。


当客户端发送一条sql查询`select * from user;`此时中间件会根据有几个子表，拆分成多个语句`select * from user1; select * from user2; select * from user3;`等多条语句查询，然后将查询的结果返回给中间件，然后汇总给客户端。这些语句是并发执行的，所以效率会很高。


#### 限流设计用JAVA实现，不能用工具类库
限流顾名思义，就是对请求或并发数进行限制；通过对一个时间窗口内的请求量进行限制来保障系统的正常运行。如果我们的服务资源有限、处理能力有限，就需要对调用我们服务的上游请求进行限制，以防止自身服务由于资源耗尽而停止服务。


1. 阈值: 在一个单位时间内允许的请求量。如QPS限制为10，说明1秒内最多接受10次请求。
2. 拒绝策略: 超过阈值的请求的拒绝策略，常见的拒绝策略有直接拒绝、排队等待等。


👉 [java如何进行限流？](https://www.zhihu.com/question/482724391/answer/2389749712)


#### Dubbo的设计和完整调用过程(要详细)
// todo


#### ES的脑裂问题怎么解决
**什么是脑裂现象**


在ElasticSearch集群初始化或者主节点宕机的情况下，由候选主节点中选举其中⼀个作为主节点。指定候选主节点的配置为`node.master: true`。当主节点负载压力过大，或者集群环境中的网络问题，导致其他节点与主节点通讯的时候，主节点没来及响应，这样的话，某些节点就认为主节点宕机，重新选择新的主节点，这样的话整个集群的工作就有问题了，比如我们集群中有10个节点，其中7个候选主节点，1个候选主节点成为了主节点，这种情况是正常的情况。但是如果现在出现了我们上面所说的主节点响应不及时，导致其他某些节点认为主节点宕机而重选主节点，那就有问题了，这剩下的6个候选主节点可能有3个候选主节点去重选主节点，最后集群中就出现了两个主节点的情况，这种情况官方成为“脑裂现象”。集群中不同的节点对于master的选择出现了分歧，出现了多个master竞争，导致主分片和副本的识别也发生了分歧，把一些分歧中的分片标识为了坏片。


|脑裂问题成因|解释|
| :----- | :----- |
|<div style="width: 100px">网络问题</div>|集群间的网络延迟导致一些节点访问不到master，认为master挂掉了从而选举出新的master，并对master上的分片和副本标红，分配新的主分片。|
|<div style="width: 100px">节点负载</div>|主节点的角色既为master又为data，访问量较大时可能会导致ES停止响应造成大面积延迟，此时其他节点得不到主节点的响应认为主节点挂掉了，会重新选取主节点。|
|<div style="width: 100px">内存回收</div>|data节点上的ES进程占用的内存较大，引发JVM的大规模内存回收，造成ES进程失去响应。|


|解决方案|解释|
| :----- | :----- |
|<div style="width: 100px">角色分离</div>|即master节点与data节点分离，限制角色；数据节点时需要承担存储和搜索的工作的，压力会很大。所以如果该节点同时作为候选主节点和数据节点，那么一旦选上它作为主节点了，这时主节点的工作压力将会非常大，出现脑裂现象的概率就增加了。|
|<div style="width: 100px">减少误判</div>|配置主节点的响应时间，在默认情况下，主节点3秒没有响应，其他节点就认为主节点宕机了，那我们可以把该时间设置得长一点，该配置是discovery.zen.ping_timeout:5。|
|<div style="width: 100px">选举触发</div>|discovery.zen.minimum_master_nodes:1(默认是1)，该属性定义的是为了形成一个集群，有主节点资格并互相连接的节点的最小数目。一个有10节点的集群，且每个节点都有成为主节点的资格，discovery.zen.minimum_master_nodes参数设置为6。正常情况下，10个节点，互相连接，大于6，就可以形成一个集群。若某个时刻，其中有3个节点断开连接。剩下7个节点，大于6，继续运行之前的集群。而断开的3个节点，小于6，不能形成一个集群。该参数就是为了防止脑裂的产生，建议设置为(候选主节点数/2)+1。|


👉 [ES脑裂问题分析及优化](https://blog.csdn.net/BruceLiu_code/article/details/110467660)


### 得物


#### new一个对象的过程发生了什么
![](/images/ReviewV/ClassLoad2.png)


1. 当虚拟机遇到一条new指令时，会去检查这个指令的参数能否在常量池中定位到一个类的符号引用，并检查代表的类是否已经被类加载器加载。如果没有被加载那么必须先执行这个类的加载。
2. 类加载检查通过后，虚拟机将为新对象分配内存，对象所需内存的大小在类加载后便可以确定。
3. 内存分配完成后，虚拟机需要将对象初始化为零值，保证对象的实例变量在代码中不赋初始值就能直接使用。类变量在类加载的准备阶段初始化为零值。
4. 对对象头进行必要信息的设置，比如如何找到类的元数据信息、对象的HashCode、GC分代年龄等。
5. 经过上述操作，一个新的对象已经产生，但是<init>方法还没有执行，所有的字段都是零值。这时候需要执行<init>方法(构造方法)把对象按照程序员的意愿进行初始化。类变量的初始化操作在类加载的初始化阶段<clinit>方法完成。


👉 [new一个对象的时候发生了什么？](https://blog.csdn.net/JavaShark/article/details/125300803)


#### Spring循环引用解决的原理是什么？
👉 [Spring循环依赖原理，如何解决？](https://baijiahao.baidu.com/s?id=1684246101101128706&wfr=spider&for=pc)


#### FactoryBean和BeanFactory区别
```java
public interface FactoryBean<T> {
	
    String OBJECT_TYPE_ATTRIBUTE = "factoryBeanObjectType";
    
	// 从factory中获取bean
	@Nullable
	T getObject() throws Exception;

	// 从beanFactory中获取类型
	@Nullable
	Class<?> getObjectType();

	// 是单例？
	default boolean isSingleton() {
		return true;
	}
    
}
```


```java
package com.example.demo.domian;

import org.springframework.beans.factory.FactoryBean;
import org.springframework.stereotype.Component;

@Component
public class MyFactoryBean implements FactoryBean {
	
    // 保存一句话，用来区分不同的对象。  
    private String message;
    // 无参构造器。
    public MyFactoryBean() {
        // 意思是：当前对象是 MyFactoryBean 的对象。
        this.message = "object of myFactoryBeanSelf";
    }
    // 有参构造器。
    public MyFactoryBean(String message) {
        this.message = message;
    }
    // 获取 message。
    public String getMessage() {
        return this.message;
    }
    
    @Override
    /**
    *  这个方法在执行时创建了新的 MyFactoryBean 类型的对象。
    *  这里继续沿用了 MyFactoryBean 类型，但是可以是别的类型
    *  比如：Person、Car、等等。
    */
    public Object getObject() throws Exception {
      // 意思是：当前对象是 MyFactoryBean 的 getObject() 创建的。
        return new MyFactoryBean("object from getObject() of MyFactoryBean");
    }
    
    @Override
    public Class<?> getObjectType() {
        return MyFactoryBean.class
    }
}
```


```java
package com.example.demo.domian;

import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = com.example.demo.domian.MyFactoryBean.class)
class MyFactoryBeanTest {
    @Autowired
    private ApplicationContext context;

    @Test
    public void test() {
        // 第一次用 myFactoryBean 去拿。  
        MyFactoryBean myBean1 = (MyFactoryBean) context.getBean("myFactoryBean");
        System.out.println("myBean1 = " + myBean1.getMessage());
        // 第二次用 &myFactoryBean 去拿。  
        MyFactoryBean myBean2 = (MyFactoryBean) context.getBean("&myFactoryBean");
        System.out.println("myBean2 = " + myBean2.getMessage());、
        // 判断两次拿到的对象是不是一样的？    
        System.out.println("myBean1.equals(myBean2) = " + myBean1.equals(myBean2));
    }
}
```


```
myBean1 = object from getObject() of MyFactoryBean
myBean2 = object of myFactoryBeanSelf
myBean1.equals(myBean2) = false
```


第一次使用myFactoryBean去容器中拿，实际上是容器中MyFactoryBean的bean调用了getObject()方法，并将结果返回。第二次使用&myFactoryBean去容器中拿，才是真正拿到了MyFactoryBean的bean。


👉 [BeanFactory和FactoryBean的区别](https://blog.csdn.net/yy_diego/article/details/115710104)


#### Synchronized原理？
```java
class Test1{
    public synchronized void test() {
    }
}

//等价于
class Test1{
    public void test() {
        // 锁的是当前对象
        synchronized(this) {
        }
    }
}
```


```java
class Test2{
    public synchronized static void test() {
    }
}
 
//等价于
class Test2{
    public static void test() {
        // 锁的是类对象，类对象只有一个
        synchronized(Test2.class) {
        }
    }
}
```


每个Java对象都可以关联一个Monitor对象，如果使用synchronized给对象上锁(重量级)之后，该对象头的Mark Word中就被设置指向Monitor对象的指针。不加synchronized的对象不会关联监视器。


![](/images/ReviewV/Monitor.png)


1. 刚开始Monitor中Owner为null。
2. 当Thread-2执行synchronized(obj)就会将Monitor的所有者Owner置为Thread-2，Monitor中只能有一个Owner。
3. 在Thread-2上锁的过程中，如果Thread-3、Thread-4、Thread-5也来执行synchronized(obj)，就会进入EntryList BLOCKED。
4. Thread-2执行完同步代码块的内容，然后唤醒EntryList中等待的线程来竞争锁，竞争的时是非公平的。
5. 图中WaitSet中的Thread-0、Thread-1是之前获得过锁，但条件不满足进入WAITING状态的线程。


👉 [synchronized原理](https://blog.csdn.net/qq_34416191/article/details/119714263)


#### CAS volatile原理？
当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量值刷新到主内存。当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效，线程接下来将从主内存中读取共享变量。


#### 内存模型？什么是主内存？什么是工作内存？
| 内存 | 解释 | 
| :----- | :----- | 
|主内存|是所有的线程所共享的，主要包括本地方法区和堆。|
|工作内存|每个线程都有一个工作内存不是共享的，工作内存中主要包括两个部分: <br>1. 一个是属于该线程私有的栈；<br>2. 对主内存部分变量拷贝的寄存器(包括程序计数器PC和CPU工作的高速缓存区)。|


1. 所有的变量都存储在主内存中(虚拟机内存的一部分)，对于所有线程都是共享的。
2. 每条线程都有自己的工作内存，工作内存中保存的是主存中某些变量的拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存中的变量。
3. 线程之间无法直接访问对方的工作内存中的变量，线程间变量的传递均需要通过主内存来完成,即：线程、主内存、工作内存。


#### 数据库索引类型？原理？
![](/images/ReviewV/Index.jpg)


#### Spring Bean生命周期？
![](/images/ReviewV/SpringBeanLifeCycle.jpg)


1. 通过XML、Java Annotation(注解)以及Java Configuration(配置类)等方式加载Spring Bean。
2. BeanDefinitionReader解析Bean的定义。在Spring容器启动过程中，会将Bean解析成Spring内部的BeanDefinition结构；理解为将spring.xml中的<bean>标签转换成BeanDefinition结构，有点类似于XML解析。
3. BeanDefinition包含了很多属性和方法。例如id、class(类名)、scope、ref(依赖的bean)等等。其实就是将bean(例如<bean>)的定义信息存储到这个对应BeanDefinition相应的属性中。例如<bean id="" class="" scope="">
4. BeanFactoryPostProcessor是Spring容器功能的扩展接口。
    1. BeanFactoryPostProcessor在spring容器加载完BeanDefinition之后，在bean实例化之前执行的。
    2. 对bean元数据(BeanDefinition)进行加工处理，也就是BeanDefinition属性填充、修改等操作。
5. BeanFactory，bean工厂。它按照我们的要求生产我们需要的各种各样的bean。
6. Aware感知接口，在实际开发中，经常需要用到Spring容器本身的功能资源例如BeanNameAware、ApplicationContextAware等等，BeanDefinition实现了BeanNameAware、ApplicationContextAware。
7. BeanPostProcessor后置处理器。在Bean对象实例化和引入注入完毕后，在显示调用初始化方法的前后添加自定义的逻辑(类似于AOP的绕环通知)。前提条件如果检测到Bean对象实现了BeanPostProcessor后置处理器才会执行Before和After方法。
    1. Before
    2. 调用初始化Bean，InitializingBean和init-method，Bean的初始化才算完成。
    3. After
8. destroy销毁。


#### Mysql优化经验？
1. 引擎选择(InnoDB、MyISAM、Memory)
2. 读写分离&一主多备
3. 分库分表
4. 优化连接池
5. 优化请求堆栈
6. 修改连接超时时间
7. 优化内存缓冲池
8. 优化并发线程数
9. 优化线程池
10. 优化日志
    1. 错误日志: 启动、关闭、运行时产生的异常记录，建议开启，设置log_error。
    2. 查询日志: 客户端连接和执行的脚本，建议关闭，设置general_log。
    3. 慢查询日志: 记录超时的查询，记录不适用索引的查询等，建议关闭，设置slow_query_log。
    4. 二进制日志: 用于数据同步复制，需发送的数据日志，多用于集群，如需开启，设置log_bin。
    5. 中继日志: 用于数据同步复制时，接收到的数据日志，多用于集群，如需开启，设置relay_log。
11. 锁优化


👉 [MySQL性能优化经验总结](https://www.jianshu.com/p/3217b537b63f)


#### Mysql锁类型？
MySQL数据库的锁都是悲观锁。


1. 读锁(共享锁)
    1. lock in share mode
2. 写锁(排他锁)
    1. MDL语句，例如update、delete、insert
    2. select ... for update
    
    
👉 [MySQL悲观锁](https://blog.csdn.net/weixin_42798851/article/details/115388662)


#### Redis使用过程中应该注意什么问题？
1. key的长度不要太长，也不要太短，要符合设计规约，value的大小要防止big key的发生。
2. hash更适合存储对象。
3. sort的集合如果非常大的话，排序就会消耗很长时间，由于redis是单线程的，所以长时间的排序操作会阻塞其他client的请求，解决办法是通过主从复制将数据复制到多个slave上，然后只在slave上做排序操作。
4. 如果你的redis集群需要做主从复制，最好事先配置好所有的从库，避免中途再去添加从库(一方面slave恢复的时间非常慢，另一方面也会给主库带来压力)。
5. 尽量避免在压力较大的主库上增加从库。
6. 如果服务数据压力过大，可以增加多台服务器节点。
7. master最好不要做任何持久化工作，包括内存快照和AOF日志文件，特别是不要启用内存快照做持久化。
8. 为了主从复制的速度和连接多稳定性，slave和master最好在同一个局域网内。


#### JVM调优参数？
| 参数 | 解释 |
| :----- | :----- |
|-Xms|设置堆的最小空间大小。|
|-Xmx|设置堆的最大空间大小。|
|-Xmn|设置新生代大小。|
|-XX:NewSize|设置新生代最小空间大小。|
|-XX:MaxNewSize|设置新生代最大空间大小。|
|-XX:PermSize|设置永久代最小空间大小。|
|-XX:MaxPermSize|设置永久代最大空间大小。|
|-Xss|设置每个线程的堆栈大小。|
|-XX:+UseParallelGC|选择垃圾收集器为并行收集器。此配置仅对年轻代有效。即上述配置下年轻代使用并发收集，而年老代仍旧使用串行收集。|
|-XX:ParallelGCThreads=20|配置并行收集器的线程数，即同时多少个线程一起进行垃圾回收。此值最好配置与处理器数目相等。|


```
java -Xmx3550m -Xms3550m -Xmn2g -Xss128k -XX:ParallelGCThreads=20 -XX:+UseConcMarkSweepGC -XX:+UseParNewGC
```


| 参数 | 解释 |
| :----- | :----- |
|-Xms3550m|设置JVM促使内存为3550m。此值可以设置与-Xmx相同，以避免每次垃圾回收完成后JVM重新分配内存。|
|-Xmn2g|设置年轻代大小为2G。堆=年轻代+年老代+持久代。持久代一般固定大小为64m，所以增大年轻代后，将会减小年老代大小。此值对系统性能影响较大，官方推荐配置为整个堆的3/8。|
|-Xss128k|设置每个线程的堆栈大小。JDK5.0以后每个线程堆栈大小为1M，以前每个线程堆栈大小为256K。更具应用的线程所需内存大小进行调整。在相同物理内存下，减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。|


#### 线程池原理？属性代表含义？
// todo


#### HashMap、ConcurrentHashMap原理？
// todo


### 饿了么


#### 项目介绍，怎么不断优化项目、架构升级？如果业务量剧增，怎么保证系统高可用、扩展性？
// todo


#### 订单量、日新增多少？分库分表怎么做？基于什么维度去做？
// todo


#### 检测到JVM内存大于配置JVM的xmx配置的内存， 三台机器中的一台机器有上面这种现象，如何解释？
![](/images/ReviewV/JVMMemory.png)


Java进程最大占用的物理内存为:


```
Max Memory = eden + survivor + old + String Constant Pool + Code cache + compressed class space + Metaspace + Thread stack(*thread num) + Direct + Mapped + JVM + Native Memory
```


Java进程的内存组成`heap+stack+metaspaceSize+directMemory`。除了通过-Xmx4g、-Xms4g参数控制程序启动的堆内存外，不要忽视-Xss1024K控制每个stack的大小。元空间限制-XX:MetaspaceSize=64m、-XX:MaxMetaspaceSize=128m。直接内存使用限制-XX:MaxDirectMemorySize=128m。


👉 [JVM实际内存占用超过Xmx的原因,设置Xmx的技巧](https://blog.csdn.net/ruanchengshen/article/details/121291173)


#### Redis热key怎么解决？
所谓热key问题就是，突然有几十万的请求去访问redis上的某个特定key。那么这样会造成流量过于集中，达到物理网卡上限，从而导致这台redis的服务器宕机。那接下来这个key的请求，就会直接怼到你的数据库上，导致你的服务不可用。


1. 凭借业务经验，进行预估哪些是热key。其实这个方法还是挺有可行性的。比如某商品在做秒杀，那这个商品的key就可以判断出是热key。缺点很明显，并非所有业务都能预估出哪些key是热key。
2. 在客户端进行收集。这个方式就是在操作redis之前，加入一行代码进行数据统计。那么这个数据统计的方式有很多种，也可以是给外部的通讯系统发送一个通知信息。缺点就是对客户端代码造成入侵。
3. 在Proxy层做收集。有些集群架构是下面这样的，Proxy可以是Twemproxy，是统一的入口。可以在Proxy层做收集上报，但是缺点很明显，并非所有的redis集群架构都有proxy。
4. 用redis自带命令。
    1. monitor命令，该命令可以实时抓取出redis服务器接收到的命令，然后写代码统计出热key是啥。当然，也有现成的分析工具可以给你使用，比如redis-faina。但是该命令在高并发的条件下，有内存增暴增的隐患，还会降低redis的性能。
    2. hotkeys参数，redis4.0.3提供了redis-cli的热点key发现功能，执行redis-cli时加上–hotkeys选项即可。但是该参数在执行的时候，如果key比较多，执行起来比较慢。
5. 自己抓包评估。Redis客户端使用TCP协议与服务端进行交互，通信协议采用的是RESP。自己写程序监听端口，按照RESP协议规则解析数据，进行分析。缺点就是开发成本高，维护困难，有丢包可能性。


1. 利用二级缓存。比如利用ehcache或者一个HashMap都可以。在你发现热key以后，把热key加载到系统的JVM中。针对这种热key请求，会直接从JVM中取，而不会走到redis层。假设此时有十万个针对同一个key的请求过来，如果没有本地缓存，这十万个请求就直接怼到同一台redis上了。现在假设你的应用层有50台机器，那么你也有JVM缓存了。这十万个请求平均分散开来，每个机器有2000个请求，会从JVM中取到value值，然后返回数据。避免了十万个请求怼到同一台redis上的情形。
2. 备份热key。不要让key走到同一台redis上不就行了。我们把这个key在多个redis上都存一份。接下来有热key请求进来的时候，我们就在有备份的redis上随机选取一台，进行访问取值返回数据。 


#### Kafka为什么性能高？
1. 基于磁盘的顺序读写。顺序写利用磁盘的顺序访问速度可以接近内存，kafka的消息都是append操作，partition是有序的，节省了磁盘的寻道时间，同时通过批量操作、节省写入次数，partition物理上分为多个segment存储，方便删除。
2. 页缓存(Page Cache)。对于一个进程来说，他会在进程内部缓存所需要的数据，然而这些数据很有可能也缓存在操作系统的页缓存中，也就是这一部分数据被缓存了两次。就算kafka服务重启，页缓存内的数据也还是存在，但进程内的数据则需要重新加载。这也在一定程度上能简化代码，而且维护页缓存和文件的一致性问题交给操作系统完成会比进程内维护要更加的安全高效。
3. 零拷贝。直接将内核缓冲区的数据发送到网卡传输。


![](/images/ReviewV/DiskRead.png)


👉 [Kafka为什么能那么快的6个原因](https://blog.csdn.net/Java0258/article/details/107663998)


#### OOM场景分析？
![](/images/ReviewV/OOM.jpg)


1. StackOverflowError
2. Java heap space
    1. 内存溢出，是指程序在申请内存时，没有足够的内存空间供其使用，出现out of memory；比如申请了一个Integer，但给它存了Long才能存下的数，那就是内存溢出。
    2. 内存泄露，是指程序在申请内存后，无法释放已申请的内存空间，一次内存泄露危害可以忽略，但内存泄露堆积后果很严重，无论多少内存，迟早会被占光。
3. GC overhead limit exceeded
4. Direct buffer memory
5. Unable to create new native thread
6. Metaspace
7. Requested array size exceeds VM limit
8. Out of swap space
9. Kill process or sacrifice child


👉 [java 9种常见的OOM场景——原因分析及解决方案](https://blog.csdn.net/zhangkaixuan456/article/details/111904430)


#### Mysql集群是怎么部署的，主从同步？
从mysql和主mysql的配置基本相同,除了以下几个配置: 


1. 从机server-id要和主机不同
    1. server-id=2
    2. log_slave_updates=1
2. 开启同步函数
    3. log_bin_trust_function_creators=1
    
    
#### Mysql双主相互备份是如何解决循环复制的？
假设A、B相互备份，A产生的binlog中携带serverId=1发送到B，B解析后执行，执行完毕也会产生binlog。此时B的binlog serverId仍然等于1，发送给A后，A看到serverId=1为自己发送的binlog，直接丢弃；同理B方向类似。


#### 怎么设置使用什么GC方式？不同年代GC收集器有哪些？
// todo


#### 线上CPU很高怎么排查
![](/images/ReviewV/CPUQuestion.png)


|jstack参数|解释|
| :----- | :----- |
|tid|java内的线程id|
|nid|操作系统级别线程的线程id|
|prio|java内定义的线程的优先级|
|os_prio|操作系统级别的优先级|


```
# 查看CPU个数
cat /proc/cpuinfo| grep "physical id"|sort|uniq|wc -l

# 获取最大CPU的PID
top -c (键盘输入大P)
```


```
# 获取最高CPU的服务
ps -ef|grep PID
```


```
# 获取最大PID中的最大NID
top -Hp PID (键盘输入大P)
```


```
# NID转换成16进制
printf "%x\n" NID
```


```
# 打印堆栈信息，并从jstack.txt寻找16进制NID
jstack -l PID > jstack.txt
或者
jstack -l PID | grep ${16进制NID} -A 100
```


👉 [记录一次线上CPU负载过高的排查过程](https://blog.csdn.net/I_peter/article/details/123543297)


👉 [线上占用CPU过高问题排查](https://blog.csdn.net/liuzhiyong0718/article/details/104862039)


#### JDK1.8的新特性
// todo


#### BIO、NIO了解
// todo


#### MQ怎么保证消息可靠性？
// todo


#### 系统负载过高怎么办？什么问题导致的？怎么排查？
1. top -c。输入P获取PID。
2. top -Hp PID。输入P获取NID。
3. printf "%x\n" NID。输入NID获取16进制NID。
4. jstack -l PID | grep 16进制NID -A 100。定位异常代码。


#### Linux操作系统简单介绍有哪些东西？
// todo


### 中通


#### JVM介绍
👉 [最全的JVM介绍](https://blog.csdn.net/m0_68850571/article/details/124169541)


#### JMM模型
![](/images/ReviewV/JMM.png)


此内存模型中规定了，所有的共享变量都是存储于主内存中，每个线程都是将主内存中的共享变量拷贝一份副本到本线程的本地内存中，然后操作此共享变量副本，修改后再同步更新到主内存中，因此高并发下就会出现变量修改的问题了。


![](/images/ReviewV/JMM8.png)


| 操作 | 解释 | 
| :----- | :----- | 
|<div style='width: 100px'>lock (锁定)</div>|将主内存变量加锁，表示为线程独占状态，可以被线程进行read。|
|<div style='width: 100px'>read(读取)</div>|线程从主内存读取数据。|
|<div style='width: 100px'>load(载入)</div>|将上一步线程从主内存中读取的数据，加载到工作内存中。|
|<div style='width: 100px'>use(使用)</div>|从工作内存中读取数据来进行我们所需要的逻辑计算。|
|<div style='width: 100px'>assign(复制)</div>|将计算后的数据赋值到工作内存中。|
|<div style='width: 100px'>store(存储)</div>|将工作内存的数据准备写入主内存。|
|<div style='width: 100px'>write(写入)</div>|将store过去的变量正式写入主内存。|
|<div style='width: 100px'>unlock(解锁)</div>|将主内存的变量解锁，解锁后其他线程可以锁定该变量。|


#### GC Root有哪些？
1. 虚拟机内部的引用，比如类加载器等；
2. native，本地方法栈引用的对象(在本地方法栈)；
3. final，常量引用的对象(比如字符串常量池的引用，在方法区)；
4. static，静态变量引用的对象(比如Java类的引用类型静态变量，在方法区)；
5. synchronized引用的对象(所有被同步锁持有的对象，在堆里)；
6. JVM虚拟机栈引用的对象(比如各个线程被调用的方法堆栈中用到的参数、局部变量和临时变量，在jvm虚拟机栈中)；
7. Thread，活动的线程；
8. Class对象，由BootstrapClassLoader加载的对象是不能被回收的。


栈是由栈帧组成，每当线程调用一个Java方法时，JVM就会在该线程对应的栈中压入一个帧，而帧是由局部变量区、操作数栈和帧数据区组成。


**为什么要有GC root**


1. 为了回收无用的对象，释放内存，需要把无用对象找到；找无用的不好找，反过来找有用的对象就比较好找；
2. gc root就是用来标记有引用关系的对象，这些在gc root引用关系链上的强引用对象都是不能回收；
3. 一般对象都放在堆里的，所以垃圾回收的重点是放在了堆里。所以堆之外的栈，方法区就是gc root对象所在的地方。


#### JVM调优经验？
调优是一个循序渐进的过程，必然需要经历多次迭代，最终才能换取一个较好的折中方案。在JVM调优这个领域，没有任何一种调优方案是适用于所有应用场景的，同时切勿极端才能够达到JVM性能调优的真正目的和意义。


1. GC的时间足够的小。
2. GC的次数足够的少。
3. 发生Full GC的周期足够的长。
4. 前两个目前是相悖的，要想GC时间小必须要一个更小的堆，要保证GC次数足够少，必须保证一个更大的堆，我们只能取其平衡。
5. 更大的年轻代必然导致更小的年老代，大的年轻代会延长普通GC的周期，但会增加每次GC的时间；小的年老代会导致更频繁的Full GC。
6. 更小的年轻代必然导致更大年老代，小的年轻代会导致普通GC很频繁，但每次的GC时间会更短；大的年老代会减少Full GC的频率。
7. 如何选择应该依赖应用程序对象生命周期的分布情况: 
    1. 如果应用存在大量的临时对象，应该选择更大的年轻代。
    2. 如果存在相对较多的持久对象，年老代应该适当增大。
8. 但很多应用都没有这样明显的特性，在抉择时应该根据以下两点: 
    1. 本着Full GC尽量少的原则，让年老代尽量缓存常用对象，JVM的默认比例3:8也是这个道理。
    2. 通过观察应用一段时间，看其他在峰值时年老代会占多少内存，在不影响FullGC的前提下，根据实际情况加大年轻代，比如可以把比例控制在1:1，但应该给年老代至少预留1/3的增长空间。


| 调优策略 | 解释 | 
| :----- | :----- | 
|<div style='width: 120px'>将新对象预留在年轻代</div>|众所周知，由于Full GC的成本远远高于Minor GC，因此某些情况下需要尽可能将对象分配在年轻代，这在很多情况下是一个明智的选择。虽然在大部分情况下，JVM会尝试在Eden区分配对象，但是由于空间紧张等问题，很可能不得不将部分年轻对象提前向年老代压缩。因此，在JVM参数调优时可以为应用程序分配一个合理的年轻代空间，以最大限度避免新对象直接进入年老代的情况发生。通过设置一个较大的年轻代预留新对象，设置合理的Survivor区并且提供Survivor区的使用率，可以将年轻对象保存在年轻代。一般来说，Survivor区的空间不够，或者占用量达到50%时，就会使对象进入年老代(不管它的年龄有多大)我们可以尝试加上-XX:TargetSurvivorRatio=90参数，这样可以提高from区的利用率，使from区使用到90%时，再将对象送入年老代。|
|<div style='width: 120px'>让大对象进入年老代</div>|我们在大部分情况下都会选择将对象分配在年轻代。但是，对于占用内存较多的大对象而言，它的选择可能就不是这样的。因为大对象出现在年轻代很可能扰乱年轻代GC，并破坏年轻代原有的对象结构。因为尝试在年轻代分配大对象，很可能导致空间不足，为了有足够的空间容纳大对象，JVM不得不将年轻代中的年轻对象挪到年老代。因为大对象占用空间多，所以可能需要移动大量小的年轻对象进入年老代，这对GC相当不利。基于以上原因，可以将大对象直接分配到年老代，保持年轻代对象结构的完整性，这样可以提高GC的效率。如果一个大对象同时又是一个短命的对象，假设这种情况出现很频繁，那对于GC来说会是一场灾难。原本应该用于存放永久对象的年老代，被短命的对象塞满，这也意味着对堆空间进行了洗牌，扰乱了分代内存回收的基本思路。因此，在软件开发过程中，应该尽可能避免使用短命的大对象。可以使用参数-XX:PetenureSizeThreshold设置大对象直接进入年老代的阈值。当对象的大小超过这个值时，将直接在年老代分配。参数-XX:PetenureSizeThreshold只对串行收集器和年轻代并行收集器有效，并行回收收集器不识别这个参数。|
|<div style='width: 120px'>设置对象进入年老代的年龄</div>|如何设置对象进入年老代的年龄堆中的每一个对象都有自己的年龄。一般情况下，年轻对象存放在年轻代，年老对象存放在年老代。为了做到这点，虚拟机为每个对象都维护一个年龄。如果对象在Eden区，经过一次GC后依然存活，则被移动到Survivor区中，对象年龄+1。以后如果对象每经过一次GC依然存活，则年龄再+1。当对象年龄达到阈值时，就移入年老代，成为老年对象。这个阈值的最大值可以通过参数-XX:MaxTenuringThreshold来设置，默认值是15。虽然-XX:MaxTenuringThreshold的值可能是15或者更大，但这不意味着新对象非要达到这个年龄才能进入年老代。事实上，对象实际进入年老代的年龄是虚拟机在运行时根据内存使用情况动态计算的，这个参数指定的是阈值年龄的最大值。即实际晋升年老代年龄等于动态计算所得的年龄与-XX:MaxTenuringThreshold中较小的那个。|
|<div style='width: 120px'>稳定的Java堆VS动荡的Java堆</div>|一般来说，稳定的堆大小对垃圾回收是有利的。获得一个稳定的堆大小的方法是使-Xms和-Xmx的大小一致，即最大堆和最小堆 (初始堆)一样。如果这样设置，系统在运行时堆大小理论上是恒定的，稳定的堆空间可以减少GC的次数。因此，很多服务端应用都会将最大堆和最小堆设置为相同的数值。但是，一个不稳定的堆并非毫无用处。稳定的堆大小虽然可以减少GC次数，但同时也增加了每次GC的时间。让堆大小在一个区间中震荡，在系统不需要使用大内存时，压缩堆空间，使GC应对一个较小的堆，可以加快单次GC的速度。基于这样的考虑，JVM还提供了两个参数用于压缩和扩展堆空间。-XX:MinHeapFreeRatio参数用来设置堆空间最小空闲比例，默认值是40。当堆空间的空闲内存小于这个数值时，JVM便会扩展堆空间。-XX:MaxHeapFreeRatio参数用来设置堆空间最大空闲比例，默认值是70。当堆空间的空闲内存大于这个数值时，便会压缩堆空间，得到一个较小的堆。当-Xmx和-Xms相等时，-XX:MinHeapFreeRatio和-XX:MaxHeapFreeRatio两个参数无效。|
|<div style='width: 120px'>增大吞吐量提升系统性能</div>|吞吐量优先的方案将会尽可能减少系统执行垃圾回收的总时间，故可以考虑关注系统吞吐量的并行回收收集器。在拥有高性能的计算机上，进行吞吐量优先优化。可以使用参数: java –Xmx3800m –Xms3800m –Xmn2G –Xss128k –XX:+UseParallelGC –XX:ParallelGC-Threads=20 –XX:+UseParallelOldGC。|
|<div style='width: 120px'>尝试使用大的内存分页</div>|CPU是通过寻址来访问内存的。32位CPU的寻址宽度是0~0xFFFFFFFF，计算后得到的大小是4G，也就是说可支持的物理内存最大是4G。但在实践过程中碰到了这样的问题，程序需要使用4G内存，而可用物理内存小于4G，导致程序不得不降低内存占用。为了解决此类问题，现代CPU引入了MMU(Memory Management Unit内存管理单元)。MMU的核心思想是利用虚拟地址替代物理地址，即CPU寻址时使用虚址，由MMU负责将虚址映射为物理地址。MMU的引入解决了对物理内存的限制，对程序来说就像自己在使用4G内存一样。内存分页(Paging)是在使用MMU的基础上，提出的一种内存管理机制。它将虚拟地址和物理地址按固定大小(4K)分割成页(page)和页帧(page frame)，并保证页与页帧的大小相同。这种机制从数据结构上保证了访问内存的高效，并使OS能支持非连续性的内存分配。在程序内存不够用时，还可以将不常用的物理内存页转移到其他存储设备上，比如磁盘，这就是大家耳熟能详的虚拟内存。在Solaris系统中，JVM可以支持Large Page Size的使用。使用大的内存分页可以增强CPU的内存寻址能力，从而提升系统的性能。java –Xmx2506m –Xms2506m –Xmn1536m –Xss128k –XX:++UseParallelGC –XX:ParallelGCThreads=20 –XX:+UseParallelOldGC –XX:+LargePageSizeInBytes=256m。过大的内存分页会导致JVM在计算Heap内部分区(perm、new、old)内存占用比例时，会出现超出正常值的划分，最坏情况下某个区会多占用一个页的大小。|
|<div style='width: 120px'>使用非占有的垃圾回收器</div>|为降低应用软件的垃圾回收时的停顿，首先考虑的是使用关注系统停顿的CMS回收器，其次为了减少Full GC次数，应尽可能将对象预留在年轻代，因为年轻代Minor GC的成本远远小于年老代的Full GC。|


👉 [JVM性能调优经验总结](https://blog.csdn.net/javalingyu/article/details/124517212)


#### JVM常用命令
1. jps [-q] [-mlvV] ${PID}
2. jinfo -flags ${PID}
3. jstat -gc ${PID} ${多少毫秒打印一次} ${打印多少次}
4. jstack -l ${PID}
5. jmap -heap ${PID}、jmap -histo ${PID}、jmap -dump:format=b,file=xxx.dump ${PID}


#### 线程池注意事项，异常处理
* **Runnable执行异常(业务异常)**


```java
public class ThreadPoolExceptionTest {
 
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(2, new ThreadFactory() {
            AtomicInteger integer = new AtomicInteger(1);
            @Override
            public Thread newThread(Runnable r) {
                return new Thread(r, "mxsm-"+integer.getAndIncrement());
            }
        });
        executorService.execute(() -> {
            System.out.println(1);
            int i = 1/0;
            System.out.println(i);
        });
        executorService.execute(() -> {
            for (;;){
                try {
                    TimeUnit.SECONDS.sleep(1);
                    System.out.println(Thread.currentThread().getName()+" 当前时间："+System.currentTimeMillis());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        System.out.println("主线程执行完成");
    }
    
}
```


```
1
主线程执行完成
Exception in thread "mxsm-1" java.lang.ArithmeticException: / by zero
mxsm-2 当前时间：1656145034793
mxsm-2 当前时间：1656145035800
......
```


线程池正常运行，Runnable的异常不会导致线程池停止运行，其他的线程正常运行。执行Runnable发生错误的线程将会被销毁会重新建一个线程，以保证固定线程池2的数量。



* **提交任务到任务队列已满异常**


```java
public class ThreadPoolExceptionTest {
 
    public static void main(String[] args) {
        ExecutorService executorService = new ThreadPoolExecutor(1, 1, 100, TimeUnit.SECONDS,new ArrayBlockingQueue<>(1),new ThreadFactory(){
            AtomicInteger integer = new AtomicInteger(1);
            @Override
            public Thread newThread(Runnable r) {
                return new Thread(r, "mxsm-"+integer.getAndIncrement());
            }
        });
        for(int i = 0; i < 3; ++i){
            final int b = i;
            executorService.execute(() -> {
                for (;;){
                    try {
                        TimeUnit.SECONDS.sleep(1);
                        System.out.println(Thread.currentThread().getName()+ b +" 当前时间："+System.currentTimeMillis());
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
        }
        System.out.println("主线程结束");
    }
}
```


```
Exception in thread "main" java.util.concurrent.RejectedExecutionException:......
mxsm-10 当前时间：1656145315473
mxsm-10 当前时间：1656145316474
......
```


线程池使用默认的拒绝策略的时候，当线程池提交任务到任务队列已满线程池会直接抛出错误，进而影响到主线程的后续的运行如果没有在主线程中进行错误处理(没有打印主线程结束)。提交任务到任务队列已满异常影响的范围和方式由拒绝策略决定。



* **线程池本身异常**


这里说的线程池本身异常包括但不仅限于在设置线程池大小的时候，可能不停的新建线程导致线程消耗完成了服务器的所有资源。


```java
/**
 *
 * 设置内存大小
 * -Xmx2m
 * -Xms2m
 *
 */
public class ThreadPoolExceptionTest {
 
    public static void main(String[] args) {
 
        ExecutorService executorService = Executors.newCachedThreadPool();
        final AtomicInteger integer = new AtomicInteger();
        for(int i = 0;i <= 100000; ++i){
            final  int b = i;
            executorService.submit(new Runnable() {
                @Override
                public void run() {
                    try {
                        System.out.println(integer.getAndIncrement());
                        TimeUnit.SECONDS.sleep(b);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
        }
        System.out.println("主线程结束");
    }
}
```


```
......
83681
83682
83683
[thread 405424 also had an error]
83684
83689
83688
......
```


线程池导致某些异常会导致线程池直接退出可能同时导致住线程或者主应用发生问题或者退出。


👉 [线程池异常如何处理你都了解吗？](https://blog.csdn.net/weixin_45987961/article/details/122783745)


#### 分布式锁使用和原理？
1. 数据库
2. redis
3. zookeeper


#### Redis怎么持久化？高可用？
在web服务器中，高可用是指服务器可以正常访问的时间，衡量的标准是在多长时间内可以提供正常服务(99.9%、99.99%、99.999%等等)。但是在Redis语境中，高可用的含义似乎要宽泛一些，除了保证提供正常服务(如主从分离、快速容灾技术)，还需要考虑数据容量的扩展、数据安全不会丢失等。


1. RDB
2. AOF


👉 [Redis高可用之持久化](https://blog.csdn.net/l688899886/article/details/125266110)


#### RPC框架实现原理？
Dubbo、Spring cloud那一套，GRPC、Thrift等等都是RPC全程远程方法调用，RPC本质上其实就是一次网络调用，那么它的实现原理主要有以下几个步骤。


| 步骤 | 解释 | 
| :----- | :----- | 
|<div style='width: 80px'>建立通信</div>|首先要解决通讯的问题。即A机器想要调用B机器，首先得建立起通信连接。主要是通过在客户端和服务器之间建立TCP连接，远程过程调用的所有相关的数据都在这个连接里面进行传输交换。|
|<div style='width: 80px'>服务寻址</div>|通常情况下我们需要提供B机器(主机名或IP地址)以及特定的端口，然后指定调用的方法或者函数的名称以及入参出参等信息，这样才能完成服务的一个调用。比如基于Web服务协议栈的RPC，就需要提供一个endpoint URI，或者是从UDDI服务上进行查找。如果是RMI调用的话，还需要一个RMI Registry来注册服务的地址。|
|<div style='width: 80px'>网络传输</div>|1. 序列化: 当A机器上的应用发起一个RPC调用时，调用方法和其参入等信息需要通过底层的网络协议如TCP传输到B机器，由于网络协议是基于二进制的，所有我们传输的参数数据都需要先进行序列化(Serialize)或者编组(marshal)成二进制的形式才能在网络中进行传输。然后通过寻址操作和网络传输将序列化或者编组之后的二进制数据发送给B机器。<br>2. 反序列化: 当B机器接收到A机器的应用发来的请求之后，又需要对接收到的参数等信息进行反序列化操作(序列化的逆操作)，即将二进制信息恢复为内存中的表达方式，然后再找到对应的方法(寻址的一部分)进行本地调用(一般是通过生成代理Proxy去调用，通常会有JDK动态代理、CGLIB动态代理、Javassist生成字节码技术等)，之后得到调用的返回值。|                                                                                                                                                        |
|<div style='width: 80px'>服务调用</div>|B机器进行本地调用(通过代理Proxy)之后得到了返回值，此时还需要再把返回值发送回A机器，同样也需要经过序列化操作，然后再经过网络传输将二进制数据发送回A机器，而当A机器接收到这些返回值之后，则再次进行反序列化操作，恢复为内存中的表达方式，最后再交给A机器上的应用进行相关处理(一般是业务逻辑处理操作)。|


![](/images/ReviewV/RPC.png)


#### 接口调用变慢排查
1. 是不是资源层面的瓶颈，硬件、配置环境之类的问题；
2. 针对查询类接口，是不是没有添加缓存，如果加了，是不是热点数据导致负载不均衡；
3. 是不是有依赖于第三方接口，导致因第三方请求拖慢了本地请求；
4. 是不是接口涉及业务太多，导致程序执行跑很久；
5. 是不是sql层面的问题导致的数据等待加长，进而拖慢接口；
6. 网络层面的原因，带宽不足、DNS解析慢；
7. 确实是代码质量差导致的，如出现内存泄漏，重复循环读取之类。


#### 为什么数据库TCP连接会被断开？
一开始百思不得其解，想着是因为Oracle数据库主动断开了连接吗？因为某些原因，比如服务器到数据库的连接太多？明显不是，这个项目还在试运行阶段，用的人不多，且观察Druid的连接监控，一般建立的连接也就几个。


后来和同时讨论的过程中得知别的项目组也发生过类似的情况，而他们和这个项目的共同之处就在于服务都是在DMZ区，外网可以访问，而数据库在内网，需要通过防火墙才能访问到数据库。于是去找负责维护网络、防火墙的同事了解，原来防火墙有一个TCP超时时间，目前设置为30min，其意义是，对于通过防火墙的所有TCP连接，如果半小时内没有任何活动，就会被防火墙拆除，这样就会导致连接中断。在连接拆除时，也不会向连接的两端发送任何数据来通知连接已经拆除。


这下数据库连接断开的原因找到了，那么这就是一个应用与数据库在不同的网络中，连接需要经过防护墙的场景中遇到的一个典型的问题。


```
cat /proc/sys/net/ipv4/tcp_retries2
```


**防火墙切断数据库连接会造成的影响**


数据库会话正在执行耗时长的SQL。切断连接之前，连接对应的Oracle会话正在执行一个耗时特别长的sql，比如存储过程而此过程中没有任何数据输出到客户端，这样当sql执行完成之后，向客户端返回结果时，如果TCP连接已经被防火墙中断，这时候显然会出现错误，连接中断，那么会话也就会中断。但是客户端还不知道，会一直处于等待服务器返回结果的状态。如果客户端没有针对这种执行耗时长的sql的连接回收机制，那么客户端这个连接将一直处于等待状态，如果客户端不断执行这种耗时长的sql，那么客户端堆积的等待连接将越来越多。Druid连接池的removeAbandoned相关配置以及逻辑，就是为了解决这种连接回收设置的。


数据库会话空闲。切断连接之前，Oracle会话一直处于空闲状态，在防火墙中断之后，客户端向Oracle服务器提交sql时，由于TCP连接中断，这时客户端侦测到连接中断，那么客户端就会报ORA-03113/ORA-03114这类错误，然后会话中断。但是在Oracle服务器端，会话一直处于等待客户端消息的状态。而对于Druid这种有testOnBorrow、testWhileIdle的检测机制，且检测失败可以重新连接的连接池，空闲的被防火墙切断的连接在后续会不断被重连，而在数据库服务器端则连接越来越多，即会话数越来越多，甚至最终超过了数据库最大连接数。


|解决方案|解释|
| :----- | :----- |
|调大防火墙的连接切断时长|---|
|TCP keepalive功能|---|
|程序不定时执行查询|---|


![](/images/ReviewV/ResponseSlow.png)


#### 业务系统架构，业务量
// todo


#### 数据库设计，优化方案
![](/images/ReviewV/DatabaseOptimize.png)


1. sql以及索引的优化。
2. 合理的数据库设计。
3. 系统配置的优化。
4. 硬件优化。


👉 [数据库优化方案整理](https://blog.csdn.net/u013628152/article/details/82184809)


### 鱼泡泡(比心)


#### 比较有成就的项目
// todo


#### 清结算怎么实现的？
// todo


#### 统一收银台设计？
// todo


#### RocketMq和Kafka区别，选型？
// todo


#### Kafka消息从生产到消费的流转过程？
1. 写流程
    1. 通过ZooKeeper找partition对应的leader，leader是负责写的；
    2. producer开始写入数据；
    3. ISR里面的follower开始同步数据，并返回给leader ACK；
    4. 返回给producer ACK。
2. 读流程
    1. 通过ZooKeeper找partition对应的leader，leader是负责读的；
    2. 通过ZooKeeper找到消费者对应的offset；
    3. 然后开始从offset往后顺序拉取数据；
    4. 提交offset(自动提交——每隔多少秒提交一次offset，手动提交、放入到事务中提交)。


👉 [Kafka生产、消费数据工作流程](https://blog.csdn.net/weixin_46055693/article/details/124871152)


#### HashMap、HashTable区别？
1. HashMap几乎可以等价于Hashtable，除了HashMap是非synchronized的，并可以接受null(HashMap可以接受为null的键值(key)和值(value)，而Hashtable则不行)。
2. HashMap是非synchronized，而Hashtable是synchronized，这意味着Hashtable是线程安全的，多个线程可以共享一个Hashtable；而如果没有正确的同步的话，多个线程是不能共享HashMap的。Java 5提供了ConcurrentHashMap，它是HashTable的替代，比HashTable的扩展性更好。
3. 另一个区别是HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构(增加或者移除元素)，将会抛出ConcurrentModificationException，但迭代器本身的remove()方法移除元素则不会抛出ConcurrentModificationException异常。但这并不是一个一定发生的行为，要看JVM。这条同样也是Enumeration和Iterator的区别。
4. 由于Hashtable是线程安全的也是synchronized，所以在单线程环境下它比HashMap要慢。如果你不需要同步，只需要单一线程，那么使用HashMap性能要好过Hashtable。
5. HashMap不能保证随着时间的推移Map中的元素次序是不变的。


#### 对线程安全的理解？
当多个线程访问一个对象(对象在堆中)时，如果不用进行额外的同步控制或其他的协调操作，调用这个对象的行为都可以获得正确的结果，我们就说这个对象是线程安全的。


#### CAS实现原理？
![](/images/ReviewV/CAS.jpg)


#### 代码加锁有几种实现方式？
1. synchronized
2. ReentrantLock


#### 快速排序算法
// todo


#### 分布式锁获取锁失败的处理，线程间的同步？
👉 [Redis分布式锁加锁失败，休眠机制](https://blog.csdn.net/CPLASF_/article/details/123953842)


#### Redis线程模型，过期机制，淘汰策略？
| 过期策略 | 解释 | 优点 | 缺点 |
| :----- | :----- | :----- |
|<div style='width: 80px; text-decoration: line-through'>定时删除</div>|<div style='text-decoration: line-through'>当对一个key设置了过期时间，当该时间到，立即执行对该key的删除。</div>|<div style='text-decoration: line-through'>定时删除对内存最友好，保证key一旦过期就能立即从内存中删除。</div>|<div style='text-decoration: line-through'>对CPU最不友好，在过期键比较多的时候，删除过期键会占用一部分CPU时间，对服务器的响应时间和吞吐量造成影响。</div>|
|<div style='width: 80px'>惰性删除</div>|当一个key被设置过期时间后，当key的过期时间到了，并不会立即从内存中删除；在我们使用该key时，先检查其是否过期，过期则将其从内存中删除。|对CPU友好，只在使用的时候才会进行过期检查，对于没用到的key不会浪费时间进行过期检查。|对内存不好用，key过期了却一直没被使用，就会一直占这内存。如果数据库中存在很多过期键不被使用，便永远不会被被删除，内存不会被释放，从而造成内存泄漏。|
|<div style='width: 80px'>定期删除</div>|每隔一段时间(该时间段可设置)，随机抽取一些设置了过期时间的key进行检查，删除里面过期的键。在Redis的配置文件redis.conf中有一个属性"hz"，默认为10，表示1s执行10次定期删除，即每隔100ms执行一次，可以修改这个配置值。随机抽取，抽取多少？同样是由redis.conf文件中的maxmemory-samples属性决定的，默认为5。|可以通过限制删除操作执行的时长和频率来减少删除操作对CPU的影响。另外定期删除，也能有效释放过期键占用的内存。|难以确定删除操作执行的时长和频率。如果执行的太频繁，定期删除策略变得和定时删除策略一样，对CPU不友好。如果执行的频率小，就和惰性删除一样了，过期键占用的内存不会及时得到释放。另外最重要的是，在获取某个键时，如果某个键的过期时间已经到了，但是还没执行定期删除，那么就会返回这个键的值，这是业务不能忍受的错误。|                                                                                                                                                                                                                                        


| 内存淘汰策略 | 解释 |
| :----- | :----- | 
|<div style='width: 120px'>no-eviction</div>|当内存不足以容纳新写入数据时，新写入操作会报错，无法写入新数据，一般不采用。|
|<div style='width: 120px'>allkeys-lru</div>|当内存不足以容纳新写入数据时，移除最近最少使用的key，这个是最常用的。|
|<div style='width: 120px'>allkeys-random</div>|当内存不足以容纳新写入的数据时，随机移除key。|
|<div style='width: 120px'>allkeys-lfu</div>|当内存不足以容纳新写入数据时，移除最不经常(最少)使用的key。|
|<div style='width: 120px'>volatile-lru</div>|当内存不足以容纳新写入数据时，在设置了过期时间的key中，移除最近最少使用的key。|
|<div style='width: 120px'>volatile-random</div>|内存不足以容纳新写入数据时，在设置了过期时间的key中，随机移除某个key 。|
|<div style='width: 120px'>volatile-lfu</div>|当内存不足以容纳新写入数据时，在设置了过期时间的key中，移除最不经常(最少)使用的key 。|
|<div style='width: 120px'>volatile-ttl</div>|当内存不足以容纳新写入数据时，在设置了过期时间的key中，优先移除过期时间最早(剩余存活时间最短)的key。|


#### 线程池参数、使用场景、参数设置分析？
```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    ...
}
```


| 线程池参数 | 解释 | 
| :----- | :----- | 
|coreSize核心线程数|1. 核心线程会一直存活，即使没有任务需要执行。<br>当线程数<核心线程数时，即使有线程空闲，线程池也会优先创建新线程处理。<br>设置allowCoreThreadTimeout=true(默认false)时，核心线程会超时关闭。|
|maximumPoolSize最大线程数|1. 当线程数>=corePoolSize，且任务队列已满时。线程池会创建新线程来处理任务。<br>2. 当线程数=maxPoolSize，且任务队列已满时，线程池会拒绝处理任务而抛出异常。|
|keepAliveTime线程空闲时间|1. 当线程空闲时间达到keepAliveTime时，线程会退出，直到线程数量=corePoolSize。<br>2. 如果allowCoreThreadTimeout=true，则会直到线程数量=0。|
|queueCapacity任务队列容量(阻塞队列)|当核心线程数达到最大时，新任务会放在队列中排队等待执行。|
|allowCoreThreadTimeout允许核心线程超时|----|
|rejectedExecutionHandler任务拒绝处理器|1. 当线程数已经达到maxPoolSize且队列已满，会拒绝新任务。<br>2. 当线程池被调用shutdown()后，会等待线程池里的任务执行完毕再shutdown。如果在调用shutdown()和线程池真正shutdown之间提交任务，会拒绝新任务。<br>线程池会调用rejectedExecutionHandler来处理这个任务。如果没有设置默认是AbortPolicy，会抛出异常。ThreadPoolExecutor采用了策略的设计模式来处理拒绝任务的几种场景。这几种策略模式都实现了RejectedExecutionHandler接口。|


| 拒绝策略 | 解释 | 
| :----- | :----- | 
|AbortPolicy|丢弃任务，抛运行时异常。|
|CallerRunsPolicy|执行任务。|
|DiscardPolicy|忽视，什么都不会发生。|
|DiscardOldestPolicy|从队列中踢出最先进入队列(最后一个执行)的任务。|


#### Mysql存储引擎、索引结构、分库分表
![](/images/ReviewV/StorageEngine.png)


| 存储引擎 | 使用场景 | 
| :----- | :----- | 
|InnoDB|适合要提供提交、回滚和崩溃后的安全恢复的事务安全能力，并要求实现并发控制。|
|MyISAM|适合于只读的数据，或者表比较小、可以忍受修复操作数据库。|
|Memory|适用于快速查找数据，用于数据分析中产生的中间数据。|


👉 [MySQL数据存储/索引/事务隔离级别/主从复制/分库分表](https://blog.csdn.net/weixin_51393513/article/details/121247919)


#### 场景题: 设计一个抢红包系统
// todo
