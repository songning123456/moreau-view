#### HTTP请求报文由哪几部分组成？
请求报文是由请求方法、请求URI、协议版本、一些可选的请求首部字段和内容实体构成。下面是在network截取的一个请求：


General：


```
Request URL: https://www.baidu.com/home/xman/data/tipspluslist?indextype=manht&_req_seqid=0x8466e3ac0001093f&asyn=1&t=1552961901647&sid=28628_1427_21083_28720_28558_28607_28585_26350_28603_28625_28703
Request Method: GET
Status Code: 200 OK
Remote Address: 112.34.112.40:443
Referrer Policy: unsafe-url
```


Response headers


```
Cache-Control: private
Connection: keep-alive
Content-Encoding: gzip
Content-Length: 78
Content-Type: text/html;charset=utf-8
Date: Tue, 19 Mar 2019 02:18:22 GMT
Expires: Tue, 19 Mar 2019 02:18:22 GMT
Server: BWS/1.0
Tracecode: 11023798470767062538031910
Vary: Accept-Encoding
```


Request headers


```
Accept: text/plain, */*; q=0.01
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Cache-Control: no-cache
Connection: keep-alive
Cookie: BIDUPSID=18640EC56A0710DC537203C0A89DDE20; 
Host: www.baidu.com
Pragma: no-cache
Referer: https://www.baidu.com/
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36
X-Requested-With: XMLHttpRequest
```


Query String Parameters


```
indextype: manht
_req_seqid: 0x8466e3ac0001093f
asyn: 1
t: 1552961901647
sid: 28628_1427_21083_28720_28558_28607_28585_26350_28603_28625_28703
```


#### HTTP协议是无状态还是有状态？
HTTP是一种不保存状态，即无状态(stateless)协议。HTTP协议自身不对请求和响应之间的通信状态进行保存。也就是说在HTTP这个级别，协议对于发送过的请求或响应都不做持久化处理。使用HTTP协议，每当有新的请求发送时，就会有对应的新响应产生。协议本身并不保留之前一切的请求或者响应报文的信息。这是为了更快地处理大量事务，确保协议的可伸缩性，而特意把HTTP协议设计成如此简单的。随着WEB的不断发展，因无状态而导致业务处理变得棘手的情况增多了。为了实现期望的保持状态功能，于是引入了Cookie技术。有了Cookie再用HTTP协议通信，就可以管理状态了。


#### HTTP方法有哪些？
| 方法 | 说明 | 支持的HTTP协议版本 |
| :----- | :----- | :----- | 
|GET|获取资源|1.0/1.1|
|POST|传输实体主体|1.0/1.1|
|PUT|传输文件|1.0/1.1|
|HEAD|获得报文首部|1.0/1.1|
|DELETE|删除文件|1.0/1.1|
|OPTIONS|询问支持的方法|1.1|
|TRACE|追踪路径|1.1|
|CONNECT|要求用隧道协议连接代理|1.1|
|LINK|建立和资源之间的联系|1.1|
|UNLINK|断开连接关系|1.1|


#### GET和POST方法的区别？
1. GET请求只能进行url编码，而POST支持多种编码方式。
2. GET请求在URL中传送的参数是有长度限制的，而POST么有。但是GET这个长度的限制不是HTTP协议限制的，是各个浏览器限制的。
3. 因为GET方法是通过URL传递参数对，所以如果参数里面有一些特殊字符，比如+号，浏览器会对+号进行编码，可能会造成后台报错。而POST就不会有这种现象。如果使用GET时，参数中有特殊字符，可以先对参数编码，然后进行传输。
4. 同源时候，GET请求里面会带Origin字段，而POST不会。这个可能造成安全检查对错误，如果有nginx服务器，可再nginx服务器上设置。
5. GET方法一般设置Content-type为application/x-www-form-urlencoded，POST方法一般设置Content-type为text/plain;charset=utf-8，对于POST请求，服务器后台以流对方式接收。


#### 什么是TCP的持久化连接？
HTTP协议的初始版本中，每进行一次HTTP通信就要断开一次TCP连接，比如，使用浏览器一个包含多张图片的HTML页面时，在发送请求访问HTML页面资源的同时，也会请求该HTML页面里面包含的其他资源。因此，每次的请求都会造成无谓的TCP连接建立和断开，增加通信量的开销。为解决上述TCP连接的问题，HTTP/1.1和一部分的HTTP/1.0想出了持久连接(HTTP Persistent Connections，也称为HTTP keep-alive或HTTP connection reuse)的方法。


持久连接的特点是：只要任意一端没有明确的提出断开连接，则保持TCP连接状态。持久连接的好处在于减少了TCP连接的重复建立和断开所造成的额外开销，减轻了服务器端的负载。


#### HTTP是如何利用Cookie进行状态管理的？
Cookie技术通过在请求和响应报文中写入Cookie信息来控制客户端的状态。Cookie会根据从服务器端发送的响应报文内的一个叫做Set-Cookies的首部字段信息，通知客户端保存Cookie。当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入Cookie值后发送出去。服务器端发现客户端发送过来的Cookie后，会去检查究竟是从哪一个客户端发来的连接请求，然后对比服务器上的记录，最后得到之前的状态信息。即：


```
1. 由客户端发出一个HTTP请求，服务器端在响应中添加Cookie后返回；
2. 客户端下一次再向服务器端发送HTTP请求时，会在请求中添加Cookie后发送。服务器端会检测这个Cookie进行认证。
```


#### 在ajax请求中如何使用Cookie？
需要在ajax请求中设置withCredentials=true参数，即：


```javascript
let requestObj = new XMLHttpRequest();
requestObj.withCredentials = true;
```


#### 在ajax请求中使用Cookie时，如何解决跨越问题？
如果是在跨域问题是在服务器端设置


```javascript
response.setHeader("Access-Control-Allow-Origin", "*");
```


当设置withCredentials=true参数后，就不可以用*了。会报错，需要这样解决：


```javascript
response.setHeader("Access-Control-Allow-Origin", req.headers.origin);
```


#### HTTP报文有哪几种，HTTP报文包含哪写部分？
用于HTTP协议交互的信息被称为HTTP报文。请求端(客户端)的HTTP报文request叫做请求报文；响应端(服务器端)response的叫做响应报文。


HTTP报文大致可以分为报文首部和报文主体两块。两者由最初出现的空行(CR+LF)来划分。通常，并不一定要有报文主体。具体的请求信息，可以通过谷歌浏览器控制台里面的network进行分析。


#### 常见的状态码有哪些？
状态码表示了响应的一个状态，可以让我们清晰的了解到这一次请求是成功还是失败，如果失败的话，是什么原因导致的，当然状态码也是用于传达语义的。如果胡乱使用状态码，那么它存在的意义就没有了。


| 名称 | 解释 | 
| :----- | :----- | 
|<div style='width: 120px'>2XX成功</div>|<span class='forest-green'>200 OK</span>：表示从客户端发来的请求在服务器端被正确处理。<br><span class='forest-green'>204 No content</span>：表示请求成功，但响应报文不含实体的主体部分。<br><span class='forest-green'>205 Reset Content</span>：表示请求成功，但响应报文不含实体的主体部分，但是与204响应不同在于要求请求方重置内容。<br><span class='forest-green'>206 Partial Content</span>：进行范围请求。|
|<div style='width: 120px'>3XX重定向</div>|<span class='forest-green'>301 moved permanently</span>：永久性重定向，表示资源已被分配了新的URL。<br><span class='forest-green'>302 found</span>：临时性重定向，表示资源临时被分配了新的URL。<br><span class='forest-green'>303 see other</span>：表示资源存在着另一个URL，应使用GET方法获取资源。<br><span class='forest-green'>304 not modified</span>：表示服务器允许访问资源，但因发生请求未满足条件的情况。<br><span class='forest-green'>307 temporary redirect</span>：临时重定向，和302含义类似，但是期望客户端保持请求方法不变向新的地址发出请求。|
|<div style='width: 120px'>4XX客户端错误</div>|<span class='forest-green'>400 bad request</span>：请求报文存在语法错误。<br><span class='forest-green'>401 unauthorized</span>：表示发送的请求需要有通过HTTP认证的认证信息。<br><span class='forest-green'>403 forbidden</span>：表示对请求资源的访问被服务器拒绝。<br><span class='forest-green'>404 not found</span>：表示在服务器上没有找到请求的资源。|
|<div style='width: 120px'>5XX服务器错误</div>|<span class='forest-green'>500 internal sever error</span>：表示服务器端在执行请求时发生了错误。<br><span class='forest-green'>501 Not Implemented</span>：表示服务器不支持当前请求所需要的某个功能。<br><span class='forest-green'>503 service unavailable</span>：表明服务器暂时处于超负载或正在停机维护，无法处理请求。|