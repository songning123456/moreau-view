### Dubbo


#### Dubbo是什么？
Dubbo是一个分布式、高性能、透明化的RPC服务框架，提供服务自动注册、自动发现等高效服务治理方案，可以和Spring框架无缝集成。


#### Dubbo的主要应用场景？
1. 透明化的远程方法调用，就像调用本地方法一样调用远程方法，只需简单配置，没有任何API侵入。
2. 软负载均衡及容错机制，可在内网替代F5等硬件负载均衡器，降低成本，减少单点。
3. 服务自动注册与发现，不再需要写死服务提供方地址，注册中心基于接口名查询服务提供者的IP地址，并且能够平滑添加或删除服务提供者。


#### Dubbo的核心功能？

| 核心功能 | 解释 |
| :----- | :----- |
| <div style="width: 200px;">Remoting(网络通信框架)</div> | 提供对多种NIO框架抽象封装，包括“同步转异步”和“请求-响应”模式的信息交换方式。 |
| <div style="width: 200px;">Cluster(服务框架)</div> | 提供基于接口方法的透明远程过程调用，包括多协议支持、以及软负载均衡、失败容错、地址路由、动态配置等集群支持。 |
| <div style="width: 200px;">Registry(服务注册)</div> | 基于注册中心目录服务，使服务消费方能动态的查找服务提供方。使地址透明，使服务提供方可以平滑增加或减少机器。 |


#### Dubbo的核心组件？
| 核心组件 | 说明 |
| :----- | :----- |
| <div style="width: 100px;">Provider</div> |  暴露服务的服务提供方。|
| <div style="width: 100px;">Consumer</div> | 调用远程服务的服务消费方。|
| <div style="width: 100px;">Registry</div> | 服务注册与发现的注册中心。 |
| <div style="width: 100px;">Monitor</div> | 统计服务的调用次数和调用时间的监控中心。|
| <div style="width: 100px;">Container</div> | 服务运行容器。|


#### Dubbo支持哪些协议，每种协议的应用场景及优缺点？
| 协议 | 应用场景 |
| :----- | :----- |
| <div style="width: 140px;color:red;">dubbo(默认)</div> | 单一长连接和NIO异步通讯，适合大并发小数据量的服务调用，以及消费者远大于提供者。传输协议TCP，异步Hessian序列化。|
| <div style="width: 140px;">rmi</div> | 采用JDK标准的rmi协议实现，传输参数和返回参数对象需要实现Serializable接口，使用java标准序列化机制，使用阻塞式短连接，传输数据包大小混合，消费者和提供者个数差不多，可传文件，传输协议TCP。多个短连接，TCP协议传输，同步传输，适用常规的远程服务调用和rmi互操作。在依赖低版本的Common-Collections包，java序列化存在安全漏洞。 |
| <div style="width: 140px;">webservice</div> | 基于WebService的远程调用协议，集成CXF实现，提供和原生WebService的互操作。多个短连接，基于HTTP传输，同步传输，适用系统集成和跨语言调用。 |
| <div style="width: 140px;">http</div> | 基于Http表单提交的远程调用协议，使用Spring的HttpInvoke实现。多个短连接，传输协议HTTP，传入参数大小混合，提供者个数多于消费者，需要给应用程序和浏览器JS调用。|
| <div style="width: 140px;">hessian</div> | 集成Hessian服务，基于HTTP通讯，采用Servlet暴露服务，Dubbo内嵌Jetty作为服务器时默认实现，提供与Hession服务互操作。多个短连接，同步HTTP传输，Hessian序列化，传入参数较大，提供者大于消费者，提供者压力较大，可传文件。 |
| <div style="width: 140px;">memcache</div> | 基于memcached实现的RPC协议。 |
| <div style="width: 140px;">redis</div> | 基于redis实现的RPC协议。|


#### Dubbo超时时间怎样设置？服务调用超时问题怎么解决？
| 名称 | 解释 |
| :----- | :----- |
| <div style="width: 200px;">服务提供者端设置超时时间</div> | 在Dubbo的用户文档中，推荐如果能在服务端多配置就尽量多配置，因为服务提供者比消费者更清楚自己提供的服务特性。|
| <div style="width: 200px;">服务消费者端设置超时时间</div> | 如果在消费者端设置了超时时间，以消费者端为主，即优先级更高。因为服务调用方设置超时时间控制性更灵活。如果消费方超时，服务端线程不会定制，会产生警告。|


Dubbo在调用服务不成功时，默认是会重试两次的。


#### 调用Dubbo的时候如何处理请求超时的情况？
Dubbo在调用服务不成功时，默认是会重试两次的。这样在服务端的处理时间超过了设定的超时时间时，就会有重复请求，比如在发邮件时，可能就会发出多份重复邮件，执行注册请求时，就会插入多条重复的注册数据，那么怎么解决超时问题呢？


<span style="color: red">对于核心的服务中心，去除Dubbo超时重试机制，并重新评估设置超时时间。业务处理代码必须放在服务端，客户端只做参数验证和服务调用，不涉及业务流程处理。</span>


```
<!-- 延迟到Spring初始化完成后，再暴露服务,服务调用超时设置为6秒,超时不重试-->  
<dubbo:provider delay="-1" timeout="6000" retries="0"/>
```


当然Dubbo的重试机制其实是非常好的QOS保证，它的路由机制，是会帮你把超时的请求路由到其他机器上，而不是本机尝试，所以dubbo的重试机器也能一定程度的保证服务的质量。但是请一定要综合线上的访问情况，给出综合的评估。


#### Dubbo有些哪些注册中心？
| 注册中心 | 解释 |
| :----- | :----- |
| <div style="width: 200px;">Multicast注册中心</div> | Multicast注册中心不需要任何中心节点，只要广播地址，就能进行服务注册和发现。基于网络中组播传输实现。 |
| <div style="width: 200px;color:red;">Zookeeper注册中心(默认)</div> | 基于分布式协调系统Zookeeper实现，采用Zookeeper的watch机制实现数据变更。 |
| <div style="width: 200px;">Redis注册中心</div> | 基于Redis实现，采用key/Map存储，主key存储服务名和类型，Map中key存储服务URL，value存储服务过期时间。基于redis的发布/订阅模式通知数据变更。|


#### Zookeeper挂掉了Dubbo还能继续使用吗？
1. 监控中心宕掉不影响使用，只是丢失部分采样数据。
2. 数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务。
3. 注册中心对等集群，任意一台宕掉后，将自动切换到另一台。
4. 注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯。
5. 服务提供者无状态，任意一台宕掉后，不影响使用。
6. 服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复。


#### Dubbo服务注册与发现的流程及其原因？
![Dubbo](/images/MicroService/Dubbo.png)


**流程说明**


1. Provider(提供者)绑定指定端口并启动服务。
2. 指供者连接注册中心，并发本机IP、端口、应用信息和提供服务信息发送至注册中心存储。
3. Consumer(消费者)，连接注册中心，并发送应用信息、所求服务信息至注册中心。
4. 注册中心根据消费者所求服务信息匹配对应的提供者列表发送至Consumer应用缓存。
5. Consumer在发起远程调用时基于缓存的消费者列表择其一发起调用。
6. Provider状态变更会实时通知注册中心、在由注册中心实时推送至Consumer。


**设计的原因**


1. Consumer与Provider解偶，双方都可以横向增减节点数。
2. 注册中心对本身可做对等集群，可动态增减节点，并且任意一台宕掉后，将自动切换到另一台。
3. 去中心化，双方不直接依懒注册中心，即使注册中心全部宕机短时间内也不会影响服务的调用。
4. 服务提供者无状态，任意一台宕掉后，不影响使用。


#### Dubbo集群的负载均衡有哪些策略？
| 策略 | 解释 |
| :----- | :----- |
| <div style="width: 150px;">Random LoadBalance</div> | 随机选取提供者策略，有利于动态调整提供者权重。截面碰撞率高，调用次数越多，分布越均匀。 |
| <div style="width: 150px;">RoundRobin LoadBalance</div> | 轮循选取提供者策略，平均分布，但是存在请求累积的问题。 |
| <div style="width: 150px;">LeastActive LoadBalance</div> | 最少活跃调用策略，解决慢提供者接收更少的请求。 |
| <div style="width: 150px;">ConstantHash LoadBalance</div> | 一致性Hash策略，使相同参数请求总是发到同一提供者，一台机器宕机，可以基于虚拟节点，分摊至其他提供者，避免引起提供者的剧烈变动。|


#### Dubbo的整体架构设计有哪些分层？
| 分层结构 | 说明 |
| :----- | :----- |
| <div style="width: 180px;">服务接口层(Service)</div> | 该层与业务逻辑相关，根据provider和consumer的业务设计对应的接口和实现。 |
| <div style="width: 180px;">配置层(Config)</div> | 对外配置接口，以ServiceConfig和ReferenceConfig为中心。|
| <div style="width: 180px;">服务代理层(Proxy)</div> | 服务接口透明代理，生成服务的客户端Stub和服务器端Skeleton。|
| <div style="width: 180px;">服务注册层(Registry)</div> | 封装服务地址的注册与发现，以服务URL为中心。|
| <div style="width: 180px;">路由层(Cluster)</div> | 封装多个提供者的路由和负载均衡，并桥接注册中心，以Invoker为中心，扩展接口为Cluster、Directory、Router和LoadBalance。|
| <div style="width: 180px;">集群层(Cluster)</div> | 封装多个提供者的路由及负载均衡，并桥接注册中心，以Invoker为中心。|
| <div style="width: 180px;">监控层(Monitor)</div> | RPC调用次数和调用时间监控。|
| <div style="width: 180px;">远程调用层(Protocol)</div> | 封装RPC调用，以Invocation和Result为中心，扩展接口为Protocol、Invoker和Exporter。|
| <div style="width: 180px;">信息交换层(Exchange)</div> | 封装请求响应模式，同步转异步，以Request和Response为中心。|
| <div style="width: 180px;">网络传输层(Transport)</div> | 抽象mina和netty为统一接口，以Message 为中心。 |


#### Dubbo为什么需要服务治理？
![serviceGovernance](/images/Dubbo/serviceGovernance.png)


1. 过多的服务URL配置困难。
2. 负载均衡分配节点压力过大的情况下也需要部署集群。
3. 服务依赖混乱，启动顺序不清晰。
4. 过多服务导致性能指标分析难度较大，需要监控。


#### Dubbo的注册中心集群挂掉，发布者和订阅者之间还能通信么？
可以的，启动Dubbo时，消费者会从Zookeeper拉取注册的生产者的地址接口等数据，缓存在本地。每次调用时，按照本地存储的地址进行调用。


#### Dubbo的集群容错方案有哪些？
| 容错方案 | 解释 |
| :----- | :----- |
| <div style="width: 180px;color:red;">Failover Cluster(默认)</div> | 失败自动切换，当出现失败，重试其它服务器。通常用于读操作，但重试会带来更长延迟。 |
| <div style="width: 180px;">Failfast Cluster</div> | 快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。 |
| <div style="width: 180px;">Failsafe Cluster</div> | 失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。 |
| <div style="width: 180px;">Failback Cluster</div> | 失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。 |
| <div style="width: 180px;">Forking Cluster</div> | 并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过forks="2"来设置最大并行数。|
| <div style="width: 180px;">Broadcast Cluster</div> | 广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。|


#### Dubbo支持哪些序列化方式？
默认使用Hessian序列化，还有Dubbo、FastJson、Java自带序列化。


#### Dubbo在安全机制方面是如何解决？
Dubbo通过Token令牌防止用户绕过注册中心直连，然后在注册中心上管理授权。Dubbo还提供服务黑白名单，来控制服务所允许的调用方。


#### Dubbo通信协议Dubbo协议为什么要消费者比提供者个数多？
因为Dubbo协议采用单一长连接，假设网络为千兆网卡(1024Mbit=128MByte)，根据测试经验数据每条连接最多只能压满7MByte(不同的环境可能不一样，供参考)，理论上1个服务提供者需要20个服务消费者才能压满网卡。


#### Dubbo通信协议Dubbo协议为什么不能传大包？
因为Dubbo协议采用单一长连接，如果每次请求的数据包大小为500KByte，假设网络为千兆网卡(1024Mbit=128MByte)，每条连接最大7MByte(不同的 环境可能不一样，供参考)，单个服务提供者的TPS(每秒处理事务数)最大为:128MByte/500KByte=262。单个消费者调用单个服务提供者的TPS(每秒处理事务数)最大为:7MByte/500KByte=14。如果能接受，可以考虑使用，否则网络将成为瓶颈。


#### Dubbo通信协议Dubbo协议为什么采用异步单一长连接？
因为服务的现状大都是服务提供者少，通常只有几台机器，而服务的消费者多，可能整个网站都在访问该服务，比如Morgan的提供者只有6台提供者，却有上百台消费者，每天有1.5亿次调用。如果采用常规的hessian服务，服务提供者很容易就被压跨，通过单一连接，保证单一消费者不会压死提供者，长连接减少连接握手验证等，并使用异步IO复用线程池防止C10K问题。
