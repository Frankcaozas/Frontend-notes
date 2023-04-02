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