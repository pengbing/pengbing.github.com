---
layout: post
title: PHP分页类
---

{{page.title}}
==============
<p>2013-02-04 - bing.peng</p>

<p>
今天为xxoophp写了个分页类，这已经是我写过的第4个php分页类了，每次都是写完用一阵子后就觉得写得有问题，然后改改或是直接重写一个。
看看这个分页类能坚持多久？直接贴代码：
</p>

{% highlight php %}

<?php
/**
 * 分页类
 * @author bing.peng
 */
class Pagination {
    
    private $tag       = 'page';// 分页参数名
    private $total     = 0;     // 总记录数
    private $totalPage = 0;     // 总页数
    private $pageRows  = 10;    // 每页记录数
    private $url;               // 不含分页参数(page=x)的url
    private $paging;            // 当前页码
    
    public function __construct(array $params = array()) {
        $this->init($params);
    }
    
    /**
     * 初始化，偏好设置
     * @param array $params
     */
    public function init( array $params = array() ) {
        if ( count($params) > 0 ) {   
            foreach ($params as $key => $val) {
                $this->$key = $val;
            }     
        }
    }
    
    /**
     * 计算，生成分页信息
     * @param int $total 总记录数
     * @return array(
     *   'url'   => '链接地址，不含page=x',
     *   'upto'  => '数据库开始行，即limit x, 10 中的x',
     *   'limit' => 'SQL语句的limit部份，如: limit 20,10',
     *   'paging'=> '当前页页码',
     *   'pre'   => '上一页页码',
     *   'next'  => '下一页页码',
     *   'total_page' => '总页数', 
     *   'total'      => '总记录数', 
     *   'page_rows'  => '每页显示的记录数',
     *   'tag'        => '分页参数名称', 
     *   'pages'      => '分页码数组',
     *   'html'       => '分页HTML代码'
     * )
     */
    public function gen( $total = null ) {
        if( !empty($total) ) {
            $this->total = $total;
        }
        
        // 计算总页数
        $this->totalPage = ceil( $this->total / $this->pageRows );
        if( $this->totalPage < 0 ) { $this->totalPage = 0; }
        
        // 获取当前页码
        $this->paging = empty($_REQUEST[$this->tag]) ? 1 : $_REQUEST[$this->tag];

        if( $this->paging < 1 ) {
            $this->paging = 1;
        }
        elseif( $this->paging > $this->totalPage ) {
            $this->paging = $this->totalPage;
        }
        
        $upto = ($this->paging - 1) * $this->pageRows;
        $upto = ($upto < 0) ? 0 : $upto;
        
        $info = array();    // 返回给用户的数组
        
        // 如果用户没有指定URL，则自动生成URL
        if( empty($this->url) ) {
            $info['url'] = $this->genURL();
        }
        else {
            $info['url'] = $this->url;
        }
        
        $info['upto']       = $upto;    // 数据库开始行
        $info['limit']      = ' LIMIT '.$info['upto'].','.$this->pageRows;  // mysql limit sql part
        $info['paging']     = $this->paging;    // 当前页
        $info['pre']        = ($this->paging <= 1) ? $this->paging : ($this->paging - 1); // 上一页
        $info['next']       = 
            ($this->paging >= $this->totalPage) ? $this->totalPage : ($this->paging + 1); // 下一页
        $info['total_page'] = $this->totalPage; // 总页数
        $info['total']      = ($this->total < 0) ? 0 : $this->total;    // 总记录数
        $info['page_rows']  = $this->pageRows;  // 每页记录数
        $info['tag']        = $this->tag;
        $info['pages']      = $this->pages($info);  // 生成分页码数组
        $info['html']       = $this->html($info);   // 生成分页HTML
        
        return $info;
    }
    
    /**
     * 生成分页码数组
     * @param int $totalPage 总页数
     * @param int $pageRows 每页显示的记录数
     * @param int $paging 当前页
     */
    private function pages( array $info ) {
        // 只有一页
        if( $info['total_page'] == 1 ) {
             return array(1);
        }
        
        // 开始页码
        $start = $info['paging'] - 5;
        if( $start < 1 ) {
            $start = 1;
        }
        
        // 结束页码
        $end = $info['paging'] + 5;
        if( $end - $start < $info['page_rows'] ) {  // 不够10个页码时，补够
            $end += $end - $start;
        }
        if( $end > $info['total_page'] ) {  // 超过总页数
            $end = $info['total_page'];
        }
        
        $arr = array();
        for( $i=$start; $i<=$end; $i++ ) {
            $arr[] = $i;
        }
        return $arr;
    }
    
    /**
     * 生成分页HTML
     * @param array() $info
     */
    private function html(array $info) {
        if( $info['total_page'] <= 1 ) { return ''; }
        
        $str = '';
        
        // 上一页
        if( $info['paging'] > 1 ) {
            $str .= "<a href=\"{$info['url']}{$this->tag}={$info['pre']}\">上一页</a>";
        }
        else {
            $str = '<span>上一页</span>';
        }
        
        // 数字页码
        foreach( $info['pages'] as $i ) {
            if( $i == $info['paging'] ) {
                $str .= "<span class=\"active\">{$i}</span>";       
            }
            else {
                $str .= "<a href=\"{$info['url']}{$this->tag}={$i}\">{$i}</a>";
            }
        }
        
        // 下一页
        if( $info['paging'] == $info['total_page'] ) {
            $str .= '<span>下一页</span>';
        }
        else {
            $str .= "<a href=\"{$info['url']}{$info['tag']}={$info['next']}\">下一页</a>";
        }
        return $str;
    }
    
    /**
     * 生成URL
     * @return string $url
     */
    private function genURL() {
        $http_protocol = ( isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] != 'off' ) ? 'https://' : 'http://';
        $url = $http_protocol . $_SERVER ['HTTP_HOST'] . $_SERVER['REQUEST_URI'];
        
        if( $pos = strpos( $url, '?' ) ) {
            $url = substr( $url, 0, $pos );
        }
        
        $url .= '?';
        
        if( !empty($_SERVER['QUERY_STRING']) ) {
            $query_string_arr = explode( '&', $_SERVER['QUERY_STRING'] );
            $pattern = '/^'.$this->tag.'=\d+$/';
            $qs_arr = array();
            foreach( $query_string_arr as $qs ) {
                if( !preg_match($pattern, $qs) ) {
                    $qs_arr[] = $qs;
                }   
            }
        
            if( count($qs_arr) > 0 ) {
                $url .= implode( '&', $qs_arr ) . '&';
            }
        }
        return $url;
    }
}

{% endhighlight %}

<p>使用示例：</p>

{% highlight php %}

<?php
require ROOT . 'libs/Pagination.lib.php';   // 载入类库

$total = 328; // 总记录数

// 分页偏好设置
$conf = array(
    // 分页参数名称，默认名称为page，URL为：xx.com/products.php?page=3
    // 如果设为'a'，URL则为：xx.com/products.php?a=3
    'tag'       => 'a', 
    'pageRows'  => '15', // 每页显示记录数
    'paging'    => 3,    // 当前页号，默认从$_GET中获取
    'url'       => ''    // 分页链接的url地址，默认为当前访问地址
);

// 生成分页对象，如果使用默认设置，不用传参数
$p = new Pagination( $conf );   

// 生成分页信息，返回分页信息数组
$pageInfo = $p-&lt;gen( $total );   // $total为总记录数

echo '<pre>';
print_r( $pageInfo );
echo '</pre>';

{% endhighlight %}

<p>
分页信息数组，涵盖了分页所需的相关信息，因为对于分页条的HTML的要求是各式各样的，本类只提供的最常用的一种形式，如果你需要生成其它形式的分页条，利用分页信息数组你也可以快速的扩展出你想要的分页条。
</p>

<p>
值得注意的是，虽然我们在分页信息数组中提供了limit部份的SQL语句，但是我并不建议你在大数量中表中使用它，因为它会引起性能问题，你应该自己编写更高效的分页SQL语句，比如：
</p>


