
## 统计当前页面出现次数最多的标签

-   题目：[如何找到当前页面出现的所有标签](https://q.shanyue.tech/fe/dom/573.html)
-   题目：[如何找到当前页面出现次数最多的 HTML 标签](https://q.shanyue.tech/fe/dom/418.html)

这是一道前端基础与编程功底具备的面试题：

-   如果你前端基础强会了解 `document.querySelector(*)` 能够列出页面内所有标签
-   如果你编程能力强能够用**递归**/**正则**快速实现同等的效果

有三种 API 可以列出页面所有标签：

1.  `document.querySelector('*')`，标准规范实现
2.  `$$('*')`，devtools 实现
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

## Cookie 属性

-   题目：[浏览器中 cookie 有哪些字段](https://q.shanyue.tech/fe/dom/560.html)

Cookie 有以下属性

-   Domain
-   Path
-   Expire/MaxAge
-   HttpOnly: 是否允许被 JavaScript 操作
-   Secure: 只能在 HTTPS 连接中配置
-   SameSite

## Cookie maxAge

-   题目：[当 cookie 没有设置 maxage 时，cookie 会存在多久](https://q.shanyue.tech/fe/dom/313.html)

如果没有 maxAge，则 cookie 的有效时间为会话时间。

## Cookie SameSite

-   题目：[SameSite Cookie 有哪些值，是如何预防 CSRF 攻击的？](https://q.shanyue.tech/fe/dom/569.html)

> 见文档 [SameSite Cookie - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite)

> 见文章 [Cookie 的 SameSite 属性](http://www.ruanyifeng.com/blog/2019/09/cookie-samesite.html)

-   None: 任何情况下都会向第三方网站请求发送 Cookie
-   Lax: 只有导航到第三方网站的 Get 链接会发送 Cookie，跨域的图片、iframe、form表单都不会发送 Cookie
-   Strict: 任何情况下都不会向第三方网站请求发送Cookie

目前，主流浏览器 Same-Site 的默认值为 `Lax`，而在以前是 `None`，将会预防大部分 CSRF 攻击，如果需要手动指定 `Same-Site` 为 `None`，需要指定 Cookie 属性 `Secure`，即在 https 下发送

## Cookie 增删改查

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

同时，也可以使用最新关于 cookie 操作的 API: [CookieStore API](https://developer.mozilla.org/en-US/docs/Web/API/CookieStore) 其中的 `cookieStore.delete(name)` 删除某个 cookie

## addEventListener()

-   题目：[浏览器中监听事件函数 addEventListener 第三个参数有那些值](https://q.shanyue.tech/fe/dom/689.html)

详见 MDN https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener