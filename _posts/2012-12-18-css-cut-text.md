---
layout: post
title: 用CSS截断文本行并添加省略号
---

{{ page.title }}
================

<p class="meta">2012-12-18 - bing.peng</p>

<p>
写网页的时候因为空间限制，我们经常需要截断文字，比如我们有一个新闻列表，这个新闻列表只有200像素宽，只能显示15个字符，而我们的新闻标题可能不止15个字符，我们就要截断超出长度的字符，然后再为截断的标题添加省略号，让用户知道这是一个被截断的标题，那么你的代码看起来应该像下面这样：
</p>

{% highlight html %}
<div id="news-box">
    <ul>
        <li><a href="#">重庆PHP走进校园巡回演讲...</a></li>
        <li><a href="#">重庆PHP社区重大决定</a></li>
        <li><a href="#">利用CI钩子实现权限认证</a></li>
        <li><a href="#">Session过期时登陆页跳出...</a></li>
    </ul>
</div>
{% endhighlight %}

<p>
为了实现这种效果，很多人都会用PHP脚本来完成这件事情：
</p>

{% highlight %}
<div id="news-box">
    <ul>
		<?php foreach($artilce_list as $article){ ?>
        <li>
            <a href="#">
                <?php echo mb_strsub($article['content'], 0, 8)?>
            </a>
        </li>
        <?php } ?>
    </ul>
</div>
{% endhighlight %}

<p>
这并不是一种妥当的做法，因为在服务器端处理会浪费服务器端的性能，写起来也麻烦，感觉不伦不类的。我们可以用CSS文字截取实现，更简单一些：
</p>

{% highlight css %}
#news-box ul li a {
    display:block; 
    width:200px;       /* 必须设容器宽度 */
    white-space:nowrap;      /* 强制文本在一行内显示，到达容器边界也不能折行 */
    text-overflow:ellipsis;  /* 对文本溢出加... */
    overflow:hidden;       /* 对超出容器边界的内容进行隐藏 */ 
}
{% endhighlight %}

