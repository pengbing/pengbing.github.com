---
layout: post
title: PHP header 函数用法演示
---

{{ page.title }}
================

<p class="meta">2012-12-16 - bing.peng</p>

PHP的header()函数，用于发送一个原始的HTTP报文头。因为自己老是记不住，所以给自己备个份，下面演示一些常见的header()函数的用法：

{% highlight php %}
<?php
// 发送一个200 正常响应
header("HTTP/1.1 200 OK");

// 发送一个404 找不到资源响应
header('HTTP/1.1 404 Not Found');

// 发送一个301 永久重定向
header('HTTP/1.1 301 Moved Permanently');

// 发送一个503 网站暂时不能访问
header('HTTP/1.1 503 Service Temporarily Unavailable');

// 网页重定向
header('Location: http://www.php-chongqing.com/');

// 设置网页3秒后重定向
header('Refresh: 3; url=http://www.php-chongqing.com/');
echo '网页将在3秒后跳转到http://www.php-chongqing.com';

// 设置网页编码
header('Content-Type: text/html; charset=utf-8');

// 设置网页输出一个图片流
header('Content-Type: image/jpeg'); 

// 设置网页输出一个pdf文档
header('Content-Type: application/pdf'); 

// 设置网页输出一个zip文档
header('Content-Type: application/zip'); 

{% endhighlight %}
