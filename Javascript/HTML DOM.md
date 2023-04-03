
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

最新的实现方案是使用 [IntersectionObserver API](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API)。

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

## sessionStorage 与 localStorage 有何区别



localStorage 生命周期是永久除非自主清除 sessionStorage 生命周期为当前窗口或标签页，关闭窗口或标签页则会清除数据

他们均只能存储字符串类型的对象

不同浏览器无法共享 localStorage 或 sessionStorage 中的信息。相同浏览器的不同页面间可以共享相同的 localStorage（页面属于相同域名和端口），但是不同页面或标签页间无法共享 sessionStorage 的信息。这里需要注意的是，页面及标 签页仅指顶级窗口，如果一个标签页包含多个 iframe 标签且他们属于同源页面，那么他们之间是可以共享 sessionStorage 的。