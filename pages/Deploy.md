#### Nginx实现请求转发的方式？负载均衡的实现方式？
**反向代理和负载均衡**


| 策略 | 解释 | 代码 | 
| :----- | :----- | :----- | 
| <div style="width: 140px">轮询(默认)</div> | <div style="width: 200px">每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。</div> | upstream backserver { <br> &emsp;&emsp;server 192.168.0.14; <br> &emsp;&emsp;server 192.168.0.15; <br> }  | 
| <div style="width: 140px">指定权重</div> | <div style="width: 200px">指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。</div> | upstream backserver { <br> &emsp;&emsp;server 192.168.0.14 weight=10; <br> &emsp;&emsp;server 192.168.0.15 weight=10; <br> }  | 
| <div style="width: 140px">IP绑定 ip_hash</div> | <div style="width: 200px">每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。</div> | upstream backserver { <br> &emsp;&emsp;ip_hash; <br> &emsp;&emsp;server 192.168.0.14:88; <br> &emsp;&emsp;server 192.168.0.15:80; <br> } | 
| <div style="width: 140px">fair(第三方)</div> | <div style="width: 200px">按后端服务器的响应时间来分配请求，响应时间短的优先分配。</div> | upstream backserver { <br> &emsp;&emsp;server server1; <br> &emsp;&emsp;server server2; <br> &emsp;&emsp;fair; <br>} | 
| <div style="width: 140px">url_hash(第三方)</div> | <div style="width: 200px">按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。</div> | upstream backserver { <br> &emsp;&emsp;server squid1:3128; <br> &emsp;&emsp;server squid2:3128; <br> &emsp;&emsp;hash $request_uri; <br> &emsp;&emsp;hash_method crc32; <br> } | 


#### Tomcat的作用以及理解？
![Tomcat](/images/Deploy/Tomcat.png)


当一个动态动态网页编写完成后是不能直接被别人通过浏览器访问的，要想访问此动态网页就必须让浏览器通过一段程序来访问此网页，这段程序就是服务器，他用来接受浏览器的请求，进行处理，将结果返回给浏览器。


#### Linux常用命令
<div class="custom-table-height">
    <table>
        <tr>
            <th>命令</th>
            <th>解释</th>
        </tr>
        <tr>
            <td>man rm(rm --help)</td>
            <td>查看帮助</td>
        </tr>
        <tr>
            <td>cd</td>
            <td>进入目录</td>
        </tr>
        <tr>
            <td>ps -ef | grep java</td>
            <td>查看进程</td>
        </tr>
        <tr>
            <td>pstree | grep java</td>
            <td>查看进程树</td>
        </tr>
        <tr>
            <td>kill somePid</td>
            <td>杀掉某进程</td>
        </tr>
        <tr>
            <td>kill -9 $(ps -ef | grep udpserver | grep java&#124;awk '{print $2}' )</td>
            <td>删除udpserver进程</td>
        </tr>
        <tr>
            <td>rpm -aq | grep php</td>
            <td>查看安装介质</td>
        </tr>
        <tr>
            <td>pwd</td>
            <td>查看当前目录</td>
        </tr>
        <tr>
            <td>ls -l -t</td>
            <td>-l 显示详情，-t 按时间排序</td>
        </tr>
        <tr>
            <td>ll</td>
            <td>相当于ls -l</td>
        </tr>
        <tr>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td>find / -name libNativeMethod.so</td>
            <td>等同 ll | grep someFile</td>
        </tr>
        <tr>
            <td>grep someText *</td>
            <td>在当前目录所有文本中查找</td>
        </tr>
        <tr>
            <td>ifconfig</td>
            <td>IP地址配置，可以使用setup命令启动字符界面来配置</td>
        </tr>
        <tr>
            <td>chmod a+x someFile</td>
            <td>所有用户都可以执行</td>
        </tr>
        <tr>
            <td>chmod u+x someFile</td>
            <td>当前用户可以执行</td>
        </tr>
        <tr>
            <td>env</td>
            <td>环境配置，相当window下set</td>
        </tr>
        <tr>
            <td>env | grep PATH</td>
            <td>查看环境变量</td>
        </tr>
        <tr>
            <td>export</td>
            <td>相当于set classpath</td>
        </tr>
        <tr>
            <td>echo</td>
            <td>输出变量名</td>
        </tr>
        <tr>
            <td>netstat -npl</td>
            <td>查看端口</td>
        </tr>
        <tr>
            <td>lsof -i:22</td>
            <td>查看端口进程</td>
        </tr>
        <tr>
            <td>cp from to</td>
            <td>拷贝文件</td>
        </tr>
        <tr>
            <td>cp -fr ./j2sdk1.4.2_04 /usr/java</td>
            <td>拷贝目录</td>
        </tr>
        <tr>
            <td>mkdir</td>
            <td>创建目录</td>
        </tr>
        <tr>
            <td>mv</td>
            <td>剪切或者重命名</td>
        </tr>
        <tr>
            <td>rm -r</td>
            <td>递归删除， -f表示force</td>
        </tr>
        <tr>
            <td>>someFile</td>
            <td>清空文件内容</td>
        </tr>
        <tr>
            <td>which java</td>
            <td>查看java进程对应的目录</td>
        </tr>
        <tr>
            <td>who</td>
            <td>显示当前用户</td>
        </tr>
    </table>
</div>


#### 如何查看内存使用情况？
![vmstat](/images/Deploy/vmstat.PNG)


<div class="custom-table-height">
    <table>
        <tr>
            <td rowspan="2">procs</td>
            <td>r</td>
            <td>列表示运行和等待cpu时间片的进程数，如果长期大于1，说明cpu不足，需要增加cpu。</td>
        </tr>
        <tr>
            <td>b</td>
            <td>列表示在等待资源的进程数，比如正在等待I/O或者内存交换等。</td>
        </tr>
        <tr>
            <td rowspan="4">memory</td>
            <td>swpd</td>
            <td>切换到内存交换区的内存数量(k表示)。如果swpd的值不为0，或者比较大，比如超过了100m，只要si、so的值长期为0，系统性能还是正常。</td>
        </tr>
        <tr>
            <td>free</td>
            <td>当前的空闲页面列表中内存数量(k表示)。</td>
        </tr>
        <tr>
            <td>buff</td>
            <td>作为buffer cache的内存数量，一般对块设备的读写才需要缓冲。</td>
        </tr>
        <tr>
            <td>cache</td>
            <td>作为page cache的内存数量，一般作为文件系统的cache，如果cache较大，说明用到cache的文件较多，如果此时IO中bi比较小，说明文件系统效率比较好。</td>
        </tr>
        <tr>
            <td rowspan="2">swap</td>
            <td>si</td>
            <td>由内存进入内存交换区数量。</td>
        </tr>
        <tr>      
            <td>so</td>
            <td>由内存交换区进入内存数量。</td>
        </tr>
        <tr>
            <td rowspan="2">io</td>
            <td>bi</td>
            <td>从块设备读入数据的总量(读磁盘)(每秒kb)。</td>
        </tr>
        <tr>      
            <td>bo</td>
            <td>块设备写入数据的总量(写磁盘)(每秒kb)。</td>
        </tr>
        <tr>
            <td rowspan="2">system</td>
            <td>in</td>
            <td>列表示在某一时间间隔中观测到的每秒设备中断数。</td>
        </tr>
        <tr>      
            <td>cs</td>
            <td>列表示每秒产生的上下文切换次数，如当cs比磁盘I/O和网络信息包速率高得多，都应进行进一步调查。</td>
        </tr>    
        <tr>
            <td rowspan="4">cpu</td>
            <td>us</td>
            <td>列显示了用户方式下所花费 CPU 时间的百分比。us的值比较高时，说明用户进程消耗的cpu时间多，但是如果长期大于50%，需要考虑优化用户的程序。</td>
        </tr>
        <tr>      
            <td>sy</td>
            <td>列显示了内核进程所花费的cpu时间的百分比。这里us+sy的参考值为80%，如果us+sy大于80%说明可能存在CPU不足。</td>
        </tr>
        <tr>      
            <td>id</td>
            <td>列显示了cpu处在空闲状态的时间百分比。</td>
        </tr>
        <tr>      
            <td>wa</td>
            <td>列显示了IO等待所占用的CPU时间的百分比。这里wa的参考值为30%，如果wa超过30%，说明IO等待严重，这可能是磁盘大量随机访问造成的，也可能磁盘或者磁盘访问控制器的带宽瓶颈造成的(主要是块操作)。</td>
        </tr>
    </table>
</div>


#### Linux如何精准匹配一个日志记录？
```
tail -n  10  test.log   // 查询日志尾部最后10行的日志
tail -n +10 test.log    // 查询10行之后的所有日志
head -n 10  test.log    // 查询日志文件中的头10行日志
head -n -10  test.log   // 查询日志文件除了最后10行的其他所有日志
```


**按行号查看**


```
// 得到关键日志的行号，例如102行
cat -n test.log |grep "地形" 

// 得到"地形"关键字所在的行号是102行，此时如果我想查看这个关键字前10行和后10行的日志
// tail -n +92 表示查询92行之后的日志
// head -n 20 表示在前面的查询结果里再查前20条记录
cat -n test.log | tail -n +92 | head -n 20 
```


**按日期查看**


```
// 两个日期必须是日志中打印出来的日志，否则无效
// 可以先 grep '2014-12-17 16:17:20' test.log 确定日志中是否有该时间点
sed -n '/2014-12-17 16:17:20/,/2014-12-17 16:17:36/p'  test.log
```


#### Linux里面如何查找指定文件某个字段？
```shell
grep "search content" filename
```
![查找指定文件](/images/Deploy/search.PNG)






