## 跨域解决方法

-   题目：[什么是跨域，如何解决](https://q.shanyue.tech/fe/dom/216.html)

**协议**，**域名**，**端口**，三者有一不一样，就是跨域

案例一：`www.baidu.com` 与 `zhidao.baidu.com` 是跨域

目前有两种最常见的解决方案：

1.  CORS，在服务器端设置几个响应头，如 `Access-Control-Allow-Origin: *`
2.  Reverse Proxy，在 nginx/traefik/haproxy 等反向代理服务器中设置为同一域名
3.  JSONP，详解见 [JSONP 的原理是什么，如何实现](https://github.com/shfshanyue/Daily-Question/issues/447)
### JSONP
https://q.shanyue.tech/fe/dom/447.html#%E4%B8%80%E4%B8%AA-jsonp-%E8%AF%B7%E6%B1%82