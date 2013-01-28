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

<div style="border:1px solid #ccc; height:200px; width:500px;">
	<iframe src="http://pengbing.me/examples/jCanvaScript-blink-animate.html"></iframe>
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




