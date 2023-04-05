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
Function.prototype.mu_bind = function (context, ...args) {
    if (!context || context === null) {
      context = window;
    }
    // 创造唯一的key值  作为我们构造的context内部方法名
    let fn = Symbol();
    context[fn] = this;
    let _this = this;
    //  bind情况要复杂一点
    const result = function (...innerArgs) {
      // 第一种情况 :若是将 bind 绑定之后的函数当作构造函数，通过 new 操作符使用，则不绑定传入的 this，而是将 this 指向实例化出来的对象
      // 此时由于new操作符作用  this指向result实例对象  而result又继承自传入的_this 根据原型链知识可得出以下结论
      // this.__proto__ === result.prototype   //this instanceof result =>true
      // this.__proto__.__proto__ === result.prototype.__proto__ === _this.prototype; //this instanceof _this =>true
      if (this instanceof _this === true) {
        // 此时this指向指向result的实例  这时候不需要改变this指向
        this[fn] = _this;
        this[fn](...[...args, ...innerArgs]); //这里使用es6的方法让bind支持参数合并
        delete this[fn];
      } else {
        // 如果只是作为普通函数调用  那就很简单了 直接改变this指向为传入的context
        context[fn](...[...args, ...innerArgs]);
        delete context[fn];
      }
    };
    // 如果绑定的是构造函数 那么需要继承构造函数原型属性和方法
    // 实现继承的方式: 使用Object.create
    result.prototype = Object.create(this.prototype);
    return result;
  };
  function Person(name, age) {
    console.log(name); //'我是参数传进来的name'
    console.log(age); //'我是参数传进来的age'
    console.log(this); //构造函数this指向实例对象
  }
  // 构造函数原型的方法
  Person.prototype.say = function () {
    console.log(123);
  };
​
  // 普通函数
  function normalFun(name, age) {
    console.log(name); //'我是参数传进来的name'
    console.log(age); //'我是参数传进来的age'
    console.log(this); //普通函数this指向绑定bind的第一个参数 也就是例子中的obj
    console.log(this.objName); //'我是obj传进来的name'
    console.log(this.objAge); //'我是obj传进来的age'
  }
​
  let obj = {
    objName: '我是obj传进来的name',
    objAge: '我是obj传进来的age',
  };
​
  // 先测试作为构造函数调用
  //   let bindFun = Person.mu_bind(obj, '我是参数传进来的name');
  //   let a = new bindFun('我是参数传进来的age');
  //   a.say(); //123
​
  //   再测试作为普通函数调用a;
  let bindFun = normalFun.mu_bind(obj, '我是参数传进来的name');
  bindFun('我是参数传进来的age');
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

