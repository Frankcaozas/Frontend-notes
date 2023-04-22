# 网络
## OSI七层模型
OSI (Open Systems Interconnection) 模型是一种网络通信的参考模型，定义了计算机网络中不同层次之间的协议和交互关系。OSI 模型分为七层，每层都有不同的功能，包括：

1.  物理层（Physical Layer）：负责传输比特流，将数据从一个节点传输到另一个节点，物理层规定了电器、光学和机械接口的特性。
2.  数据链路层（Data Link Layer）：负责在物理介质上建立逻辑连接，提供有可靠性的数据传输，数据链路层还可以检测和纠正数据传输中的错误。
3.  网络层（Network Layer）：负责在网络中传输数据包，提供路由和寻址功能，网络层还可以通过控制拥塞和流量来控制网络流量。
4.  传输层（Transport Layer）：负责对端到端的数据传输进行控制，传输层提供可靠或不可靠的数据传输，还可以提供流量控制和拥塞控制等功能。
5.  会话层（Session Layer）：负责建立、管理和终止会话，会话层还可以处理同步和检查点等问题。
6.  表示层（Presentation Layer）：负责数据格式的转换和编码，以便让应用程序可以识别和解析数据。
7.  应用层（Application Layer）：为应用程序提供服务，应用层定义了用户和应用程序之间的接口。

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
## HTTP
### 键入网址到网页显示，期间发生了什么

### HTTP 常见的状态码有哪些？

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

-   「**400 Bad Request**」表示客户端请求的报文有错误，但只是个笼统的错误。
    
-   「**403 Forbidden**」表示服务器禁止访问资源，并不是客户端的请求出错。
    
-   「**404 Not Found**」表示请求的资源在服务器上不存在或未找到，所以无法提供给客户端。
    

`5xx` 类状态码表示客户端请求报文正确，但是**服务器处理时内部发生了错误**，属于服务器端的错误码。

-   「**500 Internal Server Error**」与 400 类型，是个笼统通用的错误码，服务器发生了什么错误，我们并不知道。
    
-   「**501 Not Implemented**」表示客户端请求的功能还不支持，类似“即将开业，敬请期待”的意思。
    
-   「**502 Bad Gateway**」通常是服务器作为网关或代理时返回的错误码，表示服务器自身工作正常，访问后端服务器发生了错误。
    
-   「**503 Service Unavailable**」表示服务器当前很忙，暂时无法响应客户端，类似“网络服务正忙，请稍后重试”的意思。
### 跨域解决方法

-   题目：[什么是跨域，如何解决](https://q.shanyue.tech/fe/dom/216.html)

**协议**，**域名**，**端口**，三者有一不一样，就是跨域

案例一：`www.baidu.com` 与 `zhidao.baidu.com` 是跨域

目前有两种最常见的解决方案：

1.  CORS，在服务器端设置几个响应头，如 `Access-Control-Allow-Origin: *`
2.  Reverse Proxy，在 nginx/traefik/haproxy 等反向代理服务器中设置为同一域名
3.  JSONP，详解见 [JSONP 的原理是什么，如何实现](https://github.com/shfshanyue/Daily-Question/issues/447)
#### JSONP
https://q.shanyue.tech/fe/dom/447.html#%E4%B8%80%E4%B8%AA-jsonp-%E8%AF%B7%E6%B1%82

### get post

get参数长度限制不是因为http协议的限制，不同的浏览器有不同的限制


### 强制缓存
[强制缓存](https://xiaolincoding.com/network/2_http/http_interview.html#%E4%BB%80%E4%B9%88%E6%98%AF%E5%BC%BA%E5%88%B6%E7%BC%93%E5%AD%98)
#### stale while revalidate
（SWR）是一种用于Web缓存的机制，通常用于优化网络性能和用户体验。
SWR机制的核心思想是允许从缓存中返回“陈旧”（即过期）的数据，同时在后台更新最新数据，以便在下一次请求时返回新数据。这可以提高应用程序的响应速度和性能，并减少用户等待时间。

time < max-age    直接用缓存
max-age< time < max-age + stale-while-revalidate  在后台请求更新缓存
time > max-age + stale-while-revalidate 会立即发出一个请求，并在数据返回后将其存储在缓存中，以备下一次使用。

##### 优点
它可以有效地降低网络请求的频率，提高应用程序的性能和响应速度，并减少数据传输的成本。它也可以帮助开发人员更好地管理应用程序中的数据，从而使代码更加清晰简洁。

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

### HTTP/1.1、HTTP/2、HTTP/3 演变

#### HTTP/1.1 相比 HTTP/1.0 提高了什么性能？

HTTP/1.1 相比 HTTP/1.0 性能上的改进：

-   使用长连接的方式改善了 HTTP/1.0 短连接造成的性能开销。
-   支持管道（pipeline）网络传输，只要第一个请求发出去了，不必等其回来，就可以发第二个请求出去，可以减少整体的响应时间。

但 HTTP/1.1 还是有性能瓶颈：

-   请求 / 响应头部（Header）未经压缩就发送，首部信息越多延迟越大。只能压缩 `Body` 的部分；         
-   发送冗长的首部。每次互相发送相同的首部造成的浪费较多；
-   服务器是按请求的顺序响应的，如果服务器响应慢，会招致客户端一直请求不到数据，也就是队头阻塞；
-   没有请求优先级控制；
-   请求只能从客户端开始，服务器只能被动响应。

#### HTTP/2 做了什么优化？

HTTP/2 协议是基于 HTTPS 的，所以 HTTP/2 的安全性也是有保障的。

![HTT/1 ~ HTTP/2](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/25-HTTP2.png)

那 HTTP/2 相比 HTTP/1.1 性能上的改进：

-   头部压缩
	-  动态表编码
	- 静态表编码
-   二进制格式
-   并发传输
	- **多个 Stream 复用一条 TCP 连接，达到并发的效果**
-   服务器主动推送资源
#### http3
-   HTTP/1.1 中的管道（ pipeline）虽然解决了请求的队头阻塞，但是**没有解决响应的队头阻塞**，因为服务端需要按顺序响应收到的请求，如果服务端处理某个请求消耗的时间比较长，那么只能等响应完这个请求后， 才能处理下一个请求，这属于 HTTP 层队头阻塞。
-   HTTP/2 虽然通过多个请求复用一个 TCP 连接解决了 HTTP 的队头阻塞 ，但是**一旦发生丢包，就会阻塞住所有的 HTTP 请求**，这属于 TCP 层队头阻塞。

HTTP/2 队头阻塞的问题是因为 TCP，所以 **HTTP/3 把 HTTP 下层的 TCP 协议改成了 UDP！**

![HTTP/1 ~ HTTP/3](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/27-HTTP3.png)

UDP 发送是不管顺序，也不管丢包的，所以不会出现像 HTTP/2 队头阻塞的问题。大家都知道 UDP 是不可靠传输的，但基于 UDP 的 **QUIC 协议** 可以实现类似 TCP 的可靠性传输。

QUIC 有以下 3 个特点。
-   无队头阻塞
-   更快的连接建立
-   连接迁移
### Https

1. 客户端向服务器发送一个 HTTPS 请求。
2. 服务器向客户端发送 SSL/TLS 证书。证书包含服务器的公钥以及其它信息，如证书颁发机构、有效期等。
3. 客户端验证证书的有效性，如果证书有效，则生成一个随机数作为**对称密钥**，使用服务器的**公钥**加密该**对称密钥**，并将加密后的密钥发送给服务器。
4. 服务器使用自己的私钥解密客户端发送的对称密钥，并使用该密钥加密服务器响应的数据，然后发送加密后的数据给客户端。
5. 客户端使用对称密钥解密服务器响应的数据，完成 HTTPS 连接。

在 HTTPS 连接中，SSL/TLS 协议扮演着重要的角色，它使用**非对称**加密和**对称**加密结合的方式，保证了数据传输的安全性和完整性。此外，证书机构的身份验证也是保障 HTTPS 连接的安全性的一个重要环节。

## CDN
CDN（Content Delivery Network，内容分发网络）是指一种分布式的网络架构，通过在网络中部署大量的节点服务器，将用户所请求的内容分发到最接近用户的节点服务器上进行缓存，以提高用户访问这些内容的速度和质量。

CDN的主要功能是加速互联网内容的传输，减少网络拥堵，提高用户的访问速度和体验。具体来说，CDN可以将网站的静态资源（如图片、视频、JS、CSS等）缓存到各个节点服务器上，并通过负载均衡技术将用户请求分发到最优的节点服务器上进行响应，从而提高了用户的访问速度。

此外，CDN还可以提供安全加固、防止DDoS攻击等功能，保障网站的稳定和安全性。

## 网络安全
### xss
XSS（Cross-Site Scripting）跨站脚本攻击是一种常见的Web安全漏洞，攻击者通过在Web应用程序中注入恶意的客户端脚本，使得攻击者能够在受害者的浏览器中执行恶意脚本，从而窃取用户的敏感信息，如cookies、会话令牌、密码等，甚至可以让攻击者获得对受害者的控制权。攻击者可以通过各种手段，如在表单输入框中输入恶意代码、在URL参数中注入恶意代码、在评论、留言板等地方插入恶意脚本等方式进行攻击。因此，开发者需要注意输入验证、输出转义等安全措施来防止XSS攻击。

### csrf
CSRF（Cross-Site Request Forgery）跨站请求伪造是一种Web应用程序安全漏洞，攻击者利用用户已经登录的身份，在用户不知情的情况下发送恶意请求，从而执行某些敏感操作或者窃取用户的敏感信息。攻击者通常会通过伪装成用户信任的网站或者诱导用户点击恶意链接的方式进行攻击。比如，攻击者可以在一个看似正常的页面上插入一个表单，然后通过JavaScript代码自动提交这个表单，完成攻击。为了防范CSRF攻击，Web应用程序可以采用一些预防措施，
#### 解决方式
如CSRF Token、Referer检查、验证码等方式。

CSRF（Cross-site request forgery）攻击通常可以通过引入Referer检查来进行防御。在HTTP请求头中，Referer是指从哪个页面链接跳转过来的信息。服务器可以通过检查请求头中的Referer信息来确定请求的来源是否合法。

要使用Referer来解决CSRF攻击，可以在服务器端检查请求的Referer是否与当前站点的域名匹配。如果不匹配，则拒绝请求。

以下是一些具体的步骤来使用Referer来防御CSRF攻击：
1.  服务器在返回表单时，在表单中添加一个隐藏的字段，例如_csrf_token，并将其设置为一个随机生成的字符串。
2.  当用户提交表单时，将_csrf_token一起提交到服务器。
3.  服务器接收到请求后，验证请求头中的Referer是否与当前站点的域名匹配。
4.  如果Referer匹配，则验证_csrf_token是否与表单中提交的_csrf_token相等。
5.  如果_csrf_token相等，则请求合法，执行相应的操作；否则，拒绝请求。
通过以上的步骤，可以有效地防止CSRF攻击。但需要注意的是，由于Referer信息可以被修改或删除，因此Referer检查并不能完全保证防御CSRF攻击的安全性，还需要结合其他安全措施一起使用。

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
-   ##absolute/inset##

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

  - `absolute``
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

## 



# JavaScript
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



## Window 大小和滚动
### 1.窗口的 width/height
 	document.documentElement 的 clientWidth/clientHeight：
**不是 window.innerWidth/innerHeight**
如果这里存在一个滚动条，并且滚动条占用了一些空间，那么 clientWidth/clientHeight 会提供没有滚         动条（减去它）的 width/height。而innerWidth包括滚动条
### 2.文档的 width/height
```javascript
let scrollHeight = Math.max(
  document.body.scrollHeight, document.documentElement.scrollHeight,
  document.body.offsetHeight, document.documentElement.offsetHeight,
  document.body.clientHeight, document.documentElement.clientHeight
);

alert('Full document height, with scrolled out part: ' + scrollHeight);
```
### 3.当前滚动位置
scrollLeft
scrollTop 
或者
window.pageXOffset
window.pageYOffset 
### 4.滚动：scrollTo，scrollBy，scrollIntoView
scrollBy(x,y)   将页面滚动至 **相对于当前位置的 (x, y) 位置**。例如，scrollBy(0,10) 会将页面向下滚动 10px。

scrollTo(pageX,pageY)  将页面滚动至 **绝对坐标**，使得可见部分的左上角具有相对于文档左上角的坐标 (pageX, pageY)。就像设置了 scrollLeft/scrollTop 一样。

 elem.scrollIntoView(top) 的调用将滚动页面以使 elem 可见。它有一个参数：

- 如果 top=true（默认值），页面滚动，使 elem 出现在窗口顶部。元素的上边缘将与窗口顶部对齐。
- 如果 top=false，页面滚动，使 elem 出现在窗口底部。元素的底部边缘将与窗口底部对齐。
### 5.禁止滚动
document.body.style.overflow = "hidden"
document.body.style.overflow = ""


## 事件

### 1.拖拽事件(DragEvent)

拖拽目标的事件
1.dragstart
2.dragend

拖拽对象的事件
1.dragover  拖到上方
2.dragleave 拖离
3.drop  放下

*重要属性*

DragEvent.DataTransfer

```ts
Used to hold the data that is being dragged during a drag and drop operation. It may hold one or more data items, each of one or more data types. For more information about drag and drop, see HTML Drag and Drop API
存储拖拽时需要的数据

setData(format: string, data: string): void;
getData(format: string): string;
clearData(format?: string): void;
```

DataTransfer.types
```ts
//定义:
readonly types: ReadonlyArray<string>;

存储dataTransfer中保存的数据的类型，有文件时有'Files'

```

DataTransfer.effectAllowed
```ts
(property) DataTransfer.effectAllowed: "link" | "copy" | "none" | "copyLink" | "copyMove" | "linkMove" | "move" | "all" | "uninitialized"
Returns the kinds of operations that are to be allowed.

Can be set (during the dragstart event), to change the allowed operations.

The possible values are "none", "copy", "copyLink", "copyMove", "link", "linkMove", "move", "all", and "uninitialized"
```
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
## Promise
### [实现promise](https://zhuanlan.zhihu.com/p/58428287)
```js
class Prom {
  static resolve(value) {
    if (value && value.then) {
      return value
    }
    return new Prom((resolve) => resolve(value))
  }

  constructor(fn) {
    this.value = undefined
    this.reason = undefined
    this.status = 'PENDING'

    // 维护一个 resolve/pending 的函数队列
    this.resolveFns = []
    this.rejectFns = []

    const resolve = (value) => {
      // 注意此处的 setTimeout
      setTimeout(() => {
        this.status = 'RESOLVED'
        this.value = value
        this.resolveFns.forEach(({ fn, resolve: res, reject: rej })     =>   res(fn(value))
        )
      })
    }

    const reject = (e) => {
      setTimeout(() => {
        this.status = 'REJECTED'
        this.reason = e
        this.rejectFns.forEach(({ fn, resolve: res, reject: rej }) =>
          rej(fn(e))
        )
      })
    }

    fn(resolve, reject)
  }

  then(fn) {
    if (this.status === 'RESOLVED') {
      const result = fn(this.value)
      // 需要返回一个 Promise
      // 如果状态为 resolved，直接执行
      return Prom.resolve(result)
    }
    if (this.status === 'PENDING') {
      // 也是返回一个 Promise
      return new Prom((resolve, reject) => {
        // 推进队列中，resolved 后统一执行
        this.resolveFns.push({ fn, resolve, reject })
      })
    }
  }

  catch(fn) {
    if (this.status === 'REJECTED') {
      const result = fn(this.value)
      return Prom.resolve(result)
    }
    if (this.status === 'PENDING') {
      return new Prom((resolve, reject) => {
        this.rejectFns.push({ fn, resolve, reject })
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

