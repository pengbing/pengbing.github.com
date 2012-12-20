---
layout: post
title: mysql 主从配置
---

{{ page.title }}
================

<p class="meta">2012-12-18 - bing.peng</p>

申明一下：这篇文章不是我写的，很久以前看的了，忘了出处，从笔记里找出来的，如果这篇文章是你写的，请你联系我加链接，实在是对不住。

##1、mysql主从复制

1.1、版本一致
1.2、初始化表，并在后台启动mysql
1.3、修改root的密码


##2、修改主服务器master:

<pre class="brush:bash">
#vi /etc/my.cnf
[mysqld]
log-bin=mysql-bin   --[必须]启用二进制日志
server-id=222       --[必须]服务器唯一ID，默认是1，一般取IP最后一段
</pre>

##3、修改从服务器slave:

<pre class="brush:bash">
#vi /etc/my.cnf
[mysqld]
log-bin=mysql-bin   //[必须]启用二进制日志
server-id=226       //[必须]服务器唯一ID，默认是1，一般取IP最后一段
</pre>

##4、重启两台服务器的mysql

<pre class="brush:bash">
/etc/init.d/mysqld restart
</pre>

##5、在主服务器上建立帐户并授权slave:

<pre class="brush:bash">
#/usr/local/mysql/bin/mysql -uroot -p

--一般不用root帐号，“%”表示所有客户端都可能连，只要帐号
--密码正确，此处可用具体客户端IP代替，如192.168.145.226，加强安全。
mysql>GRANT REPLICATION SLAVE ON *.* to 'mysync'@'%' identified by 'q123456';
</pre>

##6、登录主服务器的mysql，查询master的状态
<pre class="brush:sql">
mysql>show master status;
</pre>
<table>
	<tr>
		<td>File</td>
		<td>Position</td>
		<td>Binlog_Do_DB</td>
		<td>Binlog_Ignore_DB</td>
	</tr>
	<tr>
		<td>mysql-bin.000004</td>
		<td>308</td>
		<td></td>
		<td></td>
	</tr>
</table>
1 row in set (0.00 sec)
注：执行完此步骤后不要再操作主服务器MYSQL，防止主服务器状态值变化

##7、配置从服务器Slave：
<pre class="brush:sql">
mysql>change master to aster_host='192.168.145.222',master_user='tb',master_password='q123456',
      master_log_file='mysql-bin.000004',master_log_pos=308;   //注意不要断开，“308”无单引号
mysql>start slave;    //启动从服务器复制功能
</pre>

##8、检查从服务器复制功能状态：

<pre class="brush:bash">
       Slave_IO_State: Waiting for master to send event
          Master_Host: 192.168.2.222  //主服务器地址
          Master_User: myrync         //授权帐户名，尽量避免使用root
          Master_Port: 3306           //数据库端口，部分版本没有此行
        Connect_Retry: 60
      Master_Log_File: mysql-bin.000004
  Read_Master_Log_Pos: 600        //#同步读取二进制日志的位置，大于等于>=Exec_Master_Log_Pos
       Relay_Log_File: ddte-relay-bin.000003
        Relay_Log_Pos: 251
Relay_Master_Log_File: mysql-bin.000004
     Slave_IO_Running: Yes       //此状态必须YES
    Slave_SQL_Running: Yes       //此状态必须YES
                   ......
</pre>

注：Slave_IO及Slave_SQL进程必须正常运行，即YES状态，否则都是错误的状态(如：其中一个NO均属错误)。
以上操作过程，主从服务器配置完成。

