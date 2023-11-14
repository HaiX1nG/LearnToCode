# 编写第一个shell script

## 1.创建sh01.sh文件

```shell
[root@Server02 ~]# cd /root
[root@Server02 ~]# ls
公共  视频  文档  音乐  anaconda-ks.cfg       scripts
模板  图片  下载  桌面  initial-setup-ks.cfg
[root@Server02 ~]# cd ;mkdir /root/scripts; cd/root/scripts
[root@Server02 ~]# vim sh01.sh
```

## 2. 进入sh01.sh脚本文件中

编写shell脚本:

```shell
#!/bin/bash
#2023/11/14
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
echo -e "Hello World! \a \n"
exit 0
~ 
~
~
~
~
```

保存退出

## 3.运行shell脚本

```shell
[root@Server02 ~]# sh sh01.sh
Hello World!  

```

成功输出`Hello World!`





这样我们的第一个shell脚本就完成啦!