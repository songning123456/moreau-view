#### 什么是HTTP首部字段？
HTTP协议的请求和响应报文中必定包含HTTP首部。首部内容为客户端和服务器分别处理请求和响应提供所需要的信息。报文首部由几个字段构成。在报文众多的字段当中，HTTP首部字段包含的信息最为丰富。使用首部字段是为了给浏览器和服务器提供报文主体大小、所使用的语言、认证信息等内容。HTTP首部字段是由首部字段名称和字段值构成，中间用冒号":"分隔。


#### HTTP首部字段有哪几种类型？
HTTP首部字段根据实际用途被分为以下4种类型。


| 名称 | 解释 | 
| :----- | :----- | 
|<div style='width: 180px'>通用首部字段(General Header Fields)</div>|请求报文和响应报文两方都会使用的首部。|
|<div style='width: 180px'>请求首部字段(Request Header Fields)</div>|从客户端向服务器端发送请求报文时使用的首部。补充了请求的附加内容、客户端信息、响应内容相关优先级等信息。|
|<div style='width: 180px'>响应首部字段(Response Header Fields)</div>|从服务器端向客户端返回响应报文时使用的首部。补充了响应的附加内容，也会要求客户端附加额外的内容信息。|
|<div style='width: 180px'>实体首部字段(Entity Header Fields)</div>|针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等与实体有关的信息。|


#### 通用首部字段有哪些？
| 首部字段名 | 说明 | 
| :----- | :----- | 
|Cache-Control|控制缓存的行为|
|Connection|逐跳首部、连接的管理|
|Date|创建报文的日期时间|
|Pragma|报文指令|
|Trailer|报文末端的首部一览|
|Transfer-Encoding|指定报文主体的传输编码方式|
|Upgrade|升级为其他协议|
|Via|代理服务器的相关信息|
|Warning|错误通知|


#### 请求首部字段有哪些？
| 首部字段名 | 说明 | 
| :----- | :----- | 
|Accept|用户代理可处理的媒体类型|
|Accept-Charset|优先的字符集|
|Accept-Encoding|优先的内容编码|
|Accept-Language|优先的语言(自然语言)|
|Authorization|Web认证信息|
|Expect|期待服务器的特定行为|
|From|用户的电子邮箱地址|
|Host|请求资源所在服务器|
|If-Match|比较实体标记(ETag)|
|If-Modified-Since|比较资源的更新时间|
|If-None-Match|比较实体标记(与If-Match相反)|
|If-Range|资源未更新时发送实体Bye的范围请求|
|If-Unmodified-Since|比较资源的更新时间(与If-Modified-Since相反)|
|Max-Forwards|最大传输逐跳数|
|Proxy-Authorization|代理服务器要求客户端的认证信息|
|Range|实体的字节范围请求|
|Referer|对请求中URI的原始获取方|
|TE|传输编码的优先级|
|User-Agent|HTTP客户端程序的信息|


#### 响应首部字段有哪些？
|首部字段名|说明|
| :----- | :----- | 
|Accept-Ranges|是否接受字节范围请求|
|Age|推算资源创建经过时间|
|ETag|资源的匹配信息|
|Location|令客户端重定向至指定URI|
|Proxy-Authenticate|代理服务器对客户端对认证信息|
|Retry-After|对再次发起请求的时机要求|
|Server|HTTP服务器的安装信息|
|Vary|代理服务器缓存的管理信息|
|WWW-Authenticate|服务器对客户端对认证信息|


#### 实体首部字段有哪些？
|首部字段名|	说明|
| :----- | :----- | 
|Allow|资源可支持的HTTP方法|
|Content-Encoding|实体主体使用的编码方式|
|Content-Language|实体主体的自然语言|
|Content-Length|实体主体的大小(单位：字节)|
|Content-Location|替代对应资源的URI|
|Content-MD5|实体主体的报文摘要|
|Content-Range|实体主体的位置范围|
|Content-Type|实体主体的媒体类型|
|Expires|实体主体过期的日期时间|
|Last-Modified|资源的最后修改日期时间|


#### 非HTTP/1.1首部字段？
在HTTP协议通信交互中使用到的首部字段，不限于RFC2616中定义的47种首部字段。还有Cookie、Set-Cookie和Content-Disposition等在其他RFC中定义的首部字段，它们的使用频率也很高。这些非正式的首部字段统一归纳在RFC4229 HTTP Header Field Registrations中。


#### 为Cookie服务的首部字段有哪些？
|首部字段名|说明|首部类型|
| :----- | :----- | :----- | 
|Set-Cookie|开始状态管理所使用的Cookie信息|响应首部字段|
|Cookie|服务器接收到的Cookie信息|请求首部字段|


**Set-Cookie字段的属性**


|属性|说明|
| :----- | :----- |
|NAME=VALUE	赋予Cookie的名称和其值(必需项)|
|expires=DATE|Cookie的有效期|
|path=PATH|将服务器上的文件目录作为Cookie的适用对象(若不能指定则默认为文档所在的文件目录)|
|domain=域名|作为Cookie适用对象的域名(若不指定则默认为创建Cookie的服务器的域名)|
|Secure|仅在HTTPS安全通信时才会发送Cookie|
|HttpOnly|加以限制，使Cookie不能被JavaScript脚本访问|


#### 其他首部字段？
HTTP首部字段是可以自行扩展的。所以在Web服务器和浏览器的应用上，会出现各种非标准的首部字段。


|属性|说明|
| :----- | :----- |
|<div style='width: 130px'>X-Frame-Options</div>|用于控制网站内容在其他Web网站的Frame标签内的显示问题。其主要目标是为了防止点击劫持攻击。|
|<div style='width: 130px'>X-XSS-Protection</div>|用于控制浏览器XSS防护机制的开关。(0：将XSS过滤设置成无效状态。1：将XSS过滤设置成有效状态。)|
|<div style='width: 130px'>DNT</div>|为拒绝个人信息被采集，是表示拒绝被精准广告追踪的一种方法。(0：同意被追踪。1：拒绝被追踪。)|
|<div style='width: 130px'>P3P</div>|属于HTTP响应首部字段，通过利用P3P技术，可以让Web网站上的个人隐私变成一种仅供程序可理解的技术，以达到保护用户隐私的目的。|


要进行P3P的设定，需按以下操作步骤进行：


1. 创建P3P隐私；
2. 创建P3P隐私对照文件后，保持命名在/w3c/p3p.xml；
3. 从P3P隐私中新建Compact policies后，输出到HTTP响应中。

