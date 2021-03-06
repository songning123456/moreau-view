### Zookeeper


#### Zookeeper是什么？
ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，它是集群的管理者，监视着集群中各个节点的状态根据节点提交的反馈进行下一步合理操作。最终，将简单易用的接口和性能高效、功能稳定的系统提供给用户。 


客户端的读请求可以被集群中的任意一台机器处理，如果读请求在节点上注册了监听器，这个监听器也是由所连接的Zookeeper机器来处理。对于写请求，这些请求会同时发给其他Zookeeper机器并且达成一致后，请求才会返回成功。因此，随着Zookeeper的集群机器增多，读请求的吞吐会提高但是写请求的吞吐会下降。有序性是Zookeeper中非常重要的一个特性，所有的更新都是全局有序的，每个更新都有一个唯一的时间戳，这个时间戳称为zxid(Zookeeper Transaction Id)。而读请求只会相对于更新有序，也就是读请求的返回结果中会带有这个Zookeeper最新的zxid。


#### Zookeeper提供了什么？
|名称|解释|
| :----- | :----- | 
|<div style="width: 70px">文件系统</div>|Zookeeper提供一个多层级的节点命名空间(节点称为znode)。与文件系统不同的是，这些节点都可以设置关联的数据，而文件系统中只有文件节点可以存放数据而目录节点不行。Zookeeper为了保证高吞吐和低延迟，在内存中维护了这个树状的目录结构，这种特性使得Zookeeper不能用于存放大量的数据，每个节点的存放数据上限为1M。|
|<div style="width: 70px;">通知机制</div>|client端会对某个znode建立一个watcher事件，当该znode发生变化时，这些client会收到Zookeeper的通知，然后client可以根据znode变化来做出业务上的改变等。|


#### Zookeeper有哪几种znode？
| 名称 | 解释 |
| :----- | :----- |
| <div style="width: 350px;">PERSISTENT(持久化目录节点)</div> | 客户端与Zookeeper断开连接后，该节点依旧存在。 |
| <div style="width: 350px;">PERSISTENT_SEQUENTIAL(持久化顺序编号目录节点)</div> | 客户端与Zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号。 |
| <div style="width: 350px;">EPHEMERAL(临时目录节点)</div> | 客户端与Zookeeper断开连接后，该节点被删除。 |
| <div style="width: 350px;">EPHEMERAL_SEQUENTIAL(临时顺序编号目录节点)</div> | 客户端与Zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号。 |


#### Zookeeper有哪些适合的使用场景？
| 使用场景 | 解释 |
| :----- | :----- |
| <div style='width: 250px'>命名服务(文件系统)</div> |&emsp;&emsp;命名服务是指通过指定的名字来获取资源或者服务的地址，利用Zookeeper创建一个全局的路径，即是唯一的路径，这个路径就可以作为一个名字，指向集群中的集群，提供的服务的地址，或者一个远程的对象等等。|
| <div style='width: 250px'>配置管理(文件系统、通知机制)</div> |&emsp;&emsp;程序分布式的部署在不同的机器上，将程序的配置信息放在Zookeeper的znode下，当有配置发生改变时，也就是znode发生变化时，可以通过改变Zookeeper中某个目录节点的内容，利用watcher通知给各个客户端，从而更改配置。|
| <div style='width: 250px'>集群管理(文件系统、通知机制)</div> |&emsp;&emsp;所谓集群管理无在乎两点：是否有机器退出和加入、选举master。 <br>&emsp;&emsp;对于第一点，所有机器约定在父目录下创建临时目录节点，然后监听父目录节点的子节点变化消息。一旦有机器挂掉，该机器与Zookeeper的连接断开，其所创建的临时目录节点被删除，所有其他机器都收到通知：某个兄弟目录被删除，于是，所有人都知道它上船了。 <br>&emsp;&emsp;新机器加入也是类似，所有机器收到通知：新兄弟目录加入，highcount又有了，对于第二点，我们稍微改变一下，所有机器创建临时顺序编号目录节点，每次选取编号最小的机器作为master就好。|
| <div style='width: 250px'>分布式锁(文件系统、通知机制)</div> |&emsp;&emsp;有了Zookeeper的一致性文件系统，锁的问题变得容易。锁服务可以分为两类：一个是保持独占，另一个是控制时序。<br>&emsp;&emsp;对于第一类，我们将Zookeeper上的一个znode看作是一把锁，通过create znode的方式来实现。所有客户端都去创建/distribute_lock节点，最终成功创建的那个客户端也即拥有了这把锁。用完删除掉自己创建的distribute_lock节点就释放出锁。<br>&emsp;&emsp;对于第二类，/distribute_lock已经预先存在，所有客户端在它下面创建临时顺序编号目录节点，和选master一样，编号最小的获得锁，用完删除，依次方便。|


#### Zookeeper获取分布式锁的流程？
![分布式锁1](/images/Zookeeper/分布式锁1.png)


在获取分布式锁的时候在locker节点下创建临时顺序节点，释放锁的时候删除该临时节点。客户端调用createNode方法在locker下创建临时顺序节点，然后调用getChildren(“locker”)来获取locker下面的所有子节点，注意此时不用设置任何Watcher。客户端获取到所有的子节点path之后，如果发现自己创建的节点在所有创建的子节点序号最小，那么就认为该客户端获取到了锁。如果发现自己创建的节点并非locker所有子节点中最小的，说明自己还没有获取到锁，此时客户端需要找到比自己小的那个节点，然后对其调用exist()方法，同时对其注册事件监听器。之后，让这个被关注的节点删除，则客户端的Watcher会收到相应通知，此时再次判断自己创建的节点是否是locker子节点中序号最小的，如果是则获取到了锁，如果不是则重复以上步骤继续获取到比自己小的一个节点并注册监听。当前这个过程中还需要许多的逻辑判断。


![分布式锁2](/images/Zookeeper/分布式锁2.png)


代码的实现主要是基于互斥锁，获取分布式锁的重点逻辑在于BaseDistributedLock，实现了基于Zookeeper实现分布式锁的细节。


#### Zookeeper数据复制？
Zookeeper作为一个集群提供一致的数据服务，自然它要在所有机器间做数据复制。数据复制的好处：
```
容错：一个节点出错，不致于让整个系统停止工作，别的节点可以接管它的工作; 
提高系统的扩展能力：把负载分布到多个节点上，或者增加节点来提高系统的负载能力; 
提高性能：让客户端本地访问就近的节点，提高用户访问速度。
```
从客户端读写访问的透明度来看，数据复制集群系统分下面两种：


| 名称 | 解释 |
| :----- | :----- |
| <div style="width: 160px;">写主(WriteMaster)</div> | 对数据的修改提交给指定的节点。读无此限制，可以读取任何一个节点。这种情况下客户端需要对读与写进行区别，俗称读写分离。|
| <div style="width: 160px;">写任意(Write Any)</div> | 对数据的修改可提交给任意的节点，跟读一样。这种情况下，客户端对集群节点的角色与变化透明。|


对Zookeeper来说，它采用的方式是写任意。通过增加机器，它的读吞吐能力和响应能力扩展性非常好；而写，随着机器的增多吞吐能力肯定下降(这也是它建立observer的原因)，而响应能力则取决于具体实现方式，是延迟复制保持最终一致性，还是立即复制快速响应。


#### Zookeeper工作原理？
Zookeeper的核心是原子广播，这个机制保证了各个Server之间的同步。实现这个机制的协议叫做Zab协议。Zab协议有两种模式，它们分别是恢复模式(选主)和广播模式(同步)。当服务启动或者在领导者崩溃后，Zab就进入了恢复模式，当领导者被选举出来，且大多数Server完成了和leader的状态同步以后，恢复模式就结束了。状态同步保证了leader和Server具有相同的系统状态。


#### Zookeeper是如何保证事务的顺序一致性的？
Zookeeper采用了递增的事务Id来标识，所有的proposal(提议)都在被提出的时候加上了zxid，zxid实际上是一个64位的数字，高32位是epoch(时期、纪元、世、新时代)用来标识leader是否发生改变，如果有新的leader产生出来，epoch会自增，低32位用来递增计数。当新产生proposal的时候，会依据数据库的两阶段过程，首先会向其他的server发出事务执行请求，如果超过半数的机器都能执行并且能够成功，那么就会开始执行。


#### Zookeeper下Server工作状态？
每个Server在工作过程中有三种状态：


| 名称 | 解释 |
| :----- | :----- |
| <div style="width: 100px;">LOOKING</div> | 当前Server不知道leader是谁，正在搜寻。 |
| <div style="width: 100px;">LEADING</div> | 当前Server即为选举出来的leader。 |
| <div style="width: 100px;">FOLLOWING</div> | leader已经选举出来，当前Server与之同步。 |


#### Zookeeper是如何选取主leader的？
当leader崩溃或者leader失去大多数的follower，这时Zookeeper进入恢复模式，恢复模式需要重新选举出一个新的leader，让所有的Server都恢复到一个正确的状态。Zookeeper的选举算法有两种：一种是基于basic paxos实现的，另外一种是基于fast paxos算法实现的。系统默认的选举算法为fast paxos。


#### Zookeeper同步流程？
选完Leader以后，Zookeeper就进入状态同步过程:


1. Leader等待server连接。
2. Follower连接leader，将最大的zxid发送给leader。
3. Leader根据follower的zxid确定同步点。
4. 完成同步后通知follower已经成为uptodate状态。
5. Follower收到uptodate消息后，又可以重新接受client的请求进行服务了。


#### Zookeeper节点宕机如何处理？
Zookeeper本身也是集群，推荐配置不少于3个服务器。Zookeeper自身也要保证当一个节点宕机时，其他节点会继续提供服务。如果是一个Follower宕机，还有2台服务器提供访问，因为Zookeeper上的数据是有多个副本的，数据并不会丢失;如果是一个Leader宕机，Zookeeper会选举出新的Leader。


Zookeeper集群的机制是只要超过半数的节点正常，集群就能正常提供服务。只有在Zookeeper节点挂得太多，只剩一半或不到一半节点能工作，集群才失效。所以3个节点的cluster可以挂掉1个节点(leader可以得到2票>1.5)，2个节点的cluster就不能挂掉任何1个节点了(leader可以得到1票<=1)。


#### Zookeeper watch机制？
1. 一次性触发数据发生改变时，一个watcher event会被发送到client，但是client只会收到一次这样的信息。
2. watcher event异步发送watcher的通知事件从server发送到client是异步的，这就存在一个问题，不同的客户端和服务器之间通过socket进行通信，由于网络延迟或其他因素导致客户端在不通的时刻监听到事件，由于Zookeeper本身提供了ordering guarantee，即客户端监听事件后，才会感知它所监视znode发生了变化。所以我们使用Zookeeper不能期望能够监控到节点每次的变化。Zookeeper只能保证最终的一致性，而无法保证强一致性。
3. 数据监视Zookeeper有数据监视和子数据监视get data() and exists()设置数据监视，get children()设置了子节点监视。
4. 注册watcher getData、exists、getChildren。
5. 触发watcher create、delete、setData。
6. setData()会触发znode上设置的data watch(如果set成功的话)。一个成功的create()操作会触发被创建的znode上的数据watch，以及其父节点上的child watch。而一个成功的delete()操作将会同时触发一个znode的data watch和child watch(因为这样就没有子节点了)，同时也会触发其父节点的child watch。
7. 当一个客户端连接到一个新的服务器上时，watch将会被以任意会话事件触发。当与一个服务器失去连接的时候，是无法接收到watch的。而当client重新连接时，如果需要的话，所有先前注册过的watch，都会被重新注册。通常这是完全透明的。只有在一个特殊情况下，watch可能会丢失：对于一个未创建的znode的exist watch，如果在客户端断开连接期间被创建了，并且随后在客户端连接上之前又删除了，这种情况下，这个watch事件可能会被丢失。
8. Watch是轻量级的，其实就是本地JVM的Callback，服务器端只是存了是否有设置了Watcher的布尔类型。


#### 为什么我们在分布式系统架构中需要使用Zookeeper集群？
**单点问题**


单点问题是分布式环境中最常见也是最经典的问题之一，在很多分布式系统中都会存在这样的单点问题。具体地说，单点问题是指在一个分布式系统中，如果某一个组件出现故障就会引起整个系统的可用性大大下降甚至是处于瘫痪状态，那么我们就认为该组件存在单点问题。ZooKeeper 确实已经很好地解决了单点问题。我们已经了解到，基于“过半”设计原则，ZooKeeper 在运行期间，集群中至少有过半的机器保存了最新的数据。因此，只要集群中超过半数的机器还能够正常工作，整个集群就能够对外提供服务。


**容灾**


在进行ZooKeeper的容灾方案设计过程中，我们要充分考虑到“过半原则”。也就是说，无论发生什么情况，我们必须保证ZooKeeper集群中有超过半数的机器能够正常工作。
