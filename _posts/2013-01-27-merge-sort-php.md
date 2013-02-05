---
layout: post
title: 利用归并排序合并PHP数组
---

{{page.title}}
=================
<p>2013-01-27 - bing.peng</p>

算法一直是我心中的痛，今天有朋友让我帮忙用PHP写一个方法合并两个数组。<br/>
数组A：

{% highlight php %}

<?php
$a = array(
    array('id'=>1,'a'=>2,'b'=>8),
    array('id'=>2,'a'=>1,'b'=>10),
    array('id'=>3,'a'=>8,'b' => 3),
    array('id'=>4,'a'=>17,'b'=>9),
    array('id'=>5,'a'=>2,'b'=>26),
    array('id'=>6,'a'=>7,'b'=>19)
);

{% endhighlight %}

数组B：

{% highlight php %}

<?php
$b = array(
    array('id'=>1,'c'=>35,'d'=>55),
    array('id'=>3,'c'=>23,'d'=>12),
    array('id'=>5,'c'=>88,'d'=>37)
);

{% endhighlight %}

将A、B数组中ID相同的元素合并在一起，如果只在数组A中存在，则'c','d'项填充0，如果只在数组B中存在，则'a','b'项填充0，结果如下：

{% highlight php %}

<?php
$c = array(
    array('id'=>1,'a'=>2,'b'=>8,'c'=>35,'d'=>55),
    array('id'=>2,'a'=>1,'b'=>10,'c'=>0,'d'=>0),
    ....
);

{% endhighlight %}

归并排序(Merge Sort)是利用"归并"技术来进行排序。归并是指将若干个已排序的子文件合并成一个有序的文件。具体实现如下：

{% highlight php %}

<?php
function my_array_merge( $a_arr, $b_arr ) {
    $i = 0; $j = 0;
    $n = count($a_arr);
    $m = count($b_arr);
    
    $data = array();
    
    // 合并交叉部份
    while( $i < $n && $j < $m ) {
        if( $a_arr[$i]['id'] == $b_arr[$j]['id'] ) {
            $item = array(
                'id'=> $a_arr[$i]['id'],
                'a' => $a_arr[$i]['a'],
                'b' => $a_arr[$i]['b'],
                'c' => $b_arr[$j]['c'],
                'd' => $b_arr[$j]['d'],
            );
            $i++; $j++;
        } else if( $a_arr[$i]['id'] &gt; $b_arr[$j]['id']) {
            $item = array(
                'id'=> $a_arr[$j]['id'],
                'a' => 0,
                'b' => 0,
                'c' => $b_arr[$j]['c'],
                'd' => $b_arr[$j]['d'],
            );
            $j++;
        } else {
            $item = array(
                'id'=> $a_arr[$i]['id'],
                'a' => $a_arr[$i]['a'],
                'b' => $a_arr[$i]['b'],
                'c' => 0,
                'd' => 0,
            );
            $i++;
        }
        $data[] = $item;
    } 
    
    // 合并$a余下部份
    while( $i < $n ) {
        $item = array(
            'id'=> $a_arr[$i]['id'],
            'a' => $a_arr[$i]['a'],
            'b' => $a_arr[$i]['b'],
            'c' => 0,
            'd' => 0,
        );
        $data[] = $item;
        $i++;
    }
    
    // 合并$b余下部份
    while( $j < $m ) {
        $item = array(
            'id'=> $a_arr[$j]['id'],
            'a' => 0,
            'b' => 0,
            'c' => $b_arr[$j]['c'],
            'd' => $b_arr[$j]['d'],
        );
        $data[] = $item;
        $j++;
    }
    
    return $data;
}

// call func
$c = my_array_merge($a, $b);
echo '<pre>';
print_r($c);
echo '</pre>';

{% endhighlight %}

