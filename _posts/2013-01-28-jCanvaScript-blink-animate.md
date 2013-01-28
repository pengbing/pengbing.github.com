---
layout: post
title: ��jCanvaScript������˸����
---

{{page.title}}
=================
<p>2013-01-28 - bing.peng</p>

����Ҫ��html5 canvas������ƽ��ͼ���Ƚ���һ������ѡ��<a target="_blank" href="http://jcscript.com">jCanvasScript</a>��
��Ϊ���ǵ����󲢲��Ǻܸ��ӣ�jCanvasScript��api��Լ򵥣�ͬʱ��֧����ꡢ�����¼�����ק��������<br/>

������ĳ̨�豸����ʱ����Ҫ�б�������˸��ʾ�����ǿ�ʼ��jCanvasScriptʵ����˸������Ч�����£�

<div>
	<canvas height="200" width="200" id="paper" style="background:#000;"></canvas>
</div>

jCanvaScript�ṩ��һ�������������������£�
<pre class="brush: js">

animate(object parameters)
animate(object parameters, positive int duration)
//object parameters�������������󣬱���{x:180}����ʾ������x�����ƶ���180px����
//positive int duration���������ʱ�䣬���ƶ�����x��180px��������ʱ�䣻
//function fn��������ɺ�Ļص�����
animate(object parameters, positive int duration, function fn)
animate(object parameters, positive int duration, object easing)
animate(object parameters, positive int duration, object easing, function fn)
animate(object parameters, positive int duration, object easing, object onstep, function fn)

</pre>


�������£�
<pre class="brush: html">

<!DOCTYPE HTML>
<html lang="en-US">
<head> 
<meta charset="UTF-8">
<title>��˸����</title>
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
    jc.circle(50, 50, 1, "rgba(250, 3, 3, 0.8)", true)	// ��һ����ɫ�Ļ�
    	.animate({radius:30, opacity:0}, 1000, function(){	// ��������
        	this.del();	// ÿ�ζ�����Ҫ�ػ������
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


