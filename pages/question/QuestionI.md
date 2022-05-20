# 线上服务问题定位步骤


## 现象描述


最近XX项目上出现了服务运行一段时间后不操作系统接口查询缓慢的问题，导致线上服务经常出现登录提示成功，但获取用户权限数据响应超时问题，导致登录不成功的现象，重启服务可暂时解决此问题。


## 排查思路


1. 排查是否应用服务器外网网络带宽问题；
2. 排查是否应用服务器、数据库服务器出现问题；
3. 排查是否是应用服务代码问题；
4. 排查是否是JVM问题；
5. 排查是否是数据库问题；
6. 排查是否是应用服务器和数据库服务器间网络问题。


## 排查过程


### 排查是否是应用服务器外网网络带宽问题导致接口响应缓慢


在浏览器按F12，进入开发模式，查看接口响应时间并记录，登录线上应用服务器，用curl命令直接本地服务，观察接口返回时间。


```shell
curl -H 'X-Access-Token: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDMxMjQwMjUsInVzZXJuYW1lIjoiYWRtaW4ifQ.40zZZnZu7RlxsQWpi4CfajBOT4iBP66xlePnEiyPSxQ' http://192.168.70.13:7003/skboot/sys/permission/list
```


如果在本地服务器上请求数据返回正常，基本可以确定为外网网络问题。


### 排查是否应用服务器出现问题


![](/images/QuestionI/top.png)


使用top命令查看服务器资源信息，查看cpu、内存资源使用情况。如果服务器资源情况健康，基本可以排除应用服务器方面问题。


### 应用服务代码问题


**规范化项目编码，避免可能引起莫名其妙的bug配置**


1. 清除不用的定时任务；
2. 不要在功能影响不确定的情况下修改java底层代码；
3. 不要在不清楚的情况下随意修改项目配置文件(application-*.yml)。


**分析jvm堆栈信息，排除不规范代码引起的死循环或对象创建导致的fullGC问题**


* 生成dump文件。


```shell
jmap -dump:live,format=b,file=dump.hprof 28920(java进程号)
```


可以用jvm分析工具打开dump文件，查看是否存在死锁及大对象情况。


* 观察jvm垃圾回收情况及新老年代使用占比，一般老年代占满会导致频繁fullGC导致占用服务资源造成服务卡顿
观察jvm堆栈内存使用情况。


```shell
jmap -heap 31846
```


![](/images/QuestionI/jvm.png)


观察jvm垃圾回收情况，查看FGC这一列数据情况。


```shell
jstat -gc 31846 1000 20
```


![](/images/QuestionI/jstat.png)


如果这些数据均正常，基本可以排除不存在死循环、死锁、堆内频繁对象创建及jvm频繁FGC情况。


### 排查是否是JVM堆栈分配问题


基于上述排查，基本可以看出启动脚本jvm堆栈分配情况，jvm堆栈分配为服务器物理内存的三分之二，后面我们会调整一般服务启动脚本，共享给大家。


### 排查是否是数据库问题


对于自己安装的mysql数据库，排查mysql异常日志情况，登录数据库服务器，查看mysql异常日志。


![](/images/QuestionI/mysql.png)


通过查看mysql异常日志，可以看到大量ip域名解析记录，配置mysql反向DNS域名解析属性，对于自己安装的mysql数据库，可以安装完成后按照如何配置调优。


```
# 数据库修改配置
vi /etc/my.cnf
# 新增以下配置
binlog_error_action=IGNORE_ERROR
innodb_flush_log_at_trx_commit=2
innodb_buffer_pool_size=9024M
innodb_log_files_in_group=4
innodb_log_file_size=1024M
innodb_page_cleaners=2
expire_logs_days=3
default-time_zone='+8:00'   
event_scheduler=1        
max_connections=3000
```


### 排查是否是应用服务器和数据库服务器间网络问题


以上步骤均排查完成后，仍存在此问题，进行网络层面问题排查。


1. 调用不查询数据库的接口查看是否存在慢的问题，如果是基本定位到应用服务层面的问题；
2. 调用查询数据库的接口查看是否存在慢的问题，如果存在基本可以定位为应用与数据库服务器维护的连接池TCP长连接问题，在应用服务器查询数据库连接信息。


![](/images/QuestionI/netsata.png)


数据库层面查询连接信息


```sql
show processlist
```


从数据库层面杀死数据库连接，再次查看接口请求响应情况，发现接口响应数据回归正常。


**问题原因**


应用服务器和数据库服务器不在用一个网段，且线上网络情况复杂，我们不清楚中间路由、防火墙及各种网络策略，导致应用服务启动后创建数据库连接池，在服务不操作的情况下，默认保持了最小的数据库线程连接，长时间不操作系统，保持的数据库连接由于网络问题出现了“假死”情况，且应用服务无法唤醒激活，只能杀死连接系统重新创建一个才能正常。


**解决方案**


* 在数据库连接地址上添加数据库连接失效参数


```yaml
datasource:
  master:
  #赤水测试服务器数据库
  url: jdbc:mysql://192.168.88.51:3306/zhsw-ynlj?characterEncoding=UTF-8&useUnicode=true&useSSL=false&connectTimeout=60000&socketTimeout=1800
  username: root
  password: xxxxxx
  driver-class-name: com.mysql.jdbc.Driver
```


* 配置最小数据库连接数为0，此方案长时间不操作自动杀死idle状态线程，用的时候重新创建


```yaml
dynamic:
  druid: # 全局druid参数，绝大部分值和默认保持一致。(现已支持的参数如下,不清楚含义不要乱设置)
    # 连接池的配置信息
    # 初始化大小，最小，最大
    initial-size: 5
    min-idle: 0
    maxActive: 10
    # 配置获取连接等待超时的时间
    maxWait: 60000
```


## 总结
1. 服务器环境如果可以应用服务器和数据库服务器在同一网段，且不要添加各种网络策略；
2. 应用服务开发要规范化，删除掉与本项目无关的东西，例如不用的定时任务等，配置方面、java底层方面在不了解具体情况下不少轻易改动；
3. 数据库服务方面，对于自己安装的mysql库，按照上面配置做数据库调优；
4. 应用服务启动脚本，使用项目中启动脚本，根据服务器资源情况，调整堆栈大小。






