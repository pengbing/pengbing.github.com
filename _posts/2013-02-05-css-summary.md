---
layout: post
title: CSS 总结
---

{{page.title}}
==============
<p class="meta">2013-02-05 - bing.peng</p>

什么是CSS？
----------
CSS 是 Cascading Style Sheet 的缩写。译作「层叠样式表」。是用于(增强)控制网页样式并允许将样式信息与网页内容分离的一种标记性语言。

如何使用CSS？(网页)
--------------------
1.  链接外部样式（推荐）

{% hightlight html %}

<link href="http://www.watchdata.com/msater.css" rel="stylesheet" type="text/css" />

{% endhightlight %}

特点：样式与代码分离

2.  内部定义

{% hightlight html %}

<style type="text/css"> 
body {font: 10px "Arial"} 

h1 {
    font: 15px/17px "Arial"; 
    font-weight: bold; 
    color: maroon
} 

h2 {
    font: 13px/15px "Arial"; 
    font-weight: bold; color: blue
}
 
p {
    font: 10px/12px "Arial"; 
    color: black
} 
</style>

{% endhightlight %}

特点：页面内部使用，方便

3.  内联定义/内嵌定义

{% hightlight html %}

<p style="margin-left: 0.5in; margin-right: 0.5in">
    这一行被增加了左右的外补丁
</p>

{% endhightlight %}

特点：随意、方便


