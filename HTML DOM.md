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
-   通过 `popstate event` 监听路由变化，但无法监听到 `history.pushState() history.replaceState()` 时的路由变化

hash

-   通过 `location.hash` 跳转路由
-   通过 `hashchange event` 监听路由变化