### 网络


#### HTTP支持的请求方法？响应状态返回值的含义？
| 方法 | 解释 | 
| :----- | :----- | 
| OPTIONS | 返回服务器针对特定资源所支持的HTTP请求方法。 | 
| HEAD | 与GET请求一致，只不过不会返回响应体。 | 
| GET | 请求指定的资源。 | 
| POST | 向指定资源提交数据进行处理请求，数据包含在请求体中，可能会导致新资源的创建或已有资源的修改。 | 
| PUT | 向指定资源位置上传最新内容。 | 
| DELETE | 请求服务器删除所标识的资源。 | 

<div class="custom-table-height">
    <table>
        <tr>
            <td rowspan="2">1xx</td>
            <td rowspan="2" style="width: 200px">该类型状态码表示接收到请求并且继续处理。</td>
            <td>100</td>
            <td>客户端必须继续发出请求。</td>
        </tr>
        <tr>
            <td>101</td>
            <td>客户端要求服务器根据请求转换HTTP协议版本。</td>
        </tr>
        <tr>
            <td rowspan="7">2xx</td>
            <td rowspan="7" style="width: 200px">该类型状态码表示动作被成功接收、理解和接受。</td>
            <td>200</td>
            <td>表明该请求被成功地完成，所请求的资源发送到客户端。</td>
        </tr>
        <tr>
            <td>201</td>
            <td>提示知道新文件的URL。</td>
        </tr>
        <tr>
            <td>202</td>
            <td>接受并处理，但处理未完成。</td>
        </tr>
        <tr>
            <td>203</td>
            <td>返回信息不确定或不完整。</td>
        </tr>
        <tr>
            <td>204</td>
            <td>收到请求，但返回信息为空。</td>
        </tr>
        <tr>
            <td>205</td>
            <td>服务器完成了请求，用户必须复位当前已经浏览过的文件。</td>
        </tr>
        <tr>
            <td>206</td>
            <td>服务器已经完成了部分用户的GET请求。</td>
        </tr>
        <tr>
            <td rowspan="8">3xx</td>
            <td rowspan="8" style="width: 200px">该类型状态码表示为了完成指定的动作，必须接受进一步处理。</td>
            <td>300</td>
            <td>请求的资源可在多处获得。</td>
        </tr>
        <tr>
            <td>301</td>
            <td>本网页被永久性转移到另一个URL。</td>
        </tr>
        <tr>
            <td>302</td>
            <td>请求的网页被重定向到新的地址。</td>
        </tr>
        <tr>
            <td>303</td>
            <td>建议用户访问其他URL或访问方式。</td>
        </tr>
        <tr>
            <td>304</td>
            <td>自从上次请求后，请求的网页未修改过。</td>
        </tr>
        <tr>
            <td>305</td>
            <td>请求的资源必须从服务器指定的地址获得。</td>
        </tr>
        <tr>
            <td>306</td>
            <td>前一版本HTTP中使用的代码，现已不再使用。</td>
        </tr>
        <tr>
            <td>307</td>
            <td>声明请求的资源临时性删除。</td>
        </tr>
        <tr>
            <td rowspan="18">4xx</td>
            <td rowspan="18" style="width: 200px">该类型状态码表示请求包含错误语法或不能正确执行。</td>
            <td>400</td>
            <td>客户端请求有语法错误。</td>
        </tr>
        <tr>
            <td>401</td>
            <td>请求未经授权。</td>
        </tr>
        <tr>
            <td>402</td>
            <td>保留有效ChargeTo头响应。</td>
        </tr>
        <tr>
            <td>403</td>
            <td>禁止访问，服务器收到请求，但拒绝提供服务。</td>
        </tr>
        <tr>
            <td>404</td>
            <td>可连接服务器，但服务器无法取得所请求的网页，请求资源不存在。</td>
        </tr>
        <tr>
            <td>405</td>
            <td>用户在Request-Line字段定义的方法不被允许。</td>
        </tr>
        <tr>
            <td>406</td>
            <td>根据用户发送的Accept，请求资源不可访问。</td>
        </tr>
        <tr>
            <td>407</td>
            <td>类似401，用户必须首先在代理服务器上取得授权。</td>
        </tr>
        <tr>
            <td>408</td>
            <td>客户端没有在用户指定的时间内完成请求。</td>
        </tr>
        <tr>
            <td>409</td>
            <td>对当前资源状态，请求不能完成。</td>
        </tr>
        <tr>
            <td>410</td>
            <td>服务器上不再有此资源。</td>
        </tr>
        <tr>
            <td>411</td>
            <td>服务器拒绝用户定义的Content-Length属性请求。</td>
        </tr>
        <tr>
            <td>412</td>
            <td>一个或多个请求头字段在当前请求中错误。</td>
        </tr>
        <tr>
            <td>413</td>
            <td>请求的资源大于服务器允许的大小。</td>
        </tr>
        <tr>
            <td>414</td>
            <td>请求的资源URL长于服务器允许的长度。</td>
        </tr>
        <tr>
            <td>415</td>
            <td>请求资源不支持请求项目格式。</td>
        </tr>
        <tr>
            <td>416</td>
            <td>请求中包含Range请求头字段，在当前请求资源范围内没有range指示值。</td>
        </tr>
        <tr>
            <td>417</td>
            <td>服务器不满足请求Expect头字段指定的期望值。</td>
        </tr>
        <tr>
            <td rowspan="6">5xx</td>
            <td rowspan="6" style="width: 200px">该类型状态码表示服务器或网关错误。</td>
            <td>500</td>
            <td>服务器错误。</td>
        </tr>
        <tr>
            <td>501</td>
            <td>服务器不支持请求的功能。</td>
        </tr>
        <tr>
            <td>502</td>
            <td>网关错误。</td>
        </tr>
        <tr>
            <td>503</td>
            <td>无法获得服务。</td>
        </tr>
        <tr>
            <td>504</td>
            <td>网关超时。</td>
        </tr>
        <tr>
            <td>505</td>
            <td>不支持的HTTP版本。</td>
        </tr>
    </table>
</div>


#### HTTP 502和504有何异同？
502 bad gateway，顾名思义网关错误，后端服务器tomcat没有起来，应用服务的问题(前提是接入层7层正常的情况下)。


应用服务问题一种是应用本身问题；另一种是因为依赖服务问题比如依赖服务RT高，依赖的服务有大的读取(mysql慢查，http等)，以至于调用方超过超时read时间；服务集群压力大时，也会出现502超时(502理解为不可响应或响应不过来，其实还是不可响应)。


504 gateway time-out，顾名思义网关超时，一般计算机中的超时就是配置错了，此处一般指nginx做反向代理服务器时，所连接的服务器tomcat无响应导致的。


从网络角度，502已经与后端建立了连接，但超时；504与后端连接未建立，超时。


**必现502，应用“挂了”**


1. 后端机器上检查:
    1. `ps -ef |grep java` 检查进程是否在。
    2. `sudo netstat -lntp |grep port` 检查端口有没有起来。
    3. `curl -I 127.0.0.1:port/health` 应用健康检查测试下，Your health check path。
2. 上面都正常，看下接入层access.log有没有进来:
    1. `tail -300f access.log | grep xxxx` grep关键字。
    2. `curl -I 10.10.10.10:80/java_hc` 上面都正常情况下，去接入层检查下。


**偶现502**


1. CPU使用率高，QPS增加。考虑有大流量，后端压力导致短暂不可用，考虑临时扩容。
2. 检查应用本身nginx read超时时间配置。`proxy_read_timeout 2s; # vim /opt/nginx/nginx.conf`。如果某些正常请求耗时在2s左右，那么会有少量大于2s的请求是502的。可以试着把上面耗时时间调大，看问题是否缓解。优化本身链路请求耗时是根本上的解决办法。
3. 检查接入层nginx read的配置。


#### 浏览器发生301/302跳转背后的逻辑以及它们的区别？什么是重定向限制？
**301永久重定向**


我们访问`http://www.baidu.com`会跳转到`https://www.baidu.com`。发送请求之后，就会返回301状态码，然后返回一个location，提示新的地址，浏览器就会拿着这个新的地址去访问。注意301请求是可以缓存的，即通过看status code，可以发现后面写着from cache。或者你把你的网页的名称从php修改为了html，这个过程中，也会发生永久重定向。
```javascript
//nginx301配置：把来自veryyoung.me的请求301跳到www.veryyoung.me
if ($host != 'veryyoung.me') {
    rewrite ^/(.*)$ http://www.veryyoung.me/$1 permanent;
}
```


**302临时重定向**


比如未登陆的用户访问用户中心重定向到登录页面，访问404页面会重新定向到首页。
```javascript
//nginx302配置：把来自veryyoung.me的请求302跳到www.veryyoung.me
if ($host != 'veryyoung.me') {
    rewrite ^/(.*)$ http://www.veryyoung.me/$1 redirect;
}
```


`重定向限制`是指浏览器设置，限制你的浏览器的次数。会接受“重定向”从一个主机到另一个。例如，一个网站可能已经从一个服务器或URL移动到另一个技术或其他原因，为了更方便用户，网站所有者可以实施了一项命令，告诉浏览器自动走到另一个位置(重定向)。重定向限制错误可能发生无意中当一个web页面或页面无意中创建一个循环。例如，如果页面重定向到本身，或B页面重定向回页面，那么您的web浏览器将开始构建连接在循环的每一根琴弦，直到超过限制。


#### HTTP响应的结构是怎么样的？
| 组成部分 | 解释 | 
| :----- | :----- | 
| <div style="width: 200px">状态码(Status Code)</div> | 描述了响应的状态。可以用来检查是否成功的完成了请求。请求失败的情况下，状态码可用来找出失败的原因。如果Servlet没有返回状态码，默认会返回成功的状态码HTTPServletResponse.SC_OK。 | 
| <div style="width: 200px">HTTP头部(HTTP Header)</div> | 它们包含了更多关于响应的信息。比如：头部可以指定认为响应过期的过期日期，或者是指定用来给用户安全的传输实体内容的编码格式。如何在Servlet中检索HTTP的头部看这里。 | 
| <div style="width: 200px">主体(Body)</div> | 它包含了响应的内容。它可以包含HTML代码、图片等等。主体是由传输在HTTP消息中紧跟在头部后面的数据字节组成的。 | 


#### 你知道HTTP和TCP的区别吗？
| HTTP | TCP | 
| :----- | :----- | 
|<div style='width: 300px'>1. HTTP是应用层协议，定义的是传输数据的内容的规范；<br>2. HTTP协议中的数据是利用TCP协议传输的，所以支持HTTP也就一定支持TCP；<br>3. HTTP支持的是www服务。</div>|1. TCP是底层通讯协议，定义的是数据传输和连接方式的规范；<br>2. TCP/IP是协议，它是Internet国际互联网络的基础；<br>3. TCP/IP是网络中使用的基本的通信协议。它包括上百个各种功能的协议，如：远程登录、文件传输和电子邮件等，而TCP协议和IP协议是保证数据完整传输的两个基本的重要协议。通常说TCP/IP是Internet协议族，而不单单是TCP和IP。|


#### 什么是cookie？session和cookie有什么区别？
cookie是Web服务器发送给浏览器的一块信息，浏览器会在本地文件中给每一个Web服务器存储cookie，以后浏览器在给特定的Web服务器发请求的时候，同时会发送所有为该服务器存储的cookie。


| session | cookie | 
| :----- | :----- | 
| <div style='width: 300px'>1. 基于服务器生成；<br>2. 数据保存在服务器端；<br>3. 相对更安全；<br>4. 过期时间是相对的；<br>5. 客户端无法禁用服务端的session，无论客户端浏览器做怎么样的设置，session都应该能正常工作；<br>6. 能够存储任意的Java对象。</div> | 1. 基于浏览器生成；<br>2. 数据保存在客户端；<br>3. 相对不安全；<br>4. 过期时间是绝对的；<br>5. 客户端可以选择禁用cookie；<br>6. 只能存储String类型的对象。  | 


#### GET提交方式与POST提交方式的区别？
| GET | POST | 
| :----- | :----- | 
| 1. 在浏览器回退时是无害的；<br>2. URL地址可以被收藏；<br>3. 被浏览器主动缓存；<br>4. 只能进行URL编程；<br>5. 请求参数会被完整保留在浏览器历史里；<br>6. 在URL中传送的参数有长度限制，不能大于2KB；<br>7. 只接受ASCLL字符；<br>8. 参数直接暴露在URL上，所以不能用来传递敏感信息；<br>9. 不支持文件上传。 | 1. 在浏览器回退时再次请求；<br>2. URL地址不会被收藏；<br>3. 不会被浏览器主动缓存，除非手动设置；<br>4. 支持多种编码方式；<br>5. 参数不会被保留在浏览器历史里；<br>6. 在URL中传送中参数没有长度限制；<br>7. 字符没有限制；<br>8. 参数放在request body中，安全；<br>9. 支持文件上传。 | 


#### 客户端传输的数据如何保证安全性？
| 方式 | 解释 | 特点 | 局限性 | 算法 | 
| :----- | :----- | :----- | :----- | :----- | 
| <div style="width: 120px">对称加密</div> | 加密和解密使用同一个密钥。 | 保证了数据的保密性。 | 无法解决密钥交换问题。 | DES、3DES、AES。 | 
| <div style="width: 120px">公钥加密</div> | 生成一个密钥对，加密使用私钥加密，解密使用公钥解密。 | 解决了密钥交换问题。 | 对大的数据加密速度慢。 | ———— | 
| <div style="width: 120px">单向加密</div> | 提取数据的特征码。 | 定长输出，不可逆，可检验数据的完整性。 | 无法保证数据的保密性。 | MD5、SHA1、CRC-32。 | 


#### 用浏览器请求一个链接的时候，经历了哪些过程？
```
// 参数
ip地址：192.168.31.37
子网掩码：255.255.255.0
网关地址：192.168.31.1
DNS地址：8.8.8.8
```
![HttpRequest](/images/Network/HttpRequest.png)
1. 请求www.baidu.com地址，这个时候找DNS服务器，DNS服务器解析域名之后，返回一个ip地址，比如172.194.26.108。
2. 判断本机和访问的IP是不是同一个子网，用子网掩码坐与运算。通常不在一个子网，就发送数据包给网关，也就是路由器。通过浏览器访问网站，走应用层的http协议，把浏览器发出的请求打包为数据包。把http协议请求，有请求头、空行、请求数据，就构成http请求报文，封装到一个应用层数据包里。
3. 接下来走到传输层，按照TCP协议，TCP协议会让你设置端口，发送方端口随机选择，接收端一般默认80端口。这时会把应用层数据包封装到TCP数据包，再加一个TCP头，TCP头放了端口信息。
4. 走网络层，会把TCP头和TCP数据包，放到IP数据包里，再做了一个IP头，IP头包括本机和目标机器的IP地址。通过IP协议，判断两个IP不在一个子网内，则将数据包通过以太网协议广播到网关，通过网关在发生出去。
5. 最后走到数据链路层，把IP头和IP数据包封装到以太网数据包，再增加一个以太网数据包头，头里放了本机网卡MAC地址和网关MAC地址。但以太网数据包有1500字节的限制，超过要切分为多个数据包，每个数据包包含以太网头、IP头和切割后的IP数据包。


以太网数据包通过交换机发送到网关，然后通过路由器转发到别的子网或者别的路由器。以此类推，通过N个路由器或网关转发，最终到达目标服务器，比如172.194.26.108。


目标服务器接收到以太网数据包后，从IP数据包，拿出TCP数据包，再从TCP数据包取出HTTP数据包，读取出HTTP数据包里各种协议内容，比如html页面，或者业务处理，然后再把响应结果封装成http响应报文，封装到http数据包里，再封装TCP数据包，封装IP数据包，封装以太网数据包，再通过网关发送回去，完成整个请求过程。


#### 你知道DNS域名解析的流程吗？
![DNS](/images/Network/DNS.png)
1. 浏览器先检查自身缓存中有没有被解析过的这个域名对应的IP地址。如果有，解析结束，同时域名被缓存的时间也可通过TTL属性来设置。
2. 如果浏览器缓存中没有，浏览器会检查操作系统缓存中有没有对应的已解析过的结果。而操作系统也有一个域名解析的过程。在windows中可通过C盘里一个叫hosts的文件来设置，如果你在这里指定了一个域名对应的IP地址，那浏览器会首先使用这个IP地址。但是这种操作系统级别的域名解析规程也被很多黑客利用，通过修改你的hosts文件里的内容把特定的域名解析到他指定的IP地址上，造成所谓的域名劫持。所以在windows7中将hosts文件设置成了readonly，防止被恶意篡改。
![hosts](/images/Network/hosts.PNG)
3. 如果至此还没有命中域名，才会真正的请求本地域名服务器(LDNS)来解析这个域名，这台服务器一般在你的城市的某个角落，距离你不会很远，并且这台服务器的性能都很好，一般都会缓存域名解析结果，大约80%的域名解析到这里就完成了。
4. 如果LDNS仍然没有命中，就直接跳到Root Server域名服务器请求解析。
5. 根域名服务器返回给LDNS一个所查询域的主域名服务器(gTLD Server，国际顶尖域名服务器，如.com .cn .org等)地址。
6. 此时LDNS再发送请求给上一步返回的gTLD。
7. 接受请求的gTLD查找并返回这个域名对应的Name Server的地址，这个Name Server就是网站注册的域名服务器。
8. Name Server根据映射关系表找到目标IP，返回给LDNS。
9. LDNS缓存这个域名和对应的IP。
10. LDNS把解析的结果返回给用户，用户根据TTL值缓存到本地系统缓存中，域名解析过程至此结束。


#### 聊聊HTTP协议的工作原理？
![HTTP](/images/Network/HTTP.png)


👉 [聊聊Http协议](https://www.cnblogs.com/joylee/p/htmlprotocol.html)


#### 聊聊HTTPS协议的工作原理？
![HTTPS](/images/Network/HTTPS.png)


👉 [深入理解HTTPS工作原理](https://segmentfault.com/a/1190000018992153)


1. 客户端向服务器发起HTTPS请求，连接到服务器的443端口。
2. 服务器端有一个密钥对，即公钥和私钥，是用来进行非对称加密使用的，服务器端保存着私钥，不能将其泄露，公钥可以发送给任何人。
3. 服务器将自己的公钥发送给客户端。
4. 客户端收到服务器端的证书之后，会对证书进行检查，验证其合法性，如果发现发现证书有问题，那么HTTPS传输就无法继续。严格的说，这里应该是验证服务器发送的数字证书的合法性，关于客户端如何验证数字证书的合法性，下文会进行说明。如果公钥合格，那么客户端会生成一个随机值，这个随机值就是用于进行对称加密的密钥，我们将该密钥称之为client key，即客户端密钥，这样在概念上和服务器端的密钥容易进行区分。然后用服务器的公钥对客户端密钥进行非对称加密，这样客户端密钥就变成密文了，至此，HTTPS中的第一次HTTP请求结束。
5. 客户端会发起HTTPS中的第二个HTTP请求，将加密之后的客户端密钥发送给服务器。
6. 服务器接收到客户端发来的密文之后，会用自己的私钥对其进行非对称解密，解密之后的明文就是客户端密钥，然后用客户端密钥对数据进行对称加密，这样数据就变成了密文。
7. 然后服务器将加密后的密文发送给客户端。
8. 客户端收到服务器发送来的密文，用客户端密钥对其进行对称解密，得到服务器发送的数据。这样HTTPS中的第二个HTTP请求结束，整个HTTPS传输完成。


#### 你知道浏览器的同源策略吗？
| URL | 解释 | 是否允许通信 | 
| :----- | :----- | :----- | 
| `HTTP://www.a.com/a.js` <br> `HTTP://www.a.com/b.js` | 同一域名下 | √ | 
| `HTTP://www.a.com/lab/a.js` <br> `HTTP://www.a.com/script/b.js` | 同一域名下不同文件夹 | √ | 
| `HTTP://www.a.com:8000/a.js` <br> `HTTP://www.a.com/b.js` | 同一域名，不同端口 | × | 
| `HTTP://www.a.com/a.js` <br> `HTTPs://www.a.com/b.js` | 同一域名，不同协议 | × | 
| `HTTP://www.a.com/a.js` <br> `HTTP://70.32.92.74/b.js` | 域名和域名对应ip | × | 
| `HTTP://www.a.com/a.js` <br> `HTTP://script.a.com/b.js` | 主域相同，子域不同 | × | 
| `HTTP://www.a.com/a.js` <br> `HTTP://a.com/b.js` | 同一域名，不同二级域名 | × | 
| `HTTP://www.cnblogs.com/a.js` <br> `HTTP://www.a.com/b.js` | 不同域名 | × | 


<span style="color: red">如果是协议和端口造成的跨域问题“前台”是无能为力的。</span>


<span style="color: red">在跨域问题上，域仅仅是通过“URL的首部”来识别而不会去尝试判断相同的ip地址对应着两个域或两个域是否在同一个ip上。</span>


`URL的首部`指`window.location.protocol + window.location.host`，也可以理解为“Domains, protocols and ports must match”。


#### 前端解决跨域的方案？
👉 [浏览器拦截跨域请求处理方法](HTTPs://www.cnblogs.com/PheonixHkbxoic/p/5760838.html)


**CORS**


它允许浏览器向跨源服务器，发出XMLHTTPRequest请求，从而克服了AJAX只能同源使用的限制。整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。
```javascript
// IE浏览器
var xdr = new XDomainRequest();
xdr.onload = function(){
    console.log(xdr.responseText);
}
xdr.open('get', 'HTTP://www.baidu.com');
...
xdr.send(null);

// 其它浏览器
var xhr =  new XMLHTTPRequest();
xhr.onreadystatechange = function () {
    if(xhr.readyState == 4){
        if(xhr.status >= 200 && xhr.status < 304 || xhr.status == 304){
            console.log(xhr.responseText);
        }
    }
}
xhr.open('get', 'HTTP://www.baidu.com');
...
xhr.send(null);
```
```javascript
// 实现跨浏览器的CORS
function createCORS(method, url){
    var xhr = new XMLHTTPRequest();
    if('withCredentials' in xhr){
        xhr.open(method, url, true);
    }else if(typeof XDomainRequest != 'undefined'){
        var xhr = new XDomainRequest();
        xhr.open(method, url);
    }else{
        xhr = null;
    }
    return xhr;
}
var request = createCORS('get', 'HTTP://www.baidu.com');
if(request){
    request.onload = function(){
        ...
    };
    request.send();
}
```


#### 什么是跨域？如何解决跨域问题？
👉 [什么是跨域？如何解决跨域问题？](https://blog.csdn.net/tjcjava/article/details/76468225)


#### 你知道TCP和UDP的区别吗？
👉 [TCP和UDP的9个区别是什么](https://blog.csdn.net/lzuacm/article/details/50945225)


| TCP | UDP | 
| :----- | :----- | 
| 1. 面向连接；<br>2. 可靠；<br>3. 有序；<br>4. 不保存数据的边界；<br>5. 速度比较慢；<br>6. 重量级；<br>7. 数据包报头的大小是20字节；<br>8. 流量控制；<br>9. 金融领域。  | 1. 无连接；<br>2. 不可靠；<br>3. 不提供序列性保证；<br>4. 保存数据的边界；<br>5. 速度比较快；<br>6. 轻量级；<br>7. 数据包报头是8个字节；<br>8. 不能进行流量控制；<br>9. 游戏和娱乐场所。| 


#### DDos攻击及预防？
👉 [DDoS的攻击原理与防御方法](https://blog.csdn.net/huwei2003/article/details/45476743)


1. 确保服务器的系统文件是最新的版本，并及时更新系统补丁。
2. 关闭不必要的服务。
3. 限制同时打开的SYN半连接数目，缩短SYN半连接的time out时间，限制SYN/ICMP流量。
4. 正确设置防火墙。
5. 认真检查网络设备和主机/服务器系统的日志，只要日志出现漏洞或是时间变更，那这台机器就可能遭到了攻击。
6. 限制在防火墙外与网络文件共享，这样会给黑客截取系统文件的机会，主机的信息暴露给黑客，无疑是给了对方入侵的机会。
7. 充分利用网络设备保护网络资源。
8. 用足够的机器承受黑客攻击。
9. 检查访问者的来源。


#### 你能聊聊TCP/IP四种网络模型吗？OSI七层网络模型也说一下？
**TCP/IP四种网络模型**


![TCP](/images/Network/TCP.PNG)


| TCP/IP名称 | 协议类型 | 
| :----- | :----- | 
|应用层(Application Layer)|HTTP协议|
|传输层(Transport Layer-TCP/UDP)|TCP协议|
|网络层(Internet Layer)|IP协议|
|数据链路层|以太网协议|


**OSI七层网络模型**


![OSI](/images/Network/OSI.PNG)


物理层、数据链路层、网络层、传输层、会话层、表示层、应用层。


#### TCP握手为啥是三次而不是二次或者四次呢？
![TCP握手](/images/Network/shakehand.png)


建立三次握手的时候，TCP报头用到了下面几个东西，ACK、SYN、FIN。第一次握手，客户端发送连接请求报文，此时SYN=1、ACK=0，这就是说这是个连接请求，seq=x，接着客户端处于SYN_SENT状态，等待服务器响应。第二次握手，服务端收到SYN=1的请求报文，需要返回一个确认报文，ack=x+1，SYN=1，ACK=1，seq=y，发送给客户端，自己处于SYN_RECV状态。第三次握手，客户端收到了报文，将ack=y+1，ACK=1，seq=x+1。


假设两次握手就ok了。要是客户端第一次握手过去，结果卡在某个地方了，没到服务端；完了客户端再次重试发送了第一次握手过去，服务端收到了，ok了，大家来回来去，三次握手建立了连接。结果，尴尬的是，后来那个卡在哪儿的老的第一次握手发到了服务器，服务器直接就返回一个第二次握手，这个时候服务器开辟了资源准备客户端发送数据啥的，结果呢？客户端根本就不会理睬这个发回去的二次握手，因为之前都通信过了。但是如果是三次握手，那个二次握手发回去，客户端发现根本不对，就会发送个复位的报文过去，让服务器撤销开辟的资源，别等着了。


如果四次挥手的话。第一次挥手，客户端发送报文，FIN=1，seq=u，此时进入FIN-WAIT-1状态。第二次挥手，服务端收到报文，这时候进入CLOSE_WAIT状态，返回一个报文，ACK=1，ack=u+1，seq=v。客户端收到这个报文之后，直接进入FIN-WAIT-2状态，此时客户端到服务端的连接就释放了。第三次挥手，服务端发送连接释放报文，FIN=1，ack=u+1，seq=w，服务端进入LAST-ACK状态。第四次挥手，客户端收到连接释放报文之后，发应答报文，ACK=1，ack=w+1，seq=u+1，进入TIME_WAIT状态，等待一会儿客户端进入CLOSED状态，服务端收到报文之后就进入CLOSED状态。


#### TCP粘包/拆包的原因及解决方法？
<table>
<tr>
    <th>————</th>
    <th>拆包</th>
    <th>粘包</th>
</tr>
<tr>
    <td rowspan="2">原因</td>
    <td>应用程序写入数据大于TCP缓冲区剩余空间大小。</td>
    <td>应用程序写入数据小于TCP缓冲区大小，TCP将多次写入缓冲区的数据一次发送出去。</td>
</tr>
<tr>
    <td>应用程序写入数据大于MSS(最大报文长度)，会发生拆包现象。</td>
    <td>接收数据端的应用层没有及时读取接收缓冲区中的数据。</td>
</tr>
<tr>
    <td>解决办法</td>
    <td colspan="2">1. <b>消息定长(FixedLengthFrameDecoder类)</b>：发送端将每个数据包封装为固定长度(不够的可以通过补0填充)，这样接收端每次从接收缓冲区中读取固定长度的数据就自然而然的把每个数据包拆分开来；<br>
    2. <b>将消息分为消息头和消息体(LengthFieldBasedFrameDecoder类)</b>：发送端给每个数据包添加包首部，首部中应该至少包含数据包的长度，这样接收端在接收到数据后，通过读取包首部的长度字段，便知道每一个数据包的实际长度了；<br>
    3. <b>包尾增加特殊字符分割</b>：行分隔符类LineBasedFrameDecoder或自定义分隔符类DelimiterBasedFrameDecoder。
    </td>
</tr>
</table>


#### 能聊聊BIO、NIO、AIO分别都是啥？有什么区别？
![BIO&NIO](/images/Network/BIO&NIO.jpg)


**BIO：同步阻塞式IO。**


服务端创建一个ServerSocket，客户端用一个Socket去连接那个ServerSocket，然后ServerSocket接收到Socket连接请求就创建一个Socket和一个线程去和客户端Socket通信。


客户端socket发送一个请求，服务端socket进行处理后返回响应，响应必须是等处理完成之后才能返回，这期间不能做其他事，只能等待(卡住)。而且每一个客户端接入，服务端就需要创建一个线程来服务，会导致客户端大量增加时，服务端线程会负责过高(可能几千几万个线程)，最后服务端崩溃。可以用线程池固定线程来处理客户端请求，但是高并发时会导致大量排队和延时。


**NIO：同步非阻塞IO，基于Reactor模型。**


`Channel`表示为一个已经建立好的支持I/O操作的实体(如文件和网络)的连接，在此连接上进行数据的读写操作，使用的是缓冲区来实现读写。`Buffer`在内存中预留指定大小的存储空间用来对输入/输出(I/O)的数据作临时存储。好处：1. 减少实际的物理读写次数 2. 缓冲区在创建时就被分配内存，这块内存区域一直被重用，可以减少动态分配和回收内存的次数。好比从A工地搬1w块砖到B工地，路途远(IO性能消耗大)，没有工具时(缓冲区)，一次搬1块，要搬1w次(IO读写1w次)。用卡车(缓冲区)，一次运送5K块，2次就运完了，性能大大提高了。`Selector`会不断轮询注册的channel，如果某个channel上发生了读写事件，selector就会将这些channel获取出来，我们通过SelectionKey获取有读写事件的channel，就可以进行IO操作。一个Selector就通过一个线程，就可以轮询成千上万的channel，这就意味着你的服务端可以接入成千上万的客户端。


核心就是<span style='color: red;'>非阻塞</span>，selector一个线程可以不停轮询channel，所有客户端都不会阻塞，最多排队。只有某个客户端发送了一个请求，才会启动一个线程来处理。客户端接入不会耗费一个线程，只会创建一个channel连接，然后注册到selector上，一个selector线程不断轮询所有的socket连接(channel)，发现有事件就通知，启动工作线程处理一个请求。工作线程从channel-buffer读数据，如果数据没有读完卡住，等待。然后处理后往buffer-channel里写数据，也是自己做，写数据时数据没有写完，也卡住等待，是同步的。


**AIO：基于Proactor模型的，就是异步非阻塞模型。**


每个请求会绑定一个buffer，通知操作系统去异步完成读写，此时工作线程可以做别的事情(异步)，等操作系统完成之后回调接口，把操作系统完成的数据给工作线程。


#### 你知道Netty的零拷贝技术吗？
![零拷贝](/images/Network/Zero-Copy.png)


👉 [netty深入理解系列-Netty零拷贝的实现原理](https://www.cnblogs.com/200911/articles/10432551.html)


#### 什么是ARP协议？
👉 [计网---ARP协议](https://blog.csdn.net/weixin_45480785/article/details/117196555)


#### HTTPS是先进行TCP三次握手，再进行TLS四次握手？
![](/images/Network/HTTPSTLS.png)


1. HTTPS是先进行TCP三次握手，再进行TLSv1.2四次握手。
2. HTTPS中的TLS握手过程可以同时进行三次握手，这个场景是可能存在。但是在没有说任何前提条件，而说这句话就等于耍流氓。需要下面这两个条件同时满足才可以:
    1. 客户端和服务端都开启了TCP Fast Open功能，且TLS版本是1.3；
    2. 客户端和服务端已经完成过一次通信。


👉 [给面试官上一课：HTTPS是先进行TCP三次握手，再进行TLS四次握手](https://blog.csdn.net/javaAnPou/article/details/124948633)



#### SSL/TLS协议建立的详细流程？
1. **ClientHello**。首先，由客户端向服务器发起加密通信请求，也就是ClientHello请求。在这⼀步，客户端主要向服务器发送以下信息:
    1. 客户端支持的SSL/TLS协议版本，如TLS 1.2版本。
    2. 客户端生产的随机数(Client Random)，后面用于生产会话秘钥。
    3. 客户端支持的密码套件列表，如RSA加密算法。
2. **SeverHello**。服务器收到客户端请求后，向客户端发出响应，也就是SeverHello。服务器回应的内容有如下内容:
    1. 确认SSL/TLS协议版本，如果浏览器不支持，则关闭加密通信。
    2. 服务器生产的随机数(Server Random)，后面用于生产会话秘钥。
    3. 确认的密码套件列表，如RSA加密算法。
    4. 服务器的数字证书。
3. **客户端回应**。客户端收到服务器的回应之后，首先通过浏览器或者操作系统中的CA公钥，确认服务器的数字证书的真实性。如果证书没有问题，客户端会从数字证书中取出服务器的公钥，然后使⽤它加密报文，向服务器发送如下信息:
    1. ⼀个随机数(pre-master key)。该随机数会被服务器公钥加密。
    2. 加密通信算法改变通知，表示随后的信息都将用会话秘钥加密通信。
    3. 客户端握手结束通知，表示客户端的握手阶段已经结束。这⼀项同时把之前所有内容的发生的数据做个摘要，用来供服务端校验。上面第⼀项的随机数是整个握手阶段的第三个随机数，这样服务器和客户端就同时有三个随机数，接着就用双方协商的加密算法，各自生成本次通信的会话秘钥。
4. **服务器的最后回应**。服务器收到客户端的第三个随机数(pre-master key)之后，通过协商的加密算法，计算出本次通信的会话秘钥。然后，向客户端发送最后的信息:
    1. 加密通信算法改变通知，表示随后的信息都将用会话秘钥加密通信。
    2. 服务器握手结束通知，表示服务器的握手阶段已经结束。这⼀项同时把之前所有内容的发生的数据做个摘要，用来供客户端校验。


至此，整个SSL/TLS的握手阶段全部结束。接下来，客户端与服务器进入加密通信，就完全使用普通的HTTP协议，只不过用会话秘钥加密内容。


#### 浏览器建立TCP连接之后，完成一次HTTP请求，是否会断开？
HTTP协议Header中的Connection属性决定了连接是否持久，不同HTTP协议版本有所不同。


HTTP/1.0中Connection默认为close，即每次请求都会重新建立和断开TCP连接。缺点是建立和断开TCP连接，代价过大。


HTTP/1.1中Connection默认为keep-alive，即连接可以复用，不用每次都重新建立和断开TCP连接。超时之后没有连接则主动断开。可以通过声明Connection为close进行关闭。优点是TCP连接可被重复利用，减少建立连接的损耗，SSL的开销也可以避免。刷新页面时也可以复用，从而不再建立SSL连接等。


默认情况下(HTTP/1.1)建立TCP连接不会断开，只有在请求报头中声明Connection: close才会请求完成之后关闭连接。不断开的最终目的是减少建立连接所导致的性能损耗。


#### 一个TCP连接可以对应几个HTTP请求？
1. 如果`Connection: close`，则一个TCP连接只对应一个HTTP请求。
2. 如果`Connection: Keep-alive`，则一个TCP连接可对应一个到多个HTTP请求。


#### 一个TCP连接中，可以同时发送多个HTTP请求吗？
TTP/1.1中单个TCP连接在同一时刻只能处理一个请求。HTTP/1.1在RFC 2616中规定了Pipelining来解决这个问题，但浏览器默认是关闭的。


RFC 2616中规定一个支持持久连接的客户端可以在一个连接中发送多个请求(不需要等待任意请求的响应)。收到请求的服务器必须按照请求收到的顺序发送响应。


Pipelining本身存在一些问题，比如代理服务器不能正确处理HTTP Pipelining、Head-of-line Blocking连接头阻塞(首个请求耗时过长，阻塞其他请求)。所以浏览器默认关闭该功能。


HTTP/2.0提供了多路复用技术Multiplexing，一个TCP可以并发多个HTTP请求(理论无上限，但是一般浏览器会有TCP并发数的限制)。


HTTP/1.1中为了提升性能，通常会采用连接复用和同时建立多个TCP连接的方式提升性能。


HTTP/1.1中存在Pipelining技术支持一个连接发送多个请求，但存在弊端，浏览器默认关闭。HTTP/2.0中通过多路复用技术支持一个TCP连接中并发请求HTTP。


#### 浏览器对同一Host建立TCP连接的数量有没限制？
不同浏览器限制不同，比如Chrome最多允许同一个Host可建立6个TCP连接。


如果服务器只支持HTTP/1.1，浏览器会采用在同一个Host下建立多个TCP连接来进行效率提升。如果是基于HTTPS传输，在SSL握手之后，还会尝试协商是否可以采用HTTP/2.0的Multiplexing功能。


#### keep-alive使用场景及优缺点？
开启keep-alive对内存要求高，关闭keep-alive对CPU要求高；如果内存和CPU都足够，开启和关闭keep-alive对性能影响不大；如果考虑服务器压力，如果是静态页面，大量的调用js或者图片的话，建议开启keep-alive；如果是动态网页，建议关闭keep-alive。


注意事项: 如果需要使用keep-alive功能，服务器端如果使用nginx中keepalive_timeout值要大于0。

