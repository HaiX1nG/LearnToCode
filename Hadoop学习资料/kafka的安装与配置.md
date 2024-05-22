# kafka的安装与配置

## ‼️重要通知

​	由于国内网络环境问题，为了能更好的观看文档，我更改了仓库地址到`gitee`解决了由于网络问题导致无法加载文档中图片的问题

<mark>我的文档仓库地址：</mark>

> https://gitee.com/haix1ng/LearnToCode

## 安装准备

我们先准备好`kafka_2.11-2.1.1.tgz`压缩包，如果没有请点击下面的链接<mark>自动下载</mark>。

> https://archive.apache.org/dist/kafka/2.1.1/kafka_2.11-2.1.1.tgz

我们将这个压缩包丢进`ftp`中的`pub`文件夹。

## 1. 解压kafka

```bash
[xuhaixing@master ~]$ sudo tar -xvf /var/ftp/pub/kafka_2.11-2.1.1.tgz -C /opt/software/
[sudo] password for xuhaixing:
[xuhaixing@master ~]$ mv /opt/software/kafka_2.11-2.1.1 /opt/software/kafka-2.1.1
[xuhaixing@master ~]$ chown -R xuhaixing:xuhaixing /opt/software/kafka-2.1.1
```

## 2.配置kafka

```bash
[xuhaixing@master ~]$ cd /opt/software/kafka-2.1.1/config/
[xuhaixing@master config]$ ll
total 68
-rw-r--r--. 1 root root  906 Feb  9  2019 connect-console-sink.properties
-rw-r--r--. 1 root root  909 Feb  9  2019 connect-console-source.properties
-rw-r--r--. 1 root root 5321 Feb  9  2019 connect-distributed.properties
-rw-r--r--. 1 root root  883 Feb  9  2019 connect-file-sink.properties
-rw-r--r--. 1 root root  881 Feb  9  2019 connect-file-source.properties
-rw-r--r--. 1 root root 1111 Feb  9  2019 connect-log4j.properties
-rw-r--r--. 1 root root 2262 Feb  9  2019 connect-standalone.properties
-rw-r--r--. 1 root root 1221 Feb  9  2019 consumer.properties
-rw-r--r--. 1 root root 4727 Feb  9  2019 log4j.properties
-rw-r--r--. 1 root root 1925 Feb  9  2019 producer.properties
-rw-r--r--. 1 root root 6851 Feb  9  2019 server.properties
-rw-r--r--. 1 root root 1032 Feb  9  2019 tools-log4j.properties
-rw-r--r--. 1 root root 1169 Feb  9  2019 trogdor.conf
-rw-r--r--. 1 root root 1023 Feb  9  2019 zookeeper.properties
[xuhaixing@master config]$ vim server.properties
```

在server.properties添加如下内容：

```properties
broker.id=1
host.name=master
zookeeper.connect=master:2181,slave1:2181,slave2:2181
# 该文件里还有一个zookeeper.connect的设置项，请将它注释掉
```

分发kafka：

```bash
[xuhaixing@master config]$ scp -r /opt/software/kafka-2.1.1 xuhaixing@slave1:/opt/software/
# 省略分发的内容
[xuhaixing@master config]$ scp -r /opt/software/kafka-2.1.1 xuhaixing@slave2:/opt/software/
# 省略分发的内容
```

在slave1中的server.properties添加如下内容：

```properties
broker.id=2
host.name=slave1
zookeeper.connect=master:2181,slave1:2181,slave2:2181
# 该文件里还有一个zookeeper.connect的设置项，请将它注释掉
```

在slave2中的server.properties添加如下内容：

```properties
broker.id=3
host.name=slave2
zookeeper.connect=master:2181,slave1:2181,slave2:2181
# 该文件里还有一个zookeeper.connect的设置项，请将它注释掉
```

<hr />

## 配置kafka环境变量

```bash
[xuhaixing@master config]$ vim ~/.bash_profile
```

```bash
export HADOOP_HOME=/opt/software/hadoop/hadoop-3.3.3
export ZOOKEEPER_HOME=/opt/software/zookeeper-3.4.5
export SPARK_HOME=/opt/software/spark-3.2
export HIVE_HOME=/opt/software/hive-3.1
export HBASE_HOME=/opt/software/hbase-2.2
export AZKABAN_HOME=/opt/software/azkaban
export KAFKA_HOME=/opt/software/kafka-2.1.1 # 添加了这个

export PATH=$HOME/.local/bin:$HOME/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${ZOOKEEPER_HOME}/bin:${SPARK_HOME}/bin:${SPARK_HOME}/sbin:${HIVE_HOME}/bin:${HBASE_HOME}/bin:${AZKABAN_HOME}/azkaban-executor/bin:${AZKABAN_HOME}/azkaban-web/bin:${KAFKA_HOME}/bin:$PATH # 这个

export PATH
```

```bash
[xuhaixing@master config]$ source ~/.bash_profile
```

分发：

```bash
[xuhaixing@master config]$ scp ~/.bash_profile xuhaixing@slave1:/home/xuhaixing/
.bash_profile                                                100%  736     1.2MB/s   00:00
[xuhaixing@master config]$ scp ~/.bash_profile xuhaixing@slave2:/home/xuhaixing/
.bash_profile                                                100%  736     1.1MB/s   00:00
```

我们跳转到slave1、slave2执行这个命令

> source ~/.bash_profile

<hr />

## 启动Kafka

在三台机子里分别执行这些命令：

```bash
kafka-server-start.sh
$KAFKA_HOME/bin/kafka-server-start.sh -daemon $KAFKA_HOME/config/server.properties
```

如果三台机子都出现kafka进程就启动成功了！

`master`:

```bash
[xuhaixing@master ~]$ jps
3200 JobHistoryServer
3968 RunJar
2530 NameNode
54532 Kafka # Look this!
54199 QuorumPeerMain
2715 DataNode
54603 Jps
3021 NodeManager
4110 RunJar
50750 Master
52495 AzkabanWebServer
```

`slave1`:

```bash
[xuhaixing@slave1 ~]$ jps
2273 DataNode
2465 ResourceManager
39185 Worker
41794 Jps
41399 QuorumPeerMain
2607 NodeManager
41727 Kafka # Look this!
```

`slave2`:

```bash
[xuhaixing@slave2 ~]$ jps
46723 QuorumPeerMain
2518 NodeManager
44264 Worker
2281 DataNode
47049 Kafka # Look this!
47116 Jps
2382 SecondaryNameNode
```

<mark>**如果没有启动成功请检查zookeeper中的conf/zoo.cfg和zkData/myid**</mark>
