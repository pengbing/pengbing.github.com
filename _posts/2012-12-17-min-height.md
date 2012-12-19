---
layout: post
title: 跨浏览器的最低高度实现
---

{{ page.title }}
================

<p class="meta">2012-12-18 - bing.peng</p>

实现在最小高度/最低高度，在css2.0中提供了min-height属性，利用min-height属性我们可以轻而易举的实现最小高度，比如：

<pre class="brush: css">
#box { min-height:400px; }
</pre> 

不过很可惜，在IE6中这个属性仅仅作用于td，th，tr对象，所以我们不得为IE6写一些额外的代码。
下面我们为id为box的div写一段跨浏览器的最小高度实现：

<pre class="brush: css">
#box {
    min-height:400px;
    height:auto;
    _height:400px;
    overflow:visible;
}
</pre>

一句句的解释一下：
>min-height:400px; 在非IE6中，所有浏览器都能正确解析，实现最低高度为400px，在IE6中，不能识别min-height属性，将会忽略；
>height:auto; 自度高度，所有浏览器都能正确解析；
>_height:400px; 只有IE6能正确解析，根据就近优先原则，IE6会覆盖上一句height:auto，将div的高度定义为400px，其它浏览器因为不能识别‘_’，将会忽略；
>overflow:visible; 不剪切内容也不添加滚动条，因为IE6的bug，如果内容超过容器的高度，容器会自动增高；
