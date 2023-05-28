
# 网络
## 键入网址到网页显示，期间发生了什么
- 1.输入url
	用户输入url，处理输入信息：
	如果为非url结构的字符串，交给浏览器**默认引擎**去搜索改字符串；
	若为url结构的字符串，浏览器主进程会交给 **网络进程** ,开始干活。
- 
- 2.1查找浏览器缓存（强缓存）
- 2.2DNS解析
网络进程拿到url后，先会进行DNS域名解析得到**IP地址**。如果请求协议是HTTPS，那么还需要建立TLS连接。

- 2.3建立TCP连接，三次握手
接下来就是利用IP地址和服务器建立TCP连接。连接建立之后，向服务器发送请求。

- 3服务器响应
服务器收到请求信息后，会根据请求信息生成响应行、响应头、响应体，并发给网络进程。网络进程接受了响应信息之后，就开始解析响应头的内容。
**网络进程解析响应行和响应头信息的过程：**

- 3.1重定向
如果响应行状态码为301（永久重定向）和302（临时），那么说明需要重定向到其他url。这时候网络进程会从响应头中的Location字段里读取重定向的地址，并重新发起网络请求。

- 3.2 响应数据处理
导航会通过请求头的**Content-type**字段判断响应体数据的类型。

- 4 准备渲染进程


## OSI七层模型
OSI (Open Systems Interconnection) 模型是一种网络通信的参考模型，定义了计算机网络中不同层次之间的协议和交互关系。OSI 模型分为七层，每层都有不同的功能，包括：

1.  物理层（Physical Layer）：负责传输比特流，将数据从一个节点传输到另一个节点，物理层规定了电器、光学和机械接口的特性。
2.  数据链路层（Data Link Layer）：负责在物理介质上建立逻辑连接，提供有可靠性的数据传输，数据链路层还可以检测和纠正数据传输中的错误。
3.  网络层（Network Layer）：负责在网络中传输数据包，提供路由和寻址功能，网络层还可以通过控制拥塞和流量来控制网络流量。
4.  传输层（Transport Layer）：负责对端到端的数据传输进行控制，传输层提供可靠或不可靠的数据传输，还可以提供流量控制和拥塞控制等功能。
5.  会话层（Session Layer）：负责建立、管理和终止会话，会话层还可以处理同步和检查点等问题。
6.  表示层（Presentation Layer）：负责数据格式的转换和编码，以便让应用程序可以识别和解析数据。
7.  应用层（Application Layer）：为应用程序提供服务，应用层定义了用户和应用程序之间的接口。
## TCP/IP模型

-   应用层
-   传输层
-   网络层
-   网络接口层

## 五层模型

用于教学

-   应用层
-   传输层
-   网络层
-   数据链路层
-   物理层


## TCP

### 重要字段

- 序号(sequence number)：seq序号，占32位，用来标识从TCP源端向目的端发送的字节流，发起方发送数据时对此进行标记。
- 确认号（acknowledgement number）：ack序号，占32位，只有ACK标志位为1时，确认序号字段才有效，ack=seq+1。
- 标志位（Flags）：共6个，即URG、ACK、PSH、RST、SYN、FIN等。具体含义如下：
 - URG：紧急指针（urgent pointer）有效。
 - ACK：确认序号有效。（为了与确认号ack区分开，我们用大写表示）
 - PSH：接收方应该尽快将这个报文交给应用层。
 - RST：重置连接。
 - SYN：发起一个新连接。
 - FIN：释放一个连接。
### 三次握手
[三次握手](https://xiaolincoding.com/network/3_tcp/tcp_interview.html#tcp-%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E8%BF%87%E7%A8%8B%E6%98%AF%E6%80%8E%E6%A0%B7%E7%9A%84)
![[Pasted image 20230413125506.png]]
#### 为什么是三次握手？不是两次、四次？
_原因一：避免历史连接_
_原因二：同步双方初始序列号_
_原因三：避免资源浪费
_
TCP 建立连接时，通过三次握手**能防止历史连接的建立，能减少双方不必要的资源开销，能帮助双方同步初始化序列号**。序列号能够保证数据包不重复、不丢弃和按序传输。
不使用「两次握手」和「四次握手」的原因：
-   「两次握手」：无法防止历史连接的建立，会造成双方资源的浪费，也无法可靠的同步双方序列号；
-   「四次握手」：三次握手就已经理论上最少可靠连接建立，所以不需要使用更多的通信次数。
[why](https://xiaolincoding.com/network/3_tcp/tcp_interview.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E6%98%AF%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B-%E4%B8%8D%E6%98%AF%E4%B8%A4%E6%AC%A1%E3%80%81%E5%9B%9B%E6%AC%A1)
### 四次挥手
![[Pasted image 20230413125601.png]]
![[Pasted image 20230525220705.png]]
[四次挥手过程](https://xiaolincoding.com/network/3_tcp/tcp_interview.html#tcp-%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B%E8%BF%87%E7%A8%8B%E6%98%AF%E6%80%8E%E6%A0%B7%E7%9A%84)
####  为什么挥手需要四次？

再来回顾下四次挥手双方发 `FIN` 包的过程，就能理解为什么需要四次了。
-   关闭连接时，客户端向服务端发送 `FIN` 时，仅仅表示客户端不再发送数据了但是还能接收数据。
-   服务端收到客户端的 `FIN` 报文时，先回一个 `ACK` 应答报文，而服务端可能还有数据需要处理和发送，等服务端不再发送数据时，才发送 `FIN` 报文给客户端来表示同意现在关闭连接。

从上面过程可知，服务端通常需要等待完成数据的发送和处理，所以服务端的 `ACK` 和 `FIN` 一般都会分开发送，因此是需要四次挥手。

但是**在特定情况下，四次挥手是可以变成三次挥手的**

#### 什么情况会出现三次挥手？

当被动关闭方（上图的服务端）在 TCP 挥手过程中，「**没有数据要发送」并且「开启了 TCP 延迟确认机制」，那么第二和第三次挥手就会合并传输，这样就出现了三次挥手。**

> 什么是 TCP 延迟确认机制？

当发送没有携带数据的 ACK，它的网络效率也是很低的，因为它也有 40 个字节的 IP 头 和 TCP 头，但却没有携带数据报文。 为了解决 ACK 传输效率低问题，所以就衍生出了 **TCP 延迟确认**。 TCP 延迟确认的策略：

-   当有响应数据要发送时，ACK 会随着响应数据一起立刻发送给对方
-   当没有响应数据要发送时，ACK 将会延迟一段时间，以等待是否有响应数据可以一起发送
-   如果在延迟等待发送 ACK 期间，对方的第二个数据报文又到达了，这时就会立刻发送 ACK

### 重传机制
https://xiaolincoding.com/network/3_tcp/tcp_feature.html#%E9%87%8D%E4%BC%A0%E6%9C%BA%E5%88%B6
### 滑动窗口
### 流量控制
### 拥塞控制 


## HTTP


### HTTP 状态码？

![五大类 HTTP 状态码](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/6-%E4%BA%94%E5%A4%A7%E7%B1%BBHTTP%E7%8A%B6%E6%80%81%E7%A0%81.png)

`1xx` 类状态码属于**提示信息**，是协议处理中的一种中间状态，实际用到的比较少。

`2xx` 类状态码表示服务器**成功**处理了客户端的请求，也是我们最愿意看到的状态。

-   「**200 OK**」是最常见的成功状态码，表示一切正常。如果是非 `HEAD` 请求，服务器返回的响应头都会有 body 数据。
-   「**204 No Content**」也是常见的成功状态码，与 200 OK 基本相同，但响应头没有 body 数据。
-   「**206 Partial Content**」是应用于 HTTP 分块下载或断点续传，表示响应返回的 body 数据并不是资源的全部，而是其中的一部分，也是服务器处理成功的状态。

`3xx` 类状态码表示客户端请求的资源发生了变动，需要客户端用新的 URL 重新发送请求获取资源，也就是**重定向**。
-   「**301 Moved Permanently**」表示永久重定向，说明请求的资源已经不存在了，需改用新的 URL 再次访问。
-   「**302 Found**」表示临时重定向，说明请求的资源还在，但暂时需要用另一个 URL 来访问。
301 和 302 都会在响应头里使用字段 `Location`，指明后续要跳转的 URL，浏览器会自动重定向新的 URL。
-   「**304 Not Modified**」不具有跳转的含义，表示资源未修改，重定向已存在的缓冲文件，也称缓存重定向，也就是告诉客户端可以继续使用缓存资源，用于缓存控制。

`4xx` 类状态码表示客户端发送的**报文有误**，服务器无法处理，也就是错误码的含义。
-   「**400 Bad Request**」客户端请求的报文有错误，
-    **401：Unauthorized** 身份未验证
-   「**403 Forbidden**」禁止访问资源，
-   「**404 Not Found**」资源不存在

`5xx` 类状态码表示客户端请求报文正确，但是**服务器处理时内部发生了错误**，属于服务器端的错误码。

-   「**500 Internal Server Error**」与 400 类型，是个笼统通用的错误码，服务器发生了什么错误，我们并不知道。
-   「**501 Not Implemented**」表示客户端请求的功能还不支持，类似“即将开业，敬请期待”的意思。
-   「**502 Bad Gateway**」通常是服务器作为网关或代理时返回的错误码，表示服务器自身工作正常，访问后端服务器发生了错误。
-   「**503 Service Unavailable**」表示服务器当前很忙，暂时无法响应客户端，类似“网络服务正忙，请稍后重试”的意思。
### http常见头部
User-Agent：发送请求的用户代理应用程序的名称和版本号
Host：被请求资源的主机名和端口号
Accept：可以接受的MIME类型列表
Accept-Language：可以接受的自然语言列表
Accept-Encoding：可以接受的内容编码列表
Connection：请求完成后是否关闭连接
Content-Type：请求或响应的实体的媒体类型
Content-Length：请求或响应的实体的长度（以字节为单位）
Cache-Control：缓存指令，指示请求/响应是否可以缓存
Cookie：包含与HTTP请求关联的cookie数据

### 跨域解决方法

-   题目：[什么是跨域，如何解决](https://q.shanyue.tech/fe/dom/216.html)

**协议**，**域名**，**端口**，三者有一不一样，就是跨域

案例一：`www.baidu.com` 与 `zhidao.baidu.com` 是跨域
二： localhost 端口不同也算
目前有两种最常见的解决方案：

1.  CORS，在服务器端设置几个响应头，如 `Access-Control-Allow-Origin: *`
2.  Reverse Proxy，在 nginx/traefik/haproxy 等反向代理服务器中设置为同一域名
3.  JSONP，详解见 [JSONP 的原理是什么，如何实现](https://github.com/shfshanyue/Daily-Question/issues/447)
#### JSONP
https://q.shanyue.tech/fe/dom/447.html#%E4%B8%80%E4%B8%AA-jsonp-%E8%AF%B7%E6%B1%82

### get post

get参数长度限制不是因为http协议的限制，不同的浏览器有不同的限制
### options请求

1.  检测服务器支持的请求方法
2.  CORS 中的预检请求

可以设置`Access-Control-Max-Age`这个属性。让浏览器缓存，在缓存的有效期内，所有options请求都不会发送。优化性能。

### 强制缓存
[强制缓存](https://xiaolincoding.com/network/2_http/http_interview.html#%E4%BB%80%E4%B9%88%E6%98%AF%E5%BC%BA%E5%88%B6%E7%BC%93%E5%AD%98)
强缓存指的是只要浏览器判断缓存没有过期，则直接使用浏览器的本地缓存，决定是否使用缓存的主动性在于浏览器这边。

如下图中，返回的是 200 状态码，但在 size 项中标识的是 from disk cache，就是使用了强制缓存。

![](https://cdn.xiaolincoding.com//mysql/other/1cb6bc37597e4af8adfef412bfc57a42.png)
#### stale while revalidate
（SWR）是一种用于Web缓存的机制，通常用于优化网络性能和用户体验。
SWR机制的核心思想是允许从缓存中返回“陈旧”（即过期）的数据，同时在后台更新最新数据，以便在下一次请求时返回新数据。这可以提高应用程序的响应速度和性能，并减少用户等待时间。

time < max-age    直接用缓存
max-age< time < max-age + stale-while-revalidate  在后台请求更新缓存
time > max-age + stale-while-revalidate 会立即发出一个请求，并在数据返回后将其存储在缓存中，以备下一次使用。

##### 优点
它可以有效地降低网络请求的频率，提高应用程序的性能和响应速度，并减少数据传输的成本。它也可以帮助开发人员更好地管理应用程序中的数据，从而使代码更加清晰简洁。
### 协商缓存
#### etag

当使用 ETag 字段实现的协商缓存的过程：

-   当浏览器第一次请求访问服务器资源时，服务器会在返回这个资源的同时，在 Response 头部加上 ETag 唯一标识，这个唯一标识的值是根据当前请求的资源生成的；
-   当浏览器再次请求访问服务器中的该资源时，首先会先检查强制缓存是否过期：
    -   如果没有过期，则直接使用本地缓存；
    -   如果缓存过期了，会在 Request 头部加上 If-None-Match 字段，该字段的值就是 ETag 唯一标识；
-   服务器再次收到请求后，**会根据请求中的 If-None-Match 值与当前请求的资源生成的唯一标识进行比较**：
    -   **如果值相等，则返回 304 Not Modified，不会返回资源**；
    -   如果不相等，则返回 200 状态码和返回资源，并在 Response 头部加上新的 ETag 唯一标识；
-   如果浏览器收到 304 的请求响应状态码，则会从本地缓存中加载资源，否则更新资源。

####  etag生成原理 
一般是：
对于静态文件（如css、js、图片等），ETag的生成策略是：content-length的16进制 + last-modified

对于字符串或Buffer，ETag的生成策略是：字符串/Buffer长度的16进制+对应的hash值
hash比较消耗性能

#### etag精度比 Last-Modified 高的原因
Last-Modified 精度只到秒

### HTTP1.1=>HTTP2 =>HTTP3 

#### HTTP1.1 

HTTP/1.1 相比 HTTP/1.0 性能上的改进：

-   使用长连接的方式改善了 HTTP/1.0 短连接造成的性能开销。
-   支持管道（pipeline）网络传输，只要第一个请求发出去了，不必等其回来，就可以发第二个请求出去，可以减少整体的响应时间。
- 缓存控制

但 HTTP/1.1 还是有性能瓶颈：

-   请求 / 响应头部（Header）未经压缩就发送，首部信息越多延迟越大。只能压缩 `Body` 的部分；         
-   发送冗长的首部。每次互相发送相同的首部造成的浪费较多；
-   服务器是按请求的顺序响应的，如果服务器响应慢，会招致客户端一直请求不到数据，也就是队头阻塞；
-   没有请求优先级控制；
-   请求只能从客户端开始，服务器只能被动响应。

#### HTTP2 

![HTT/1 ~ HTTP/2](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/25-HTTP2.png)

那 HTTP/2 相比 HTTP/1.1 性能上的改进：
-   报文头部压缩
	-  动态表编码
	- 静态表编码
-   二进制格式
-   并发传输
	- **多个 Stream 复用一条 TCP 连接，达到并发的效果**
	- 不同帧之间可以以乱序发送，因为根据**帧首部的流ID**可以重新进行组装，从而实现单连接的请求并发
-   服务器主动推送资源
-    可设置请求优先级
![[Pasted image 20230422174304.png]]
-   二进制分帧传输：HTTP1中的报文是文本格式；HTTP2把报文从小到大划分成**帧**，**消息**和**数据流**。其中帧是通信的基本单位，消息是一个完整的http请求或者响应，由帧组成，流是TCP连接中的一个双向虚拟通道，可以承载多个消息
-   多路复用：HTTP1如果想实现并发多个请求，必须建立多个TCP连接，而且存在**慢启动**，**队头阻塞**，**带宽竞争**的问题；HTTP2基于二进制分帧，同域名下所有通信都在单个TCP连接上完成，一个连接可以承载多个双向数据流。不同帧之间可以以乱序发送，因为根据**帧首部的流ID**可以重新进行组装，从而实现单连接的请求并发
-   报文头部压缩：请求可能会发送很多相同的请求头如Cookie，User-Agent，HTTP2.0会存在客户端和服务端，各维护一个表，相同的数据不做重复发送
-   服务端推送：在客户端请求之前主动发送数据，加快页面响应速度
-   可设置请求优先级

#### http3
QUIC 有以下 3 个特点。
-   无队头阻塞
-   更快的连接建立
	-   集成了TLS加密功能，减少了握手花费的RTT个数
-   连接迁移
	- 只需要记住连接 ID、TLS 密钥等

-   HTTP/1.1 中的管道（ pipeline）虽然解决了请求的队头阻塞，但是**没有解决响应的队头阻塞**，因为服务端需要按顺序响应收到的请求，如果服务端处理某个请求消耗的时间比较长，那么只能等响应完这个请求后， 才能处理下一个请求，这属于 HTTP 层队头阻塞。
-   HTTP/2 虽然通过多个请求复用一个 TCP 连接解决了 HTTP 的队头阻塞 ，但是**一旦发生丢包，就会阻塞住所有的 HTTP 请求**，这属于 TCP 层队头阻塞。

HTTP/2 队头阻塞的问题是因为 TCP，所以 **HTTP/3 把 HTTP 下层的 TCP 协议改成了 UDP！**

![HTTP/1 ~ HTTP/3](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/27-HTTP3.png)

UDP 发送是不管顺序，也不管丢包的，所以不会出现像 HTTP/2 队头阻塞的问题。大家都知道 UDP 是不可靠传输的，但基于 UDP 的 **QUIC 协议** 可以实现类似 TCP 的可靠性传输。


### Https
4次握手
1. `client hello`:客户端向服务器发送一个 HTTPS 请求。TLS 版本号、支持的密码套件列表(`RAS ECDHE`)，以及生成的**随机数（_Client Random_）**，这个随机数会被服务端保留，它是生成对称加密密钥的材料之一。
2. `server hello`:服务器向客户端发送 SSL/TLS 证书。证书包含服务器的公钥以及其它信息，如证书颁发机构、有效期等。
3. 客户端验证证书的有效性，如果证书有效，则生成一个新的随机数作为**对称密钥**，使用服务器的**公钥**加密该**对称密钥**，并将加密后的密钥发送给服务器。
4. 服务器使用自己的私钥解密客户端发送的对称密钥，并使用该密钥加密服务器响应的数据，然后发送加密后的数据给客户端

之后客户端使用对称密钥解密服务器响应的数据，完成 HTTPS 连接。

在 HTTPS 连接中，SSL/TLS 协议扮演着重要的角色，它使用**非对称**加密和**对称**加密结合的方式，保证了数据传输的安全性和完整性。此外，证书机构的身份验证也是保障 HTTPS 连接的安全性的一个重要环节。

#### 证书验证
![[Pasted image 20230426192046.png]]
CA 签发证书的过程，如上图左边部分：

-   首先 CA 会把持有者的公钥、用途、颁发者、有效时间等信息打成一个包，然后对这些信息进行 Hash 计算，得到一个 Hash 值；
-   然后 CA 会使用自己的私钥将该 Hash 值加密，生成 Certificate Signature(签名)
-   最后将 Certificate Signature 添加在文件证书上，形成数字证书；

客户端校验服务端的数字证书的过程，如上图右边部分：

-   首先客户端会使用同样的 Hash 算法获取该证书的 Hash 值 H1；
-   通常浏览器和操作系统中集成了 CA 的[公钥信息](https://www.zhihu.com/search?q=%E5%85%AC%E9%92%A5%E4%BF%A1%E6%81%AF&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1914075935%7D)，浏览器收到证书后可以使用 CA 的[公钥解密](https://www.zhihu.com/search?q=%E5%85%AC%E9%92%A5%E8%A7%A3%E5%AF%86&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1914075935%7D) Certificate Signature 内容，得到一个 Hash 值 H2 ；
-   最后比较 H1 和 H2，如果值相同，则为可信赖的证书，否则则认为证书不可信。
#### http和https的区别

**一、 HTTP与HTTPS的区别**

**1、端口**：http端口号是 80；https端口号是 443

**2、传输协议**：https在http和tcp之间加入了TLS/SSL层

**3、安全性**：https使用了TLS/SSL加密，比http更加的安全， http明文传输

**4** https需要申请ca证书，可以识别服务器身份

#### 中间人攻击
HTTPS 在用户主动信任了伪造证书的时候也会发生中间人攻击

## DNS解析过程
所以域名的层级关系类似一个树状结构：

- 根 DNS 服务器（.）
- 顶级域 DNS 服务器（.com）
- 权威 DNS 服务器（server.com）
1. 解析 URL：首先，解析程序会从用户提供的 URL 中提取出主机名（域名部分），例如 `www.example.com`。
2. 查询本地缓存：解析程序会检查本地的 DNS 缓存，看是否已经解析过该域名。如果缓存中存在该域名的解析结果并且没有过期，解析程序将直接使用缓存中的 IP 地址，跳过后续步骤。
3. 查询根域名服务器：如果本地缓存中不存在域名解析结果，解析程序将向根域名服务器发送查询请求。根域名服务器是 DNS 系统的起点，它保存了顶级域名服务器的信息。
4. 查询顶级域名服务器：根域名服务器返回顶级域名服务器的 IP 地址给解析程序。然后，解析程序向顶级域名服务器发送查询请求，请求解析该域名的权威域名服务器。
5. 查询权威域名服务器：顶级域名服务器将权威域名服务器的 IP 地址返回给解析程序。解析程序继续向权威域名服务器发送查询请求，请求解析该域名的主机记录。
6. 解析主机记录：权威域名服务器返回域名对应的 IP 地址给解析程序。解析程序将 IP 地址存储在本地缓存中，并将结果返回给应用程序或浏览器。
7. 解析完成：解析程序将 IP 地址返回给应用程序或浏览器，应用程序可以使用 IP 地址与目标服务器建立连接，并发送请求。
8. 
## WebSocket
WebSocket 是一种在 Web 应用程序中实现实时、双向通信的协议。它提供了一种在客户端和服务器之间建立持久性连接的方式，以便它们可以通过这个连接进行实时数据传输。

以下是一些关于 WebSocket 的重要特点和使用方式：

1. 建立连接：WebSocket 连接的建立是通过在客户端和服务器之间进行一次特殊的握手过程来完成的。这个握手过程类似于标准的 HTTP 请求和响应，但包含了一些特定的请求头和响应头字段。一旦握手成功，WebSocket 连接就被建立并保持打开状态，允许双向通信。
    
2. 实时双向通信：WebSocket 提供了实时、双向的通信机制，使得服务器可以主动向客户端推送数据，而不需要客户端发起请求。这种实时性非常适合用于聊天应用、实时数据更新等场景。
    
3. 较低的延迟和开销：相对于传统的轮询或长轮询方式，WebSocket 具有较低的延迟和网络开销。由于连接是持久性的，不需要在每次通信时重新建立连接，从而减少了额外的网络开销和延迟。
    
4. 跨域支持：与传统的 AJAX 请求不同，WebSocket 可以轻松跨越不同域名（跨域）进行通信，而不受同源策略的限制。这使得 WebSocket 更加灵活，并允许在不同域之间实现实时通信。
    
5. 客户端和服务器端支持：现代的 Web 浏览器都支持原生的 WebSocket API，使得在客户端实现 WebSocket 连接变得简单。同时，许多服务器端编程语言和框架也提供了对 WebSocket 的支持，例如 Node.js 的 WebSocket 模块、Java 的 Tyrus、Python 的 WebSocket 模块等。
    

在使用 WebSocket 时，通常需要编写客户端和服务器端的代码来处理连接的建立、消息的传输和处理等操作。客户端和服务器端可以使用相应的 WebSocket API 来管理连接，并通过发送和接收消息来实现实时通信。

总结来说，WebSocket 提供了一种在 Web 应用程序中实现实时、双向通信的标准化方式，具有较低的延迟和网络开销，并且能够跨域进行通信。这使得它成为构建实时应用、多人游戏、即时聊天和实时数据更新等场景的理想选择。

## CDN
CDN（Content Delivery Network，内容分发网络）是指一种分布式的网络架构，通过在网络中部署大量的节点服务器，将用户所请求的内容分发到最接近用户的节点服务器上进行缓存，以提高用户访问这些内容的速度和质量。

CDN的主要功能是加速互联网内容的传输，减少网络拥堵，提高用户的访问速度和体验。具体来说，CDN可以将网站的静态资源（如图片、视频、JS、CSS等）缓存到各个节点服务器上，并通过负载均衡技术将用户请求分发到最优的节点服务器上进行响应，从而提高了用户的访问速度。

此外，CDN还可以提供安全加固、防止DDoS攻击等功能，保障网站的稳定和安全性。

## 网络安全
### xss
XSS（Cross-Site Scripting）跨站脚本攻击是一种常见的Web安全漏洞，攻击者通过在Web应用程序中注入恶意的客户端脚本，使得攻击者能够在受害者的浏览器中执行恶意脚本，从而窃取用户的敏感信息，如cookies、会话令牌、密码等，甚至可以让攻击者获得对受害者的控制权。攻击者可以通过各种手段，如在表单输入框中输入恶意代码、在URL参数中注入恶意代码、在评论、留言板等
地方插入恶意脚本等方式进行攻击。
分类
- 反射型：将恶意代码注入URL参数中，请求网页时浏览器请求数据并执行恶意脚本
- DOM型：将恶意脚本注入到网站的DOM元素中，当用户浏览包含恶意脚本的页面时，恶意脚本会被执行。
- 持久型： 恶意代码存储在服务器的数据库中，请求网页时浏览器请求数据并执行恶意脚本
因此，开发者需要注意输入验证、输出转义等安全措施来防止XSS攻击。

### csrf
CSRF（Cross-Site Request Forgery）攻击是一种利用受害者在已认证的情况下意外执行了某些操作的攻击方式。攻击者可以利用受害者在未登出或超时的情况下绕过身份验证来发送一个或多个恶意请求，使得受害者执行不希望执行的操作。以下是CSRF攻击的一般流程：

1.  受害者登录受信任网站，并保持会话处于活动状态。
2.  受害者在未登出或超时的情况下访问了一个恶意网站。
3.  恶意网站向受害者的浏览器发送一个带有恶意请求的HTML页面或邮件，例如，一张图片或链接。
4.  受害者的浏览器收到恶意请求后，会自动将会话cookie附加到该请求中，并向受信任网站发送该请求。
5.  受信任网站接收到恶意请求后，会将该请求视为来自受害者，并执行恶意请求中指定的操作。
    
攻击者可以利用CSRF攻击来执行各种恶意操作，例如：

1.  更改受害者的密码、电子邮件地址或其他帐户信息。
2.  将受害者添加到恶意网络。
3.  利用受害者的帐户进行未经授权的购买、发布评论或发送垃圾邮件等。
#### 解决方式
1.  验证HTTP Referer：在服务器端验证请求来源是否来自受信任的站点。但是，这种方法可能无法防止伪造的HTTP Referer。
    
2.  在请求中添加CSRF Token：在表单中添加随机生成的CSRF Token，然后在服务器端验证该Token是否与会话中的Token匹配。
    
3.  实现SameSite Cookie：在cookie中设置SameSite属性，以便在跨站点请求时不发送cookie。
    
4.  在敏感操作前要求用户进行身份验证：在进行敏感操作（例如更改密码或删除帐户）之前，要求用户重新进行身份验证，例如输入密码或其他证明身份的信息。

# HTML&浏览器
## Cookie

存储大小4kb

### Cookie 属性

-   题目：[浏览器中 cookie 有哪些字段](https://q.shanyue.tech/fe/dom/560.html)

Cookie 有以下属性

-   Domain
-   Path
-   Expire/MaxAge
-   HttpOnly: 是否允许被 JavaScript 操作
-   Secure: 只能在 HTTPS 连接中配置
-   SameSite

#### Cookie maxAge

如果没有 maxAge，则 cookie 的有效时间为会话时间。

#### Cookie SameSite

-   题目：[SameSite Cookie 有哪些值，是如何预防 CSRF 攻击的？](https://q.shanyue.tech/fe/dom/569.html)

> 见文档 [SameSite Cookie - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite)

> 见文章 [Cookie 的 SameSite 属性](http://www.ruanyifeng.com/blog/2019/09/cookie-samesite.html)

-   None: 任何情况下都会向第三方网站请求发送 Cookie
-   Lax: 只有导航到第三方网站的 Get 链接会发送 Cookie，跨域的图片、iframe、form表单都不会发送 Cookie
-   Strict: 任何情况下都不会向第三方网站请求发送Cookie

目前，主流浏览器 Same-Site 的默认值为 `Lax`，而在以前是 `None`，将会预防大部分 CSRF 攻击，如果需要手动指定 `Same-Site` 为 `None`，需要指定 Cookie 属性 `Secure`，即在 https 下发送

### Cookie 增删改查

-   题目：[如何设置一个 Cookie](https://q.shanyue.tech/fe/dom/161.html)
-   题目：[如何删除一个 Cookie](https://q.shanyue.tech/fe/dom/162.html)

通过把该 `cookie` 的过期时间改为过去时即可删除成功，具体操作的话可以通过操作两个字段来完成

1.  `max-age`: 将要过期的最大秒数，设置为 `-1` 即可删除
2.  `expires`: 将要过期的绝对时间，存储到 `cookies` 中需要通过 `date.toUTCString()` 处理，设置为过期时间即可删除

很明显，`max-age` 更为简单，以下代码可在命令行控制台中进行测试

```
// max-age 设置为 -1 即可成功
document.cookie = 'a=3; max-age=-1'
```

```
> document.cookie
< ""

> document.cookie = 'a=3'
< "a=3"

> document.cookie
< "a=3"

// 把该字段的 max-age 设置为 -1
> document.cookie = 'a=3; max-age=-1'
< "a=3; max-age=-1"

// 删除成功
> document.cookie
< ""
```

同时，也可以使用最新关于 cookie 操作的 API: [CookieStore API](https://developer.mozilla.org/en-US/docs/Web/API/CookieStore) 其中的 `cookieStore.delete(name)` 删除某个 cookie(HTTPS才能用)

### cookie localstorage sessionstorage

|         | cookie   | localstorage | sessionstorage|
|  ----   | ----      |     ----     |        ---- |  
| 大小     | 4kb       |      2.5MB-10MB    |2.5MB-10MB |
| 跟随http发送  | 会 |      不      |        不       | 
 时间                    可设置     永久                     会话关闭删除


## 块级元素 行内元素 块级行内元素
#### 1. 块级元素 block

-   每个块级元素都是独自占一行；
-   高度，行高，外边距（margin）以及内边距（padding）都可以控制；
-   元素的宽度如果不设置的话，默认为父元素的宽度（父元素宽度100%；
-   多个块状元素标签写在一起，默认排列方式为从上至下；

```html
 <address>  // 定义地址 
 <caption>  // 定义表格标题 
 <dd>      // 定义列表中定义条目 
 <div>     // 定义文档中的分区或节 
 <dl>    // 定义列表 
 <dt>     // 定义列表中的项目 
 <fieldset>  // 定义一个框架集 
 <form>  // 创建 HTML 表单 
 <h1>    // 定义最大的标题
 <h2>    // 定义副标题
 <h3>     // 定义标题
 <h4>     // 定义标题
 <h5>     // 定义标题
 <h6>     // 定义最小的标题
 <hr>     // 创建一条水平线
 <legend>    // 元素为 fieldset 元素定义标题
 <li>     // 标签定义列表项目
 <noframes>    // 为那些不支持框架的浏览器显示文本，于 frameset 元素内部
 <noscript>    // 定义在脚本未被执行时的替代内容
 <ol>     // 定义有序列表
 <ul>    // 定义无序列表
 <p>     // 标签定义段落
 <pre>     // 定义预格式化的文本
 <table>     // 标签定义 HTML 表格
 <tbody>     // 标签表格主体（正文）
 <td>    // 表格中的标准单元格
 <tfoot>     // 定义表格的页脚（脚注或表注）
 <th>    // 定义表头单元格
 <thead>    // 标签定义表格的表头
 <tr>     // 定义表格中的行

```
#### 2. 行内元素 inline

行内元素不可以设置宽（width）和高（height），但可以与其他行内元素位于同一行，行内元素内一般不可以包含块级元素。行内元素的高度一般由元素内部的字体大小决定，宽度由内容的长度控制。 行内元素有以下特点：

-   不会独占一行，相邻的行内元素会排列在同一行里，直到一行排不下才会自动换行，其宽度随元素的内容而变化；
-   高宽无效，对外边距（margin）和内边距（padding）仅设置左右方向有效 上下无效；
-   设置行高有效，等同于给父级元素设置行高；
-   元素的宽度就是它包含的文字或图片的宽度，不可改变；
-   行内元素中不能放块级元素，a 链接里面不能再放链接；

```html
 <a>     // 标签可定义锚 
 <abbr>     // 表示一个缩写形式 
 <acronym>     // 定义只取首字母缩写 
 <b>     // 字体加粗 
 <bdo>     // 可覆盖默认的文本方向 
 <big>     // 大号字体加粗 
 <br>     // 换行 
 <cite>     // 引用进行定义 
 <code>    // 定义计算机代码文本
 <dfn>     // 定义一个定义项目
 <em>     // 定义为强调的内容
 <i>     // 斜体文本效果
 <kbd>     // 定义键盘文本
 <label>     // 标签为 input 元素定义标注（标记）
 <q>     // 定义短的引用
 <samp>     // 定义样本文本
 <select> // 创建单选或多选菜单
 <small>     // 呈现小号字体效果
 <span>     // 组合文档中的行内元素
 <strong> // 加粗
 <sub>     // 定义下标文本
 <sup>     // 定义上标文本
 <textarea>     // 多行的文本输入控件
 <tt>     // 打字机或者等宽的文本效果
 <var>    // 定义变量
复制代码
```

#### 3. 行内块级元素 inline-block

行内块级元素，它既具有块级元素的特点，也有行内元素的特点，它可以自由设置元素宽度和高度，也可以在一行中放置多个行内块级元素。比如：input、img就是行内块级元素，它可设置高宽以及一行多个。 具体特点如下：

-   高度、行高、外边距以及内边距都可以控制；
-   默认宽度就是它本身内容的宽度，不独占一行，但是之间会有空白缝隙，设置它上一级的 font-size 为 0，才会消除间隙；

```html
<button> 
<input>   
<textarea> 
<select> 
<img>
复制代码
```

#### 4. 元素类型转换 display

display：block ，定义元素为块级元素

display : inline ，定义元素为行内元素

display：inline-block，定义元素为行内块级元素

## 统计当前页面出现次数最多的标签

-   题目：[如何找到当前页面出现的所有标签](https://q.shanyue.tech/fe/dom/573.html)
-   题目：[如何找到当前页面出现次数最多的 HTML 标签](https://q.shanyue.tech/fe/dom/418.html)

这是一道前端基础与编程功底具备的面试题：

-   如果你前端基础强会了解 `document.querySelector(*)` 能够列出页面内所有标签
-   如果你编程能力强能够用**递归**/**正则**快速实现同等的效果

有三种 API 可以列出页面所有标签：

1.  `document.querySelector('*')`，标准规范实现
2.  `$$('*')`，devtools 实现

## 渲染 HTML 的过程

1.  解析 HTML：浏览器将 HTML 文档解析为 DOM 树。解析过程包括识别标签、构建 DOM 树结构等。
2.  解析 CSS：浏览器将 CSS 样式表解析为样式规则，并将其应用于 DOM 树中的相应元素。
3.  布局：浏览器根据 CSS 样式规则和 DOM 树的结构，计算出每个元素在视口中的位置和大小，并形成一颗渲染树。
4.  绘制：浏览器将渲染树中的元素按照其在视口中的位置和大小绘制出来。
5.  合成：浏览器将绘制出来的图像合成为最终的视觉输出。

在这个过程中，如果浏览器遇到 JavaScript 代码，会暂停渲染进程并执行 JavaScript 代码。执行完 JavaScript 代码后，再恢复渲染进程。

## 如何优化动画?

将动画的 position 属性 设置为 absolute 或者 fixed，将动画脱离文档流，这样他的回流就 不会影响到页面了。

## 图片懒加载

-   题目：[网站开发中，如何实现图片的懒加载](https://q.shanyue.tech/fe/dom/1.html)

###  getBoundingClientRect

### 最新的实现方案是使用 [IntersectionObserver API](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API)。

```js
const observer = new IntersectionObserver((changes) => {
  changes.forEach((change) => {
    // intersectionRatio
    if (change.isIntersecting) {
      const img = change.target
      img.src = img.dataset.src
      observer.unobserve(img)
    }
  })
})

observer.observe(img)
```

###  LazyLoading 属性

浏览器觉得懒加载这事可以交给自己做，你们开发者加个属性就好了。实在是...！

```
<img src="shanyue.jpg" loading="lazy" />
```
## sessionStorage 与 localStorage 有何区别

localStorage 生命周期是永久除非自主清除 sessionStorage 生命周期为当前窗口或标签页，关闭窗口或标签页则会清除数据

他们均只能存储字符串类型的对象

不同浏览器无法共享 localStorage 或 sessionStorage 中的信息。相同浏览器的不同页面间可以共享相同的 localStorage（页面属于相同域名和端口），但是不同页面或标签页间无法共享 sessionStorage 的信息。这里需要注意的是，页面及标 签页仅指顶级窗口，如果一个标签页包含多个 iframe 标签且他们属于同源页面，那么他们之间是可以共享 sessionStorage 的。

## 如何设置一个支持过期时间的 localStorage

设置如下数据结构，当用户存储数据时，存储至 `__value` 字段。并将过期时间存储至 `__expires` 字段。

```
{  __value, __expires }
```

而当每次获取数据时，判断当前时间是否已超过 `__expires` 过期时间，如果超过，则返回 `undefined`，并删除该数据。
## addEventListener()

-   题目：[浏览器中监听事件函数 addEventListener 第三个参数有那些值](https://q.shanyue.tech/fe/dom/689.html)

详见 MDN https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener

## 什么是事件冒泡和事件捕获 事件委托
https://github.com/CsongL/javaScript-learning/blob/main/interviewJs/BrowserDom/%E4%BA%8B%E4%BB%B6%E5%86%92%E6%B3%A1%E4%BA%8B%E4%BB%B6%E6%8D%95%E8%8E%B7%E4%BA%8B%E4%BB%B6%E5%A7%94%E6%89%98.md

## 什么是事件委托，e.currentTarget 与 e.target 有何区别

-   题目：[什么是事件委托，e.currentTarget 与 e.target 有何区别](https://q.shanyue.tech/fe/dom/558.html)

![](https://xunlianying.feishu.cn/space/api/box/stream/download/asynccode/?code=YjdjNDRjMGU4MGQ5MDRhMGFlOWMyNWFjZGRiOWI1NTJfZXlLZGFyZXpLNHl6M3FielVHWEthM2JENkVrdXlJdWRfVG9rZW46Ym94Y25CQUpWd0RTQXF5djJBUzdDZFB1dHNnXzE2ODA2MDkwMDM6MTY4MDYxMjYwM19WNA)

事件委托指当有大量子元素触发事件时，将事件监听器绑定在父元素进行监听，此时数百个事件监听器变为了一个监听器，提升了网页性能。

另外，React 把所有事件委托在 Root Element，用以提升性能。
## 所有元素一定会经过事件冒泡， 捕获过程吗

不是所有的元素都会经过事件捕获和冒泡的过程。这个过程是基于事件类型和事件处理程序的注册方式来决定的。

对于大多数事件类型，事件处理程序默认是在事件冒泡阶段进行注册和触发的，也就是从目标元素开始往上冒泡，直到达到根元素。这种默认的事件处理方式适用于大多数情况，也是最常用的方式。

然而，对于某些事件类型，例如`focus`和`blur`事件，它们的默认事件处理程序是在事件捕获阶段进行注册和触发的，也就是从根元素开始往下捕获，直到达到目标元素。这是因为在这些事件类型中，捕获阶段可以更好地处理焦点相关的问题。

另外，也可以通过在事件处理程序中调用`event.stopPropagation()`方法来停止事件的继续传播。这个方法会阻止事件继续向上冒泡或向下捕获，从而影响事件的传播过程。

总之，事件捕获和冒泡的过程是基于事件类型和事件处理程序的注册方式来决定的，并不是所有的元素都会经过这个过程。
## e.preventDefault

-   题目：[DOM 中如何阻止事件默认行为，如何判断事件否可阻止？](https://q.shanyue.tech/fe/dom/556.html)

如下：

-   `e.preventDefault()`: 取消事件
-   `e.cancelable`: 事件是否可取消

如果 `addEventListener` 第三个参数 `{ passive: true}`，`preventDefault` 将会会无效

## input 事件

-   题目：[React 中监听 input 的 onChange 事件的原生事件是什么](https://q.shanyue.tech/fe/dom/611.html)
-   题目：[input 中监听值的变化是在监听什么事件](https://q.shanyue.tech/fe/dom/215.html)

重点要了解下 `input` 事件，比如 React 的 `onChange` 在底层实现时，就是用了原生的 `input` 事件，可观察以下代码输出。

```js
import "./styles.css";

export default function App() {
  return (
    <div className="App">
      <input
        onChange={(e) => {
          console.log("Event: ", e);
          console.log("NativeEvent: ", e.nativeEvent);
          console.log("CurrentTarget: ", e.nativeEvent.currentTarget);
          console.log("NativeEvent Type: ", e.nativeEvent.type);
        }}
      />
    </div>
  );
}
```

## ClipBoard API

-   题目：[在浏览器中如何获取剪切板中内容](https://q.shanyue.tech/fe/dom/315.html)
-   题目：[浏览器的剪切板中如何监听复制事件](https://q.shanyue.tech/fe/dom/444.html)
-   题目：[如何实现页面文本不可复制](https://q.shanyue.tech/fe/dom/454.html)

通过 `Clipboard API` 可以获取剪切板中内容，但需要获取到 `clipboard-read` 的权限，以下是关于读取剪贴板内容的代码：

```js
// 是否能够有读取剪贴板的权限
// result.state == "granted" || result.state == "prompt"
const result = await navigator.permissions.query({ name: "clipboard-read" })

// 获取剪贴板内容
const text = await navigator.clipboard.readText()
```

> 注: 该方法在 `devtools` 中不生效

有 CSS 和 JS 两种方法禁止复制，以下任选其一或结合使用

使用 CSS 如下：

```css
user-select: none;
```

或使用 JS 如下，监听 `selectstart` 事件，禁止选中。

当用户选中一片区域时，将触发 `selectstart` 事件，Selection API 将会选中一片区域。禁止选中区域即可实现页面文本不可复制。

```js
document.body.onselectstart = e => {  
  e.preventDefault();
}

document.body.oncopy = e => {  
  e.preventDefault();
}
```

## fetch 中 credentials 指什么意思
[fetch mdn](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie)
`credentials` 指在使用 `fetch` 发送请求时是否应当发送 `cookie`

-   `omit`: 从不发送 `cookie`.
-   `same-origin`: 同源时发送 `cookie` (浏览器默认值)
-   `include`: 同源与跨域时都发送 `cookie`

## 如何取消请求的发送

-   题目：[如何取消请求的发送](https://q.shanyue.tech/fe/dom/502.html)

以下两种 API 的方式如下

-   XHR 使用 `xhr.abort()`
-   fetch 使用 `AbortController`
## 如何把 DOM 转化为图片

-   题目：[如何把 DOM 转化为图片](https://q.shanyue.tech/fe/dom/437.html)

简单总结：DOM -> SVG -> Canvas -> JPEG/PNG

## React/Vue 中的 router 实现原理如何

-   题目：[React/Vue 中的 router 实现原理如何](https://q.shanyue.tech/fe/dom/463.html)

前端路由有两种实现方式:

history API

-   通过 `history.pushState()` 跳转路由
-   通过 `popstate event` 监听路由变化，`history.forward() history.back()`会触发，  但无法监听到 `history.pushState() history.replaceState()` 时的路由变化

hash

-   通过 `location.hash` 跳转路由
-   通过 `hashchange event` 监听路由变化

## 垃圾回收机制（一般是JS引擎做的）

### 是什么？

一种自动管理内存的技术，判断对象是否需要回收该对象所使用的内存空间，避免内存泄漏和溢出

### 怎么做？

#### 标记清除法（周期性执行或内存使用达到一定阈值触发）

![](https://cdn.nlark.com/yuque/0/2023/png/12396624/1681733726802-ad7e1b67-96e2-45de-a6be-581e3afd7f89.png)

1.  从全局对象开始，递归遍历所有对象，对可达对象进行标记
2.  遍历内存中所有对象，若发现某个对象没有被标记，则表示该对象不再被使用，可以回收
3.  回收空间，更新内存

缺点：内存碎片

解决方式： 标记整理法

#### 引用计数法

为每个对象维护一个计数器，记录有多少个引用指向该对象，当计数器变为0时进行垃圾回收

缺点：循环引用

#### 分代回收法（V8）

![](https://cdn.nlark.com/yuque/0/2023/png/12396624/1681732565487-f42e8f8a-32db-43c3-b539-9d141f504f4d.png)

基于一个假设：大部分对象在内存中存在时间较短，少数对象存活时间较长；基于此将内存分为新生代（1-8M，小）和老生代（大）两部分，分别存放存活时间较短和较长的对象，并且分别使用不同的垃圾回收器

##### 副垃圾回收器-新生代

-   Scavenge算法（对半划分新生代区域）：创建新对象时，先放入新生代的对象区域，当对象区域快满了的时候进行gc，**将存活的对象复制到空闲区域中有序排列（实现了内存整理）。复制完成后对象区域和空闲区域角色互换**，使得新生代的两块区域可以无限重复使用
-   对象晋升策略：在新生代经过两次gc还存活的对象会被移动到老生区中

##### 主垃圾回收器-老生代

-   标记-清除法
-   标记-整理法：让所有存活对象都向一端移动

**什么是全停顿？**
正在执行的js脚本暂停等待垃圾回收，完毕后恢复执行

# CSS 
## 原子化css
[重新构想原子化](https://antfu.me/posts/reimagine-atomic-css-zh)
## 盒模型
CSS 的盒模型主要包括以下两种，可通过 [box-sizing](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing) 属性进行配置：
-   `content-box`：默认属性。width 只包含 content
-   `border-box`：width 包含 (content、padding、border)

## CSS specificity (权重)
### 固定优先级
!important 	                      Infinity，“ 无穷大”（无法被超越）
行内样式（style=“”）	       1000
id选择器（#id）	               100
类选择器（.className）	10
属性选择器（a[rel="external"]）	10
伪类选择器（a:hover,li:nth-child）	10
标签选择器（div,h1,p）	1
伪元素选择器	                1
子选择器、相邻选择器	0
通配符选择器（*）	        0

### 变化优先级
相邻选择器（h1+p）  
子选择器（ul>li）  
[后代选择器](https://so.csdn.net/so/search?q=%E5%90%8E%E4%BB%A3%E9%80%89%E6%8B%A9%E5%99%A8&spm=1001.2101.3001.7020)（li a）

> `[:not](https://codepen.io/shanyue/pen/dyGQqBe)` [的优先级影响 - codepen](https://codepen.io/shanyue/pen/dyGQqBe) 可以看出 `:not` 对选择器的优先级无任何影响

## '+' 与 '~' 选择器有什么不同

-   [【Q315】'+' 与 '~' 选择器有什么不同](https://github.com/shfshanyue/Daily-Question/issues/317)
-   `+` 选择器匹配紧邻的兄弟元素
-   `~` 选择器匹配随后的所有兄弟元素

## z-index 与层叠上下文
`z-index` 高数值一定在低数值前边吗，div 层级如下所示

-   A -> 3
    -   AA -> 1000
-   B -> 4
    -   BB -> 10

代码见 [zindex - codepen](https://codepen.io/shanyue/pen/XWMVpxJ)
不一定，更复杂的示例见 [层叠上下文 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)

## 水平垂直居中

-   题目: [如何实现一个元素的水平垂直居中](https://github.com/shfshanyue/Daily-Question/issues/10)
-   代码: [absolute/translate](https://codepen.io/shanyue/pen/XWMdabg?editors=1100)

宽高不定的块状元素水平垂直居中，可见以下示例

-   [absolute/translate](https://codepen.io/shanyue/pen/XWMdabg?editors=1100)

同时提供几种不同的思路：

-   flex:
    -   `justify-content: center`
    -   `align-content: center`
-   grid
    -   `place-items: center`
-   absolute/translate
    -   `position: absolute`
    -   `left/top: 50%`
    -   `transform: translate(50%)`

## 左侧固定、右侧自适应

-   题目: [css 如何实现左侧固定300px，右侧自适应的布局](https://github.com/shfshanyue/Daily-Question/issues/18)
-   代码: [代码](https://codepen.io/shanyue/pen/GRWmbyb?editors=1100)

提供 `flex` 与 `grid` 布局的两种思路

-   flex:
    -   左侧: `flex-basis: 200px`
    -   右侧: `flex-grow: 1; flex-shrink: 0;`
-   grid
    -   父容器: `grid-template-columns: 200px 1fr;`

## 三栏均分布局

-   题目: [如何实现三列均分布局](https://github.com/shfshanyue/Daily-Question/issues/572)
-   代码: [如何实现三列均分布局](https://codepen.io/shanyue/pen/yLMzxqX)

同样提供 `flex` 与 `grid` 的两种方案

-   flex:
    -   方案一: `flex: 1;`
    -   方案二: `flex-basis: calc(100% / 3)`
-   grid:
    -   父容器: `grid-template-columns: 1fr 1fr 1fr`

进一步问题：如何实现十六列均分布局？

## CSS 配置暗黑模式

-   题目：[如何使用 CSS 实现网站的暗黑模式 (Dark Mode)](https://github.com/shfshanyue/Daily-Question/issues/375)

最简单来讲，可通过媒体查询 `@media (prefers-color-scheme: dark)` 与 CSS 变量实现。

`window.matchMedia('(prefers-color-scheme: dark)').matches`

## bfc

块级格式化上下文

当一个元素生成 BFC 时，它的子元素会按照一定的规则进行布局，不会影响到 BFC 以外的元素。常见的触发 BFC 的方式有：

- `html` 根元素

- `float`（不为 `none` 即可）
  - `left`
  - `right`

- position
  - `absolute`
  - ``fixed`

- `display`
  - `inline-block`
  - `flex、inline-flex`

  - `grid、inline-grid`

  - `table`、`table-cell`、`table-caption`

  - `flow-root`

BFC 的作用主要有以下几个方面：

1. 清除浮动。在一个 BFC 中，浮动元素会被父元素完全包裹，不会溢出到外部区域。
2. 避免 margin 重叠。在一个 BFC 中，相邻的两个元素的 margin 不会发生重叠。
3. 创建独立的渲染环境。在一个 BFC 中，子元素的布局不会受到外部元素的影响，可以更灵活地控制布局。

## dislay: none 和 visibility: none
display:none会将元素从页面中完全移除，不占据任何空间；visibility:none则会将元素隐藏，但仍然占据空间。

对于动画，display:none会使动画无法执行，而visibility:none则可以执行动画。

## 画三角形
```css
/* 不设置border-bottom即可 */
/*  transparent 透明*/
.caret {
  width: 0;
  height: 0;
  border-top: 50px solid black;
  border-right: 50px solid transparent;
  border-left: 50px solid transparent;
}
```

## flex
**容器属性**
- `flex-direction `
- `justify-content` 主轴对齐方式
	- `flex-start`
	- `flex-end`
	- `cneter`
	- `space-between/around/evenly`
- `align-items` 交叉轴对齐方式
	- `flex-start`
	- `flex-end`
	- `cneter`
	- `baseline`
	- `stretch`
- `flex-wrap` 默认不换行
- `align-content` 如果有多行，定义在交叉轴对齐方式
**子元素属性**
- `order` 排序
- `align self`单独的交叉轴对齐方式
- `flex` 包括 `grow shrink basis`
	- **默认 0 1 auto**
	- **flex 1 ：1 1 0**
	- **flex auto: 1 1 auto** 

## 布局

### 水平居中

-   绝对定位：子绝父相**position:absolute** + margin:auto（left+right为0，需要宽高） / left:50%（负margin，需要宽高/transform:translateX(-50%)，无需宽高） / calc
-   弹性布局flex：父元素display:flex，justify-content:center（主轴水平轴居中）
-   利用块级元素撑满父元素的特点：**元素宽度确定**，左右margin:auto就可以平分剩余空间
-   利用行内元素居中：一个块级元素包裹一个行内元素，行内元素居中，**不需要知道宽高**，设父元素text-align:center，子元素display:inline-block

### 垂直居中

-   绝对定位：子绝父相**position:absolute** + margin:auto（top+bottom为0，需要宽高） / top:50%（负margin，需要宽高 / transform:translateY(-50%)，无需宽高） / calc
-   弹性布局flex：父元素display:flex，align-items:center（垂直轴居中）
-   利用内边距撑开父元素：**父元素不设宽高**，设父元素的padding:20px 0自动填充父元素（无需宽高）
-   利用line-height等于height使**元素的单行文本**垂直居中：height:10px; line-height:10px（注意不能设line-height:100%，line-height的百分比是相对当前字体尺寸而言的）（需要宽高）
-   利用vertical-align:middle（只能是行内元素），如果是div，要设父元素display:table，子元素display:table-cell（无需宽高）

### 水平垂直居中

-   绝对定位：子绝父相**position:absolute** + margin:auto（top+bottom+left+right为0，需要宽高） / top:50%（负margin，需要宽高 / transform:translate(-50%,-50%)，无需宽高） / calc
-   弹性布局flex：父元素display:flex，justify-content:center，align-items:center（水平轴居中+垂直轴居中）（无需宽高）
-   利用父元素text-align:center+父元素line-height=父元素height+子元素vertical-align:middle+子元素display:inline-block（需要宽高）

### 两栏布局（左边固定，右边自适应）

-   利用float：两个div，左边float:left，width:20px，右边margin-left=width
-   绝对定位：子绝父相，两个div，左边absolute或者fixed，右边margin-left=width
-   flex布局：三个div，父元素display:flex，自适应子元素flex:1

### 三栏布局（左右固定，中间自适应）

-   利用float：三个div，左右float，中间margin
-   绝对定位：子绝父相，左右leftright各为0，top为0，中间设margin
-   flex布局：父元素display:flex，中间子元素flex:1




# JavaScript
## 基本数据类型
-   string
-   number
-   bigint
-   boolean
-   undefined
-   symbol
-   null
## pnpm
### 好处
-   幽灵依赖解决 
	- 在.pnpm下铺平
	
-   （节省磁盘）node_modules 体积过大问题 
	- 同一个依赖一个项目内只会有一份
	- 通过硬链接到全局 store
    

### 原理
npm2 是通过嵌套的方式管理 node_modules 的，会有同样的依赖复制多次的问题。

npm3+ 和 yarn 是通过铺平的扁平化的方式来管理 node_modules，解决了嵌套方式的部分问题，但是引入了幽灵依赖的问题，并且同名的包只会提升一个版本的，其余的版本依然会复制多次。

包是从全局 store 硬连接到虚拟 store 的，这里的虚拟 store 就是 node_modules/.pnpm。

我们打开 node_modules 看一下：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9b4dc807ca6e4ae7a955c8dd6385cb46~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

确实不是扁平化的了，依赖了 express，那 node_modules 下就只有 express，没有幽灵依赖。

展开 .pnpm 看一下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/65a69589bd534fdd97bdbeb6e3e1024c~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

所有的依赖都在这里铺平了，都是从全局 store 硬连接过来的，然后包和包之间的依赖关系是通过软链接组织的。
![[截屏2023-04-11 00.46.42.png]]
比如 .pnpm 下的 expresss，这些都是软链接，
**express 的真实地址在 node_modules/.pnpm/express@4.18.1/node_modules/express**
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c50d8dc8a2a4466ba9e5eccd5c15614e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

也就是说，所有的依赖都是从全局 store 硬连接到了 node_modules/.pnpm 下，然后之间通过软链接来相互依赖。

  ![[Pasted image 20230410114701.png]]
### 软链接 硬链接
A symbolic link, also known as a soft link, is a special type of file that acts as a pointer to another file or directory. It is essentially a shortcut to another file, and when you open a symbolic link, you're actually accessing the file or directory that it points to. Symbolic links are created using the `ln -s` command.
For example, if you have a file called `file1.txt` and you create a symbolic link to it called `file2.txt`, any changes made to `file1.txt` will also be reflected in `file2.txt`, since they are pointing to the same file.

A hard link, on the other hand, is a reference to a file or directory that points directly to the data on the disk. Unlike a symbolic link, a hard link is an actual file or directory entry that is created by the operating system. Hard links are created using the `ln` command.
For example, if you have a file called `file1.txt` and you create a hard link to it called `file2.txt`, any changes made to `file1.txt` will also be reflected in `file2.txt`, since they are both pointing to the same physical data on the disk. However, if you delete `file1.txt`, `file2.txt` will still remain on the disk, because it is a hard link to the same data.

## 什么是 Javascript 的事件流？有哪些事件流模型？

JavaScript 的事件流是指浏览器中所有事件的传递和处理过程。事件流可以分为三个阶段：事件捕获、目标阶段和事件冒泡。

1.  **事件捕获阶段**：事件从最外层的文档节点一直往下传递，直到事件到达事件的目标元素。在这个过程中，事件会经过父节点和祖先节点，直到到达目标节点。如果在这个过程中有事件处理程序，则事件将被调用。
2.  **目标阶段**：事件到达了目标元素后，将在目标元素上调用事件处理程序。如果有多个事件处理程序绑定到目标元素上，将按照它们的顺序执行。
3.  **事件冒泡阶段**：事件在目标元素上处理后，会从目标元素开始，向上传递回文档节点。在这个过程中，事件也会遇到任何绑定的事件处理程序。

目前有两种事件流模型：

1.  **W3C 标准事件模型（DOM2级事件模型）**：事件流由三个阶段组成：捕获阶段、目标阶段和冒泡阶段。这种事件模型中，事件处理程序的调用顺序与它们注册的顺序相同。
2.  **Microsoft 事件模型（IE 事件模型）**：事件流由两个阶段组成：目标阶段和冒泡阶段。在这个事件模型中，事件处理程序的调用顺序与它们注册的相反。

总体来说，W3C 标准事件模型是当前主流的事件流模型。对于事件流，我们需要了解事件捕获、目标阶段和事件冒泡，以便于正确地使用事件处理程序。

## Map weakMap
-   `Map`: 可使用任何数据类型作为 key，但因其在内部实现原理中需要维护两个数组，存储 key/value，因此垃圾回收机制无法回收
-   `WeakMap`: 只能使用引用数据类型作为 key。弱引用，不在内部维护两个数组，可被垃圾回收，但因此无法被遍历！即没有与枚举相关的 API，如 `keys`、`values`、`entries` 等
## 实现private变量

symbol
闭包
 '#'
https://juejin.cn/post/7042244444782870558#heading-7

## 上传文件ajax请求特点

1. 数据传输方式：一般的Ajax请求是通过发送JSON或者XML等格式的文本数据来传递数据，而上传文件的Ajax请求则需要通过表单数据或二进制数据流来传输文件。
2. HTTP请求头：上传文件的Ajax请求需要设置合适的HTTP请求头，比如设置请求方法为“POST”，Content-Type为“multipart/form-data”，以及设置“X-Requested-With”为“XMLHttpRequest”等头部信息。
3. FormData对象：在上传文件的Ajax请求中，需要使用FormData对象来构造表单数据，其中包括文件数据、文本数据以及其他数据。FormData对象可以通过JavaScript API进行创建和操作，也可以通过表单元素的实例来创建。
4. XMLHttpRequest对象：上传文件的Ajax请求与一般的Ajax请求一样需要使用XMLHttpRequest对象来发送请求，但是在上传文件时需要对XMLHttpRequest对象进行特殊的处理，比如监听onprogress事件来显示上传进度、监听onload事件来处理上传结果等。

##  CommonJS、AMD、CMD、ES Module 

1. CommonJS:一种用于服务器端的模块化规范，主要用于 Node.js，它通过 require 来引 入模块，通过 module.exports 或 exports 来导出模块。
2. AMD:一种用于浏览器端异步加载模块的规范，主要用于 RequireJS，它通过 define 来 定义模块，通过 require 来异步加载模块。
3. CMD:一种用于浏览器端延迟执行的模块化规范，主要用于 SeaJS，它通过 define 来定 义模块，通过 require 来延迟执行模块。
4. ES Module:一种官方规范，用于浏览器端和 Node.js 中，通过 import 来引入模块，通 过 export 来导出模块。

其中，ES Module 是官方规范，具有更好的兼容性和可扩展性，而 CommonJS 和 AMD/CMD 则更多地用于 Node.js 和浏览器端的异步加载，各有优缺点。

1. **CommonJS**:						 							 							

   Node.js 实现了 CommonJS 规范，在 Node.js 中，每个文件就是一个模块，有自己的作用 域，也就是说，在一个文件里定义的变量或函数，都是私有的，对其他文件不可⻅。如果想提 供给其它文件使用，可将它们挂载到 exports 或 module.exports 接口对象上，比如:其中，module 是一个对象，代表当前模块，它有一个属性 exports ，是对外的接口，所以 module.exports 和 exports 保存的是同一个引用。

   如果要获取另一个模块暴露的接口，则使用 require(xxx) ，如果是第三方模块，xxx 为模块 名;如果是自定义模块，xxx 为模块文件路径。require() 会读入并执行一个 JS 文件，然后返 回该模块的module.exports 对象，如果没有发现指定模块，会报错。

2. **AMD**:AMD 是一种异步模块化方案，主要用于浏览器端。它采用异步加载模块的方式， 可以在⻚面加载时并行加载多个模块，提高了⻚面的加载速度。AMD 的模块化语法主要 是通过 define 和 require 来实现的，最著名的实现是 RequireJS。
3. **CMD**:CMD 是另一种异步模块化方案，也是主要用于浏览器端的。它采用异步加载模块 的方式，但是与 AMD 不同的是，CMD 是按需加载模块，只有在需要使用某个模块时才 会加载。CMD 的模块化语法主要是通过 define 和 require 来实现的，最著名的实现是 SeaJS。
4. **ES6 Module**:ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，极大 可能成为浏览器和服务器通用的模块解决方案。其模块功能主要由两个命令构成:export 和 import。export 命令用于规定模块的对外接口，import 命令用于输入其他模块提供的 功能。

### ES6 Module 与 CommonJS 的差异:
 CommonJS 模块输出的是一个值的拷⻉，ES6 模块输出的是值的引用。

CommonJS 模块输出的是值的拷⻉，也就是说，一旦输出一个值，模块内部的变化 就影响不到这个值。

ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到 模块加载命令import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只 读引用，到被加载的那个模块里面去取值。换句话说，ES6 的import有点像 Unix 系 统的“符号连接”，原始值变了，import加载的值也会跟着变。因此，ES6 模块是动态 引用，并且不会缓存值，模块里面的变量绑定其所在的模块。

CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
 运行时加载: CommonJS 模块就是对象;即在输入时是先加载整个模块，生成一个对

象，然后再从这个对象上面读取方法，这种加载称为“运行时加载”。

编译时加载: ES6 模块不是对象，而是通过 export 命令显式指定输出的代码，import 时采用静态命令的形式。即在import时可以指定加载某个输出值，而不是加载整个模 块，这种加载称为“编译时加载”。

CommonJS 加载的是一个对象(即module.exports属性)，该对象只有在脚本运行完才会生 成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生 成。

## ES5 类 ES6 class 区别? 

在 ES5 中，我们通常使用构造函数来创建对象和实现继承，而在 ES6 中，引入了类(class) 的概念来简化对象创建和继承的过程。下面是 ES5 中类和 ES6 中的 class 的一些区别:

- 语法:ES5 中使用构造函数来定义类，而 ES6 中使用 class 关键字来定义类。
-  继承方式:ES5 中使用原型链继承来实现继承，而 ES6 中使用 extends 关键字和super 函数来实现继承。
   类方法的定义方式不同:ES5 中类方法定义在构造函数的原型对象上，而 ES6 中类方法定义在类的内部。

- 类属性的定义方式:ES5 中类属性定义在构造函数的原型对象上，而 ES6 中类属性 定义在类的内部，并且需要使用 static 关键字来定义静态属性。

- 类的调用方式: ES6 中需要使用 new 关键字来创建类的实例，而 ES5 中类的调用方 式更加简单，只需要像函数一样调用即可。

 ES6 底层实现上仍然是基于原型链

## xhr fetch axios

[xhr mdn](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)

### 取消请求
- `xhr.abort`
- `fetch`
	- fetch cofig里传`AbortController.signal` , 调用`AbortController.abort()`
- `axios`
```js
const CancelToken = axios.CancelToken; 
const source = CancelToken.source();
axios.post('/user/12345', 
		   { name: 'new name' }, 
		   { cancelToken: source.token })
source.cancel()
```

## 遍历对象key
- `for in` 所有可枚举的，可以遍历原型链
- `Object.keys()` 自身可枚举的，原型链不可
- `Object.getOwnPropertyNames()` 所有自身属性名（包括枚举和不可枚举），没有symbol
- `Reflect.ownKeys() 包括getOwnPropertyNames() + getOwnPropertySymbols()`

## for in for of 
`for...in` 和 `for...of` 是 JavaScript 中用于迭代对象和数组的循环结构，但它们在用法和迭代的方式上有所不同。

`for...in` 循环用于遍历对象的可枚举属性。它会迭代对象及其原型链上的所有可枚举属性，并将属性名赋值给循环变量。这意味着除了遍历对象自身的属性，还会遍历继承的属性。这是因为对象在 JavaScript 中是通过原型链连接的。

`for...of` 循环用于遍历可迭代对象（例如数组、字符串、Set、Map 等）中的元素。它遍历对象自身的属性，而不涉及原型链。对于每次迭代，它将对象的值赋给循环变量。


## 原型链
以下是使用 ES5 的语法手写一个类的继承的示例：

```js
// 父类
function Animal(name) {
  this.name = name;
}

Animal.prototype.sayName = function() {
  console.log(this.name);
}

// 子类
function Dog(name, breed) {
  Animal.call(this, name); // 调用父类构造函数，设置子类实例的属性
  this.breed = breed;
}

// 子类继承父类的原型
Dog.prototype = Object.create(Animal.prototype);

// 修复子类的构造函数引用
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function() {
  console.log("Woof!");
}

// 创建子类实例
var myDog = new Dog("Max", "Labrador");
myDog.sayName(); // 输出: Max
myDog.bark();    // 输出: Woof!
```
*为什么要写prototype.constructor*
在上述示例中，通过将子类的原型对象设置为父类的一个实例（通过 `Object.create()` 方法），子类就能继承父类的原型属性和方法。然后，为了修复子类的构造函数引用，我们将 `prototype.constructor` 设置为子类本身。

如果不手动修复 `prototype.constructor`，它将指向父类的构造函数。这意味着当你尝试创建子类实例时，实际上会调用父类的构造函数来创建一个父类实例，而不是子类实例。这通常会导致错误的行为和预期之外的结果。因此，为了确保正确的继承关系，我们需要手动修复 `prototype.constructor`。
## 事件循环
Js异步、事件循环与消息队列、微任务与宏任务
[https://zhuanlan.zhihu.com/p/139967525](https://zhuanlan.zhihu.com/p/139967525)

await 后面的是微任务
```js
async function async1() {
  console.log(1);
  await async2(); // async2立即执行，1之后立刻打印3
  console.log(2);
}
async function async2() {
  console.log(3);
}
async1();
setTimeout(() => console.log(4), 0);
new Promise(resolve => {
  resolve();
  console.log(5);
}).then(() => {
  console.log(6);
  Promise.resolve().then(()=>{
    console.log(7);
  });
});
console.log(8);
// 1 3 5 8 2 6 7 4
```

resolve 一个promise要等其他微任务先执行2个
- https://q.shanyue.tech/fe/js/727.html
## 异常
在前端开发中，要及时捕获异常并进行处理，可以采取以下方法：

1.  使用try-catch语句：在可能发生异常的代码块中使用try-catch语句包裹起来。这样，如果try块中的代码发生异常，就会立即跳转到catch块中执行相应的处理逻辑。在catch块中，你可以记录错误信息、显示错误提示或者进行其他的异常处理。


```js
try {   // 可能发生异常的代码 } 
catch (error) {   // 异常处理逻辑 }
```


2.  window.onerror事件：window对象上有一个`onerror`事件，可以用于全局捕获未被捕获的异常。你可以通过给`window.onerror`赋值一个函数来处理异常，当发生未被捕获的异常时，该函数会被调用。你可以在这个函数中记录异常信息或者进行其他的处理。

`window.onerror = function(message, source, lineno, colno, error) {   // 异常处理逻辑 };`

3.  使用Promise.catch()方法：如果你的代码中使用了Promise对象，可以通过Promise.catch()方法捕获Promise链中的异常。在catch()方法中可以处理异常，也可以继续抛出新的异常。

`yourPromise()   .then(result => {     // 处理成功的逻辑   })   .catch(error => {     // 异常处理逻辑   });`

4.  监听unhandledrejection事件：当Promise被reject并且没有被catch时，会触发unhandledrejection事件。你可以通过监听该事件来捕获未被处理的Promise异常。

`window.addEventListener('unhandledrejection', event => {   // 异常处理逻辑 });`

# 手写

##  手写 call、apply 及 bind 函数

#### 1.实现call函数
#### 2.实现apply函数
```javascript
Function.prototype.myCall = function (ctx = globalThis, ...args) {
  const key = Symbol("key");
  ctx[key] = this;
  const res=ctx[key](...args);
  delete ctx[key]
  return res
};

Function.prototype.myApply = function (ctx = globalThis, args) {
  const key = Symbol("key");
  ctx[key] = this;
  const res=ctx[key](...args);
  delete ctx[key]
  return res
};
```
#### 3.实现bind函数

```js
Function.prototype.fakeBind = function(obj, ...args) {
  return (...rest) => this.call(obj, ...args, ...rest)
}
```

```js
//如果想new bind后的函数
Function.prototype.mybind = function (ctx, ...args) {
  const fn = this

  const bindFn = function (...rest) {
    if(this instanceof bindFn){//new 的情况
      return new bindFn(...args, ...rest)
    }
    return fn.call(ctx, ...args, ...rest)
  }
  return bindFn
}
```


## Promise
### [实现promise]
(https://zhuanlan.zhihu.com/p/58428287)
```js
class myPromise {
  static resolve(val) {
    if (val && val.then) return val
    return new myPromise((resolve) => resolve(val))
  }
  constructor(fn) {
    this.resolveCallbacks = []
    this.rejectCallbacks = []
    this.error = undefined
    this.state = 'pending'
    this.value = undefined
    const resolve = (val) => {
      setTimeout(() => {
        this.value = val
        this.state = 'fulfilled'
        this.resolveCallbacks.forEach(({fn, resolve:res, reject: rej}) => {
          res(fn(this.value)) // 应该把fn放入微任务队列
        })
      })
    }
    const reject = (error) => {
      this.error = error
      this.state = 'rejected'
      this.rejectCallbacks.forEach(({fn, resolve:res, reject: rej})=>{
        rej(fn(this.error))
      })
    }
    fn(resolve, reject)
  }

  then(fn) {
    if (this.state === 'fulfilled') {
      const result = fn(this.value)
      // 需要返回一个 Promise
      // 如果状态为 resolved，直接执行（直接推入微任务队列）
      return myPromise.resolve(result)
    }
    if (this.state === 'pending') {
      return new myPromise((resolve, reject)=>{
        this.resolveCallbacks.push({fn, resolve, reject})
      })
    }
  }
  catch(fn){
    if (this.state === 'rejected') {
      const result = fn(this.error)
      return myPromise.resolve(result)
    }
    if (this.state === 'pending') {
      return new myPromise((resolve, reject)=>{
        this.rejectCallbacks.push({fn, resolve, reject})
      })
    }
  }
}

Prom.resolve(10)
  .then((o) => o * 10)
  .then((o) => o + 10)
  .then((o) => {
    console.log(o)
  })

return new Prom((resolve, reject) => reject('Error')).catch((e) => {
  console.log('Error', e)
})
```
### async await原理

```ts
function myAsync(fn) {
  return function () {
    const gen = fn()
    return new Promise((resolve, reject) => {
      
      let result
      function next(key, value) {
        try {
          result = gen[key](value)
        } catch (err) {
          reject(err)
        }
        if (result.done) {
          resolve(result.value)
        } else {
          return Promise.resolve(result.value).then(
            (val) => next('next', val),
            (err) => next('throw', err)
          )
        }
      }
      next('next')
    })
  }
}
```
### Promise.all

-   代码: [Promise.all](https://codepen.io/shanyue/pen/JjWEqBL?editors=0012)
-   题目: [Promise.all](https://github.com/shfshanyue/Daily-Question/issues/500)

```js
function pAll (_promises) {
  return new Promise((resolve, reject) => {
    // Iterable => Array
    const promises = Array.from(_promises)
    // 结果用一个数组维护
    const r = []
    const len = promises.length
    let count = 0
    for (let i = 0; i < len; i++) {
      // Promise.resolve 确保把所有数据都转化为 Promise
      Promise.resolve(promises[i]).then(o => { 
        // 因为 promise 是异步的，保持数组一一对应
        r[i] = o;

        // 如果数组中所有 promise 都完成，则返回结果数组
        if (++count === len) {
          resolve(r)
        }
        // 当发生异常时，直接 reject
      }).catch(e => reject(e))
    }
  })
}
```
### Promise.allSettled
返回所有的结果
```ts
const myPromiseSettled = (items) => {
  const onResolved = (value) => ({ status: "fulfilled", value });
  const onRejected = (reason) => ({ status: "rejected", reason });
  return Promise.all(
    items.map((item) => Promise.resolve(item).then(onResolved, onRejected))
  );
};
```
### race
```js
const promiseRace = (promises) => {
  return new Promise((resolve, reject) => {
    for(let i=0;i<promises.length;i++) {
      promises[i].then(res => resolve(res)).catch(err => reject(err))
    }
  })
}
```
### map
限制并发量， 类似Promise.all 
```ts
const map = function (items, fn, concurrency = Infinity) {
  const arr = Array.from(items)
  let curIndex = 0
  const len = items.length
  const result = []
  return new Promise((resolve) => {
    function next() {
      const idx = curIndex
      curIndex++
      Promise.resolve(arr[idx])
        .then((item) => fn(item, idx))
        .then((val) => {
          result[idx] = val
          if (curIndex === len) resolve(result)
          else next()
        })
    }
    for (let i = 0; i <= concurrency && i < len; i++) {
      next()
    }
  })
}
```
## Array
### isArray
```js
const isArray = Array.isArray || list => ({}).toString.call(list) === '[object Array]'
```
### Array.prototype.flat 

-   题目: [【Q443】实现一个数组扁平化的函数 flatten](https://github.com/shfshanyue/Daily-Question/issues/451)
-   代码: [【Q443】实现一个数组扁平化的函数 flatten](https://codepen.io/shanyue/pen/xxdjQXG?editors=0012)

`reduce` 与 `concat` 简直是绝配

```js
function flatten (list, depth = 1) {
  if (depth === 0) return list
  return list.reduce((a, b) => a.concat(Array.isArray(b) ? flatten(b, depth - 1) : b), [])
}
```
重写：
```js
function flatt(arr, depth = 1){
	if(depth === 0 )return arr
	arr.reduce((pre, val)=> pre.concat(Array.isArray(val) ? flatt(val, depth-1) : val), [])
}
```
### Array.prototype.reduce 

-   代码: [Array.prototype.reduce](https://codepen.io/shanyue/pen/dyWmLgQ?editors=0012)
-   题目: [Array.prototype.reduce](https://github.com/shfshanyue/Daily-Question/issues/658)

```js
const reduce = (list, fn, ...init) => {
  let next = init.length ? init[0] : list[0]
  for (let i = init.length ? 0 : 1; i < list.length; i++) {
    next = fn(next, list[i], i)
  }
  return next
}
```

该题目看起来简单，实际做起来有许多边界问题需要注意，如

1.  回调函数中第一个 Index 是多少？
2.  数组为稀疏数组如何处理？
注意，flatten 拥有第二个参数 depth

## sleep/delay

-   题目: [【Q435】JS 如何实现一个 sleep/delay 函数](https://github.com/shfshanyue/Daily-Question/issues/442)
-   代码: [【Q435】JS 如何实现一个 sleep/delay 函数](https://codepen.io/shanyue/pen/qBmoNRq?editors=0012)

`sleep` 函数既是面试中常问到的一道代码题，也是日常工作，特别是测试中常用的一个工具函数。

```js
const sleep = (seconds) => new Promise(resolve => setTimeout(resolve, seconds))

function delay (func, seconds, ...args) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      Promise.resolve(func(...args)).then(resolve)
    }, seconds)
  })
}
```
## deepClone
```js
function deepClone(source, weakmap) {
  const isPrimitive = (val) => {
    return /Number|Boolean|BigInt|String|Symbol|Null|Undefined|Function/.test(
      Object.prototype.toString.call(val)
    )
  }
  weakmap || (weakmap = new WeakMap())
  if (isPrimitive(source)) return source
  const typeStr = Object.prototype.toString.call(source)
  const type = typeStr.substring(8, typeStr.length - 1)
  console.log(type)
  let result = null
  switch (type) {
    case 'Array':
      result = source.map((val) => deepClone(val, weakmap))
    case 'Date':
      result = new Date(source)
    case 'RegExp':
      result = new RegExp(source)
    case 'Set':
      const set = new Set()
      Object.entries(set).forEach(([key, val]) => {
        set.add(val)
      })
      result = set
    case 'Map':
      const map = new Map()
      Object.entries(map).forEach(([key, val]) => {
        map.set(key, val)
      })
      result = map
    case 'Object':
      if (weakmap.has(source)) return weakmap.get(source)
      else {
        result = {}
        weakmap.set(source, result) //必须写在递归复制属性前面！
        for (key in source) {
          result[key] = deepClone(source[key], weakmap)
        }
      }
  }
  return result
}
```
## isEqual
```ts
function isEqual(x, y) {
  if (x === y) {
    return true;
  } else if ((typeof x === 'object' && x !== null) && (typeof y === 'object' && y !== null)) {
    const keysX = Object.keys(x);
    const keysY = Object.keys(y);
    if (keysX.length !== keysY.length) {
      return false;
    }
    for (const key of keysX) {
      if(!isEqual(x[key], y[key])) {
        return false;
      }
    }
    return true;
  } else {
    return false;
  }
}
```

## String.prototype.trim 

-   题目: [如何去除字符串首尾空白字符](https://github.com/shfshanyue/Daily-Question/issues/667)

在正则表达式中，`\s` 指匹配一个空白字符，包括空格、制表符、换页符和换行符。等价于`[ \f\n\r\t\v\u00a0\u1680\u180e\u2000-\u200a\u2028\u2029\u202f\u205f\u3000\ufeff]`。

```js
const trim = str => str.trim || str.replace(/^\s+|\s+$/g, '')
```

一般在考察正则时会考察该 API

## debonce 防抖
n 秒内只执行最后一次   清零
场景：input button点击提交请求 resize
```ts
function debonce(fn: Function, time: number){
	let timer
	return (...args)=>{
		clearTimeout(timer)
		timer = setTimeout(()=>{
			fn(...args)
		}, time)
	}
}
```
## throttle 节流
n秒内只能执行一次  加锁 scroll
```ts
function throttle(fn: Function, time: number ){
	let timer
	return (...args)=>{
		if(timer) return 
		else{
			timer = setTimeout(()=>{
				fn(...args)
				timer = null
			}, time)
		}
	}
}
```

## 分割url参数
- 1.使用 `URLSearchParams`
```js
const urlParams = new URLSearchParams(window.location.search);
const paramValue = urlParams.get('paramName');
```
- 2. 
```js
function getQueryParam(url) {
  const params = {};
  const searchParams = url.split('?')[1];
  if (!searchParams) {
    return null;
  }

  searchParams.split('&').forEach((param) => {
    const [key, value] = param.split('=');
    params[key] = decodeURIComponent(value);
  });

  return params
}```
## curry 函数科里化
```ts
function curry(fn, ...args) {
  //fn.length 参数的个数
  return fn.length <= args.length ? fn(...args) : curry.bind(null,fn, ...args)
}
```
## new
```ts
function mu_new(fn,...arg){
    // 首先创建空对象
    const obj = {};
    // 将空对象的原型proto指向构造函数的原型prototype
    Object.setPrototypeOf(obj, fn.prototype)
    // 将this指向新创建的对象，并且执行构造函数
    const result = fn.apply(obj,arg);
    // 执行结果有返回值并且是一个对象，返回执行的结果，否侧返回新创建的对象
    return result instanceof Object ? result : obj;
}
```
## compose
```js

const compose = (...fns) =>
  // 注意 f、g 的位置，如果实现从左到右计算，则置换顺序
  fns.reduce((f, g) => (...args) => f(g(...args)))
```
## 用setTimeout实现setInterval  
setInterval计时可能不准确，原因是setInterval的回调需要等到系统计算资源空闲下来才会执行，并且下一次触发时间是在setInterval回调执行完毕之后才开始计时。如果setInterval内执行的计算过于耗时，或者有其他耗时任务在执行，会导致setInterval计时不准。  

```ts
function mySetInterval(func, interval) {
  setTimeout(function() {
    func();
    mySetInterval(func, interval);
  }, interval);
}
mySetInterval(() => console.log(1), 1000)
```
# React
## Fiber Architecture
https://github.com/acdlite/react-fiber-architecture

## Diff 算法
https://juejin.cn/post/7204285137046782012#heading-9

https://juejin.cn/post/7131741751152214030
## React diff如何从 O(3)优化到 O(n)
React 和 Vue 做的假设是：

-   检测VDOM的变化只发生在同一层
-   检测VDOM的变化依赖于用户指定的key

如果变化发生在不同层或者同样的元素用户指定了不同的key或者不同元素用户指定同样的key， React 和 Vue都不会检测到，就会发生莫名其妙的问题。

但是React 认为， 前端碰到上面的第一种情况概率很小，第二种情况又可以通过提示用户，让用户去解决，因此 这个取舍是值得的。 没有牺牲空间复杂度，却换来了在大多数情况下时间上的巨大提升。 明智的选择！
## 合成事件
1.  跨浏览器兼容性
2.  性能优化：React 通过事件委托来处理事件，这意味着事件只需要绑定到组件树的根节点上，而不是每个单独的元素上。这样可以减少事件处理器的数量，从而提高应用程序的性能。
3.  可以自定义事件：React 合成事件可以被自定义和扩展，你可以创建自己的合成事件类型并在应用程序中使用它们。
4.  避免内存泄漏：当一个元素被从 DOM 中移除时，React 会自动清除与之关联的事件处理器，从而避免内存泄漏问题。

## hooks原理

### 为什么hoos不能写在if里

hooks在FIber节点上是数组加链表， 按顺序维护， 执行一个hook index++，如果某些hook没有执行，顺序会被打乱


## Scheduler
[ Scheduler 为什么使用 MessageChannel 实现](https://juejin.cn/post/6953804914715803678)
## React serverComponent 和 SSR区别
1.  SSR返回的是一个Html，而React Server Component返回的是一个React可解析的结构。
2.  SSR返回的页面会让页面重新刷新，丢失掉之前页面上的状态，比如表单选中之类的；而React Server Component返回的并不会让页面重新刷新而丢失状态。
## NEXT js
### ISR原理
SWR
- [[计算机网络#stale while revalidate]]

# Git

## git reset revert区别

`git reset` 是彻彻底底的回退，该commit之后的所有修改将完全消失，包括提交记录。

**优点**：

- 彻底回退到指定版本，干净清爽；
- 提交时间线清晰，没有冗杂；

**缺点**：

- 记录彻底清除，无法再次恢复

`git revert` revert仅仅是撤销指定commit的修改，并不影响后续的commit，但所撤销的commit被后续的commit修改了同一地方则会产生冲突；

## rebase
Git rebase 是一个常用的 Git 命令，它可以将一个分支的提交记录（包括提交信息、作者、时间等）应用到另一个分支上。除了合并分支，git rebase 还有以下作用：

1.  整理提交历史

使用 git rebase 可以对提交历史进行整理，使得提交记录更加清晰、有条理。通过将多个小的提交合并成一个更大的提交，可以减少提交历史中的无关细节，让提交历史更加易于理解和维护。

2.  修改提交记录

使用 git rebase 还可以修改提交记录。例如，可以修改提交信息、修改提交的顺序、合并多个提交记录等。这对于代码审查、版本控制和代码维护非常有用。

3.  消除冲突

当两个分支上有冲突时，使用 git rebase 可以将这些冲突合并在一起，并在最终的提交历史中显示出来。这可以使冲突的解决更加清晰、简单，并且更容易恢复到之前的状态。

总之，git rebase 是一个非常有用的 Git 命令，它可以使提交历史更加清晰、有条理，让代码维护更加简单、易于理解。

# 性能优化
- 压缩代码
- 缓存
- 预加载 懒加载
- 服务端渲染
- cdn