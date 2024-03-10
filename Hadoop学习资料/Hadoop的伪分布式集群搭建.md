#  Hadoop的伪分布式集群搭建

## 什么是伪分布式集群？

​	顾名思义，伪分布就是假分布式，假就假在__只有一台机器而不是多台机器来完成一个任务,但是模拟了分布式的这个过程__，所以伪分布式下Hadoop也就是虽然在一个机器上配置了`hadoop`的所有节点，但伪分布式完成了所有分布式所必须的事件。伪分布式Hadoop和单机版最大区别就在于需要配置HDFS。

### 既然了解了什么是伪分布式集群那么就跟着我一起来搭建吧！

首先我们要配置好ftp来传输hadoop3.3.3的压缩包

如果还没配置ftp的同学可以看我这篇配置ftp教学

> https://github.com/HaiX1nG/LearnToCode/blob/main/Linux%20shell%E5%91%BD%E4%BB%A4/%E9%85%8D%E7%BD%AE%E4%B8%8E%E7%AE%A1%E7%90%86FTP%E6%9C%8D%E5%8A%A1%E5%99%A8.md

<hr />

okkk!

配置完成的同学请打开文件资源管理器，在地址栏上输入ftp://你的master主机ip,然后将hadoop3.3.3的压缩包和jdk1.8.0_212的压缩包拖到里面去,传输完成后用ssh远程链接一下master开始我们的搭建之旅吧！



我们先配置java

```bash
[katomegumi@master ~]$ cd /usr/local/
[katomegumi@master local]$ sudo mkdir java
[katomegumi@master local]$ ll
total 0
drwxr-xr-x. 2 root root   6 Apr 11  2018 bin
drwxr-xr-x. 2 root root   6 Apr 11  2018 etc
drwxr-xr-x. 2 root root   6 Apr 11  2018 games
drwxr-xr-x. 2 root root   6 Apr 11  2018 include
drwxr-xr-x. 4 root root 114 Mar  6 23:20 java
drwxr-xr-x. 2 root root   6 Apr 11  2018 lib
drwxr-xr-x. 2 root root   6 Apr 11  2018 lib64
drwxr-xr-x. 2 root root   6 Apr 11  2018 libexec
drwxr-xr-x. 2 root root   6 Apr 11  2018 sbin
drwxr-xr-x. 5 root root  49 Feb 28 13:13 share
drwxr-xr-x. 2 root root   6 Apr 11  2018 src
[katomegumi@master local]$ cd java
[katomegumi@master java]$ sudo mv /var/ftp/pub/jdk-8u212-linux-x64.tar.gz /usr/local/java
```

然后我们把移动过来的安装包解压到当前文件夹下

```bash
[katomegumi@master java]$ sudo tar -zxvf jdk-8u212-linux-x64.tar.gz
[katomegumi@master java]$ ll
drwxr-xr-x. 7  10 143       245 Apr  2  2019 jdk1.8.0_212
-rw-------. 1 ftp ftp 195013152 Mar  6 23:18 jdk-8u212-linux-x64.tar.gz
[katomegumi@master java]$ cd jdk1.8.0_212/
[katomegumi@master jdk1.8.0_212]$ pwd
/usr/local/java/jdk1.8.0_212
[katomegumi@master jdk1.8.0_212]$ sudo vim /etc/profile
[sudo] password for katomegumi: 
```



在profile文件下插入

```sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_212
export PATH=${JAVA_HOME}/bin:$PATH
```



保存并退出

然后输入

```bash
[katomegumi@master jdk1.8.0_212]$ source /etc/profile
[katomegumi@master jdk1.8.0_212]$ java -version
java version "1.8.0_212"
Java(TM) SE Runtime Environment (build 1.8.0_212-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.212-b10, mixed mode)
```

这样java就配置完成了

<hr />

## 现在开始步入正题

做一些准备工作

```bash
[katomegumi@master ~]$ cd /opt/
[katomegumi@master opt]$ ll
total 0
drwxr-xr-x. 2 root root 6 Oct 31  2018 rh
[katomegumi@master opt]$ sudo mkdir software
[sudo] password for katomegumi: 
[katomegumi@master opt]$ ll
total 0
drwxr-xr-x. 2 root root 6 Oct 31  2018 rh
drwxr-xr-x. 2 root root 6 Mar 10 17:16 software
[katomegumi@master opt]$ cd software/
[katomegumi@master software]$ sudo mkdir hadoop
[katomegumi@master software]$ ll
total 0
drwxr-xr-x. 2 root root 6 Mar 10 17:16 hadoop
[katomegumi@master software]$ sudo tar -zxvf /var/ftp/pub/hadoop-3.3.3.tar.gz -C /opt/software/hadoop
# 此处省略N多个解压条目
[katomegumi@master software]$ ll
total 0
drwxr-xr-x. 3 root root 26 Mar 10 17:17 hadoop
[katomegumi@master software]$ cd hadoop/
[katomegumi@master hadoop]$ ll
total 0
drwxr-xr-x. 10 1024 1024 215 May 10  2022 hadoop-3.3.3
[katomegumi@master hadoop]$ cd ..
[katomegumi@master software]$ cd ..
[katomegumi@master opt]$ sudo chown -R katomegumi:katomegumi /opt/software
[katomegumi@master opt]$ ll
total 0
drwxr-xr-x. 2 root       root        6 Oct 31  2018 rh
drwxr-xr-x. 3 katomegumi katomegumi 20 Mar 10 17:16 software
```



现在我们来配置一下HADOOP_HOME

```bash
[katomegumi@master opt]$ sudo vim /etc/profile
```



```shell
export JAVA_HOME=/usr/local/java/jdk1.8.0_212
export HADOOP_HOME=/opt/software/hadoop/hadoop-3.3.3
export PATH=${JAVA_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:$PATH
```



```bash
[katomegumi@master opt]$ source /etc/profile
[katomegumi@master opt]$ hadoop version
Hadoop 3.3.3
Source code repository https://github.com/apache/hadoop.git -r d37586cbda38c338d9fe481addda5a05fb516f71
Compiled by stevel on 2022-05-09T16:36Z
Compiled with protoc 3.7.1
From source with checksum eb96dd4a797b6989ae0cdb9db6efc6
This command was run using /opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-common-3.3.3.jar
```



okk配置hadoop环境完成！

现在我们来开始搭建！



```bash
[katomegumi@master opt]$ cd software/hadoop/hadoop-3.3.3/etc/hadoop/
[katomegumi@master hadoop]$ ll
total 176
-rw-r--r--. 1 katomegumi katomegumi  9213 May 10  2022 capacity-scheduler.xml
-rw-r--r--. 1 katomegumi katomegumi  1335 May 10  2022 configuration.xsl
-rw-r--r--. 1 katomegumi katomegumi  2567 May 10  2022 container-executor.cfg
-rw-r--r--. 1 katomegumi katomegumi   774 May 10  2022 core-site.xml
-rw-r--r--. 1 katomegumi katomegumi  3999 May 10  2022 hadoop-env.cmd
-rw-r--r--. 1 katomegumi katomegumi 16654 May 10  2022 hadoop-env.sh
-rw-r--r--. 1 katomegumi katomegumi  3321 May 10  2022 hadoop-metrics2.properties
-rw-r--r--. 1 katomegumi katomegumi 11765 May 10  2022 hadoop-policy.xml
-rw-r--r--. 1 katomegumi katomegumi  3414 May 10  2022 hadoop-user-functions.sh.example
-rw-r--r--. 1 katomegumi katomegumi   683 May 10  2022 hdfs-rbf-site.xml
-rw-r--r--. 1 katomegumi katomegumi   775 May 10  2022 hdfs-site.xml
-rw-r--r--. 1 katomegumi katomegumi  1484 May 10  2022 httpfs-env.sh
-rw-r--r--. 1 katomegumi katomegumi  1657 May 10  2022 httpfs-log4j.properties
-rw-r--r--. 1 katomegumi katomegumi   620 May 10  2022 httpfs-site.xml
-rw-r--r--. 1 katomegumi katomegumi  3518 May 10  2022 kms-acls.xml
-rw-r--r--. 1 katomegumi katomegumi  1351 May 10  2022 kms-env.sh
-rw-r--r--. 1 katomegumi katomegumi  1860 May 10  2022 kms-log4j.properties
-rw-r--r--. 1 katomegumi katomegumi   682 May 10  2022 kms-site.xml
-rw-r--r--. 1 katomegumi katomegumi 13700 May 10  2022 log4j.properties
-rw-r--r--. 1 katomegumi katomegumi   951 May 10  2022 mapred-env.cmd
-rw-r--r--. 1 katomegumi katomegumi  1764 May 10  2022 mapred-env.sh
-rw-r--r--. 1 katomegumi katomegumi  4113 May 10  2022 mapred-queues.xml.template
-rw-r--r--. 1 katomegumi katomegumi   758 May 10  2022 mapred-site.xml
drwxr-xr-x. 2 katomegumi katomegumi    24 May 10  2022 shellprofile.d
-rw-r--r--. 1 katomegumi katomegumi  2316 May 10  2022 ssl-client.xml.example
-rw-r--r--. 1 katomegumi katomegumi  2697 May 10  2022 ssl-server.xml.example
-rw-r--r--. 1 katomegumi katomegumi  2681 May 10  2022 user_ec_policies.xml.template
-rw-r--r--. 1 katomegumi katomegumi    10 May 10  2022 workers
-rw-r--r--. 1 katomegumi katomegumi  2250 May 10  2022 yarn-env.cmd
-rw-r--r--. 1 katomegumi katomegumi  6329 May 10  2022 yarn-env.sh
-rw-r--r--. 1 katomegumi katomegumi  2591 May 10  2022 yarnservice-log4j.properties
-rw-r--r--. 1 katomegumi katomegumi   690 May 10  2022 yarn-site.xml
```

我们要配置几个文件



配置`core-site.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:9820</value>
        </property>
</configuration>
```

配置`hadoop-env.sh`,这里的`katomegumi`换成你自己的用户名

```sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_212
export HDFS_NAMENODE_USER=katomegumi
export HDFS_DATANODE_USER=katomegumi
export HDFS_SECONDARYNAMENODE_USER=katomegumi
```

配置`hdfs-site.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
        <property>
                <name>dfs.replication</name>
                <value>1</value>
        </property>
</configuration>
```

<hr />

现在配置ssh免密登录

```bash
[katomegumi@master hadoop]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/katomegumi/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/katomegumi/.ssh/id_rsa.
Your public key has been saved in /home/katomegumi/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:kRsLcnRVDKkM1r8WLJ57ap2tVSShiyec6txh2Fr1EUk katomegumi@master
The key's randomart image is:
+---[RSA 2048]----+
|      ....o=E    |
|     .o....o.o   |
|    ..oo++. + .  |
|     o o+B+. +   |
|       .So+o. .  |
|       +o+o. o   |
|      o =+ oo    |
|     o =o.+..    |
|      +.oo..     |
+----[SHA256]-----+
[katomegumi@master hadoop]$ ssh-copy-id master
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/katomegumi/.ssh/id_rsa.pub"
The authenticity of host 'master (172.24.43.10)' can't be established.
ECDSA key fingerprint is SHA256:qvkCPSPniJ1JCDtEM8+zu/GRwcSirMZbHhtXq9B4pUY.
ECDSA key fingerprint is MD5:ca:5f:a3:03:f5:40:ae:d1:b9:eb:88:7a:72:fc:6c:9d.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
katomegumi@master's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'master'"
and check to make sure that only the key(s) you wanted were added.
```

okk!

让我们来到下一步！

我们来初始化`namenode`

```bash
[katomegumi@master hadoop]$ cd ..
[katomegumi@master etc]$ cd ..
[katomegumi@master hadoop-3.3.3]$ hdfs namenode -format
WARNING: /opt/software/hadoop/hadoop-3.3.3/logs does not exist. Creating.
2024-03-10 17:38:21,450 INFO namenode.NameNode: STARTUP_MSG: 
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = master/172.24.43.10
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 3.3.3
STARTUP_MSG:   classpath = /opt/software/hadoop/hadoop-3.3.3/etc/hadoop:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/slf4j-reload4j-1.7.36.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/snappy-java-1.1.8.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jsr305-3.0.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/nimbus-jose-jwt-9.8.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-common-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/hadoop-annotations-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-net-3.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/accessors-smart-2.4.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/j2objc-annotations-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/curator-framework-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/netty-3.10.6.Final.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jersey-servlet-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-databind-2.13.2.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/audience-annotations-0.5.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-servlet-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jsr311-api-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jaxb-impl-2.2.3-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/dnsjava-2.1.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-util-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-xml-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/hadoop-shaded-protobuf_3_7-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-compress-1.21.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/guava-27.0-jre.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-math3-3.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-admin-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jersey-server-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-webapp-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-security-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/paranamer-2.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-beanutils-1.9.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-server-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/listenablefuture-9999.0-empty-to-avoid-conflict-with-guava.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/stax2-api-4.2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jaxb-api-2.2.11.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/metrics-core-3.2.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-xc-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-mapper-asl-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/gson-2.8.9.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-core-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-asn1-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/reload4j-1.2.18.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jsp-api-2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jersey-json-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/avro-1.7.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-client-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-xdr-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/token-provider-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/hadoop-shaded-guava-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-http-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-annotations-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/checker-qual-2.5.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-cli-1.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-identity-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/httpcore-4.4.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/asm-5.0.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-core-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/slf4j-api-1.7.36.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/json-smart-2.4.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-crypto-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/curator-recipes-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-config-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-io-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-pkix-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-core-asl-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-lang3-3.12.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/hadoop-auth-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jakarta.activation-api-1.2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-text-1.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/woodstox-core-5.3.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jettison-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/animal-sniffer-annotations-1.17.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jsch-0.1.55.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/zookeeper-jute-3.5.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-jaxrs-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jul-to-slf4j-1.7.36.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-simplekdc-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-io-2.8.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/curator-client-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-util-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-collections-3.2.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-util-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-server-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/zookeeper-3.5.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/protobuf-java-2.5.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/failureaccess-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-util-ajax-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/re2j-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-codec-1.15.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-configuration2-2.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jcip-annotations-1.0-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-daemon-1.0.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jersey-core-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/httpclient-4.5.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/javax.servlet-api-3.1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-logging-1.1.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-registry-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-common-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-kms-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-nfs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-common-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/snappy-java-1.1.8.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jsr305-3.0.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/nimbus-jose-jwt-9.8.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-common-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/hadoop-annotations-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-net-3.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/accessors-smart-2.4.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/j2objc-annotations-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/curator-framework-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/netty-3.10.6.Final.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/netty-all-4.1.68.Final.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jersey-servlet-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-databind-2.13.2.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/audience-annotations-0.5.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-servlet-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jsr311-api-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jaxb-impl-2.2.3-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/dnsjava-2.1.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-util-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-xml-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/hadoop-shaded-protobuf_3_7-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-compress-1.21.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/guava-27.0-jre.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-math3-3.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-admin-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jersey-server-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-webapp-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-security-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/paranamer-2.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-beanutils-1.9.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-server-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/listenablefuture-9999.0-empty-to-avoid-conflict-with-guava.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/stax2-api-4.2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jaxb-api-2.2.11.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/okio-1.6.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-xc-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-mapper-asl-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/gson-2.8.9.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-core-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-asn1-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/reload4j-1.2.18.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/json-simple-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jersey-json-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/avro-1.7.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-client-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/okhttp-2.7.5.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-xdr-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/token-provider-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/hadoop-shaded-guava-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-http-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-annotations-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/checker-qual-2.5.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-cli-1.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-identity-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/httpcore-4.4.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/asm-5.0.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-core-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/json-smart-2.4.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-crypto-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/curator-recipes-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-config-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-io-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-pkix-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-core-asl-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-lang3-3.12.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/hadoop-auth-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jakarta.activation-api-1.2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-text-1.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/woodstox-core-5.3.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jettison-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/animal-sniffer-annotations-1.17.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jsch-0.1.55.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/zookeeper-jute-3.5.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-jaxrs-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/leveldbjni-all-1.8.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-simplekdc-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-io-2.8.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/curator-client-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-util-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-collections-3.2.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-util-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-server-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/zookeeper-3.5.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/protobuf-java-2.5.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/failureaccess-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-util-ajax-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/re2j-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-codec-1.15.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-configuration2-2.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jcip-annotations-1.0-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-daemon-1.0.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jersey-core-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/httpclient-4.5.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/javax.servlet-api-3.1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-logging-1.1.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-client-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-rbf-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-rbf-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-httpfs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-client-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-native-client-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-nfs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-native-client-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-hs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-common-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-core-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-shuffle-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-nativetask-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-app-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-uploader-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-hs-plugins-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jetty-plus-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jackson-jaxrs-base-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jetty-jndi-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/java-util-1.9.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/snakeyaml-1.26.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jetty-annotations-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax.websocket-client-api-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/fst-2.50.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-servlet-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jakarta.xml.bind-api-2.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/bcpkix-jdk15on-1.60.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/objenesis-2.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/asm-tree-9.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jackson-jaxrs-json-provider-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/metrics-core-3.2.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/aopalliance-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/asm-analysis-9.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jackson-module-jaxb-annotations-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax.websocket-api-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax-websocket-client-impl-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax.ws.rs-api-2.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jersey-guice-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/mssql-jdbc-6.2.1.jre7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-common-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-api-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jetty-client-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jersey-client-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax.inject-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-server-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/bcprov-jdk15on-1.60.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/geronimo-jcache_1.0_spec-1.0-alpha-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/ehcache-3.3.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/asm-commons-9.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/HikariCP-java7-2.4.12.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jna-5.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/swagger-annotations-1.5.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-client-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/guice-4.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax-websocket-server-impl-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/json-io-2.5.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jline-3.9.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/guice-servlet-4.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-tests-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-router-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-applications-unmanaged-am-launcher-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-api-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-client-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-applicationhistoryservice-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-applications-mawo-core-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-common-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-timeline-pluginstorage-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-applications-distributedshell-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-sharedcachemanager-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-registry-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-services-core-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-common-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-nodemanager-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-services-api-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-resourcemanager-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-web-proxy-3.3.3.jar
STARTUP_MSG:   build = https://github.com/apache/hadoop.git -r d37586cbda38c338d9fe481addda5a05fb516f71; compiled by 'stevel' on 2022-05-09T16:36Z
STARTUP_MSG:   java = 1.8.0_212
************************************************************/
2024-03-10 17:38:21,458 INFO namenode.NameNode: registered UNIX signal handlers for [TERM, HUP, INT]
2024-03-10 17:38:21,528 INFO namenode.NameNode: createNameNode [-format]
2024-03-10 17:38:21,823 INFO namenode.NameNode: Formatting using clusterid: CID-b08307c6-9e19-47d5-810d-ac0c98d4d110
2024-03-10 17:38:21,841 INFO namenode.FSEditLog: Edit logging is async:true
2024-03-10 17:38:21,858 INFO namenode.FSNamesystem: KeyProvider: null
2024-03-10 17:38:21,858 INFO namenode.FSNamesystem: fsLock is fair: true
2024-03-10 17:38:21,859 INFO namenode.FSNamesystem: Detailed lock hold time metrics enabled: false
2024-03-10 17:38:21,861 INFO namenode.FSNamesystem: fsOwner                = katomegumi (auth:SIMPLE)
2024-03-10 17:38:21,861 INFO namenode.FSNamesystem: supergroup             = supergroup
2024-03-10 17:38:21,861 INFO namenode.FSNamesystem: isPermissionEnabled    = true
2024-03-10 17:38:21,861 INFO namenode.FSNamesystem: isStoragePolicyEnabled = true
2024-03-10 17:38:21,862 INFO namenode.FSNamesystem: HA Enabled: false
2024-03-10 17:38:21,918 INFO common.Util: dfs.datanode.fileio.profiling.sampling.percentage set to 0. Disabling file IO profiling
2024-03-10 17:38:21,925 INFO blockmanagement.DatanodeManager: dfs.block.invalidate.limit: configured=1000, counted=60, effected=1000
2024-03-10 17:38:21,925 INFO blockmanagement.DatanodeManager: dfs.namenode.datanode.registration.ip-hostname-check=true
2024-03-10 17:38:21,927 INFO blockmanagement.BlockManager: dfs.namenode.startup.delay.block.deletion.sec is set to 000:00:00:00.000
2024-03-10 17:38:21,927 INFO blockmanagement.BlockManager: The block deletion will start around 2024 Mar 10 17:38:21
2024-03-10 17:38:21,928 INFO util.GSet: Computing capacity for map BlocksMap
2024-03-10 17:38:21,928 INFO util.GSet: VM type       = 64-bit
2024-03-10 17:38:21,930 INFO util.GSet: 2.0% max memory 1.7 GB = 34.7 MB
2024-03-10 17:38:21,930 INFO util.GSet: capacity      = 2^22 = 4194304 entries
2024-03-10 17:38:21,996 INFO blockmanagement.BlockManager: Storage policy satisfier is disabled
2024-03-10 17:38:21,996 INFO blockmanagement.BlockManager: dfs.block.access.token.enable = false
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManagerSafeMode: dfs.namenode.safemode.threshold-pct = 0.999
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManagerSafeMode: dfs.namenode.safemode.min.datanodes = 0
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManagerSafeMode: dfs.namenode.safemode.extension = 30000
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManager: defaultReplication         = 1
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManager: maxReplication             = 512
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManager: minReplication             = 1
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManager: maxReplicationStreams      = 2
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManager: redundancyRecheckInterval  = 3000ms
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManager: encryptDataTransfer        = false
2024-03-10 17:38:22,000 INFO blockmanagement.BlockManager: maxNumBlocksToLog          = 1000
2024-03-10 17:38:22,030 INFO namenode.FSDirectory: GLOBAL serial map: bits=29 maxEntries=536870911
2024-03-10 17:38:22,031 INFO namenode.FSDirectory: USER serial map: bits=24 maxEntries=16777215
2024-03-10 17:38:22,031 INFO namenode.FSDirectory: GROUP serial map: bits=24 maxEntries=16777215
2024-03-10 17:38:22,031 INFO namenode.FSDirectory: XATTR serial map: bits=24 maxEntries=16777215
2024-03-10 17:38:22,040 INFO util.GSet: Computing capacity for map INodeMap
2024-03-10 17:38:22,040 INFO util.GSet: VM type       = 64-bit
2024-03-10 17:38:22,040 INFO util.GSet: 1.0% max memory 1.7 GB = 17.4 MB
2024-03-10 17:38:22,040 INFO util.GSet: capacity      = 2^21 = 2097152 entries
2024-03-10 17:38:22,041 INFO namenode.FSDirectory: ACLs enabled? true
2024-03-10 17:38:22,041 INFO namenode.FSDirectory: POSIX ACL inheritance enabled? true
2024-03-10 17:38:22,041 INFO namenode.FSDirectory: XAttrs enabled? true
2024-03-10 17:38:22,041 INFO namenode.NameNode: Caching file names occurring more than 10 times
2024-03-10 17:38:22,044 INFO snapshot.SnapshotManager: Loaded config captureOpenFiles: false, skipCaptureAccessTimeOnlyChange: false, snapshotDiffAllowSnapRootDescendant: true, maxSnapshotLimit: 65536
2024-03-10 17:38:22,045 INFO snapshot.SnapshotManager: SkipList is disabled
2024-03-10 17:38:22,048 INFO util.GSet: Computing capacity for map cachedBlocks
2024-03-10 17:38:22,048 INFO util.GSet: VM type       = 64-bit
2024-03-10 17:38:22,049 INFO util.GSet: 0.25% max memory 1.7 GB = 4.3 MB
2024-03-10 17:38:22,049 INFO util.GSet: capacity      = 2^19 = 524288 entries
2024-03-10 17:38:22,053 INFO metrics.TopMetrics: NNTop conf: dfs.namenode.top.window.num.buckets = 10
2024-03-10 17:38:22,053 INFO metrics.TopMetrics: NNTop conf: dfs.namenode.top.num.users = 10
2024-03-10 17:38:22,053 INFO metrics.TopMetrics: NNTop conf: dfs.namenode.top.windows.minutes = 1,5,25
2024-03-10 17:38:22,056 INFO namenode.FSNamesystem: Retry cache on namenode is enabled
2024-03-10 17:38:22,056 INFO namenode.FSNamesystem: Retry cache will use 0.03 of total heap and retry cache entry expiry time is 600000 millis
2024-03-10 17:38:22,057 INFO util.GSet: Computing capacity for map NameNodeRetryCache
2024-03-10 17:38:22,057 INFO util.GSet: VM type       = 64-bit
2024-03-10 17:38:22,057 INFO util.GSet: 0.029999999329447746% max memory 1.7 GB = 533.1 KB
2024-03-10 17:38:22,057 INFO util.GSet: capacity      = 2^16 = 65536 entries
2024-03-10 17:38:22,079 INFO namenode.FSImage: Allocated new BlockPoolId: BP-544811669-172.24.43.10-1710063502074
2024-03-10 17:38:22,089 INFO common.Storage: Storage directory /tmp/hadoop-katomegumi/dfs/name has been successfully formatted.
2024-03-10 17:38:22,108 INFO namenode.FSImageFormatProtobuf: Saving image file /tmp/hadoop-katomegumi/dfs/name/current/fsimage.ckpt_0000000000000000000 using no compression
2024-03-10 17:38:22,351 INFO namenode.FSImageFormatProtobuf: Image file /tmp/hadoop-katomegumi/dfs/name/current/fsimage.ckpt_0000000000000000000 of size 405 bytes saved in 0 seconds .
2024-03-10 17:38:22,363 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid >= 0
2024-03-10 17:38:22,381 INFO namenode.FSNamesystem: Stopping services started for active state
2024-03-10 17:38:22,381 INFO namenode.FSNamesystem: Stopping services started for standby state
2024-03-10 17:38:22,385 INFO namenode.FSImage: FSImageSaver clean checkpoint: txid=0 when meet shutdown.
2024-03-10 17:38:22,386 INFO namenode.NameNode: SHUTDOWN_MSG: 
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at master/172.24.43.10
************************************************************/
[katomegumi@master hadoop-3.3.3]$ ll
total 88
drwxr-xr-x. 2 katomegumi katomegumi   203 May 10  2022 bin
drwxr-xr-x. 3 katomegumi katomegumi    20 May 10  2022 etc
drwxr-xr-x. 2 katomegumi katomegumi   106 May 10  2022 include
drwxr-xr-x. 3 katomegumi katomegumi    20 May 10  2022 lib
drwxr-xr-x. 4 katomegumi katomegumi   288 May 10  2022 libexec
-rw-rw-r--. 1 katomegumi katomegumi 23472 May 10  2022 LICENSE-binary
drwxr-xr-x. 2 katomegumi katomegumi  4096 May 10  2022 licenses-binary
-rw-rw-r--. 1 katomegumi katomegumi 15217 May 10  2022 LICENSE.txt
drwxrwxr-x. 2 katomegumi katomegumi    43 Mar 10 17:38 logs
-rw-rw-r--. 1 katomegumi katomegumi 29473 May 10  2022 NOTICE-binary
-rw-rw-r--. 1 katomegumi katomegumi  1541 Apr 22  2022 NOTICE.txt
-rw-rw-r--. 1 katomegumi katomegumi   175 Apr 22  2022 README.txt
drwxr-xr-x. 3 katomegumi katomegumi  4096 May 10  2022 sbin
drwxr-xr-x. 4 katomegumi katomegumi    31 May 10  2022 share
```

看到新增的logs文件夹了吗？说明我们配置成功了

让我们启动一下hadoop吧！

```bash
[katomegumi@master hadoop-3.3.3]$ sbin/start-dfs.sh
Starting namenodes on [master]
Starting datanodes
localhost: Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
Starting secondary namenodes [master]
[katomegumi@master hadoop-3.3.3]$ jps
47171 Jps
46647 NameNode
47034 SecondaryNameNode
46815 DataNode
```

__看！我们成功了__





### 让我们打开电脑里的浏览器输入master的ip:9870看看吧   http://172.24.43.10:9870/

成功你会看到如下页面！

![](../images/屏幕截图 2024-03-10 174642.png)
