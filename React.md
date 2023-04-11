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


## hooks原理
[[|stale while revalidate]]
# NEXT js
## ISR原理
SWR
- [[计算机网络#stale while revalidate]]
