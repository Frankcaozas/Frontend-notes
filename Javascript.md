# pnpm
npm2 是通过嵌套的方式管理 node_modules 的，会有同样的依赖复制多次的问题。

npm3+ 和 yarn 是通过铺平的扁平化的方式来管理 node_modules，解决了嵌套方式的部分问题，但是引入了幽灵依赖的问题，并且同名的包只会提升一个版本的，其余的版本依然会复制多次。

pnpm 则是用了另一种方式，不再是复制了，而是都从全局 store 硬连接到 node_modules/.pnpm，然后之间通过软链接来组织依赖关系。


首先介绍下 link，也就是软硬连接，这是操作系统提供的机制，硬连接就是同一个文件的不同引用，而软链接是新建一个文件，文件内容指向另一个路径。当然，这俩链接使用起来是差不多的。

如果不复制文件，只在全局仓库保存一份 npm 包的内容，其余的地方都 link 过去呢？

这样不会有复制多次的磁盘空间浪费，而且也不会有路径过长的问题。因为路径过长的限制本质上是不能有太深的目录层级，现在都是各个位置的目录的 link，并不是同一个目录，所以也不会有长度限制。

没错，pnpm 就是通过这种思路来实现的。

再把 node_modules 删掉，然后用 pnpm 重新装一遍，执行 pnpm install。

你会发现它打印了这样一句话：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1b2d51d9a17743a4bafc42f1bbfd310c~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

包是从全局 store 硬连接到虚拟 store 的，这里的虚拟 store 就是 node_modules/.pnpm。

我们打开 node_modules 看一下：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9b4dc807ca6e4ae7a955c8dd6385cb46~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

确实不是扁平化的了，依赖了 express，那 node_modules 下就只有 express，没有幽灵依赖。

展开 .pnpm 看一下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/65a69589bd534fdd97bdbeb6e3e1024c~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

所有的依赖都在这里铺平了，都是从全局 store 硬连接过来的，然后包和包之间的依赖关系是通过软链接组织的。

比如 .pnpm 下的 expresss，这些都是软链接，

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c50d8dc8a2a4466ba9e5eccd5c15614e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

也就是说，所有的依赖都是从全局 store 硬连接到了 node_modules/.pnpm 下，然后之间通过软链接来相互依赖。

  ![[Pasted image 20230410114701.png]]

# DOM
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
## Promise.all ⭐️⭐️⭐️⭐️⭐️

-   代码: [Promise.all](https://codepen.io/shanyue/pen/JjWEqBL?editors=0012)
-   题目: [Promise.all](https://github.com/shfshanyue/Daily-Question/issues/500)

乍看简单，实现时方觉不易。

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

## isArray
```js
const isArray = Array.isArray || list => ({}).toString.call(list) === '[object Array]'
```

## sleep/delay ⭐⭐⭐⭐⭐

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

## Array.prototype.flat ⭐️⭐️⭐️⭐️⭐️

-   题目: [【Q443】实现一个数组扁平化的函数 flatten](https://github.com/shfshanyue/Daily-Question/issues/451)
-   代码: [【Q443】实现一个数组扁平化的函数 flatten](https://codepen.io/shanyue/pen/xxdjQXG?editors=0012)

`reduce` 与 `concat` 简直是绝配

```js
function flatten (list, depth = 1) {
  if (depth === 0) return list
  return list.reduce((a, b) => a.concat(Array.isArray(b) ? flatten(b, depth - 1) : b), [])
}
```

注意，flatten 拥有第二个参数 depth

## Array.prototype.reduce ⭐️⭐️⭐️

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
## String.prototype.trim ⭐️⭐️⭐️

-   题目: [如何去除字符串首尾空白字符](https://github.com/shfshanyue/Daily-Question/issues/667)

在正则表达式中，`\s` 指匹配一个空白字符，包括空格、制表符、换页符和换行符。等价于`[ \f\n\r\t\v\u00a0\u1680\u180e\u2000-\u200a\u2028\u2029\u202f\u205f\u3000\ufeff]`。

```js
const trim = str => str.trim || str.replace(/^\s+|\s+$/g, '')
```

一般在考察正则时会考察该 API

## debonce 防抖
n 秒内只执行最后一次   清零
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
n秒内只能执行一次  加锁
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

## 什么是 Javascript 的事件流？有哪些事件流模型？
JavaScript 的事件流是指浏览器中所有事件的传递和处理过程。事件流可以分为三个阶段：事件捕获、目标阶段和事件冒泡。

1.  **事件捕获阶段**：事件从最外层的文档节点一直往下传递，直到事件到达事件的目标元素。在这个过程中，事件会经过父节点和祖先节点，直到到达目标节点。如果在这个过程中有事件处理程序，则事件将被调用。
2.  **目标阶段**：事件到达了目标元素后，将在目标元素上调用事件处理程序。如果有多个事件处理程序绑定到目标元素上，将按照它们的顺序执行。
3.  **事件冒泡阶段**：事件在目标元素上处理后，会从目标元素开始，向上传递回文档节点。在这个过程中，事件也会遇到任何绑定的事件处理程序。

目前有两种事件流模型：

1.  **W3C 标准事件模型（DOM2级事件模型）**：事件流由三个阶段组成：捕获阶段、目标阶段和冒泡阶段。这种事件模型中，事件处理程序的调用顺序与它们注册的顺序相同。
2.  **Microsoft 事件模型（IE 事件模型）**：事件流由两个阶段组成：目标阶段和冒泡阶段。在这个事件模型中，事件处理程序的调用顺序与它们注册的相反。

总体来说，W3C 标准事件模型是当前主流的事件流模型。对于事件流，我们需要了解事件捕获、目标阶段和事件冒泡，以便于正确地使用事件处理程序。

## pnpm
https://juejin.cn/post/7036319707590295565
https://zhuanlan.zhihu.com/p/419399115