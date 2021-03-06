#### HTTP的缺点有哪些？
```
1. 通信使用明文(不加密)，内容可能会被窃听；
2. 不验证通信方的身份，因此可能遭遇伪装；
3. 无法证明报文的完整性，所以有可能已遭篡改。
```


**通信使用明文(不加密)，内容可能会被窃听**


由于HTTP本身不具备加密的功能，所以也无法做到对通信(使用HTTP协议通信的请求和响应内容)进行加密。即HTTP报文使用明文(未经过加密的报文)方式发送。如果要问为什么通信时不加密是一个缺点，这是因为按TCP/IP协议族的工作机制，通信内容在所有的通信线路上都有可能遭到窥视。


在目前大家正在研究的如何防止窃听保护信息的几种对称中，最为普及的就是加密技术。加密的对象可以有这么几个。


```
通信的加密：
HTTP可以通过和SSL(Secure Socket Layer，安全套接层)或TLS(Transport Layer Security，安全传输层协议)的
组合使用，加密HTTP的通信内容。用SSL建立安全通信线路之后，就可以在这条线路傻姑娘进行HTTP通信了。与SSL组合
使用的HTTP被称为HTTPS(HTTP Secure，超文本传输安全协议)或HTTP over SSL。
```


```
内容的加密：
还有一种将参与通信的内容本身进行加密的方法。由于HTTP协议中段还有加密机制，那么就对HTTP协议传输的内容本身
加密。即把HTTP报文里所含的内容进行加密处理。
```


**任何人都可以发起请求**


HTTP协议的实现本身非常简单，不论是谁发送过来的请求都会返回响应，因此不确认通信方，会存在以下各种隐患：


1. 无法确定请求发送至目标的Web服务器是否是按真实意图返回响应的那台服务器。有可能是已伪装的web服务器。
2. 无法确定响应返回到的客户端是否是按真实意图接收响应的那个客户端。有可能是已伪装的客户端。
3. 无法确定正在通信的对方是否具备访问权限。因为某些Web服务器上保存着重要的信息，只想发给特定用户通信的权限。
4. 无法判断请求是来自何方、出自谁手。
5. 即使是无意义的请求也会照单全收。无法阻止海量请求下的Dos攻击(Denial of Service，拒绝服务攻击)。


虽然使用HTTP协议无法确定通信方，但如果使用SSL则可以。SSL不仅提供加密处理，而且还使用了一种被称为证书的手段，可用于确定方。证书是由值得信任的第三方机构颁发，用以证明服务器和客户端是实际存在的。另外，伪造证书从技术角度来说是异常困难的一件事情。所以只要能够确认通信方持有的证书，即可判断通信方的真实意图。


**无法证明报文完整性，可能已遭到篡改**


由于HTTP协议无法证明通信的报文完整性，因此，在请求送出之后直到对方接收之前的这段时间内，即使请求或响应的内容遭到篡改，也没有办法获悉。像这样，请求和响应在传输途中，遭攻击者拦截并篡改内容的攻击称为中间人攻击。


#### 什么是HTTPS？
HTTPS并非是应用层的一种新协议，只是HTTP通信接口部分用SSL(Secure Socket Layer)和TLS(Transport Layer Security)协议代替而已。通常，HTTP直接和TCP通信。当使用SSL时，则演变成先和SSL通信，再有SSL和TCP通信了。简言之，所谓的HTTPS，其实就是身披SSL协议这层外壳的HTTP。在采用SSL后，HTTP就拥有了HTTPS的加密、证书和完整性保护这些功能。SSL是独立于HTTP的协议，所以不光是HTTP协议，其他运行在应用层的SMTP和Telnet等协议均可配合SSL协议使用。可以说SSL是当今世界应用最为广泛的网络安全技术。


#### HTTPS是如何进行加密的？
HTTPS采用共享密钥加密和公开密钥加密的混合加密机制。


**共享密钥加密**


加密和解密同用一个密钥的方式称为共享密钥加密，也被称为对称密钥加密。以共享密钥方式加密时必须将密钥也发给对方。可究竟怎样才能安全地转交？在互联网上转发密钥时，如果通信被监听那么密钥就可会落入攻击者之后，同时也就失去了加密的意义，另外还得设法安全地保管接收到的密钥。


**使用两把密钥的公开密钥加密**


公开密钥加密的方式很好的解决了共享密钥加密的问题。公开密钥加密使用一对非对称的密钥。一把为私钥(私有密钥)，一把为公钥(公开密钥)，其中私有密钥不能让任何人得知，而公开密钥则可以随意公布。发送密文的那一端，使用对方的公开密钥进行加密处理，对方接收到被加密的信息后，使用私钥对此密文进行解密。利用这种方式，不需要发送用来解密的私钥。从而解决了共享密钥加密存在的问题。


**使用混合加密机制的HTTPS**


HTTPS采用共享密钥加密和公开密钥加密的混合加密机制。如果为了实现密钥的安全，那么可以考虑仅使用公开密钥加密技术(公开密钥加密比共享密钥加密处理速度要慢)。所以，应当充分利用两者的优势，将多种方法组合起来进行通信：


在交换密钥环节，使用公开密钥加密技术(比如说，从客户端到服务器，那么在这样一个安全的通信中，客户端可以将共享密钥加密中的密钥用公开密钥加密中的公钥进行加密，发送给服务器，然后服务器使用公开密钥加密技术的私钥进行解密，就拿到了共享密钥加密技术中的私钥)，之后建立通信报文交换的阶段则可使用共享密钥加密技术。


![https](/images/Web/https.png)


#### HTTPS的通信步骤是什么？
1.客户端发送报文进行SSL通信。报文中包含客户端支持的SSL的指定版本、加密组件列表(加密算法及密钥长度等)。<br>
2.服务器应答，并在应答报文中包含SSL版本以及加密组件。服务器的加密组件内容是从接受到的客户端加密组件内筛选出来的。<br>
3.服务器发送报文，报文中包含公开密钥证书。<br>
4.服务器发送报文通知客户端，最初阶段SSL握手协商部分结束。<br>
5.SSL第一次握手结束之后，客户端发送一个报文作为回应。报文中包含通信加密中使用的一种被称Pre-master secret的随机密码串。该密码串已经使用服务器的公钥加密。<br>
6.客户端发送报文，并提示服务器，此后的报文通信会采用Pre-master secret密钥加密。<br>
7.客户端发送Finished报文。该报文包含连接至今全部报文的整体校验值。这次握手协商是否能够完成成功，要以服务器是否能够正确解密该报文作为判定标准。<br>
8.服务器同样发送Change Cipher Spec报文。<br>
9.服务器同样发送Finished报文。<br>
10.服务器和客户端的Finished报文交换完毕之后，SSL连接就算建立完成。<br>
11.应用层协议通信，即发送HTTP响应。<br>
12.最后由客户端断开链接。断开链接时，发送close_notify报文。


#### 相比HTTP，HTTPS有哪些缺点？
与纯文本通信相比，加密通信会消耗更多的CPU和内存资源。如果每次通信都加密，会消耗相当多的资源，平摊到一台计算机上时，能够处理的请求数量必定也会随之减少。因此，如果是非敏感信息则使用HTTP通信，只有在包含个人信心等敏感数据时，才利用HTTPS加密通信。特别是每当那些访问量比较多的Web网站进行加密处理时，它们所承担着的负载不容小觑。在进行加密处理时，并非对所有内容都进行加密处理，而是仅在那些需要信息隐藏时才会加密，以节约资源。