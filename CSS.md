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