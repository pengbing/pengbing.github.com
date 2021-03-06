---
layout: post
title: PHP执行shell脚本命令
---

{{ page.title }}
================

<p>2012-12-16 - bing.peng</p>

<p>封装了一个PHP执行shell脚本的类：</p>

{% highlight php %}
<?
/**
 * PHP执行shell脚本命令封装
 * @author bing.peng
 */
class Shell {
 
    private $result = null;
    private $errorInfo = null;
 
    /**
     * 执行shell命令
     * @param string $cmd shell命令
     * @return string||boolean 成功返回string结果，失败返回false
     */
    public function exec($cmd) {
        $descriptorspec = array(
            // stdin is a pipe that the child will read from
            0 => array("pipe", "r"),   
            // stdout is a pipe that the child will write to
            1 => array("pipe", "w"), 
            // stderr is a file to write to  
            2 => array("pipe", "w")  
        );
 
        $process = proc_open('/bin/bash', $descriptorspec, $pipes);
 
        if (is_resource($process)) {
            // 在输入流中输入要执行的命令
            fwrite($pipes[0], $cmd); 
            fclose($pipes[0]);
 
            // 在输出流中获取输出内容
            $this->result = stream_get_contents($pipes[1]);
            fclose($pipes[1]);
 
            // 在错误流中获取错误信息
            $this->errorInfo = stream_get_contents($pipes[2]);
            fclose($pipes[2]);
        }
 
        // 获取打开进程的状态
        $status = proc_get_status($process);
 
        proc_close($process);
 
        if($status['exitcode'] == 0) { // 正确执行
            return $this->result;
        }
        else {
            return false;
        }
    }
 
    /**
     * 获取错误信息
     */
    public function getError() {
        return $this->errorInfo;
    }
}


// example
$sh = new Shell();
if( $rs = $sh->exec("pwd") ) {
    echo $rs;
}
else {
    echo $sh->getError();
}

{% endhighlight %}

