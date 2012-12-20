---
layout: post
title: 让IIS服务器支持pathinfo形式的URL
---

{{ page.title }}
================


<p class="meta">2012-12-16 - bing.peng</p>

现在的PHP框架，大多都是采用单一入口，然后用pathinfo的形式将要执行的类、方法、参数放到URL中，比如我们使用CodeIgniter框架的话，URL看起来将会像这样：

http://www.php-chongqing.com/index.php/article/107

Apache默认支持pathinfo形的URL，但IIS并不支持，所以像CI那样的URL在IIS中是跑不起来的，你需要在URL中使用查询字符串，URL看起来会像这样：

http://www.php-chongqing.com/index.php?c=article&m=show&id=107

这样的URL看起来真是有足够丑。

想要在IIS中支持pathinfo，需要再安装一个模块，网站有很多例子，这里我就不再说了。但是我们通常都是购买的虚拟主机，虚拟主机大都使用IIS，空间商并没有让虚拟主机支持pathinfo，而你又没有权限去安装模块，更气愤的是你让管理员安装模块，他根本就不理你。我就有过这种痛苦的经历，当时郁闷得一踏糊涂，因为之前看过一阵子wordpress源码，记得里面有一段代码是用PHP代码实现pathinfo支持的，仔细阅读后，改了一、两个小地方，放到单一入口index.php的开始处即可，需要注意的是这段代码并不能使Nginx支持pathinfo，如何让Nginx支持pathinfo，请参考我的另一篇文章《Windows XP环境安装PHP+Nginx》。

<pre class="brush:php">
// Fix for IIS when running with PHP ISAPI
if ( empty( $_SERVER['REQUEST_URI'] ) || 
	( php_sapi_name() != 'cgi-fcgi' && 
		preg_match( '/^Microsoft-IIS\//', $_SERVER['SERVER_SOFTWARE'] ) ) ) {
    // IIS Mod-Rewrite
    if (isset($_SERVER['HTTP_X_ORIGINAL_URL'])) {
        $_SERVER['REQUEST_URI'] = $_SERVER['HTTP_X_ORIGINAL_URL'];
    }
    // IIS Isapi_Rewrite
    else if (isset($_SERVER['HTTP_X_REWRITE_URL'])) {
        $_SERVER['REQUEST_URI'] = $_SERVER['HTTP_X_REWRITE_URL'];
    }
    else {
        // Use ORIG_PATH_INFO if there is no PATH_INFO
        if ( !isset($_SERVER['PATH_INFO']) && isset($_SERVER['ORIG_PATH_INFO']) )
			$_SERVER['PATH_INFO'] = $_SERVER['ORIG_PATH_INFO'];

		// Some IIS + PHP configurations puts the script-name in the path-info
		// (No need to append it twice)
		if ( isset($_SERVER['PATH_INFO']) ) {
			if ( $_SERVER['PATH_INFO'] == $_SERVER['SCRIPT_NAME'] )
				$_SERVER['REQUEST_URI'] = $_SERVER['PATH_INFO'];
			else
				$_SERVER['REQUEST_URI'] = 
					$_SERVER['SCRIPT_NAME'] . $_SERVER['PATH_INFO'];
        }

		// Append the query string if it exists and isn't null
		if (isset($_SERVER['QUERY_STRING']) && !empty($_SERVER['QUERY_STRING'])) {
			$_SERVER['REQUEST_URI'] .= '?' . $_SERVER['QUERY_STRING'];
		}
    }
}

$PHP_SELF = $_SERVER['PHP_SELF'];
$_SERVER['PHP_SELF'] = $PHP_SELF = preg_replace("/(\?.*)?$/",'',$_SERVER["REQUEST_URI"]);

</pre>

