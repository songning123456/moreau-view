#### 数据库集群会产生哪些问题？
1. 自增id问题。
2. 数据关联查询问题(水平拆分)。
3. 数据同步问题。


**数据库集群下自增id问题的解决？**
```
UUID(不推荐, 不能建索引)；
设置id步长(缺点：需要在设计数据库时需要确定库的数量，才能定好步长间隔)；
雪花算法(sharding-jdbc使用雪花算法)或Redis。
```


#### Mysql如何实现读写分离？
| 方式 | 原理 | 
| :----- | :----- | 
|Mycat| Mycat原理MyCAT主要是通过对SQL的拦截，然后经过一定规则的分片解析、路由分析、读写分离分析、缓存分析等，然后将SQL发给后端真实的数据块，并将返回的结果做适当处理返回给客户端。特点：第三方客户端，反向代理。|
|SpringBoot动态切换数据源|动态切换数据源，根据配置的文件，业务动态切换访问的数据库。此方案通过Spring的AOP、AspactJ来实现动态织入，通过编程继承实现Spring中的AbstractRoutingDataSource，来实现数据库访问的动态切换，不仅可以方便扩展，不影响现有程序，而且对于此功能的增删也比较容易。|


在Spring 2.0+中引入了AbstractRoutingDataSource, 该类充当了DataSource的路由中介。能有在运行时, 根据某种key值来动态切换到真正的DataSource上。
```
项目中需要集成多个数据源分别为读和写的数据源，绑定不同的key；
采用AOP技术进行拦截业务逻辑层方法，判断方法的前缀是否需要写或者读的操作；
如果方法的前缀是写的操作的时候直接切换为写的数据源，反之切换为读的数据源也可以自己定义注解进行封装。
```
    

#### 数据库性能优化？
1. 添加适当的索引，索引对查询速度影响很大，必须添加索引。主键索引、唯一索引、普通索引、全文索引。
2. 对sql语句的一些优化(查询执行速度比较慢的sql语句)。
3. 读写分离(主从数据库)。
4. 分表分区(分表把一张大表分成多张表；分区把一张表里面的分配到不同的区域存储)。
5. 对mysql服务器硬件的升级操作。


#### 数据库事务的特性？
| 事务特性 | 解释 | 
| :----- | :----- | 
| <div style="width: 150px">原子性(Atomicity)</div> | 原子性指的是事务中包含的程序作为数据库的逻辑工作单位，它所做的对数据修改操作要么全部执行，要么完全不执行，这种特性称为原子性。 | 
| <div style="width: 150px">一致性(Consistency)</div> | 一致性指的是在一个事务执行之前和执行之后数据库都必须处于一致性状态(中途是否一致不用管)，这种特性称为一致性。 | 
| <div style="width: 150px">隔离性(Isolation)</div> | 隔离性指的是并发的事务是相互隔离的。多个事务并发执行时，一个事务的执行不应影响其他事务的执行。 | 
| <div style="width: 150px">持久性(Durability)</div> | 持久性指当系统或介质发生故障时，确保已提交的更新不能丢失。 | 


#### 数据库的隔离级别？
| 隔离原因 | 解释 | 
| :----- | :----- | 
| <div style="width: 145px">脏读</div> | 一个事务读取到了另一个事务未提交的数据操作结果，可能造成所有数据一起回滚。 | 
| <div style="width: 145px">不可重复读<span style='color: red;'>(修改)</span></div> | 事务T1读取某一数据后，事务T2对其做了修改，当事务T1再次读该数据时得到与前一次不同的值。这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。 | 
| <div style="width: 145px">幻读<span style='color: red;'>(新增或者删除)</span></div> | 事务在操作过程中进行两次查询，第二次查询的结果包含了第一次查询中未出现的数据或者缺少了第一次查询中出现的数据(并不要求两次查询的SQL语句相同)。这是因为在两次查询过程中有另外一个事务插入数据造成的。 | 


|隔离级别|解释|
| :----- | :----- |
|<div style="width: 240px">READ UNCOMMITED(读未提交)</div>|事务A把id=1，name=张三修改为李四，但是还未提交，这时事务B已经看到id=1的name为李四了。|
|<div style="width: 240px">READ COMMITED(读提交)</div>|事务A把id=1, name=张三修改为李四，但是还未提交。事务B第一次读，读到是张三，看不见李四。紧接着事务A提交了事务，事务A在第二次读的时候，读到的是修改后的李四。这个也叫不可重复读，就是所谓一个事务内对一个数据的两次读，可能读到不一样的值。|
|<div style="width: 240px">REPEATABLE READ(可重复读)</div>|无论事务A什么时候把张三修改为李四，事务B不管什么时刻读到值，都是事务B刚开启时读到的值。事务开启后对一行读取的值都是一样的。|
|<div style="width: 240px">SERIALIZABLE(串行化)</div>|事务A运行期间，事务Ｂ不允许运行。事务Ａ提交完事务后，事务Ｂ才开始运行。串行化是为了解决幻读。|


| 隔离级别 | 脏读 | 不可重复读 | 幻读 | 
| :----- | :----- | :----- | :----- | 
| READ UNCOMMITED(读未提交) | √ | √ | √ | 
| READ COMMITED(读提交) | × | √ | √ | 
| REPEATABLE READ(可重复读) | × | × | √ | 
| SERIALIZABLE(串行化) | × | × | × | 


默认隔离级别**REPEATABLE READ(可重复读)**。


#### Mysql常有的索引有哪些？它们的区别是什么？
**Hash索引、B+树索引**


1. 如果是等值查询，那么哈希索引明显有绝对优势，因为只需要经过一次算法即可找到相应的键值；当然了，这个前提是，键值都是唯一的。如果键值不是唯一的，就需要先找到该键所在位置，然后再根据链表往后扫描，直到找到相应的数据。
2. 如果是范围查询检索，这时候哈希索引就毫无用武之地了，因为原先是有序的键值，经过哈希算法后，有可能变成不连续的了，就没办法再利用索引完成范围查询检索。
3. 同理，哈希索引也没办法利用索引完成排序，以及like 'xxx%'这样的部分模糊查询(这种部分模糊查询，其实本质上也是范围查询)。
4. 哈希索引也不支持多列联合索引的最左匹配规则。
5. B+树索引的关键字检索效率比较平均，不像B树那样波动幅度大，在有大量重复键值情况下，哈希索引的效率也是极低的，因为存在所谓的哈希碰撞问题。


**什么是B+树**


![B+](/images/Database/B+.png)


1. 中间元素不存数据，只是当索引用，所有数据都保存在叶子结点中。
2. 所有的中间节点在子节点中要么是最大的元素要么是最小的元素。
3. 叶子结点包含所有的数据，和指向这些元素的指针，而且叶子结点的元素形成了自小向大这样子的链表。


#### Mysql索引使用规则？索引查询失效的情况？索引缺点以及使用注意？
```sql
// product表 
create index (shop_id, product_id, gmt_create);
```


**使用规则**


| 名称 | 解释 | 
| :----- | :----- | 
|全列匹配|where条件正好用到这3个字段，就用到联合索引。|
|最左前缀匹配| SQL里，正好用到联合索引最左边的一个或几个列表。|
|最左前缀匹配，但中间某个值没有匹配|如果sql用了联合索引第一列和第三列，会按照第一列在索引里找，找完后对结果集根据第三列做全表扫描，不会走第三列的索引了。|
|前缀匹配|如果不是等值，或比如=, >, <=等操作，而是Like操作。则只有Like ‘xx%’ (%在最后)这种才能用上索引。|
|范围匹配|只有符号最左前缀的列的范围才用到索引，范围之后的列用不到索引。比如: select * from product where shop_id >= 1 and product_id = 1；这里就只用到shop_id索引。|
|包含函数|对某个列用了函数，则该列不走索引。|


**失效情况**


1. like以%开头，索引无效；当like前缀没有%，后缀有%时，索引有效。
2. or语句前后没有同时使用索引。当or左右查询字段只有一个是索引，该索引失效，只有当or左右查询字段均为索引时，才会生效。
3. 组合索引，不是使用第一列索引，索引失效。
4. 数据类型出现隐式转化。如varchar不加单引号的话可能会自动转换为int型，使索引无效，产生全表扫描。
5. 在索引字段上使用not，<>，!=。不等于操作符是永远不会用到索引的，因此对它的处理只会产生全表扫描。优化方法：key<>0 改为 key>0 or key<0。
6. 对索引字段进行计算操作、字段上使用函数。
7. 当全表扫描速度比索引速度快时，mysql会使用全表扫描，此时索引失效。


**缺点**


增加磁盘消耗，占用磁盘文件，同时高并发频繁插入和修改索引，会导致性能损耗。


**使用注意**


1. 尽量创建少的索引，一个表，2至3个。
2. 一个字段的值几乎都不太一样的，比如id，这样用索引效果最好。


<span style='color: red;'>互联网系统中，一般尽量降低SQL复杂度，用简单的主键索引(聚簇索引)+少数联合索引，可以覆盖一个表的所有SQL查询，更复杂的业务逻辑，应该放在Java代码里实现。SQL越简单，后面迁移，分库分表，读写分离的成本更低，减少对SQL的改造。MySQL最好用在在线及时存储，不要用于计算(Join, 子查询，函数等等)。高并发场景下，计算放在Java内存里。</span>


#### 常用SQL语句的优化？
1. SELECT子句中避免使用 *，尽量应该根据业务需求按字段进行查询。
2. 尽量多使用COMMIT，如对大数据量的分段批量提交释放了资源，减轻了服务器压力。
3. 用UNION-ALL替换UNION，因为UNION-ALL不会过滤重复数据，所执行效率要快于UNION，并且UNION可以自动排序，而UNION-ALL不会。
4. 避免在索引列上使用计算和函数，这样索引就不能使用。
5. 用 >= 替换 >。
6. 用 NOT EXISTS 或 (外连接+判断为空) 方案替换 NOT IN 操作符。
7. LIKE操作符。


#### left join、inner join和right join的区别？
![join-method](/images/Database/join-method.jpg)
![用户-课程表](/images/Database/test-join.PNG)


| 名称 | 解释 | 
| :----- | :----- | 
| left join | 返回包括左表中的所有记录和右表中联结字段相等的记录。 | 
| inner join | 返回包括右表中的所有记录和左表中联结字段相等的记录。 | 
| right join | 只返回两个表中联结字段相等的行。 | 


**left join**
```sql
select user.id as user_id, user.user_name, class.id as class_id, class.class_name from user left join class on user.id = class.id
```
![left join](/images/Database/left-join.PNG)


**right join**
```sql
select user.id as user_id, user.user_name, class.id as class_id, class.class_name from user right join class on user.id = class.id
```
![right join](/images/Database/right-join.PNG)


**inner join**
```sql
select user.id as user_id, user.user_name, class.id as class_id, class.class_name from user inner join class on user.id = class.id
```
![inner join](/images/Database/inner-join.PNG)


#### Mysql主从复制的原理？
![主从复制](/images/Database/master-slave.png)


1. master在每个事务更新数据完成之前，将该操作记录串行地写入到binlog文件中。
2. slave开启一个I/O Thread，该线程在master打开一个普通连接，主要工作是binlog dump process。如果读取的进度已经跟上了master，就进入睡眠状态并等待master产生新的事件。I/O线程最终的目的是将这些事件写入到中继日志(relay log)中。
3. SQL Thread会读取中继日志，并顺序执行该日志中的SQL事件，从而与主数据库中的数据保持一致。


#### Mysql的MyISAM和InnoDB存储引擎的区别是啥？
| 名称 | 解释 | 
| :----- | :----- | 
|MyISAM|不支持事务，不支持外键约束，索引文件和数据文件分开，这样在内存里可以缓存更多的索引，对查询的性能会更好，适用于那种少量的插入，大量查询的场景。|
|InnoDB|主要特点就是支持事务，走聚簇索引，强制要求有主键，支持外键约束，高并发、大数据量、高可用等相关成熟的数据库架构，分库分表、读写分离、主备切换，全部都可以基于innodb存储引擎。|


#### union和union all区别？
| union | union all | 
| :----- | :----- | 
| 对两个结果集进行并集操作，不包括重复行，同时进行默认规则的排序。| 对两个结果集进行并集操作，包括重复行，不进行排序。|


#### SQL注入？
👉 [SQL注入详解](https://www.cnblogs.com/myseries/p/10821372.html)


#### drop、truncate和delete的区别？
👉 [drop、truncate和delete的区别](https://blog.csdn.net/ws0513/article/details/49980547)


#### count(*)和count(字段)的区别？
👉 [count(*)和count(字段)的区别](https://blog.csdn.net/liuyanaoao/article/details/106716720)


| count(*) | count(字段) | 
| :----- | :----- | 
| 计入null值 | 不计入null值 |


#### 查询一个表中某个数据重复条数大于1的所有信息？
```sql
select 字段1 from 表名 group by 字段1 having count(*) > 1
```


#### 谈谈你对Mysql的MVCC的理解？
👉 [面试官：谈谈你对Mysql的MVCC的理解？](https://baijiahao.baidu.com/s?id=1629409989970483292&wfr=spider&for=pc)


👉 [MVCC多版本并发控制](https://www.jianshu.com/p/8845ddca3b23)


#### (a, b, c)联合索引，只查询b会用到索引吗？
👉 [有mysql联合索引(A, B, C)，那么AC查询会用到索引吗？](https://www.cnblogs.com/CgWblog/p/14929888.html)


👉 [透彻理解联合索引(a,b,c)](https://blog.csdn.net/qq_41076577/article/details/107569200)


#### 什么是最左匹配原则？
👉 [最左匹配原则及其成因](https://blog.csdn.net/qq_37768971/article/details/100026327)

