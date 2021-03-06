---
layout: post
title: mysql 主从配置
---

{{ page.title }}
================

<p class="meta">2012-12-18 - bing.peng</p>

<p class="note">非原创，忘了出处，十分抱歉，需要加外链联系我，谢谢</p>

## 1. mysql主从复制

+ 版本一致
+ 初始化表，并在后台启动mysql
+ 修改root的密码   

<br/><br/> 

## 2. 修改主服务器master:

<pre class="terminal">
#vi /etc/my.cnf
[mysqld]
log-bin=mysql-bin   // [必须]启用二进制日志
server-id=222       // [必须]服务器唯一ID，默认是1，一般取IP最后一段
</pre>

<br /><br />

## 3. 修改从服务器slave:

<pre class="terminal">
#vi /etc/my.cnf
[mysqld]
log-bin=mysql-bin   // [必须]启用二进制日志
server-id=226       // [必须]服务器唯一ID，默认是1，一般取IP最后一段
</pre>

<br /><br />

## 4. 重启两台服务器的mysql

<pre class="terminal">
$ /etc/init.d/mysqld restart
</pre>

<br /><br />

## 5. 在主服务器上建立帐户并授权slave:

<pre class="terminal">
#/usr/local/mysql/bin/mysql -uroot -p
mysql>GRANT REPLICATION SLAVE ON *.* to 'mysync'@'%' identified by 'q123456';
</pre>

一般不用root帐号，“%”表示所有客户端都可能连，只要帐号ãX !C!/密码正确，此处可用具体客户端IP代替，如192.168.145.226，加强安全。

<br /><br />

## 6. 登录主服务器的mysql，查询master的状态

<pre class="terminal">
mysql>show master status;
File             |  Position  |  Binlog_Do_DB  |  Binlog_Ignore_DB
mysql-bin.000004 |  308       |                | 
1 row in set (0.00 sec)
</pre>

注：执行完此步骤后不要再操作主服务器MYSQL，防止主服务器状态值变化

<br /><br />

## 7. 配置从服务器Slave：

<pre class="terminal">
mysql>change master to aster_host='192.168.145.222',master_user='tb',master_password='q123456',
      master_log_file='mysql-bin.000004',master_log_pos=308;   //注意不要断开，“308”无单引号
mysql>start slave;    //启动从服务器复制功能
</pre>

<br /><br />

##8. 检查从服务器复制功能状态：

<pre class="terminal">
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

