# zookeeper的搭建

__相信跟着我前面的文档，大家的hadoop完全分布式一定顺利的搭建完毕了吧？__

让我们开始搭建zookeeper完全分布式啦！

如果配置了`ftp`服务可以直接跟着我做，如果没搭建`ftp`服务但会使用其他`ssh`连接工具将包丢进服务器的同学可以自己改一下路径即可。

1. 将`zookeeper`的安装包丢进服务器

- 我们现在打开`文件资源管理器`在上面的地址栏输入`ftp://ip地址`进入到ftp的文件夹，将安装包`zookeeper-3.4.5.tar.gz`这个安装包丢进去
- 用`ssh`工具连接上我们的服务器

2. 解压`zookeeper-3.4.5.tar.gz`

    ```bash
    [xuhaixing@master ~]$ sudo tar -xvf /var/ftp/pub/zookeeper-3.4.5.tar.gz -C /opt/software/
    [sudo] password for xuhaixing:
    # 省略了一些解压的文件输出目录
    [xuhaixing@master ~]$ sudo chown -R xuhaixing:xuhaixing /opt/software/ # 更改文件目录权限
    [xuhaixing@master ~]$ cd /opt/software/
    [xuhaixing@master software]$ ll
    total 4
    drwxr-xr-x.  3 xuhaixing xuhaixing   26 Mar 13 18:43 hadoop
    drwxr-xr-x. 10 xuhaixing xuhaixing 4096 Nov  5  2012 zookeeper-3.4.5
    ```

    

3. 配置`zookeeper`

- 复制重命名

    ```bash
    [xuhaixing@master software]$ cd zookeeper-3.4.5/
    [xuhaixing@master zookeeper-3.4.5]$ ll
    total 1488
    drwxr-xr-x.  2 xuhaixing xuhaixing     149 Apr  5 14:35 bin
    -rw-r--r--.  1 xuhaixing xuhaixing   75988 Oct  1  2012 build.xml
    -rw-r--r--.  1 xuhaixing xuhaixing   70223 Oct  1  2012 CHANGES.txt
    drwxr-xr-x.  2 xuhaixing xuhaixing      77 Apr  5 14:35 conf
    drwxr-xr-x. 10 xuhaixing xuhaixing     130 Apr  5 14:35 contrib
    drwxr-xr-x.  2 xuhaixing xuhaixing    4096 Apr  5 14:35 dist-maven
    drwxr-xr-x.  6 xuhaixing xuhaixing    4096 Apr  5 14:35 docs
    -rw-r--r--.  1 xuhaixing xuhaixing    1953 Oct  1  2012 ivysettings.xml
    -rw-r--r--.  1 xuhaixing xuhaixing    3120 Oct  1  2012 ivy.xml
    drwxr-xr-x.  4 xuhaixing xuhaixing     235 Apr  5 14:35 lib
    -rw-r--r--.  1 xuhaixing xuhaixing   11358 Oct  1  2012 LICENSE.txt
    -rw-r--r--.  1 xuhaixing xuhaixing     170 Oct  1  2012 NOTICE.txt
    -rw-r--r--.  1 xuhaixing xuhaixing    1770 Oct  1  2012 README_packaging.txt
    -rw-r--r--.  1 xuhaixing xuhaixing    1585 Oct  1  2012 README.txt
    drwxr-xr-x.  5 xuhaixing xuhaixing      47 Apr  5 14:35 recipes
    drwxr-xr-x.  8 xuhaixing xuhaixing     154 Apr  5 14:35 src
    -rw-r--r--.  1 xuhaixing xuhaixing 1315806 Nov  5  2012 zookeeper-3.4.5.jar
    -rw-r--r--.  1 xuhaixing xuhaixing     833 Nov  5  2012 zookeeper-3.4.5.jar.asc
    -rw-r--r--.  1 xuhaixing xuhaixing      33 Nov  5  2012 zookeeper-3.4.5.jar.md5
    -rw-r--r--.  1 xuhaixing xuhaixing      41 Nov  5  2012 zookeeper-3.4.5.jar.sha1
    [xuhaixing@master zookeeper-3.4.5]$ \
    > cd conf
    [xuhaixing@master conf]$ ll
    total 12
    -rw-r--r--. 1 xuhaixing xuhaixing  535 Oct  1  2012 configuration.xsl
    -rw-r--r--. 1 xuhaixing xuhaixing 2161 Oct  1  2012 log4j.properties
    -rw-r--r--. 1 xuhaixing xuhaixing  808 Oct  1  2012 zoo_sample.cfg
    [xuhaixing@master conf]$ cp ./zoo_sample.cfg zoo.cfg
    [xuhaixing@master conf]$ ll
    total 16
    -rw-r--r--. 1 xuhaixing xuhaixing  535 Oct  1  2012 configuration.xsl
    -rw-r--r--. 1 xuhaixing xuhaixing 2161 Oct  1  2012 log4j.properties
    -rw-r--r--. 1 xuhaixing xuhaixing  808 Apr  5 14:43 zoo.cfg
    -rw-r--r--. 1 xuhaixing xuhaixing  808 Oct  1  2012 zoo_sample.cfg
    [xuhaixing@master conf]$ vim zoo.cfg
    ```

- 编辑`zoo.cfg`

    ```sh
    # The number of milliseconds of each tick
    tickTime=2000
    # The number of ticks that the initial
    # synchronization phase can take
    initLimit=10
    # The number of ticks that can pass between
    # sending a request and getting an acknowledgement
    syncLimit=5
    # the directory where the snapshot is stored.
    # do not use /tmp for storage, /tmp here is just
    # example sakes.
    dataDir=/opt/software/zookeeper-3.4.5/zkData
    # the port at which the clients will connect
    clientPort=2181
    #
    # Be sure to read the maintenance section of the
    # administrator guide before turning on autopurge.
    #
    # http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
    #
    # The number of snapshots to retain in dataDir
    #autopurge.snapRetainCount=3
    # Purge task interval in hours
    # Set to "0" to disable auto purge feature
    #autopurge.purgeInterval=1
    server.1=200.200.200.10:2888:3888
    server.2=200.200.200.11:2888:3888
    server.3=200.200.200.12:2888:3888 
    ```

- 手动创建zkData,myid

    ```bash
    [xuhaixing@master conf]$ cd ..
    [xuhaixing@master zookeeper-3.4.5]$ ll
    total 1488
    drwxr-xr-x.  2 xuhaixing xuhaixing     149 Apr  5 14:35 bin
    -rw-r--r--.  1 xuhaixing xuhaixing   75988 Oct  1  2012 build.xml
    -rw-r--r--.  1 xuhaixing xuhaixing   70223 Oct  1  2012 CHANGES.txt
    drwxr-xr-x.  2 xuhaixing xuhaixing      92 Apr  5 14:49 conf
    drwxr-xr-x. 10 xuhaixing xuhaixing     130 Apr  5 14:35 contrib
    drwxr-xr-x.  2 xuhaixing xuhaixing    4096 Apr  5 14:35 dist-maven
    drwxr-xr-x.  6 xuhaixing xuhaixing    4096 Apr  5 14:35 docs
    -rw-r--r--.  1 xuhaixing xuhaixing    1953 Oct  1  2012 ivysettings.xml
    -rw-r--r--.  1 xuhaixing xuhaixing    3120 Oct  1  2012 ivy.xml
    drwxr-xr-x.  4 xuhaixing xuhaixing     235 Apr  5 14:35 lib
    -rw-r--r--.  1 xuhaixing xuhaixing   11358 Oct  1  2012 LICENSE.txt
    -rw-r--r--.  1 xuhaixing xuhaixing     170 Oct  1  2012 NOTICE.txt
    -rw-r--r--.  1 xuhaixing xuhaixing    1770 Oct  1  2012 README_packaging.txt
    -rw-r--r--.  1 xuhaixing xuhaixing    1585 Oct  1  2012 README.txt
    drwxr-xr-x.  5 xuhaixing xuhaixing      47 Apr  5 14:35 recipes
    drwxr-xr-x.  8 xuhaixing xuhaixing     154 Apr  5 14:35 src
    -rw-r--r--.  1 xuhaixing xuhaixing 1315806 Nov  5  2012 zookeeper-3.4.5.jar
    -rw-r--r--.  1 xuhaixing xuhaixing     833 Nov  5  2012 zookeeper-3.4.5.jar.asc
    -rw-r--r--.  1 xuhaixing xuhaixing      33 Nov  5  2012 zookeeper-3.4.5.jar.md5
    -rw-r--r--.  1 xuhaixing xuhaixing      41 Nov  5  2012 zookeeper-3.4.5.jar.sha1
    [xuhaixing@master zookeeper-3.4.5]$ mkdir zkData
    [xuhaixing@master zookeeper-3.4.5]$ touch zkData/myid
    ```

- 分发zookeeper-3.4.5到`slave1`和`slave2`

    ```bash
    [xuhaixing@master zookeeper-3.4.5]$ scp -r /opt/software/zookeeper-3.4.5/ xuhaixing@slave1:/opt/software/
    # 省略了一些传输的文件目录信息
    [xuhaixing@master zookeeper-3.4.5]$ scp -r /opt/software/zookeeper-3.4.5/ xuhaixing@slave2:/opt/software/
    # 同上
    ```

- 配置`myid`

    - 我们在master主机上

        ```bash
        [xuhaixing@master zookeeper-3.4.5]$ vim zkData/myid
        ```

        `myid`

        ```tex
        1
        ```

    - 我们在slave1主机上

        ```bash
        [xuhaixing@slave1 ~]$ vim /opt/software/zookeeper-3.4.5/zkData/myid
        ```

        `myid`

        ```tex
        2
        ```

    - 我们在slave2主机上

        ```bash
        [xuhaixing@slave2 ~]$ vim /opt/software/zookeeper-3.4.5/zkData/myid
        ```

        `myid`

        ```tex
        3
        ```



4. 启动zookeeper集群

- 在master主机上(第一次启动一定会失败，因为只启动了一个机器，zookeeper无法进行内部选举，启动的机器必须大于1，如果`hadoop`集群没启动的记得启动一下`hadoop`集群)

    ```bash
    [xuhaixing@master zookeeper-3.4.5]$ bin/zkServer.sh start
    JMX enabled by default
    Using config: /opt/software/zookeeper-3.4.5/bin/../conf/zoo.cfg
    Starting zookeeper ... STARTED
    [xuhaixing@master zookeeper-3.4.5]$ bin/zkServer.sh status
    JMX enabled by default
    Using config: /opt/software/zookeeper-3.4.5/bin/../conf/zoo.cfg
    Error contacting service. It is probably not running.
    ```

- 在`slave1`主机上

    ```bash
    [xuhaixing@slave1 ~]$ /opt/software/zookeeper-3.4.5/bin/zkServer.sh start
    JMX enabled by default
    Using config: /opt/software/zookeeper-3.4.5/bin/../conf/zoo.cfg
    Starting zookeeper ... STARTED
    [xuhaixing@slave1 ~]$ /opt/software/zookeeper-3.4.5/bin/zkServer.sh status
    JMX enabled by default
    Using config: /opt/software/zookeeper-3.4.5/bin/../conf/zoo.cfg
    Mode: leader
    ```

    再回到master主机上

    ```bash
    [xuhaixing@master zookeeper-3.4.5]$ bin/zkServer.sh status
    JMX enabled by default
    Using config: /opt/software/zookeeper-3.4.5/bin/../conf/zoo.cfg
    Mode: follower
    ```

    这下就成功启动了！

- 在`slave2`主机上

    ```bash
    [xuhaixing@slave2 ~]$ /opt/software/zookeeper-3.4.5/bin/zkServer.sh start
    JMX enabled by default
    Using config: /opt/software/zookeeper-3.4.5/bin/../conf/zoo.cfg
    Starting zookeeper ... STARTED
    [xuhaixing@slave2 ~]$ /opt/software/zookeeper-3.4.5/bin/zkServer.sh status
    JMX enabled by default
    Using config: /opt/software/zookeeper-3.4.5/bin/../conf/zoo.cfg
    Mode: follower
    ```

    

最后我们使用`jps`命令检查一下zookeeper是否真的启动完毕

master:
```bash
[xuhaixing@master zookeeper-3.4.5]$ jps
5393 JobHistoryServer
4770 NameNode
5250 NodeManager
4947 DataNode
7939 Jps
7757 QuorumPeerMain # 这个就是zookeeper的进程
```

slave1:

```bash
[xuhaixing@slave1 ~]$ jps
3108 NodeManager
5544 Jps
2971 ResourceManager
5451 QuorumPeerMain
2781 DataNode
```

slave2:

```bash
[xuhaixing@slave2 ~]$ jps
5345 Jps
2899 SecondaryNameNode
5254 QuorumPeerMain
2775 DataNode
3006 NodeManager
```

<hr />

好啦！到这我们的zookeeper也就搭建完毕了，你们也成功了吗？:happy:

