---
layout: post
title: 自动URL，看上去很美
---

{{ page.title }}
================

<p class="meta">2012-12-20 - bing.peng</p>

所谓自动URL指的是像 *CodeIgniter* 之类的框架获取用户访问的URL段后，根据约定规则，自动找到你要调用的Controller类，要执行的方法，以及要传递的参数。

例如，我们在CI的controllers文件下新建Article.php，代码如下：

{% highlight php %}

class Article extends CI_Controller {
     public function __construct() {
         parent::__construct();
     }
 
     public function show($id) {
         echo 'article id:' . $id;
     }
} 

{% endhighlight %}

我们马上可以在浏览器中输入：
> xxoo.com/index.php/article/show/2
访问到Article类中的show()方法，并到2当作参数传递给了show()方法，真的很方便，简单、直观，真的是这样吗？我们来看看实际情况。

在实际项目中，我们的代码通常都分为前台、后台两部份，为了拥有更好的可读性，我们在controllers下建两个文件夹，分别为controllers/front（放前台代码）和controllers/manage(放后台代码)，我们再把刚才的Article.php文件放到front文件夹中去，那么此时的完整路径为：
> controllers/front/Article.php
我们再用浏览器来访问Article类中的show()方法，你发现你要输入‘xxoo.com/index.php/front/article/show/2’，没有问题，我们还是可以访问到。
但是我们发现这个URL不但有点丑，还完全暴露了我们的文件路径，没关系，CI提供了路由功能，只需要在route.php中增加一行配置就可以：

{% highlight php %}
$route['article/(:num)'] = "front/article/show/$1";
{% endhighlight %}

现在我们得到了我们想要的URL，当你写下一个功能时，你会发现你又需要路由，你所写的每一个功能都需要路由，对么？

新的问题又来了，由于我们的项目适当的有一点点大，我们的前台有一个‘用户中心’，我们为了提高代码的可读性，再在controllers/front/建一个新文件夹userCenter，再增加一个User.php文件，添加如下代码：

{% highlight php %}

<?php
class User extends CI_Controller {
     public function __construct() {
         parent::__construct();
     }
 
     public function add() {
         echo 'user add';
     }
} 

{% endhighlight %}

我们在浏览器输入：
> xxoo.com/index.php/front/userCenter/user/add

先不说这个url层级太深，奇丑无比，你会发现你访问不到User类的add()方法，不好意思，CI不能支持两级或两级以上的目录。

所有和CI类似的，号称0配置的自动URL框架，都是看上去很美，完全不实用，如果你不路由，在实际项目中，你的URL会奇丑无比，路由吧，又要多写一大堆代码。

我们在实现xxoophp的时候，也一直在纠结要不要实现自动URL，实事上我们最初的时候实现了自动URL，并且可以支持多级目录，后来考虑再三，还是把自动匹配URL从xxoophp中移除，采用全手动配置，我们借鉴了 *python djiango* 框架的实现，反正都要路由，为何不一上来就路由？同样是访问到front/Article.php中的show()方法，xxoophp中的写法，在conf/urls.conf.php中增加一行配置，好吧，你可以理解为CI的路由：

{% highlight php %}

<?php
$urls['article/(\d+)'] = array('c'=>'front/Article','f'=>'show');

{% endhighlight %}

我们用原生的正则表达式的匹配URL，我们认为任何PHPer都应该已经很熟悉正则表达式，我们还认为用正则表达式你可以控制得更多，比如你想限制文章id只能是1-100的整数：

{% highlight php %}

<?php
$urls['article/([1-100])'] = array('c'=>'front/Article','f'=>'show');

{% endhighlight %}

没错，正则中的分组会被当作参数传递给show()方法，我们清楚指明了访问该URL要调用的类文件，执行的类方法，任何人拿到项目代码，只需要查看urls.conf.php就可以对整个项目了如指掌。

你也许会说，我的网站有很多很多很多URL配置，成千上万，不好意思，如果你的网站真的有那么大，估计我们这个小框架已经不能满足你的要求了，你可以试着用下yii，Symfony之类框架。

