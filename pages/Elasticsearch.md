#### Elasticsearch是什么？
基于lucene，隐藏复杂性，提供简单易用的restful API接口、java API接口(还有其他语言的API接口)。


1. 分布式的文档存储引擎。
2. 分布式的搜索引擎和分析引擎。
3. 分布式，支持PB级数据。


#### Elasticsearch的核心概念？
| 名称 | 解释 | 
| :----- | :----- | 
|<div style="width: 180px;">Near Realtime(NRT)</div>|近实时，两个意思，从写入数据到数据可以被搜索到有一个小延迟(大概1秒)；基于es执行搜索和分析可以达到秒级。|
|<div style="width: 180px;">Cluster(集群)</div>|包含多个节点，每个节点属于哪个集群是通过一个配置(集群名称，默认是Elasticsearch)来决定的，对于中小型应用来说，刚开始一个集群就一个节点很正常。|
|<div style="width: 180px;">Node(节点)</div>|集群中的一个节点，节点也有一个名称(默认是随机分配的)，节点名称很重要(在执行运维管理操作的时候)，默认节点会去加入一个名称为“Elasticsearch”的集群，如果直接启动一堆节点，那么它们会自动组成一个Elasticsearch集群，当然一个节点也可以组成一个Elasticsearch集群。|
|<div style="width: 180px;">Document(文档)、field</div>|文档，ES中的最小数据单元，一个document可以是一条客户数据，一条商品分类数据，一条订单数据，通常用JSON数据结构表示，每个index下的type中，都可以去存储多个document。一个document里面有多个field，每个field就是一个数据字段。|
|<div style="width: 180px;">Index(索引)</div>|包含一堆有相似结构的文档数据，比如可以有一个客户索引，商品分类索引，订单索引，索引有一个名称。一个index包含很多document，一个index就代表了一类类似的或者相同的document。比如说建立一个product index，商品索引，里面可能就存放了所有的商品数据，所有的商品document。|
|<div style="width: 180px;">Type(类型)</div>|每个索引里都可以有一个或多个type，type是index中的一个逻辑数据分类，一个type下的document，都有相同的field，比如博客系统，有一个索引，可以定义用户数据type，博客数据type，评论数据type。|
|<div style="width: 180px;">shard</div>|单台机器无法存储大量数据，Elasticsearch可以将一个索引中的数据切分为多个shard，分布在多台服务器上存储。有了shard就可以横向扩展，存储更多数据，让搜索和分析等操作分布到多台服务器上去执行，提升吞吐量和性能。每个shard都是一个lucene index。|
|<div style="width: 180px;">replica</div>|任何一个服务器随时可能故障或宕机，此时shard可能就会丢失，因此可以为每个shard创建多个replica副本。replica可以在shard故障时提供备用服务，保证数据不丢失，多个replica还可以提升搜索操作的吞吐量和性能。primary shard(建立索引时一次设置，不能修改，默认5个)，replica shard(随时修改数量，默认1个)，默认每个索引10个shard，5个primary shard，5个replica shard，最小的高可用配置，是2台服务器。|


#### Elasticsearch核心概念、数据库核心概念？
| Elasticsearch | 数据库 | 
| :----- | :----- | 
| Index | 库 |
| Type | 表 |
| Document | 行 |


#### shard、replica机制？
1. index包含多个shard。
2. 每个shard都是一个最小工作单元，承载部分数据，lucene实例，完整的建立索引和处理请求的能力。
3. 增减节点时，shard会自动在nodes中负载均衡。
4. primary shard和replica shard，每个document肯定只存在于某一个primary shard以及其对应的replica shard中，不可能存在于多个primary shard。
5. replica shard是primary shard的副本，负责容错，以及承担读请求负载。
6. primary shard的数量在创建索引的时候就固定了，replica shard的数量可以随时修改。
7. primary shard的默认数量是5，replica默认是1，默认有10个shard，5个primary shard，5个replica shard。
8. primary shard不能和自己的replica shard放在同一个节点上(否则节点宕机，primary shard和副本都丢失，起不到容错的作用)，但是可以和其他primary shard的replica shard放在同一个节点上。


#### 单node环境下如何创建index？
```json
PUT /test_index
{
   "settings" : {
      "number_of_shards" : 3,
      "number_of_replicas" : 1
   }
}
```
1. 单node环境下，创建一个index，有3个primary shard，3个replica shard。
2. 集群status是yellow。
3. 这个时候，只会将3个primary shard分配到仅有的一个node上去，另外3个replica shard是无法分配的。
4. 集群可以正常工作，但是一旦出现节点宕机，数据全部丢失，而且集群不可用，无法承接任何请求。


#### 剖析Elasticsearch并发冲突问题？
| 方案 | 解释 | 
| :----- | :----- | 
| 悲观锁 | 常见关系型数据库，比如mysql，读取时加锁。|
| 乐观锁 | 不加锁。写的时候判断当前version和ES的version是否一致。|


#### Elasticsearch document路由原理？
1. 当客户端创建document的时候，ES就需要决定这个doc是放在这个index的哪个shard上，这个过程称为document routing，即数据路由。
2. primary shard一旦index建立，是不允许修改的，但是replica shard可以随时修改。
3. 路由算法：shard = hash(routing) % number_of_primary_shards。
4. 从hash函数中，产出的hash值一定是相同的。
5. 无论hash值是几，无论是什么数字，对number_of_primary_shards求余数，结果一定是在0~number_of_primary_shards-1之间这个范围内的。


#### Elasticsearch写入一致性原则？
consistency，one(primary shard)，all(all shard)，quorum(default)。


| 名称 | 解释 | 
| :----- | :----- | 
|<div style="width: 180px;">one(primary shard)</div>| 要求我们这个写操作，只要有一个primary shard是active活跃可用的，就可以执行。|
|<div style="width: 180px;">all(all shard)</div>| 要求我们这个写操作，必须所有的primary shard和replica shard都是活跃的，才可以执行这个写操作。|
|<div style="width: 180px;">quorum(default)</div>| 默认的值，要求所有的shard中，必须是大部分的shard都是活跃的，可用的，才可以执行这个写操作quorum=(primary+number_of_replicas)/2+1。|


#### DeepPaging操作的问题？
简单说就是搜索特别深导致性能低下的问题。


比如总共60000条数据，3个shard，每个shard分2W条，每页10条，如果要搜到1000页时，取的是第几条到第几条数据？实际上每个shard都要将内部的2W条数据的第10001~10010条拿出来，不是10条，是10010条取出，3个shard都返回10010条数据给coordinate node，这个协调节点共收到30030条，再进行排序。_score，相关度分数，然后取到排位最高的前10条数据，这就是我们要的第1000页的10条数据。这个过程耗费网络带宽，内存，CPU，有大量性能问题，所以要避免deep paging操作。


#### 倒排索引相关问题？
简单说统计每个单词在哪些doc中出现，建立索引，方便后续快速查到该单词的文档。


搜索的时候，要依靠倒排索引；排序的时候，需要依靠正排索引。看到每个document的每个field，然后进行排序。所谓的正排索引，其实就是doc values。在建立索引的时候，一方面会建立倒排索引，以供搜索用；一方面会建立正排索引，也就是doc values，以供排序、聚合、过滤等操作使用。doc values是被保存在磁盘上的，此时如果内存足够，OS会自动将其缓存在内存中，性能还是会很高；如果内存不足够，OS会将其写入磁盘上。


preference决定了哪些shard会被用来执行搜索操作。例如_primary、_primary_first、 _local、_only_node:xyz、_prefer_node:xyz、_shards:2,3。


bouncing results问题，两个document排序，field值相同；不同的shard上可能排序不同；每次请求轮询打到不同的replica shard上；每次页面上看到的搜索结果的排序都不一样。这就是bouncing result，也就是跳跃的结果。解决方案就是将preference设置为一个字符串，比如说user_id让每个user每次搜索的时候都使用同一个replica shard去执行，就不会看到bouncing results了。


| 结构 | 好处 | 坏处 |
| :----- | :----- | :----- | 
|1. 包含这个关键词的document list；<br>2. 包含这个关键词的所有document的数量：IDF(inverse document frequency)；<br>3. 这个关键词在每个document中出现的次数：TF(term frequency)；<br>4. 这个关键词在这个document中的次序；<br>5. 每个document的长度：length norm；<br>6. 包含这个关键词的所有document的平均长度。|1. 不需要锁，提升并发能力，避免锁的问题；<br>2. 数据不变，一直保存在os cache中，只要cache内存足够；<br>3. filter cache一直驻留在内存，因为数据不变；<br>4. 可以压缩，节省cpu和io开销。|1. 每次都要重新构建整个索引。|


#### 什么是分词器？
**切分词**；**Normalization(提升recall召回率)**。


Normalization，建立倒排索引的时候，会执行一个操作，也就是说对拆分出的各个单词进行相应的处理，以提升后面搜索的时候能够搜索到相关联的文档的概率。时态的转换、单复数的转换、同义词的转换、大小写的转换。分词器将一段文本进行各种处理(时态转换、单复数转换等等)，最后处理好的结果才会拿去建立倒排索引。


query string必须以和index建立时相同的analyzer进行分词。query string对exact value和full text的区别对待。exact value在建立倒排索引的时候、分词的时候，是将整个值一起作为一个关键词建立到倒排索引中的；full text会经历各种各样的处理，分词，normalization(时态转换、同义词转换、大小写转换)，才会建立到倒排索引中。同时exact value和full text类型的field就决定了，在一个搜索过来的时候，对exact value field或者是full text field进行搜索的行为也是不一样的，会跟建立倒排索引的行为保持一致；比如说exact value搜索的时候，就是直接按照整个值进行匹配，full text query string也会进行分词和normalization再去倒排索引中去搜索。


#### 什么是mapping？mapping的核心数据类型？dynamic mapping时数据类型转换？如何查看mapping？
自动或手动为index中type建立的一种数据结构和相关配置，简称mapping。dynamic mapping自动为我们建立index、创建type以及type对应的mapping。mapping中包含了每个field对应的数据类型，以及如何分词等设置，包括自动设置数据类型，也可以提前手动创建index和type的mapping，自己对各field进行设置，包括数据类型、索引行为、分词器等等。


mapping核心数据类型string、byte、short、integer、long、float、double、boolean、date。


dynamic mapping时true or false转boolean、123转long、123.45转double、2017-01-01转date、"hello world"转string/text。


查询mapping：GET /index/_mapping/type。


#### filter与query对比及区别？
| filter | query | 
| :----- | :----- | 
|1. 仅仅只是按照搜索条件过滤出需要的数据而已，不计算任何相关度分数，对相关度没有任何影响；<br>2. 只是根据一些条件筛选出一部分数据，不关注其排序；<br>3. 不希望一些搜索条件来影响你的document排序；<br>4. 不需要计算相关度分数，不需要按照相关度分数进行排序，同时还有内置的自动cache最常使用filter的数据。|1. 会去计算每个document相对于搜索条件的相关度，并按照相关度进行排序；<br>2. 进行搜索需要将最匹配搜索条件的数据先返回；<br>3. 越符合这些搜索条件的document越排在前面返回；<br>4. 要计算相关度分数，按照分数进行排序，而且无法cache结果。|


#### ES相关度评分算法？
| 算法名称 | 解释 | 
| :----- | :----- | 
|<div style="width: 240px;">term frequency/inverse document frequency</div>|简称为TF/IDF算法|
|<div style="width: 240px;">Term frequency</div>|搜索文本中的各个词条在field文本中出现了多少次，出现次数越多就越相关。|
|<div style="width: 240px;">Inverse document frequency</div>|搜索文本中的各个词条在整个索引的所有文档中出现了多少次，出现的次数越多就越不相关。|


#### ES的写入流程(buffer、segment、commit)？
1. 数据写入buffer缓冲和translog日志文件。
2. 每隔一秒钟，buffer中的数据被写入新的segment file，并进入os cache，此时segment被打开并供search使用，不立即执行commit。(数据写入os cache，并被打开供搜索的过程，叫做refresh，默认是每隔1秒refresh一次。也就是说，每隔一秒就会将buffer中的数据写入一个新的index segment file，先写入os cache中。所以，ES是近实时的，数据写入到可以被搜索，默认是1秒。)
3. buffer被清空。
4. 重复1~3，新的segment不断添加，buffer不断被清空，而translog中的数据不断累加。
5. 当translog长度达到一定程度的时候，commit操作发生。
```
buffer中的所有数据写入一个新的segment，并写入os cache，打开供使用；
buffer被清空；
一个commit point被写入磁盘，标明了所有的index segment；
filesystem cache中的所有index segment file缓存数据，被fsync强行刷到磁盘上；
现有的translog被清空，创建一个新的translog。
```
    
    
#### 基于translog和commit point如何进行数据恢复？
fsync+清空translog，就是flush，默认每隔30分钟flush一次，或者当translog过大的时候，也会flush。POST /my_index/_flush，一般来说别手动flush，让它自动执行就可以了。translog每隔5秒被fsync一次到磁盘上。在一次增删改操作之后，当fsync在primary shard和replica shard都成功之后，那次增删改操作才会成功。但是这种在一次增删改时强行fsync translog可能会导致部分操作比较耗时，也可以允许部分数据丢失，设置异步fsync translog。
```json
PUT /my_index/_settings
{
    "index.translog.durability": "async",
    "index.translog.sync_interval": "5s"
}
```


#### 磁盘文件如何合并(segment merge)？
每秒一个segment file文件过多，而且每次search都要搜索所有的segment，很耗时。默认会在后台执行segment merge操作。在merge的时候，被标记为deleted的document也会被彻底物理删除。


每次merge操作的执行流程：
```
选择一些有相似大小的segment，merge成一个大的segment；
将新的segment flush到磁盘上去；
写一个新的commit point，包括了新的segment，并且排除旧的那些segment；
将新的segment打开供搜索；
将旧的segment删除。
```


#### partial update(部分更新)实现原理？
```json
post /index/type/id/_update
{
   "doc": {
      "要修改的少数几个field即可，不需要全量的数据"
   }
}

eg：

PUT /test_index/test_type/10
{
  "test_field1": "test1",
  "test_field2": "test2"
}

POST /test_index/test_type/10/_update
{
  "doc": {
    "test_field2": "updated test2"
  }
}
```


1. 内部先获取document。
2. 在内存中封装用户提交的新document，发送PUT请求到ES内部。
3. 将老的document标记为deleted。
4. 将新的document存入索引中。


比全量替换的优点：所有查询、修改和写回操作都发生在ES的一个shard内部，避免网络开销(减少2次网络请求)提升性能。


#### bulk操作相关问题？
每一个操作要两个json串，语法如下：


```json
{"action": {"metadata"}}
{"data"}
eg：
{"index": {"_index": "test_index", "_type", "test_type", "_id": "1"}}
{"test_field1": "test1", "test_field2": "test2"}
```


可以执行操作的类型如下：


| 操作 | 解释 |
| :----- | :----- | 
| delete | 删除一个文档，只要1个json串就可以了。|
| create | PUT /index/type/id/_create，强制创建。|
| index | 普通的put操作，可以是创建文档，也可以是全量替换文档。|
| update | 执行的partial update操作。|


bulk request会加载到内存里，如果太大的话，性能反而会下降，因此需要反复尝试一个最佳的bulk size。一般从1000~5000条数据开始，尝试逐渐增加。另外，如果看大小的话，最好是在5~15MB之间。


#### 如何重建索引？
一个field的设置是不能被修改的，如果要修改一个field，那么应该重新按照新的mapping建立一个index，然后将数据批量查询出来，重新用bulk api写入index中。批量查询的时候，建议采用scroll api，并且采用多线程并发的方式来reindex数据，每次scroll就查询指定日期的一段数据，交给一个线程即可。

 
1. 一开始依靠dynamic mapping插入数据，但是不小心有些数据是2017-01-01这种日期格式的，所以title这种field被自动映射为了date类型，实际上它应该是string类型的。
2. 当后期向索引中加入string类型的title值的时候就会报错。
3. 如果此时想修改title的类型是不可能的。
4. 唯一的办法就是进行reindex，也就是说重新建立一个索引，使用scroll api将旧索引的数据批量查询出来，再导入新索引。
5. 采用bulk api将scroll查出来的一批数据，批量写入新索引。


<span style="color: red">如果说旧索引的名字是old_index，新索引的名字是new_index，终端java应用已经在使用old_index在操作了，难道还要去停止java应用，修改使用的index为new_index，才重新启动java应用吗？这个过程中就会导致java应用停机，可用性降低。</span>


**解决方案**


给java应用一个别名，这个别名是指向旧索引的，java应用先用着，java应用先用goods_index alias来操作，此时实际指向的是旧的my_index。重建索引后，将goods_index alias切换到my_index_new上去。java应用会直接通过index别名使用新的索引中的数据，java应用程序不需要停机，零提交，高可用。
