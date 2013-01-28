---
layout: post
title: 用jCanvaScript创建闪烁动画
---

{{page.title}}
=================
<p>2013-01-28 - bing.peng</p>

<p>
今天要用html5 canvas画机房平面图，比较了一堆类库后，选了<a target="_blank" href="http://jcscript.com">jCanvasScript</a>，
因为我们的需求并不是很复杂，jCanvasScript的api相对简单，同时还支持鼠标、键盘事件，拖拽，动画。
</p>

<p>机房的某台设备故障时，需要有报警，闪烁提示。于是开始用jCanvasScript实现闪烁动画，效果如下：</p>

<div>
	<canvas height="200" width="200" id="paper" style="background:#000;"></canvas>
</div>

<p>jCanvaScript提供了一个动画函数，定义如下：</p>
<pre class="brush:js">

animate(object parameters)
animate(object parameters, positive int duration)
//object parameters：动画参数对象，比如{x:180}，表示对象在x轴上移动到180px处；
//positive int duration：动画完成时间，如移动对象到x轴180px处，过程时间；
//function fn：动画完成后的回调函数
animate(object parameters, positive int duration, function fn)
animate(object parameters, positive int duration, object easing)
animate(object parameters, positive int duration, object easing, function fn)
animate(object parameters, positive int duration, object easing, object onstep, function fn)

</pre>


代码如下：
<pre class="brush:html">

<!DOCTYPE HTML>
<html lang="en-US">
<head> 
<meta charset="UTF-8">
<title>闪烁动画</title>
<style type="text/css">
* { padding:0; margin:0 }
body { background:#000; }
</style>
</head>
<body>
<canvas height="200" width="200" id="paper"></canvas>

<script type="text/javascript" src="http://jcscript.com/data/js/jCanvaScript.1.5.18.min.js"></script>
<script type="text/javascript">
jc.start("paper", true);
setInterval(function(){
    jc.circle(50, 50, 1, "rgba(250, 3, 3, 0.8)", true)	// 画一个红色的画
    	.animate({radius:30, opacity:0}, 1000, function(){	// 创建动画
        	this.del();	// 每次动画需要重绘对象本身
        });
}, 1000);
jc.start("paper", true);
</script>	
</body>
</html>

</pre>

<script type="text/javascript" src="http://jcscript.com/data/js/jCanvaScript.1.5.18.min.js"></script>
<script type="text/javascript">
jc.start("paper", true);
setInterval(function(){
    jc.circle(50, 50, 1, "rgba(250, 3, 3, 0.8)", true)
    	.animate({radius:30, opacity:0}, 1000, function(){
        	this.del();
        });
}, 1000);
jc.start("paper", true);
</script>	



