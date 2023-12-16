# REDHAT8 的MYSQL远程连接示范

## 跟着本教程一起操作能解决90%的MYSQL远程连接不上的问题

__接下来就跟着我一起操作吧!__

我们先准备一台能ping通外网的redhat8虚拟机

- 未安装过`mysql`的redhat8
- windows系统上安装`navicat`

准备工作做好了就跟着我一起来吧!

1. 安装`mysql`,启动并且设置开机自启.

```bash
[root@Client2 ~]# sudo yum install mysql-server
Updating Subscription Management repositories.
This system is registered to Red Hat Subscription Management, but is not receiving updates. You can use subscription-manager to assign subscriptions.
Red Hat Enterprise Linux 8 for x86_64 - A 3.5 MB/s |  58 MB     00:16    
Red Hat Enterprise Linux 8 for x86_64 - B 2.2 MB/s |  64 MB     00:29    ==============================                            ] 4.7 MB/s |  35 MB     00:06 ETA
上次元数据过期检查：0:00:07 前，执行于 2023年12月16日 星期六 15时43分31秒。
依赖关系解决。
==========================================================================
 软件包        架构   版本         仓库                              大小
==========================================================================
安装:
 mysql-server  x86_64 8.0.32-1.module+el8.8.0+18446+fca6280e
                                   rhel-8-for-x86_64-appstream-rpms  32 M
安装依赖关系:
 mariadb-connector-c-config
               noarch 3.1.11-2.el8_3
                                   rhel-8-for-x86_64-appstream-rpms  15 k
 protobuf-lite x86_64 3.5.0-15.el8 rhel-8-for-x86_64-appstream-rpms 149 k
 mecab         x86_64 0.996-2.module+el8.8.0+18436+8918dd75
                                   rhel-8-for-x86_64-appstream-rpms 394 k
 mysql         x86_64 8.0.32-1.module+el8.8.0+18446+fca6280e
                                   rhel-8-for-x86_64-appstream-rpms  15 M
 mysql-common  x86_64 8.0.32-1.module+el8.8.0+18446+fca6280e
                                   rhel-8-for-x86_64-appstream-rpms 137 k
 mysql-errmsg  x86_64 8.0.32-1.module+el8.8.0+18446+fca6280e
                                   rhel-8-for-x86_64-appstream-rpms 629 k
启用模块流:
 mysql                8.0                                                

事务概要
==========================================================================
安装  7 软件包

总下载：48 M
安装大小：245 M
确定吗？[y/N]： y
下载软件包：
(1/7): mariadb-connector-c-config-3.1.11- 6.6 kB/s |  15 kB     00:02    
(2/7): protobuf-lite-3.5.0-15.el8.x86_64.  55 kB/s | 149 kB     00:02    
(3/7): mecab-0.996-2.module+el8.8.0+18436 117 kB/s | 394 kB     00:03    
(4/7): mysql-common-8.0.32-1.module+el8.8  71 kB/s | 137 kB     00:01    
(5/7): mysql-errmsg-8.0.32-1.module+el8.8 370 kB/s | 629 kB     00:01    
(6/7): mysql-8.0.32-1.module+el8.8.0+1844 2.3 MB/s |  15 MB     00:06    
(7/7): mysql-server-8.0.32-1.module+el8.8 1.7 MB/s |  32 MB     00:19    
--------------------------------------------------------------------------
总计                                      2.0 MB/s |  48 MB     00:23     
警告：/var/cache/dnf/rhel-8-for-x86_64-appstream-rpms-9d3886b51bb367d7/packages/mariadb-connector-c-config-3.1.11-2.el8_3.noarch.rpm: 头V3 RSA/SHA256 Signature, 密钥 ID fd431d51: NOKEY
Red Hat Enterprise Linux 8 for x86_64 - A 4.9 MB/s | 5.0 kB     00:00    
导入 GPG 公钥 0xFD431D51:
 Userid: "Red Hat, Inc. (release key 2) <security@redhat.com>"
 指纹: 567E 347A D004 4ADE 55BA 8A5F 199E 2F91 FD43 1D51
 来自: /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
确定吗？[y/N]： y
导入公钥成功
导入 GPG 公钥 0xD4082792:
 Userid: "Red Hat, Inc. (auxiliary key) <security@redhat.com>"
 指纹: 6A6A A7C9 7C88 90AE C6AE BFE2 F76F 66C3 D408 2792
 来自: /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
确定吗？[y/N]： y
导入公钥成功
运行事务检查
事务检查成功。
运行事务测试
事务测试成功。
运行事务
  准备中  :                                                           1/1 
  安装    : mariadb-connector-c-config-3.1.11-2.el8_3.noarch          1/7 
  安装    : mysql-common-8.0.32-1.module+el8.8.0+18446+fca6280e.x86   2/7 
  安装    : mysql-8.0.32-1.module+el8.8.0+18446+fca6280e.x86_64       3/7 
  安装    : mysql-errmsg-8.0.32-1.module+el8.8.0+18446+fca6280e.x86   4/7 
  安装    : mecab-0.996-2.module+el8.8.0+18436+8918dd75.x86_64        5/7 
  运行脚本: mecab-0.996-2.module+el8.8.0+18436+8918dd75.x86_64        5/7 
  安装    : protobuf-lite-3.5.0-15.el8.x86_64                         6/7 
  运行脚本: mysql-server-8.0.32-1.module+el8.8.0+18446+fca6280e.x86   7/7 
  安装    : mysql-server-8.0.32-1.module+el8.8.0+18446+fca6280e.x86   7/7 
  运行脚本: mysql-server-8.0.32-1.module+el8.8.0+18446+fca6280e.x86   7/7 
ValueError: /var/log/mysql(/.*)? 的文件上下文已定义

  验证    : mariadb-connector-c-config-3.1.11-2.el8_3.noarch          1/7 
  验证    : protobuf-lite-3.5.0-15.el8.x86_64                         2/7 
  验证    : mecab-0.996-2.module+el8.8.0+18436+8918dd75.x86_64        3/7 
  验证    : mysql-8.0.32-1.module+el8.8.0+18446+fca6280e.x86_64       4/7 
  验证    : mysql-common-8.0.32-1.module+el8.8.0+18446+fca6280e.x86   5/7 
  验证    : mysql-errmsg-8.0.32-1.module+el8.8.0+18446+fca6280e.x86   6/7 
  验证    : mysql-server-8.0.32-1.module+el8.8.0+18446+fca6280e.x86   7/7 
Installed products updated.

已安装:
  mysql-server-8.0.32-1.module+el8.8.0+18446+fca6280e.x86_64              
  mariadb-connector-c-config-3.1.11-2.el8_3.noarch                        
  protobuf-lite-3.5.0-15.el8.x86_64                                       
  mecab-0.996-2.module+el8.8.0+18436+8918dd75.x86_64                      
  mysql-8.0.32-1.module+el8.8.0+18446+fca6280e.x86_64                     
  mysql-common-8.0.32-1.module+el8.8.0+18446+fca6280e.x86_64              
  mysql-errmsg-8.0.32-1.module+el8.8.0+18446+fca6280e.x86_64              

完毕！
[root@Client2 ~]# systemctl start mysqld
[root@Client2 ~]# systemctl enable mysqld
Created symlink /etc/systemd/system/multi-user.target.wants/mysqld.service → /usr/lib/systemd/system/mysqld.service.
```

2. 配置`mysql`安全性脚本

```bash
[root@Client2 ~]# sudo mysql_secure_installation 

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 0
Please set the password for root here.

New password: 

Re-enter new password: 

Estimated strength of the password: 100 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : n

 ... skipping.
By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : n'

 ... skipping.
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done! 
```

__这样`mysql`的允许远程root登录权限就开启了__

但是这还没完全成功!

接着跟我来!

> 我们先测试一下mysql是否安装成功
>
> mysql -uroot -p

如果让你输入密码登录就说明成功了.

我们输入`quit`退出`mysql`

进行下一步

```bash
[root@Client2 ~]# grep bind-address /etc/my.cnf
```

检查bind-address是否配置,默认是没有配置的.

```bash
[root@Client2 ~]# vim /etc/my.cnf
```

进入编辑,我们添加

bind-address = 0.0.0.0

这样就呢能允许所有IP远程连接`mysql`服务器了,当然这个IP可以根据自己的需要设置.

```mysql
#
# This group is read both both by the client and the server
# use it for options that affect everything
#
[client-server]
bind-address = 0.0.0.0

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d

```

`:wq`保存并退出

3. 配置mysql中的用户权限,80%的人连接不上可能都死在了这里

```mysql
[root@Client2 ~]# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.32 Source distribution

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> USE mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SHOW TABLES;
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| func                                                 |
| general_log                                          |
| global_grants                                        |
| gtid_executed                                        |
| help_category                                        |
| help_keyword                                         |
| help_relation                                        |
| help_topic                                           |
| innodb_index_stats                                   |
| innodb_table_stats                                   |
| password_history                                     |
| plugin                                               |
| procs_priv                                           |
| proxies_priv                                         |
| replication_asynchronous_connection_failover         |
| replication_asynchronous_connection_failover_managed |
| replication_group_configuration_version              |
| replication_group_member_actions                     |
| role_edges                                           |
| server_cost                                          |
| servers                                              |
| slave_master_info                                    |
| slave_relay_log_info                                 |
| slave_worker_info                                    |
| slow_log                                             |
| tables_priv                                          |
| time_zone                                            |
| time_zone_leap_second                                |
| time_zone_name                                       |
| time_zone_transition                                 |
| time_zone_transition_type                            |
| user                                                 |
+------------------------------------------------------+
37 rows in set (0.00 sec)

mysql> update user set host = '%' where user = 'root' and host = 'localhost';
Query OK, 0 rows affected (0.01 sec)
Rows matched: 0  Changed: 0  Warnings: 0

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)

mysql> quit
Bye
[root@Client2 ~]# service mysqld restart
Redirecting to /bin/systemctl restart mysqld.service
```

4. 接下来我们设置一下防火墙

```bash
[root@Client2 ~]# sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens160
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
	
[root@Client2 ~]# sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
success
[root@Client2 ~]# sudo firewall-cmd --reload
success
```

<hr />

一切准备就绪,我们用navicat远程连接一下mysql服务器吧!

单击左上角的连接-->mysql-->添加连接名,我这里设置的是Client2-->主机,这里填入您的mysql服务器所在IP,不知道如何查看所在IP可以在redhat8终端输入`ifconfig`查看-->主机端口默认3306-->用户名默认root就好-->密码你自己设置的什么就填写什么,我勾选了保存密码省的下次连接再次输入密码.



__这样我们就连接成功了!你们呢?__