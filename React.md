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


## Scheduler
[ Scheduler 为什么使用 MessageChannel 实现](https://juejin.cn/post/6953804914715803678)
## React serverComponent 和 SSR区别
1.  SSR返回的是一个Html，而React Server Component返回的是一个React可解析的结构。
2.  SSR返回的页面会让页面重新刷新，丢失掉之前页面上的状态，比如表单选中之类的；而React Server Component返回的并不会让页面重新刷新而丢失状态。
# NEXT js
## ISR原理
SWR
- [[计算机网络#stale while revalidate]]
