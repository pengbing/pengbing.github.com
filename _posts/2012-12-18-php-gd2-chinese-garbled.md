---
layout: post
title: PHP GD2 中文乱码处理
---

{{ page.title }}
================

<p class="meta">2012-12-18 - bing.peng</p>

<p>
使用PHP GD2扩展库进行图片操作时，输出中文时往往会引起中文乱码，如：
</p>

{% highlight php %}
<?php
header("content-type:image/png");
$image = imagecreate(300,80);
$bg=imagecolorallocate($image,220,40,120);
$textcolor=imagecolorallocate($image,88,215,120);
$text = '重庆PHP社区';
imagestring($image,30,30,30,$text,$textcolor);
imagepng($image);
imagedestroy($image);
{% endhighlight %}

<p>
引起中文乱码有两种情况：
</p>

##1.编码问题

对所输出的字符串转码

{% highlight php %}
<?php
$text = '重庆PHP社区';
$text = iconv("gbk", "utf-8", $text); // 根据实际编码而定
{% endhighlight %}

##2.字体问题

大多数情况，不能显示中文都是因为GD2库本身没有中文字体引起的，所以要指定字体来显示所输出的字符串。
首先，拷贝所需要的字体到服务器目录，将代码修改成如下：

{% highlight php %}
<?php
header("content-type:image/png");
$image = imagecreate(300,80);
$bg=imagecolorallocate($image,220,40,120);
$textcolor=imagecolorallocate($image,88,215,120);
$font = 'simhei.ttf';
imagettftext($image, 20, 0, 40, 60, $textcolor, $font,'重庆PHP社区');
imagepng($image);
imagedestroy($image);
{% endhighlight %}

