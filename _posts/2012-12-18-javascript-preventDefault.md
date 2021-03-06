---
layout: post
title: 现代Javascript之阻止浏览器默认行为
---

{{ page.title }}
================

<p class="meta">2012-12-18 - bing.peng</p>

直接代码：

{% highlight html %}

<a id="baidu" href="http://www.baidu.com">百度</a>

{% endhighlight %}

<p>
上面是一个超级链接，点击链接，浏览器会发起一个HTTP请求，把我们带到百度去。有没有办法改变浏览器的行为，当点击链接后不跳到百度去呢？跳到Google去？或是什么都不做（卸载事件）。
</p>

<p>
分析一下，点击链接就会触发javascript的点击事件，而我们可以在这个函数里改变浏览器的默认行为，代码：
</p>

{% highlight javascript %}

var oA = document.getElementById("baidu");
oA.onclick = function() {
    this.href = "http://www.google.com";
}

{% endhighlight %}

<p>
怎么样？我们实现了点击百度链接跳转到Google首页了吧？但是怎么让浏览器什么都不做呢？
W3C规范中，事件处理函数都会携带一个事件对象作为参数，唯一的一个参数，事件对象中携带了卸载浏览器默认事件的方法，代码：
</p>

{% highlight javascript %}

var oA = document.getElementById("baidu");
oA.onclick = function(e) { // 参数e为W3C规范中的事件对象(event)
    e.preventDefault(); 
}

{% endhighlight %}

<p>
启动你的Firefox看看，浏览器是不是什么都不做了？再用IE看看，浏览器还是跳到百度去了？注意，我刚才说的是W3C规范，地球人都知道IE不遵守W3C规范，IE定义了自己的事件对象window.event，IE到事件对象挂到了全局对象window下面，你可以直接的访问它，所以在IE中点击链接后，你希望浏览器什么也不做，代码如下：
</p>

{% highlight javascript %}

var oA = document.getElementById("baidu");
oA.onclick = function() { 
    window.event.returnValue = false;
    return false;
}

{% endhighlight %}

<p>写一段兼容IE、Firefox、Chrome的阻止浏览器默认行为的代码：</p>

{% highlight javascript %}

var oA = document.getElementById("baidu");
oA.onclick = function(e) { 
    if( e && e.preventDefault ) {
        e.preventDefault();
    }
    else {
        window.event.returnValue = false;
    }
    return false;
}

{% endhighlight %}

