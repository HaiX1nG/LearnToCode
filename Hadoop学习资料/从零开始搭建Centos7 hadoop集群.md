# 从零开始搭建CentOS7 `hadoop`集群

<hr />

前言：鉴于班级里很多同学虚拟机搭建基础薄弱，此次教程有我来带领大家开始搭建集群所需要的环境！

- 本节课程有N个环节
    - 1. 虚拟机的安装
        2. 初始用户的密码修改
        3. 配置网络，配置网卡，配置路由，修改主机名称完成远程`ssh`远程链接
        4. 克隆奴隶机`slave1`、`slave2`
        5. 同步阿里时区，永久关闭防火墙
        6. 配置ftp
        7. 配置`jdk`
        8. 配置`docker`
        9. 配置`hadoop`环境
        10. `hadoop`伪分布式的实现
        11. `hadoop`完全分布式的实现

还请同学们耐心跟着教程一步一步搭建！:smile:

<hr />

## 一、 虚拟机的安装

我们在这里使用的镜像文件为`CentOS-7-x86_64-DVD-2009.iso`如果没有该镜像文件的同学可以去找有该镜像文件的同学用U盘传输，如果都没有我们就去阿里云等国内镜像获取镜像文件

阿里云CentOS7的镜像仓库地址：

> https://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/

<hr />

ok!拿到镜像文件我们就开始第一步：

1. 打开`VMware Workstation Pro`新建虚拟机：

    ![](../images/2.png)

![](../images/3.png)

![](../images/4.png)

![](../images/5.png)

![](../images/6.png)

![](../images/7.png)

__记住这里最好规范命名一下，方便区分__

![](../images/8.png)

__这里的cpu配置根据自己的电脑配置来设置__

![](../images/9.png)

__这块的运行内存根据自己电脑的运行内存大小来设置，记住给自己的宿主机留一些运行内存，不然会蓝屏死机！__

剩下的四步直接默认下一步就可以了！

![](../images/10.png)

__这里最好不要小于50GB__

然后直接一直点下一步直到点击完成！

![](../images/11.png)

![](../images/12.png)

配置完毕这些后我们启动虚拟机来到安装页面！

![](../images/13.png)

__无论你什么英语水平都一定要选英语，不然小心被骂！__

![](../images/14.png)

__这里设置成shanghai__

![](../images/15.png)

![](../images/16.png)

这里一定要勾上`Server with GUI`，不然就没有图形页面了

![](../images/17.png)

![](../images/18.png)

![](../images/19.png)

![](../images/20.png)

![](../images/21.png)

![](../images/22.png)

![](../images/23.png)

![](../images/24.png)

__这里把root密码全都设置成123456，不准是其他密码！__

![](../images/25.png)

一切加载完毕后点击`Reboot`重启

<hr />

### 好了同学们，虚拟机安装完了，我们来配置一下里面的设置吧！

![](../images/26.png)

我们把这里的协议勾选上然后点击右下角的按钮！

开机进入页面后一路next

![](../images/27.png)

![](../images/28.png)

__这里一定一定要设置成自己的真实姓名，不然老师的怒火你们会品尝到的！__

![](../images/29.png)

__这里设置难一点，但是一定要记得，我们一会儿再改！__

<hr />

## 二、 初始用户密码的修改

好的，让我们来到下一步，我们来修改一下初始用户的密码为`123456`

![](../images/30.png)

```bash
[xuhaixing@localhost ~]$ sudo passwd xuhaixing

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for xuhaixing: # 这个是你刚才设置的初始密码
Changing password for user xuhaixing.
New password: # 这里是输入新密码，下面给的提示不用管
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: # 二次确认密码
passwd: all authentication tokens updated successfully.
[xuhaixing@localhost ~]$ 
```

到这里我们的初始用户的密码就修改成功了！

<hr />

## 三、 配置网络，配置网卡，配置路由，修改主机名称完成远程`ssh`远程链接

首先我们打开`VMware Workstation Pro`的虚拟网卡配置，我这边是以管理员身份启动的`VMware Workstation Pro`所以修改的时候不用二次赋权，__没有赋权的记得赋权才能修改！__

![](../images/31.png)

![](../images/32.png)

我们可以看到总共有三个网卡分别为`VMnet0`,`VMnet1`,`VMnet8`,如果没有这三张网卡的人可以点击`添加网络`创建所缺少的网卡，同时确保`VMnet0`处于桥接模式，外部链接处于自动桥接；确保`VMnet1`处于仅主机模式，并且主机连接处于__已连接__状态；确保`VMnet8`处于NAT模式并且主机连接显示__已连接__



!*重点:子网IP的网段必须与NAT设置中的网关IP处于同一网段下！这里的子网IP和网关IP可以随便设置，但前提是你能记得住！

如我这里的子网IP为`200.200.200.0`，所以我的网关IP应该是`200.200.200.2`

![](../images/33.png)

### 如果这里也跟上了的同学可以跟我进行下一步操作了:tada:

打开`bash`

1. 配置`ifcfg-ens33`

```bash
[xuhaixing@localhost ~]$ sudo vim /etc/sysconfig/network-scripts/ifcfg-ens33 
[sudo] password for xuhaixing: 
```

进入到`ifcfg-ens33`文件内按i进行编辑操作：

请修改下面注释了的几个选项，没有的就手动添加！__千万不要复制__

```sh
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static # 这里设置为静态
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=646f4d52-6803-4d62-a608-0634b41d34d7
DEVICE=ens33
ONBOOT=yes # 这里设置为yes
IPADDR=200.200.200.10 # 这是你虚拟机的ip
GATEWAY=200.200.200.2 # 这是你虚拟机的网关ip
PREFIX=24 # 这是子网掩码
DNS1=202.101.224.68 # 这是南昌市的DNS
DNS2=202.101.224.69 # 同上
```

接下来重启network，尝试ping一下百度网址，看看是否能连接外网

```bash
[xuhaixing@localhost ~]$ sudo systemctl restart network
[xuhaixing@localhost ~]$ ping www.baidu.com
PING www.a.shifen.com (183.2.172.42) 56(84) bytes of data.
64 bytes from 183.2.172.42 (183.2.172.42): icmp_seq=1 ttl=128 time=19.9 ms
64 bytes from 183.2.172.42 (183.2.172.42): icmp_seq=2 ttl=128 time=17.8 ms
64 bytes from 183.2.172.42 (183.2.172.42): icmp_seq=3 ttl=128 time=19.8 ms
64 bytes from 183.2.172.42 (183.2.172.42): icmp_seq=4 ttl=128 time=20.2 ms
^C
--- www.a.shifen.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 17.864/19.487/20.228/0.956 ms
```

OK!配置成功，我们来用远程链接工具通过ssh远程连接一下我们的这台虚拟机

我们可以用`cmd`，`powershell`， `finalshell`， `xshell`， `MobaXterm`来连接我们的虚拟机，我们这用`finalshell`来演示！

![](../images/34.png)

__名称__这里我们给它命名为XX系统-XX角色

例如我这里CentOS7-master

__主机__这里就是你刚才配置的IPADDR里的ip

例如我这里的200.200.200.10

端口默认即可

用户名就是你的初始用户名

我这里是`xuhaixing`

密码是`123456`

![](../images/35.png)

连接成功！

现在我们来修改主机名称！

```bash
[xuhaixing@localhost ~]$ sudo vim /etc/hostname
[sudo] password for xuhaixing:
```

进入文件后按i进行编辑，删除掉原有的内容然后添加master即可保存退出

```sh
master
~
~
~
~
```

然后我们重启虚拟机，主机名称就能成功修改了

```bash
[xuhaixing@localhost ~]$ reboot # 这是重启的命令
==== AUTHENTICATING FOR org.freedesktop.login1.reboot ===
Authentication is required for rebooting the system.
Authenticating as: xuhaixing
Password:
```

看重启后变为了`[xuhaixing@master ~]$`

以前是`[xuhaixing@localhost ~]$`

这个时候我们开始克隆虚拟机

<hr />

## 四、 克隆奴隶机slave1、slave2

我们先__保存一个当前状态的快照__，以防万一！

我们先将虚拟机关机，不然无法克隆！

```bash
[xuhaixing@master ~]$ poweroff # 这是关机的命令
==== AUTHENTICATING FOR org.freedesktop.login1.power-off ===
Authentication is required for powering off the system.
Authenticating as: xuhaixing
Password:
==== AUTHENTICATION COMPLETE ===

Remote side unexpectedly closed network connection

```

![](../images/36.png)

![](../images/37.png)

![](../images/38.png)

![](../images/39.png)

这样我们的slave1就创建好了，slave2也是同样的方法创建，这里我就不演示了:angel:

好！我们同时启动slave1，和slave2

现在我们还无法远程ssh连接slave1和slave2，我们进入图形页面打开bash配置一下刚才的IPADDR

这里拿slave1做演示，slave2也是一样的步骤

```bash
[xuhaixing@master ~]$ sudo vim /etc/sysconfig/network-scripts/ifcfg-ens33 
[sudo] password for xuhaixing: 

```

```sh
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=646f4d52-6803-4d62-a608-0634b41d34d7
DEVICE=ens33
ONBOOT=yes
IPADDR=200.200.200.11 # 只要修改这里
GATEWAY=200.200.200.2
PREFIX=24
DNS1=202.101.224.68
DNS2=202.101.224.69

```

我的master的ip为200.200.200.10

那我的slave1就设置为200.200.200.11

那我的slave2就设置为200.200.200.12（这个我就不演示了，步骤大差不差）

```bash
[xuhaixing@master ~]$ sudo systemctl restart network
[xuhaixing@master ~]$ ping baidu.com
PING baidu.com (39.156.66.10) 56(84) bytes of data.
64 bytes from 39.156.66.10 (39.156.66.10): icmp_seq=1 ttl=128 time=33.0 ms
64 bytes from 39.156.66.10 (39.156.66.10): icmp_seq=2 ttl=128 time=34.2 ms
```

ok!

接下来我们修改一下slave1的主机名称，slave2也是大致的步骤我就不演示了，这里拿slave1演示

```bash
[xuhaixing@master ~]$ sudo vim /etc/hostname
[sudo] password for xuhaixing:
```

```sh
slave1
~
~
~
```

```bash
[xuhaixing@master ~]$ reboot # 这是重启的命令
==== AUTHENTICATING FOR org.freedesktop.login1.reboot ===
Authentication is required for rebooting the system.
Authenticating as: xuhaixing
Password:
```

看重启后变为了`[xuhaixing@slave1 ~]$`

以前是`[xuhaixing@master ~]$`

<hr />

### 这个时候我们配置并分发路由

进入master

```bash
[xuhaixing@master ~]$ sudo vim /etc/hosts
[sudo] password for xuhaixing:

```

```sh
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
200.200.200.10 master
200.200.200.11 slave1
200.200.200.12 slave2
```

设置成这样保存并退出

```bash
[xuhaixing@master ~]$ ping slave1
PING slave1 (200.200.200.11) 56(84) bytes of data.
64 bytes from slave1 (200.200.200.11): icmp_seq=1 ttl=64 time=0.332 ms
64 bytes from slave1 (200.200.200.11): icmp_seq=2 ttl=64 time=0.340 ms
64 bytes from slave1 (200.200.200.11): icmp_seq=3 ttl=64 time=0.393 ms
^C
--- slave1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.332/0.355/0.393/0.027 ms
[xuhaixing@master ~]$ ping slave2
PING slave2 (200.200.200.12) 56(84) bytes of data.
64 bytes from slave2 (200.200.200.12): icmp_seq=1 ttl=64 time=0.337 ms
64 bytes from slave2 (200.200.200.12): icmp_seq=2 ttl=64 time=0.258 ms
64 bytes from slave2 (200.200.200.12): icmp_seq=3 ttl=64 time=0.297 ms
^C
--- slave2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.258/0.297/0.337/0.035 ms

```

都能ping通，接下来就要分发路由到slave1、slave2中了

```bash
[xuhaixing@master ~]$ sudo scp /etc/hosts root@slave1:/etc/
The authenticity of host 'slave1 (200.200.200.11)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'slave1,200.200.200.11' (ECDSA) to the list of known hosts.
root@slave1's password:
hosts                                                                                                             100%  224   319.1KB/s   00:00
[xuhaixing@master ~]$ sudo scp /etc/hosts root@slave2:/etc/
The authenticity of host 'slave2 (200.200.200.12)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'slave2,200.200.200.12' (ECDSA) to the list of known hosts.
root@slave2's password:
hosts                                                                                                             100%  224   338.7KB/s   00:00
[xuhaixing@master ~]$

```

分发完成，我们去slave1 和 slave2 ping一下master试试

```bash

[xuhaixing@slave1 ~]$ ping master
PING master (200.200.200.10) 56(84) bytes of data.
64 bytes from master (200.200.200.10): icmp_seq=1 ttl=64 time=0.213 ms
64 bytes from master (200.200.200.10): icmp_seq=2 ttl=64 time=0.242 ms
^C
--- master ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.213/0.227/0.242/0.020 ms
[xuhaixing@slave1 ~]$

```

```bash

[xuhaixing@slave2 ~]$ ping master
PING master (200.200.200.10) 56(84) bytes of data.
64 bytes from master (200.200.200.10): icmp_seq=1 ttl=64 time=0.213 ms
64 bytes from master (200.200.200.10): icmp_seq=2 ttl=64 time=0.242 ms
^C
--- master ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.213/0.227/0.242/0.020 ms
[xuhaixing@slave2 ~]$

```

路由配置完毕！

<hr />

## 五、 同步阿里时区，永久关闭防火墙

三台机子的命令是一样的，分别去三个机子执行这些命令

```bash

[xuhaixing@master ~]$ sudo ntpdate ntp1.aliyun.com
[sudo] password for xuhaixing:
13 Mar 17:25:54 ntpdate[2504]: step time server 120.25.115.20 offset -28800.355854 sec
[xuhaixing@master ~]$ date
Wed Mar 13 17:25:59 CST 2024
[xuhaixing@master ~]$ sudo systemctl stop firewalld.service
[xuhaixing@master ~]$ sudo systemctl disable firewalld.service
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
[xuhaixing@master ~]$ sudo systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)

Mar 14 01:05:09 master systemd[1]: Starting firewalld - dynamic firewall daemon...
Mar 14 01:05:09 master systemd[1]: Started firewalld - dynamic firewall daemon.
Mar 14 01:05:09 master firewalld[851]: WARNING: AllowZoneDrifting is enabled. This is considered an insecure configuration option. It wil... it now.
Mar 13 17:26:58 master systemd[1]: Stopping firewalld - dynamic firewall daemon...
Mar 13 17:26:59 master systemd[1]: Stopped firewalld - dynamic firewall daemon.
Hint: Some lines were ellipsized, use -l to show in full.

```

这样就完成了！

<hr />

## 六、 配置ftp

1. 安装vsftpd服务

    ```bash
    [xuhaixing@master ~]$ rpm -q vsftpd
    package vsftpd is not installed
    [xuhaixing@master ~]$ sudo yum clean all
    [sudo] password for xuhaixing:
    Loaded plugins: fastestmirror, langpacks
    Cleaning repos: base extras updates
    Cleaning up list of fastest mirrors
    [xuhaixing@master ~]$ sudo yum install vsftpd -y
    Loaded plugins: fastestmirror, langpacks
    Determining fastest mirrors
     * base: mirrors.ustc.edu.cn
     * extras: mirrors.huaweicloud.com
     * updates: mirrors.163.com
    base                                                                                                                         | 3.6 kB  00:00:00
    extras                                                                                                                       | 2.9 kB  00:00:00
    updates                                                                                                                      | 2.9 kB  00:00:00
    (1/4): base/7/x86_64/group_gz                                                                                                | 153 kB  00:00:00
    (2/4): extras/7/x86_64/primary_db                                                                                            | 250 kB  00:00:00
    (3/4): updates/7/x86_64/primary_db                                                                                           |  25 MB  00:00:34
    (4/4): base/7/x86_64/primary_db                                                                                              | 6.1 MB  00:03:21
    Resolving Dependencies
    --> Running transaction check
    ---> Package vsftpd.x86_64 0:3.0.2-29.el7_9 will be installed
    --> Finished Dependency Resolution
    
    Dependencies Resolved
    
    ====================================================================================================================================================
     Package                          Arch                             Version                                  Repository                         Size
    ====================================================================================================================================================
    Installing:
     vsftpd                           x86_64                           3.0.2-29.el7_9                           updates                           173 k
    
    Transaction Summary
    ====================================================================================================================================================
    Install  1 Package
    
    Total download size: 173 k
    Installed size: 353 k
    Downloading packages:
    warning: /var/cache/yum/x86_64/7/updates/packages/vsftpd-3.0.2-29.el7_9.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY--:-- ETA
    Public key for vsftpd-3.0.2-29.el7_9.x86_64.rpm is not installed
    vsftpd-3.0.2-29.el7_9.x86_64.rpm                                                                                             | 173 kB  00:00:06
    Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    Importing GPG key 0xF4A80EB5:
     Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
     Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
     Package    : centos-release-7-9.2009.0.el7.centos.x86_64 (@anaconda)
     From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
      Installing : vsftpd-3.0.2-29.el7_9.x86_64                                                                                                     1/1
      Verifying  : vsftpd-3.0.2-29.el7_9.x86_64                                                                                                     1/1
    
    Installed:
      vsftpd.x86_64 0:3.0.2-29.el7_9
    
    Complete!
    [xuhaixing@master ~]$ sudo yum install ftp -y
    Loaded plugins: fastestmirror, langpacks
    Loading mirror speeds from cached hostfile
     * base: mirrors.ustc.edu.cn
     * extras: mirrors.huaweicloud.com
     * updates: mirrors.163.com
    Resolving Dependencies
    --> Running transaction check
    ---> Package ftp.x86_64 0:0.17-67.el7 will be installed
    --> Finished Dependency Resolution
    
    Dependencies Resolved
    
    ====================================================================================================================================================
     Package                         Arch                               Version                                  Repository                        Size
    ====================================================================================================================================================
    Installing:
     ftp                             x86_64                             0.17-67.el7                              base                              61 k
    
    Transaction Summary
    ====================================================================================================================================================
    Install  1 Package
    
    Total download size: 61 k
    Installed size: 96 k
    Downloading packages:
    ftp-0.17-67.el7.x86_64.rpm                                                                                                   |  61 kB  00:00:00
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
      Installing : ftp-0.17-67.el7.x86_64                                                                                                           1/1
      Verifying  : ftp-0.17-67.el7.x86_64                                                                                                           1/1
    
    Installed:
      ftp.x86_64 0:0.17-67.el7
    
    Complete!
    [xuhaixing@master ~]$ rpm -qa|grep vsftpd
    vsftpd-3.0.2-29.el7_9.x86_64
    ```

2. 配置vsftpd随系统自启，开放SELinux

    ```bash
    [xuhaixing@master ~]$ sudo systemctl status vsftpd
    ● vsftpd.service - Vsftpd ftp daemon
       Loaded: loaded (/usr/lib/systemd/system/vsftpd.service; disabled; vendor preset: disabled)
       Active: inactive (dead)
    [xuhaixing@master ~]$ sudo systemctl start vsftpd
    [xuhaixing@master ~]$ sudo systemctl status vsftpd
    ● vsftpd.service - Vsftpd ftp daemon
       Loaded: loaded (/usr/lib/systemd/system/vsftpd.service; disabled; vendor preset: disabled)
       Active: active (running) since Wed 2024-03-13 17:42:55 CST; 2s ago
      Process: 2898 ExecStart=/usr/sbin/vsftpd /etc/vsftpd/vsftpd.conf (code=exited, status=0/SUCCESS)
     Main PID: 2899 (vsftpd)
        Tasks: 1
       CGroup: /system.slice/vsftpd.service
               └─2899 /usr/sbin/vsftpd /etc/vsftpd/vsftpd.conf
    
    Mar 13 17:42:55 master systemd[1]: Starting Vsftpd ftp daemon...
    Mar 13 17:42:55 master systemd[1]: Started Vsftpd ftp daemon.
    [xuhaixing@master ~]$ sudo systemctl enable vsftpd
    Created symlink from /etc/systemd/system/multi-user.target.wants/vsftpd.service to /usr/lib/systemd/system/vsftpd.service.
    [xuhaixing@master ~]$ sudo setsebool -P ftpd_full_access=on
    [xuhaixing@master ~]$ sudo systemctl restart vsftpd
    ```

3. 配置vsftpd的配置文件

    ```bash
    [xuhaixing@master ~]$ sudo mv /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf.bak
    [sudo] password for xuhaixing:
    [xuhaixing@master ~]$ su root
    Password:
    [root@master xuhaixing]# grep -v "#" /etc/vsftpd/vsftpd.conf.bak > /etc/vsftpd/vsftpd.conf
    [root@master xuhaixing]# su xuhaixing
    [xuhaixing@master ~]$ cat /etc/vsftpd/vsftpd.conf -n
         1  anonymous_enable=YES
         2  local_enable=YES
         3  write_enable=YES
         4  local_umask=022
         5  dirmessage_enable=YES
         6  xferlog_enable=YES
         7  connect_from_port_20=YES
         8  xferlog_std_format=YES
         9  listen=NO
        10  listen_ipv6=YES
        11
        12  pam_service_name=vsftpd
        13  userlist_enable=YES
        14  tcp_wrappers=YES
    [xuhaixing@master ~]$ sudo vim /etc/vsftpd/vsftpd.conf
    ```

```sh
anonymous_enable=YES
# 允许匿名用户登录
anon_root=/var/ftp
# 设置匿名用户的根目录为/var/ftp
anon_upload_enable=YES
# 允许匿名用户上传文件
anon_mkdir_write_enable=YES
# 允许匿名用户创建目录
anon_other_write_enable=YES
# 允许匿名用户修改目录名称或删除目录
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=NO
listen_ipv6=YES

pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES
```

4. 允许SELinux,设置本地系统权限,将属主设置为`ftp`,或者为pub目录赋予其他用户写权限.

```bash
[xuhaixing@master ~]$ ll -ld /var/ftp/pub
drwxr-xr-x. 2 root root 6 Jun 10  2021 /var/ftp/pub
[xuhaixing@master ~]$ sudo chown ftp /var/ftp/pub
[xuhaixing@master ~]$ sudo chmod o+w /var/ftp/pub
[xuhaixing@master ~]$ ll -ld /var/ftp/pub
drwxr-xrwx. 2 ftp root 6 Jun 10  2021 /var/ftp/pub
[xuhaixing@master ~]$ sudo systemctl restart vsftpd
[xuhaixing@master ~]$ sudo setenforce 0
[xuhaixing@master ~]$ sudo systemctl restart vsftpd
```

slave1 和 slave2 也按照这个配置就可以了

<hr />

## 七、 配置`jdk`

我们打开文件资源管理器在地址栏输入`ftp://ip`

![](../images/40.png)

回车进去后看到一个pub文件夹就说明我们成功进入了master的ftp公共文件夹，我们可以把`jdk-8u212-linux-x64.tar.gz`的安装包复制进去

然后用finalshell进入master的bash

```bash
[xuhaixing@master ~]$ cd /usr/local/
[xuhaixing@master local]$ sudo mkdir java
[sudo] password for xuhaixing:
[xuhaixing@master local]$ cd java
[xuhaixing@master java]$ sudo mv /var/ftp/pub/jdk-8u212-linux-x64.tar.gz /usr/local/java
[xuhaixing@master java]$ ll
total 190444
-rw-------. 1 ftp ftp 195013152 Mar 13 18:07 jdk-8u212-linux-x64.tar.gz
```

开始解压

```bash

[xuhaixing@master java]$ sudo tar -zxvf jdk-8u212-linux-x64.tar.gz
jdk1.8.0_212/
jdk1.8.0_212/README.html
jdk1.8.0_212/LICENSE
jdk1.8.0_212/include/
jdk1.8.0_212/include/jawt.h
jdk1.8.0_212/include/linux/
jdk1.8.0_212/include/linux/jawt_md.h
jdk1.8.0_212/include/linux/jni_md.h
jdk1.8.0_212/include/classfile_constants.h
jdk1.8.0_212/include/jvmticmlr.h
jdk1.8.0_212/include/jni.h
jdk1.8.0_212/include/jdwpTransport.h
jdk1.8.0_212/include/jvmti.h
jdk1.8.0_212/THIRDPARTYLICENSEREADME-JAVAFX.txt
jdk1.8.0_212/lib/
jdk1.8.0_212/lib/ct.sym
jdk1.8.0_212/lib/orb.idl
jdk1.8.0_212/lib/missioncontrol/
jdk1.8.0_212/lib/missioncontrol/artifacts.xml
jdk1.8.0_212/lib/missioncontrol/dropins/
jdk1.8.0_212/lib/missioncontrol/dropins/README.TXT
jdk1.8.0_212/lib/missioncontrol/plugins/
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.osgi.compatibility.state.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.osgi.compatibility.state.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ui.sdk.scheduler_1.2.0.v20140422-1847.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.swt.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.ds_1.4.200.v20131126-2331.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ecf.provider.filetransfer.httpclient4.ssl_1.0.0.v20140827-1444.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.css.core_0.10.100.v20140424-2042.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.jasper.glassfish_2.2.2.v201205150955.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.jsp.jasper_1.0.400.v20130327-1442.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.swt.gtk.linux.x86_64_3.103.1.v20140903-1947.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jface.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.touchpoint.eclipse.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.director.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.publisher.eclipse_1.1.200.v20140414-0825.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.touchpoint.eclipse_2.1.200.v20140512-1650.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.felix.gogo.runtime_0.10.0.v201209301036.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.flightrecorder.controlpanel.ui.configuration_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench3.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.preferences_3.5.200.v20140224-1527.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx.ext_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.transport.ecf.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.net.linux.x86_64.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.touchpoint.natives_1.1.100.v20140523-0116.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jetty.security_8.1.14.v20131031.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.updatechecker.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.update.configurator_3.3.300.v20140518-1928.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jetty.util_8.1.14.v20131031.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.components.ui_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.renderers.swt_0.12.1.v20140903-1023.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.publisher.eclipse.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.greychartplugin_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.intro.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.text.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.views.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.model.workbench_1.1.0.v20140512-1820.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.net.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.operations_2.4.0.v20131119-0908.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.frameworkadmin.equinox_1.0.500.v20131211-1531.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.components.ui.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.frameworkadmin.equinox.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.common_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.forms_3.6.100.v20140422-1825.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.css.swt.theme.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.swt.gtk.linux.x86_64.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.extensionlocation.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.jdp_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.css.swt_0.11.101.v20140818-1343.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rcp.application_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.swt.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.views.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.batik.util_1.7.0.v201011041433.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench_1.2.1.v20140901-1244.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ecf.provider.filetransfer.httpclient4_1.0.800.v20140827-1444.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.beans.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.core.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.core.di.extensions_0.12.0.v20140417-2033.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.alert.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.sat4j.core_2.3.5.v201308161310.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.directorywatcher.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.net_1.2.200.v20120807-0927.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.runtime_3.10.0.v20140318-2214.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.renderers.swt.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.extensionlocation.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ui.sdk_1.0.300.v20140407-1803.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.w3c.css.sac_1.3.1.v200903091627.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.lucene.core_3.5.0.v20120725-1805.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/icons/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/icons/send-email-16.png
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/icons/console_view.png
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/icons/time-span-16.png
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/icons/hprof-16.png
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/icons/day-of-week-16.png
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/icons/date-span-16.png
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/icons/diagnostic-command-16.png
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/com.jrockit.mc.rjmx.actionProvider.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/com.jrockit.mc.rjmx.syntheticattribute.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/triggerEvaluators.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/com.jrockit.mc.rjmx.metadataprovider.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/com.jrockit.mc.rjmx.syntheticnotification.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/com.jrockit.mc.rjmx.service.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/triggerActions.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/com.jrockit.mc.rjmx.attributeTransformation.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/triggerConstraints.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/com.jrockit.mc.rjmx.descriptorProvider.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/schema/triggerActionExceptionHandlers.exsd
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/META-INF/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/plugin.xml
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/plugin.properties
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/lib/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/lib/smtp.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/lib/imap.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/lib/mailapi.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/lib/pop3.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/lib/gimap.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/lib/dsn.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx_5.5.2.174165/rjmx.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.http.jetty_3.0.200.v20131021-1843.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/icons/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/icons/file_obj.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/icons/alert_obj.png
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/icons/new-trigger-wiz.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/icons/flight_recorder.png
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/META-INF/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/plugin.xml
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/toc.xml
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/olh.htm
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/title.htm
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/olh001.htm
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/css/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/css/blafdoc.css
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/html/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/html/cpyr.htm
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/topnav.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/conticon.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/larrow.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/rightnav.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/rarrow.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/leftnav.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/feedback.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/doclib.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/contbig.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/prodbig.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/toc.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/booklist.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/feedbck2.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/masterix.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/mix.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/index.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/bookbig.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/help.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/prodicon.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/oracle.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/indxicon.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/bookicon.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/dcommon/gifs/uarrow.gif
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/html/preface.htm
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/plugin.properties
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/notification_plugin.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification_5.5.2.174165/com.jrockit.mc.console.ui.notification_contexts.xml
jdk1.8.0_212/lib/missioncontrol/plugins/org.w3c.dom.events_3.0.0.draft20060413_v201105210656.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.docs_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.console_1.1.0.v20140131-1639.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.osgi.services.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.bidi_0.10.0.v20130327-1442.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.osgi.services_3.4.0.v20140312-2051.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.app_1.3.200.v20130910-1609.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.rcp_4.3.100.v20141007-2301.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.util_1.0.500.v20130404-1337.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.repository_2.3.0.v20131211-1531.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.osgi.services.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.commands_3.6.100.v20140528-1422.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.reconciler.dropins_1.1.200.v20131119-0908.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.core.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.simpleconfigurator.manipulator.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.osgi.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.artifact.repository_1.1.300.v20131211-1531.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.ui.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.ui.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.artifact.repository.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.di.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.ui.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jetty.io_8.1.14.v20131031.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.greychart.ui.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.browser.attach.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.emf.ecore_2.10.1.v20140901-1043.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.widgets_1.0.0.v20140514-1823.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.httpcomponents.httpcore_4.2.5.v201311072007.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ui.sdk.scheduler.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.director.app.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.operations.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ecf.provider.filetransfer.ssl_1.0.0.v20140827-1444.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.console.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.updatechecker_1.1.200.v20131119-0908.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.model.workbench.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ecf.provider.filetransfer_3.2.200.v20140827-1444.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.diagnostic.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rcp.intro_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.security.ui_1.1.200.v20130626-2037.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.jarprocessor_1.0.300.v20131211-1531.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.touchpoint.natives.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.simpleconfigurator.manipulator_2.0.0.v20131217-1203.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.widgets.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.http.registry_1.1.300.v20130402-1529.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.core.di_1.4.0.v20140414-1837.jar
jdk1.8.0_212/lib/missioncontrol/plugins/javax.inject_1.0.0.v20091030.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.expressions_3.4.600.v20140128-0851.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench3_0.12.0.v20140227-2118.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.director.app.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.historicaldata.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.bindings_0.10.200.v20140424-2042.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.publisher_1.3.0.v20140911-0143.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.widgets.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jetty.server_8.1.14.v20131031.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.base.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jface.text.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ql.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.css.swt.theme_0.9.300.v20140424-2042.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.browser.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.swt.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.felix.gogo.command_0.10.0.v201209301215.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.osgi.compatibility.state_1.0.1.v20140709-1414.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.osgi_3.10.1.v20140909-1633.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.metadata.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.touchpoint.eclipse.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.garbagecollector_1.0.200.v20131115-1210.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.updatechecker.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jetty.servlet_8.1.14.v20131031.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.addons.swt.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.metadata.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.commands.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jface.text_3.9.1.v20140827-1810.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.diagnostic.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ui.sdk.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.property_1.4.200.v20140214-0004.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.property.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.core.commands_0.10.2.v20140424-2344.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.p2.ui.overridden_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.observable.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.jarprocessor.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.registry_3.5.400.v20140428-1507.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.metadata.repository.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.simpleconfigurator.manipulator.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.jarprocessor.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.frameworkadmin.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.flightrecorder.controlpanel.ui.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.directorywatcher.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.di.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.mbeanbrowser_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.forms.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.publisher.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.garbagecollector.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.workbench.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.css.core.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.core_2.3.0.v20131211-1531.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ui.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/javax.el_2.2.0.v201303151357.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.browser.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.diagnostic_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.css.swt.theme.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64_1.1.200.v20141007-2033/
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64_1.1.200.v20141007-2033/META-INF/
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64_1.1.200.v20141007-2033/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64_1.1.200.v20141007-2033/eclipse_1605.so
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64_1.1.200.v20141007-2033/about.html
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64_1.1.200.v20141007-2033/launcher.gtk.linux.x86_64.properties
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.greychart.ui.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.engine.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.http.servlet_1.1.500.v20140318-1755.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rcp.intro.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.net.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jetty.http_8.1.14.v20131031.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.simpleconfigurator.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.w3c.dom.svg_1.1.0.v201011041433.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.services_1.1.0.v20140328-1925.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.docs.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.core.commands.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.felix.gogo.shell_0.10.0.v201212101605.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ecf_3.4.0.v20140827-1444.jar
jdk1.8.0_212/lib/missioncontrol/plugins/javax.xml_1.3.4.v201005080400.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.frameworkadmin.equinox.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/javax.servlet_3.0.0.v201112011016.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.metadata.repository_1.2.100.v20131209-2144.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jface_3.10.1.v20140813-1009.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ui_2.3.0.v20140404-1657.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.operations.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jface.databinding.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.services.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.commands.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.sun.el_2.2.0.v201303151357.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.commons.codec_1.6.0.v201305230611.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.observable.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ecf.identity_3.4.0.v20140827-1444.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.core.contexts_1.3.100.v20140407-1019.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.webapp.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.engine_2.3.0.v20140506-1720.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.mbeanbrowser.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rcp.intro.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.emf.ecore.change_2.10.0.v20140901-1043.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx.ui.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.batik.css_1.7.0.v201011041433.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.css.core.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.publisher.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jetty.continuation_8.1.14.v20131031.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ql_2.0.100.v20131211-1531.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.sat4j.pb_2.3.5.v201404071733.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.security_1.2.0.v20130424-1801.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.browser_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.director.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ui.sdk.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.engine.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.transport.ecf.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.net.linux.x86_64.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench3.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.commands_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jface.text.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.bindings.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help_3.6.0.v20130326-1254.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.net.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.css.swt.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.osgi.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.concurrent_1.1.0.v20130327-1442.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.components.ui.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.swt.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.flightrecorder.configuration_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.batik.util.gui_1.7.0.v200903091627.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx.ui_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.browser.attach_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.ui_4.0.100.v20140401-0608.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.alert_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.core.services_1.2.1.v20140808-1251.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.observable_1.4.1.v20140210-1835.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/macGrey.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/winClassicTSFrame.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/winXPTSFrame.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/win7TSFrame.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/win7Handle.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/winXPBluHandle.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/winXPOlive.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/winClassicHandle.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/winXPBluTSFrame.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/macHandle.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/winXPBlue.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/win7.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/gtkHandle.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/winXPHandle.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/gtkTSFrame.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/macTSFrame.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/images/dragHandle.png
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/META-INF/
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4-dark_win.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4-dark.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4_default_win7.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4_default_mru_on_win7.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4_default_winxp_olv.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/high-contrast.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4_basestyle.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/dark/
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/dark/e4-dark_basestyle.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/dark/e4-dark_globalstyle.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/dark/e4-dark_partstyle.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/dark/e4-dark_preferencestyle.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4_classic_winxp.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4_default_gtk.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4-dark_mac.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4_default_winxp_blu.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4_classic_win7.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/css/e4_default_mac.css
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/plugin.xml
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/plugin.properties
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.themes_1.0.1.v20140819-1717/about.html
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.flightrecorder.ui.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding_1.4.2.v20140729-1044.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.flightrecorder.ui_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.frameworkadmin_2.0.100.v20131209-2144.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.historicaldata.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.frameworkadmin.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.property.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.swt_3.103.1.v20140903-1938.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.emf.ecore.xmi_2.10.1.v20140901-1043.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.touchpoint.natives.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.console_1.0.300.v20131113-1212.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.core_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.intro_3.4.200.v20130326-1254.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.views_3.7.0.v20140408-0703.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.repository.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.net_1.2.200.v20140124-2013.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.resources_3.9.1.v20140825-1431.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.directorywatcher_1.1.0.v20131211-1531.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.text.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rcp.application.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jface.databinding.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ecf.filetransfer_5.0.0.v20140827-1444.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.addons.swt.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.transport.ecf_1.1.0.v20140408-1354.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.swt_0.12.100.v20140530-1436.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.swt.gtk.linux.x86_64.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.workbench_3.106.1.v20140827-1737.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.common_3.6.200.v20130402-1505.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.ui.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.beans.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.w3c.dom.smil_1.0.0.v200806040011.jar
jdk1.8.0_212/lib/missioncontrol/plugins/javax.servlet.jsp_2.2.0.v201112011158.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.historicaldata_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.lucene.analysis_3.5.0.v20120725-1805.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui_3.106.0.v20140812-1751.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.ui_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.flightrecorder.controlpanel.ui_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.jsp.jasper.registry_1.0.300.v20130327-1442.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.alert.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.greychart.ui_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.base_4.0.200.v20141007-2301.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.renderers.swt.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.httpcomponents.httpclient_4.2.6.v201311072007.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.garbagecollector.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.databinding.beans_1.2.200.v20140214-0004.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.bindings.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.emf.common_2.10.1.v20140901-1043.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.flightrecorder.ui.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rcp.application.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.flightrecorder_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.css.swt.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.metadata_2.2.0.v20131211-1531.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.apache.commons.logging_1.1.1.v201101211721.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.ibm.icu_52.1.0.v201404241930.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.extensionlocation_1.2.100.v20131119-0908.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.webapp_3.6.300.v20140407-1855.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.webapp.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ui.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ql.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.publisher.eclipse.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jface.databinding_1.6.200.v20140528-1422.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.text_3.5.300.v20130515-1451.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.metadata.repository.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.notification.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.repository.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.browser.jdp.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.rjmx.ui.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.ui.sdk.scheduler.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.artifact.repository.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.attach_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/javax.annotation_1.2.0.v201401042248.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.di_1.0.0.v20140328-2112.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.docs.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.reconciler.dropins.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.event_1.3.100.v20140115-1647.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.intro.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.contenttype_3.4.200.v20140207-1251.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.flightrecorder.controlpanel.ui.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.browser.jdp_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.reconciler.dropins.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.browser.attach.zh_CN_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.simpleconfigurator.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.workbench.addons.swt_1.1.1.v20140903-0821.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ecf.ssl_1.1.0.v20140827-1444.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.update.configurator.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.console.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.director_2.3.100.v20140224-1921.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher_1.3.0.v20140415-2008.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.help.base.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.services.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.jobs_3.6.0.v20140424-0053.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.console.ui.mbeanbrowser.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.workbench.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.jface.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.p2.director.app_1.0.300.v20140228-1829.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.core.filesystem_1.4.100.v20140514-1614.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.equinox.simpleconfigurator_1.1.0.v20131217-1203.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.core.commands.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.net.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.update.configurator.nl_ja_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/com.jrockit.mc.browser.jdp.ja_5.5.2.174165.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.ui.forms.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/plugins/org.eclipse.e4.ui.model.workbench.nl_zh_4.4.0.v20140623020002.jar
jdk1.8.0_212/lib/missioncontrol/configuration/
jdk1.8.0_212/lib/missioncontrol/configuration/org.eclipse.equinox.simpleconfigurator/
jdk1.8.0_212/lib/missioncontrol/configuration/org.eclipse.equinox.simpleconfigurator/bundles.info
jdk1.8.0_212/lib/missioncontrol/configuration/config.ini
jdk1.8.0_212/lib/missioncontrol/configuration/org.eclipse.update/
jdk1.8.0_212/lib/missioncontrol/configuration/org.eclipse.update/platform.xml
jdk1.8.0_212/lib/missioncontrol/icon.xpm
jdk1.8.0_212/lib/missioncontrol/THIRDPARTYLICENSEREADME.txt
jdk1.8.0_212/lib/missioncontrol/mc.jar
jdk1.8.0_212/lib/missioncontrol/p2/
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.core/
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.core/cache/
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.core/cache/artifacts.xml
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.core/cache/binary/
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.core/cache/binary/com.jrockit.mc.rcp.product_root_5.5.2.174165
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.core/cache/binary/org.eclipse.rcp_root_4.4.0.v20141007-2301
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.core/cache/binary/com.oracle.jmc.executable.gtk.linux.x86_64_5.5.2
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/profileRegistry/
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/profileRegistry/JMC.profile/
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/profileRegistry/JMC.profile/1512678749009.profile.gz
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/profileRegistry/JMC.profile/.lock
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/profileRegistry/JMC.profile/1512678748591.profile.gz
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/profileRegistry/JMC.profile/.data/
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/profileRegistry/JMC.profile/1512678763613.profile.gz
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/profileRegistry/JMC.profile/1512678761228.profile.gz
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/.settings/
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/.settings/org.eclipse.equinox.p2.artifact.repository.prefs
jdk1.8.0_212/lib/missioncontrol/p2/org.eclipse.equinox.p2.engine/.settings/org.eclipse.equinox.p2.metadata.repository.prefs
jdk1.8.0_212/lib/missioncontrol/.eclipseproduct
jdk1.8.0_212/lib/missioncontrol/features/
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.rcp.ja_5.5.2.174165/
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.rcp.ja_5.5.2.174165/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.rcp.ja_5.5.2.174165/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/META-INF/eclipse.inf
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.common_2.10.1.v20140901-1043/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.flightrecorder_5.5.2.174165/
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.flightrecorder_5.5.2.174165/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.flightrecorder_5.5.2.174165/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.console_5.5.2.174165/
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.console_5.5.2.174165/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.console_5.5.2.174165/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/META-INF/eclipse.inf
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.emf.ecore_2.10.1.v20140901-1043/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/META-INF/eclipse.inf
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/asl-v20.txt
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.feature_3.9.0.v20140827-1444/about.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.core.feature_1.3.0.v20140523-0116/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.core.feature_1.3.0.v20140523-0116/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.core.feature_1.3.0.v20140523-0116/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.core.feature_1.3.0.v20140523-0116/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.core.feature_1.3.0.v20140523-0116/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.core.feature_1.3.0.v20140523-0116/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.core.feature_1.3.0.v20140523-0116/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.core.feature_1.3.0.v20140523-0116/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.core.feature_1.3.0.v20140523-0116/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_ja_4.4.0.v20140623020002/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_ja_4.4.0.v20140623020002/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_ja_4.4.0.v20140623020002/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_ja_4.4.0.v20140623020002/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_ja_4.4.0.v20140623020002/eclipse_update_120.jpg
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_ja_4.4.0.v20140623020002/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_ja_4.4.0.v20140623020002/about.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/META-INF/eclipse.inf
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.ssl.feature_1.0.0.v20140827-1444/about.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_zh_4.4.0.v20140623020002/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_zh_4.4.0.v20140623020002/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_zh_4.4.0.v20140623020002/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_zh_4.4.0.v20140623020002/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_zh_4.4.0.v20140623020002/eclipse_update_120.jpg
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_zh_4.4.0.v20140623020002/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.babel.nls_eclipse_zh_4.4.0.v20140623020002/about.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.rcp.feature_1.2.0.v20140523-0116/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.rcp.feature_1.2.0.v20140523-0116/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.rcp.feature_1.2.0.v20140523-0116/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.rcp.feature_1.2.0.v20140523-0116/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.rcp.feature_1.2.0.v20140523-0116/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.rcp.feature_1.2.0.v20140523-0116/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.rcp.feature_1.2.0.v20140523-0116/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.rcp.feature_1.2.0.v20140523-0116/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.equinox.p2.rcp.feature_1.2.0.v20140523-0116/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/META-INF/eclipse.inf
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.ssl.feature_1.0.0.v20140827-1444/about.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/META-INF/eclipse.inf
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/asl-v20.txt
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.ssl.feature_1.0.0.v20140827-1444/about.html
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.rcp.zh_CN_5.5.2.174165/
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.rcp.zh_CN_5.5.2.174165/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.rcp.zh_CN_5.5.2.174165/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.e4.rcp_1.3.100.v20141007-2033/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.e4.rcp_1.3.100.v20141007-2033/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.e4.rcp_1.3.100.v20141007-2033/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.e4.rcp_1.3.100.v20141007-2033/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.e4.rcp_1.3.100.v20141007-2033/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.e4.rcp_1.3.100.v20141007-2033/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.e4.rcp_1.3.100.v20141007-2033/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.rcp_5.5.2.174165/
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.rcp_5.5.2.174165/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.rcp_5.5.2.174165/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.rcp.product_5.5.2.174165/
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.rcp.product_5.5.2.174165/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.rcp.product_5.5.2.174165/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.help_2.0.102.v20141007-2301/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.help_2.0.102.v20141007-2301/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.help_2.0.102.v20141007-2301/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.help_2.0.102.v20141007-2301/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.help_2.0.102.v20141007-2301/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.help_2.0.102.v20141007-2301/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.help_2.0.102.v20141007-2301/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.rcp_4.4.0.v20141007-2301/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.rcp_4.4.0.v20141007-2301/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.rcp_4.4.0.v20141007-2301/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.rcp_4.4.0.v20141007-2301/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.rcp_4.4.0.v20141007-2301/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.rcp_4.4.0.v20141007-2301/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.rcp_4.4.0.v20141007-2301/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/META-INF/eclipse.inf
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/asl-v20.txt
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.filetransfer.httpclient4.feature_3.9.1.v20140827-1444/about.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/META-INF/
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/META-INF/eclipse.inf
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/META-INF/ECLIPSE_.SF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/META-INF/ECLIPSE_.RSA
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/META-INF/MANIFEST.MF
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/license.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/epl-v10.html
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/feature.xml
jdk1.8.0_212/lib/missioncontrol/features/org.eclipse.ecf.core.feature_1.1.0.v20140827-1444/about.html
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.core_5.5.2.174165/
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.core_5.5.2.174165/feature.properties
jdk1.8.0_212/lib/missioncontrol/features/com.jrockit.mc.feature.core_5.5.2.174165/feature.xml
jdk1.8.0_212/lib/jexec
jdk1.8.0_212/lib/dt.jar
jdk1.8.0_212/lib/javafx-mx.jar
jdk1.8.0_212/lib/amd64/
jdk1.8.0_212/lib/amd64/jli/
jdk1.8.0_212/lib/amd64/jli/libjli.so
jdk1.8.0_212/lib/amd64/libjawt.so
jdk1.8.0_212/lib/visualvm/
jdk1.8.0_212/lib/visualvm/platform/
jdk1.8.0_212/lib/visualvm/platform/lib/
jdk1.8.0_212/lib/visualvm/platform/lib/locale/
jdk1.8.0_212/lib/visualvm/platform/lib/locale/boot_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/lib/locale/org-openide-util_ja.jar
jdk1.8.0_212/lib/visualvm/platform/lib/locale/org-openide-util_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/lib/locale/org-openide-modules_ja.jar
jdk1.8.0_212/lib/visualvm/platform/lib/locale/org-openide-util-lookup_ja.jar
jdk1.8.0_212/lib/visualvm/platform/lib/locale/org-openide-util-lookup_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/lib/locale/org-openide-modules_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/lib/locale/boot_ja.jar
jdk1.8.0_212/lib/visualvm/platform/lib/boot.jar
jdk1.8.0_212/lib/visualvm/platform/lib/org-openide-util.jar
jdk1.8.0_212/lib/visualvm/platform/lib/org-openide-modules.jar
jdk1.8.0_212/lib/visualvm/platform/lib/org-openide-util-lookup.jar
jdk1.8.0_212/lib/visualvm/platform/lib/nbexec
jdk1.8.0_212/lib/visualvm/platform/core/
jdk1.8.0_212/lib/visualvm/platform/core/locale/
jdk1.8.0_212/lib/visualvm/platform/core/locale/org-openide-filesystems_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/core/locale/core_ja.jar
jdk1.8.0_212/lib/visualvm/platform/core/locale/core_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/core/locale/org-openide-filesystems_ja.jar
jdk1.8.0_212/lib/visualvm/platform/core/org-openide-filesystems.jar
jdk1.8.0_212/lib/visualvm/platform/core/core.jar
jdk1.8.0_212/lib/visualvm/platform/modules/
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-keyring-fallback.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-explorer_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-autoupdate-ui_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-queries_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-print_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-multitabs_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-options-api_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-execution_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-awt_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-editor-mimelookup-impl_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-execution_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-progress-ui_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-keyring_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-editor-mimelookup-impl_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-ui_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-sampler_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-loaders_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-windows_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-awt_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-multiview_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-api-progress_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-lib-uihandler_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-favorites_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-io-ui_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-progress-ui_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-options-api_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-applemenu_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-api-search_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-uihandler_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-sendopts_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-options-keymap_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-spi-quicksearch_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-explorer_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-actions_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-api-visual_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-output2_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-keyring_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-nodes_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-autoupdate-services_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-dialogs_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-applemenu_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-autoupdate-cli_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-options-keymap_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-execution_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-io_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-windows_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-api-annotations-common_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-autoupdate-services_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-dialogs_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-sendopts_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-masterfs-nio2_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-masterfs_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-templates_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-autoupdate-ui_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-ui_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-editor-mimelookup_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-output2_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-text_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-templates_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-javahelp_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-loaders_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-swing-plaf_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-swing-plaf_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-keyring-fallback_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-api-annotations-common_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-compat_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-io_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-keyring-impl_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-util-enumerations_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-io-ui_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-multiview_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-api-visual_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-core-kit_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-text_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-core-kit_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-options_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-actions_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-nodes_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-spi-actions_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-settings_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-api-progress_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-keyring-fallback_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-util-enumerations_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-masterfs-nio2_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-lib-uihandler_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-favorites_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-javahelp_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-queries_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-windows_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-multitabs_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-sampler_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-spi-actions_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-uihandler_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-autoupdate-cli_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-spi-quicksearch_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-options_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-core-execution_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-masterfs_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-keyring-impl_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-api-search_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-swing-tabcontrol_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-swing-outline_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-swing-outline_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-settings_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-compat_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-editor-mimelookup_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-swing-tabcontrol_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-netbeans-modules-print_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/locale/org-openide-windows_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-swing-tabcontrol.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-explorer.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-dialogs.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-keyring.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-progress-ui.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-execution.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-spi-actions.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-core-io-ui.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-swing-outline.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-keyring-impl.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-print.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-core-windows.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-autoupdate-cli.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-settings.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-compat.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-options-keymap.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-api-annotations-common.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-options.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-awt.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-autoupdate-ui.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-api-progress.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-editor-mimelookup-impl.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-sendopts.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-nodes.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-uihandler.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-masterfs.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-api-visual.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-windows.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-masterfs-nio2.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-core-multitabs.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-spi-quicksearch.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-queries.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-favorites.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-core.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-core-multiview.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-options-api.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-core-output2.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-core-kit.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-autoupdate-services.jar
jdk1.8.0_212/lib/visualvm/platform/modules/ext/
jdk1.8.0_212/lib/visualvm/platform/modules/ext/locale/
jdk1.8.0_212/lib/visualvm/platform/modules/ext/locale/updater_ja.jar
jdk1.8.0_212/lib/visualvm/platform/modules/ext/locale/updater_zh_CN.jar
jdk1.8.0_212/lib/visualvm/platform/modules/ext/jhall-2.0_05.jar
jdk1.8.0_212/lib/visualvm/platform/modules/ext/updater.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-util-enumerations.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-swing-plaf.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-api-search.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-javahelp.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-text.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-editor-mimelookup.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-templates.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-core-ui.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-sampler.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-core-execution.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-loaders.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-lib-uihandler.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-netbeans-modules-applemenu.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-actions.jar
jdk1.8.0_212/lib/visualvm/platform/modules/org-openide-io.jar
jdk1.8.0_212/lib/visualvm/platform/VERSION.txt
jdk1.8.0_212/lib/visualvm/platform/.lastModified
jdk1.8.0_212/lib/visualvm/platform/update_tracking/
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-util-lookup.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-core.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-editor-mimelookup.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-options-keymap.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-options-api.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-spi-actions.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-autoupdate-ui.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-filesystems.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-keyring-impl.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-api-progress.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-util-enumerations.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-masterfs.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-text.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-print.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-lib-uihandler.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-core-multitabs.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-util.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-keyring.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-sendopts.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-core-output2.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-core-kit.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-templates.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-bootstrap.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-loaders.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-core-io-ui.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-actions.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-execution.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-javahelp.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-core-windows.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-options.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-explorer.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-core-ui.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-editor-mimelookup-impl.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-keyring-fallback.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-masterfs-nio2.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-compat.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-sampler.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-swing-plaf.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-api-visual.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-swing-outline.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-autoupdate-cli.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-spi-quicksearch.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-windows.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-queries.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-nodes.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-settings.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-core-execution.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-api-search.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-core-multiview.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-modules.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-progress-ui.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-api-annotations-common.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-dialogs.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-applemenu.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-autoupdate-services.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-swing-tabcontrol.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-io.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-core-startup.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-openide-awt.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-uihandler.xml
jdk1.8.0_212/lib/visualvm/platform/update_tracking/org-netbeans-modules-favorites.xml
jdk1.8.0_212/lib/visualvm/platform/config/
jdk1.8.0_212/lib/visualvm/platform/config/Modules/
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-core.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-editor-mimelookup.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-options-keymap.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-options-api.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-spi-actions.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-autoupdate-ui.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-keyring-impl.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-api-progress.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-util-enumerations.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-masterfs.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-text.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-print.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-lib-uihandler.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-core-multitabs.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-keyring.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-sendopts.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-core-output2.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-core-kit.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-templates.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-loaders.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-core-io-ui.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-actions.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-execution.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-javahelp.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-core-windows.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-options.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-explorer.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-core-ui.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-editor-mimelookup-impl.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-keyring-fallback.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-masterfs-nio2.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-compat.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-sampler.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-swing-plaf.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-api-visual.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-swing-outline.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-autoupdate-cli.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-spi-quicksearch.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-windows.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-queries.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-nodes.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-settings.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-core-execution.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-api-search.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-core-multiview.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-progress-ui.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-api-annotations-common.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-dialogs.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-applemenu.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-autoupdate-services.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-swing-tabcontrol.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-io.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-openide-awt.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-uihandler.xml
jdk1.8.0_212/lib/visualvm/platform/config/Modules/org-netbeans-modules-favorites.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-netbeans-core.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-netbeans-modules-options-api.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-openide-filesystems.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-openide-text.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-openide-util.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-openide-loaders.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-openide-execution.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-openide-explorer.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-netbeans-modules-queries.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-openide-nodes.xml
jdk1.8.0_212/lib/visualvm/platform/config/ModuleAutoDeps/org-openide-modules.xml
jdk1.8.0_212/lib/visualvm/etc/
jdk1.8.0_212/lib/visualvm/etc/visualvm.clusters
jdk1.8.0_212/lib/visualvm/etc/visualvm.conf
jdk1.8.0_212/lib/visualvm/profiler/
jdk1.8.0_212/lib/visualvm/profiler/lib/
jdk1.8.0_212/lib/visualvm/profiler/lib/locale/
jdk1.8.0_212/lib/visualvm/profiler/lib/locale/jfluid-server_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/lib/locale/jfluid-server_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/lib/deployed/
jdk1.8.0_212/lib/visualvm/profiler/lib/deployed/jdk15/
jdk1.8.0_212/lib/visualvm/profiler/lib/deployed/jdk15/linux-amd64/
jdk1.8.0_212/lib/visualvm/profiler/lib/deployed/jdk15/linux-amd64/libprofilerinterface.so
jdk1.8.0_212/lib/visualvm/profiler/lib/deployed/jdk16/
jdk1.8.0_212/lib/visualvm/profiler/lib/deployed/jdk16/linux-amd64/
jdk1.8.0_212/lib/visualvm/profiler/lib/deployed/jdk16/linux-amd64/libprofilerinterface.so
jdk1.8.0_212/lib/visualvm/profiler/lib/jfluid-server-15.jar
jdk1.8.0_212/lib/visualvm/profiler/lib/jfluid-server.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-utilities_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-selector-api_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-selector-ui_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-api_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-lib-profiler-common_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-oql_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-attach_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-snaptracer_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-lib-profiler-charts_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-selector-api_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-snaptracer_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-lib-profiler_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-lib-profiler_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-utilities_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-lib-profiler-ui_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-oql_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-attach_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-api_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-lib-profiler-charts_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-lib-profiler-common_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-heapwalker_ja.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-selector-ui_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-lib-profiler-ui_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/locale/org-netbeans-modules-profiler-heapwalker_zh_CN.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-modules-profiler-selector-api.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-modules-profiler-api.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-modules-profiler.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-lib-profiler-common.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-modules-profiler-selector-ui.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-modules-profiler-oql.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-lib-profiler-ui.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-lib-profiler-charts.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-modules-profiler-attach.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-modules-profiler-heapwalker.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-lib-profiler.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-modules-profiler-snaptracer.jar
jdk1.8.0_212/lib/visualvm/profiler/modules/org-netbeans-modules-profiler-utilities.jar
jdk1.8.0_212/lib/visualvm/profiler/VERSION.txt
jdk1.8.0_212/lib/visualvm/profiler/.lastModified
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-modules-profiler.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-modules-profiler-api.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-modules-profiler-snaptracer.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-lib-profiler-common.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-modules-profiler-attach.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-modules-profiler-oql.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-lib-profiler-charts.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-modules-profiler-selector-api.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-modules-profiler-selector-ui.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-modules-profiler-heapwalker.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-lib-profiler.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-modules-profiler-utilities.xml
jdk1.8.0_212/lib/visualvm/profiler/update_tracking/org-netbeans-lib-profiler-ui.xml
jdk1.8.0_212/lib/visualvm/profiler/config/
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-modules-profiler.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-modules-profiler-api.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-modules-profiler-snaptracer.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-lib-profiler-common.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-modules-profiler-attach.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-modules-profiler-oql.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-lib-profiler-charts.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-modules-profiler-selector-api.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-modules-profiler-selector-ui.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-modules-profiler-heapwalker.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-lib-profiler.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-modules-profiler-utilities.xml
jdk1.8.0_212/lib/visualvm/profiler/config/Modules/org-netbeans-lib-profiler-ui.xml
jdk1.8.0_212/lib/visualvm/visualvm/
jdk1.8.0_212/lib/visualvm/visualvm/core/
jdk1.8.0_212/lib/visualvm/visualvm/core/locale/
jdk1.8.0_212/lib/visualvm/visualvm/core/locale/core_visualvm.jar
jdk1.8.0_212/lib/visualvm/visualvm/core/locale/com-sun-tools-visualvm-modules-startup_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/core/locale/com-sun-tools-visualvm-modules-startup_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/core/com-sun-tools-visualvm-modules-startup.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-threaddump_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-attach_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-api-caching_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-tools_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-profiler_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-host-remote_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-modules-appui_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-application-views_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-uisupport_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-jvmstat_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-profiling_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-application-views_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-jmx_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-core_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/org-netbeans-core-windows_visualvm.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-host-views_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-application_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-heapdump_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-host-remote_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-modules-appui_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-coredump_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-threaddump_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-charts_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-tools_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-sampler_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/org-netbeans-core_visualvm.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-jmx_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-jvmstat_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-sa_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/org-netbeans-modules-profiler_visualvm.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-api-caching_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-coredump_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-charts_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-heapdump_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-attach_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-host_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-host_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-profiling_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-sampler_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-profiler_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-core_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-uisupport_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-jvm_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-host-views_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-jvm_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-application_ja.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/locale/com-sun-tools-visualvm-sa_zh_CN.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-sa.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-host-remote.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-jvm.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-coredump.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-profiling.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-sampler.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-charts.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-attach.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-heapdump.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-tools.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-application.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-jmx.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-jvmstat.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-modules-appui.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-profiler.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-host-views.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-uisupport.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-application-views.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-host.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-core.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-api-caching.jar
jdk1.8.0_212/lib/visualvm/visualvm/modules/com-sun-tools-visualvm-threaddump.jar
jdk1.8.0_212/lib/visualvm/visualvm/.lastModified
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-core.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-profiler.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-profiling.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-attach.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-jmx.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-api-caching.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-sa.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-application.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-modules-startup.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-application-views.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-heapdump.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-coredump.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-modules-appui.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-uisupport.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-threaddump.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-jvm.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-host-views.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-sampler.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-host.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-jvmstat.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-host-remote.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-charts.xml
jdk1.8.0_212/lib/visualvm/visualvm/update_tracking/com-sun-tools-visualvm-tools.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-core.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-modules-uihandler.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-lib-uihandler.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-profiler.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-profiling.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-attach.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-jmx.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-openide-compat.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-api-caching.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-sa.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-application.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-application-views.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-heapdump.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-modules-options-keymap.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-api-visual.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-coredump.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-modules-templates.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-modules-core-kit.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-modules-appui.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-modules-favorites.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-uisupport.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-threaddump.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-jvm.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-host-views.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-sampler.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-core-io-ui.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-host.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-jvmstat.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-core-execution.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-openide-util-enumerations.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-openide-options.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-core-output2.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-openide-execution.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-host-remote.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-charts.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-modules-print.xml_hidden
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/com-sun-tools-visualvm-tools.xml
jdk1.8.0_212/lib/visualvm/visualvm/config/Modules/org-netbeans-modules-spi-actions.xml_hidden
jdk1.8.0_212/lib/sa-jdi.jar
jdk1.8.0_212/lib/ant-javafx.jar
jdk1.8.0_212/lib/ir.idl
jdk1.8.0_212/lib/packager.jar
jdk1.8.0_212/lib/jconsole.jar
jdk1.8.0_212/lib/tools.jar
jdk1.8.0_212/src.zip
jdk1.8.0_212/javafx-src.zip
jdk1.8.0_212/THIRDPARTYLICENSEREADME.txt
jdk1.8.0_212/COPYRIGHT
jdk1.8.0_212/man/
jdk1.8.0_212/man/ja_JP.UTF-8/
jdk1.8.0_212/man/ja_JP.UTF-8/man1/
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jps.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/javaws.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/pack200.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/extcheck.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/appletviewer.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/rmid.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/servertool.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jar.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jdeps.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/javah.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jconsole.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jvisualvm.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/javac.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/policytool.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/native2ascii.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jinfo.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/rmiregistry.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/serialver.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/javadoc.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/tnameserv.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/schemagen.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/wsgen.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jsadebugd.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/javapackager.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/unpack200.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jarsigner.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jmap.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/xjc.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jstat.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/wsimport.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/rmic.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/idlj.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jjs.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jrunscript.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jstatd.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jmc.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/java.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/javap.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jdb.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jcmd.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jstack.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/jhat.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/keytool.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/orbd.1
jdk1.8.0_212/man/ja_JP.UTF-8/man1/javafxpackager.1
jdk1.8.0_212/man/ja
jdk1.8.0_212/man/man1/
jdk1.8.0_212/man/man1/jps.1
jdk1.8.0_212/man/man1/javaws.1
jdk1.8.0_212/man/man1/pack200.1
jdk1.8.0_212/man/man1/extcheck.1
jdk1.8.0_212/man/man1/appletviewer.1
jdk1.8.0_212/man/man1/rmid.1
jdk1.8.0_212/man/man1/servertool.1
jdk1.8.0_212/man/man1/jar.1
jdk1.8.0_212/man/man1/jdeps.1
jdk1.8.0_212/man/man1/javah.1
jdk1.8.0_212/man/man1/jconsole.1
jdk1.8.0_212/man/man1/jvisualvm.1
jdk1.8.0_212/man/man1/javac.1
jdk1.8.0_212/man/man1/policytool.1
jdk1.8.0_212/man/man1/native2ascii.1
jdk1.8.0_212/man/man1/jinfo.1
jdk1.8.0_212/man/man1/rmiregistry.1
jdk1.8.0_212/man/man1/serialver.1
jdk1.8.0_212/man/man1/javadoc.1
jdk1.8.0_212/man/man1/tnameserv.1
jdk1.8.0_212/man/man1/schemagen.1
jdk1.8.0_212/man/man1/wsgen.1
jdk1.8.0_212/man/man1/jsadebugd.1
jdk1.8.0_212/man/man1/javapackager.1
jdk1.8.0_212/man/man1/unpack200.1
jdk1.8.0_212/man/man1/jarsigner.1
jdk1.8.0_212/man/man1/jmap.1
jdk1.8.0_212/man/man1/xjc.1
jdk1.8.0_212/man/man1/jstat.1
jdk1.8.0_212/man/man1/wsimport.1
jdk1.8.0_212/man/man1/rmic.1
jdk1.8.0_212/man/man1/idlj.1
jdk1.8.0_212/man/man1/jjs.1
jdk1.8.0_212/man/man1/jrunscript.1
jdk1.8.0_212/man/man1/jstatd.1
jdk1.8.0_212/man/man1/jmc.1
jdk1.8.0_212/man/man1/java.1
jdk1.8.0_212/man/man1/javap.1
jdk1.8.0_212/man/man1/jdb.1
jdk1.8.0_212/man/man1/jcmd.1
jdk1.8.0_212/man/man1/jstack.1
jdk1.8.0_212/man/man1/jhat.1
jdk1.8.0_212/man/man1/keytool.1
jdk1.8.0_212/man/man1/orbd.1
jdk1.8.0_212/man/man1/javafxpackager.1
jdk1.8.0_212/jre/
jdk1.8.0_212/jre/LICENSE
jdk1.8.0_212/jre/THIRDPARTYLICENSEREADME-JAVAFX.txt
jdk1.8.0_212/jre/README
jdk1.8.0_212/jre/lib/
jdk1.8.0_212/jre/lib/fontconfig.RedHat.6.bfc
jdk1.8.0_212/jre/lib/fontconfig.SuSE.10.bfc
jdk1.8.0_212/jre/lib/calendars.properties
jdk1.8.0_212/jre/lib/locale/
jdk1.8.0_212/jre/lib/locale/zh_TW.BIG5/
jdk1.8.0_212/jre/lib/locale/zh_TW.BIG5/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/zh_TW.BIG5/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/zh.GBK/
jdk1.8.0_212/jre/lib/locale/zh.GBK/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/zh.GBK/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/zh_HK.BIG5HK/
jdk1.8.0_212/jre/lib/locale/zh_HK.BIG5HK/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/zh_HK.BIG5HK/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/pt_BR/
jdk1.8.0_212/jre/lib/locale/pt_BR/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/pt_BR/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/zh/
jdk1.8.0_212/jre/lib/locale/zh/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/zh/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/ja/
jdk1.8.0_212/jre/lib/locale/ja/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/ja/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/it/
jdk1.8.0_212/jre/lib/locale/it/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/it/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/ko/
jdk1.8.0_212/jre/lib/locale/ko/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/ko/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/de/
jdk1.8.0_212/jre/lib/locale/de/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/de/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/sv/
jdk1.8.0_212/jre/lib/locale/sv/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/sv/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/ko.UTF-8/
jdk1.8.0_212/jre/lib/locale/ko.UTF-8/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/ko.UTF-8/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/es/
jdk1.8.0_212/jre/lib/locale/es/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/es/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/zh_TW/
jdk1.8.0_212/jre/lib/locale/zh_TW/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/zh_TW/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/locale/fr/
jdk1.8.0_212/jre/lib/locale/fr/LC_MESSAGES/
jdk1.8.0_212/jre/lib/locale/fr/LC_MESSAGES/sunw_java_plugin.mo
jdk1.8.0_212/jre/lib/images/
jdk1.8.0_212/jre/lib/images/icons/
jdk1.8.0_212/jre/lib/images/icons/sun-java_HighContrastInverse.png
jdk1.8.0_212/jre/lib/images/icons/sun-java_LowContrast.png
jdk1.8.0_212/jre/lib/images/icons/sun-java_HighContrast.png
jdk1.8.0_212/jre/lib/images/icons/sun-java.png
jdk1.8.0_212/jre/lib/images/cursors/
jdk1.8.0_212/jre/lib/images/cursors/motif_CopyNoDrop32x32.gif
jdk1.8.0_212/jre/lib/images/cursors/motif_LinkNoDrop32x32.gif
jdk1.8.0_212/jre/lib/images/cursors/motif_CopyDrop32x32.gif
jdk1.8.0_212/jre/lib/images/cursors/cursors.properties
jdk1.8.0_212/jre/lib/images/cursors/motif_MoveDrop32x32.gif
jdk1.8.0_212/jre/lib/images/cursors/motif_MoveNoDrop32x32.gif
jdk1.8.0_212/jre/lib/images/cursors/motif_LinkDrop32x32.gif
jdk1.8.0_212/jre/lib/images/cursors/invalid32x32.gif
jdk1.8.0_212/jre/lib/psfontj2d.properties
jdk1.8.0_212/jre/lib/oblique-fonts/
jdk1.8.0_212/jre/lib/oblique-fonts/LucidaTypewriterOblique.ttf
jdk1.8.0_212/jre/lib/oblique-fonts/LucidaSansOblique.ttf
jdk1.8.0_212/jre/lib/oblique-fonts/LucidaTypewriterBoldOblique.ttf
jdk1.8.0_212/jre/lib/oblique-fonts/LucidaSansDemiOblique.ttf
jdk1.8.0_212/jre/lib/oblique-fonts/fonts.dir
jdk1.8.0_212/jre/lib/meta-index
jdk1.8.0_212/jre/lib/javafx.properties
jdk1.8.0_212/jre/lib/fontconfig.RedHat.6.properties.src
jdk1.8.0_212/jre/lib/fontconfig.SuSE.11.properties.src
jdk1.8.0_212/jre/lib/management/
jdk1.8.0_212/jre/lib/management/management.properties
jdk1.8.0_212/jre/lib/management/jmxremote.access
jdk1.8.0_212/jre/lib/management/snmp.acl.template
jdk1.8.0_212/jre/lib/management/jmxremote.password.template
jdk1.8.0_212/jre/lib/javaws.jar
jdk1.8.0_212/jre/lib/fontconfig.SuSE.10.properties.src
jdk1.8.0_212/jre/lib/classlist
jdk1.8.0_212/jre/lib/jexec
jdk1.8.0_212/jre/lib/jvm.hprof.txt
jdk1.8.0_212/jre/lib/desktop/
jdk1.8.0_212/jre/lib/desktop/icons/
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/16x16/
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/16x16/apps/
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/16x16/apps/sun-javaws.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/16x16/apps/sun-jcontrol.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/16x16/apps/sun-java.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/16x16/mimetypes/
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/16x16/mimetypes/gnome-mime-application-x-java-archive.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/16x16/mimetypes/gnome-mime-application-x-java-jnlp-file.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/16x16/mimetypes/gnome-mime-text-x-java.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/48x48/
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/48x48/apps/
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/48x48/apps/sun-javaws.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/48x48/apps/sun-jcontrol.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/48x48/apps/sun-java.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/48x48/mimetypes/
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/48x48/mimetypes/gnome-mime-application-x-java-archive.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/48x48/mimetypes/gnome-mime-application-x-java-jnlp-file.png
jdk1.8.0_212/jre/lib/desktop/icons/LowContrast/48x48/mimetypes/gnome-mime-text-x-java.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/16x16/
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/16x16/apps/
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/16x16/apps/sun-javaws.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/16x16/apps/sun-jcontrol.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/16x16/apps/sun-java.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/16x16/mimetypes/
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/16x16/mimetypes/gnome-mime-application-x-java-archive.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/16x16/mimetypes/gnome-mime-application-x-java-jnlp-file.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/16x16/mimetypes/gnome-mime-text-x-java.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/48x48/
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/48x48/apps/
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/48x48/apps/sun-javaws.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/48x48/apps/sun-jcontrol.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/48x48/apps/sun-java.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/48x48/mimetypes/
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/48x48/mimetypes/gnome-mime-application-x-java-archive.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/48x48/mimetypes/gnome-mime-application-x-java-jnlp-file.png
jdk1.8.0_212/jre/lib/desktop/icons/hicolor/48x48/mimetypes/gnome-mime-text-x-java.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/16x16/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/16x16/apps/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/16x16/apps/sun-javaws.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/16x16/apps/sun-jcontrol.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/16x16/apps/sun-java.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/16x16/mimetypes/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/16x16/mimetypes/gnome-mime-application-x-java-archive.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/16x16/mimetypes/gnome-mime-application-x-java-jnlp-file.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/16x16/mimetypes/gnome-mime-text-x-java.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/48x48/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/48x48/apps/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/48x48/apps/sun-javaws.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/48x48/apps/sun-jcontrol.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/48x48/apps/sun-java.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/48x48/mimetypes/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/48x48/mimetypes/gnome-mime-application-x-java-archive.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/48x48/mimetypes/gnome-mime-application-x-java-jnlp-file.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrast/48x48/mimetypes/gnome-mime-text-x-java.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/16x16/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/16x16/apps/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/16x16/apps/sun-javaws.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/16x16/apps/sun-jcontrol.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/16x16/apps/sun-java.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/16x16/mimetypes/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/16x16/mimetypes/gnome-mime-application-x-java-archive.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/16x16/mimetypes/gnome-mime-application-x-java-jnlp-file.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/16x16/mimetypes/gnome-mime-text-x-java.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/48x48/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/48x48/apps/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/48x48/apps/sun-javaws.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/48x48/apps/sun-jcontrol.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/48x48/apps/sun-java.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/48x48/mimetypes/
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/48x48/mimetypes/gnome-mime-application-x-java-archive.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/48x48/mimetypes/gnome-mime-application-x-java-jnlp-file.png
jdk1.8.0_212/jre/lib/desktop/icons/HighContrastInverse/48x48/mimetypes/gnome-mime-text-x-java.png
jdk1.8.0_212/jre/lib/desktop/mime/
jdk1.8.0_212/jre/lib/desktop/mime/packages/
jdk1.8.0_212/jre/lib/desktop/mime/packages/x-java-jnlp-file.xml
jdk1.8.0_212/jre/lib/desktop/mime/packages/x-java-archive.xml
jdk1.8.0_212/jre/lib/desktop/applications/
jdk1.8.0_212/jre/lib/desktop/applications/sun_java.desktop
jdk1.8.0_212/jre/lib/desktop/applications/sun-java.desktop
jdk1.8.0_212/jre/lib/desktop/applications/sun-javaws.desktop
jdk1.8.0_212/jre/lib/security/
jdk1.8.0_212/jre/lib/security/policy/
jdk1.8.0_212/jre/lib/security/policy/limited/
jdk1.8.0_212/jre/lib/security/policy/limited/US_export_policy.jar
jdk1.8.0_212/jre/lib/security/policy/limited/local_policy.jar
jdk1.8.0_212/jre/lib/security/policy/unlimited/
jdk1.8.0_212/jre/lib/security/policy/unlimited/US_export_policy.jar
jdk1.8.0_212/jre/lib/security/policy/unlimited/local_policy.jar
jdk1.8.0_212/jre/lib/security/java.policy
jdk1.8.0_212/jre/lib/security/cacerts
jdk1.8.0_212/jre/lib/security/java.security
jdk1.8.0_212/jre/lib/security/trusted.libraries
jdk1.8.0_212/jre/lib/security/javaws.policy
jdk1.8.0_212/jre/lib/security/blacklist
jdk1.8.0_212/jre/lib/security/blacklisted.certs
jdk1.8.0_212/jre/lib/fontconfig.SuSE.11.bfc
jdk1.8.0_212/jre/lib/jfr/
jdk1.8.0_212/jre/lib/jfr/default.jfc
jdk1.8.0_212/jre/lib/jfr/profile.jfc
jdk1.8.0_212/jre/lib/plugin.jar
jdk1.8.0_212/jre/lib/charsets.jar
jdk1.8.0_212/jre/lib/jce.jar
jdk1.8.0_212/jre/lib/amd64/
jdk1.8.0_212/jre/lib/amd64/libbci.so
jdk1.8.0_212/jre/lib/amd64/libjavafx_font_freetype.so
jdk1.8.0_212/jre/lib/amd64/libawt_headless.so
jdk1.8.0_212/jre/lib/amd64/libdt_socket.so
jdk1.8.0_212/jre/lib/amd64/libmlib_image.so
jdk1.8.0_212/jre/lib/amd64/libglassgtk2.so
jdk1.8.0_212/jre/lib/amd64/libjavafx_font.so
jdk1.8.0_212/jre/lib/amd64/libnpjp2.so
jdk1.8.0_212/jre/lib/amd64/libj2pcsc.so
jdk1.8.0_212/jre/lib/amd64/libprism_sw.so
jdk1.8.0_212/jre/lib/amd64/libavplugin-57.so
jdk1.8.0_212/jre/lib/amd64/libjava.so
jdk1.8.0_212/jre/lib/amd64/libavplugin-56.so
jdk1.8.0_212/jre/lib/amd64/libglass.so
jdk1.8.0_212/jre/lib/amd64/libj2gss.so
jdk1.8.0_212/jre/lib/amd64/libjsig.so
jdk1.8.0_212/jre/lib/amd64/libnpt.so
jdk1.8.0_212/jre/lib/amd64/libjsdt.so
jdk1.8.0_212/jre/lib/amd64/libjfxmedia.so
jdk1.8.0_212/jre/lib/amd64/libjsoundalsa.so
jdk1.8.0_212/jre/lib/amd64/libresource.so
jdk1.8.0_212/jre/lib/amd64/jli/
jdk1.8.0_212/jre/lib/amd64/jli/libjli.so
jdk1.8.0_212/jre/lib/amd64/libjdwp.so
jdk1.8.0_212/jre/lib/amd64/libglib-lite.so
jdk1.8.0_212/jre/lib/amd64/libjawt.so
jdk1.8.0_212/jre/lib/amd64/libmanagement.so
jdk1.8.0_212/jre/lib/amd64/libsunec.so
jdk1.8.0_212/jre/lib/amd64/libavplugin-ffmpeg-57.so
jdk1.8.0_212/jre/lib/amd64/libsaproc.so
jdk1.8.0_212/jre/lib/amd64/libverify.so
jdk1.8.0_212/jre/lib/amd64/libjfr.so
jdk1.8.0_212/jre/lib/amd64/libjaas_unix.so
jdk1.8.0_212/jre/lib/amd64/libjpeg.so
jdk1.8.0_212/jre/lib/amd64/libsplashscreen.so
jdk1.8.0_212/jre/lib/amd64/libunpack.so
jdk1.8.0_212/jre/lib/amd64/libzip.so
jdk1.8.0_212/jre/lib/amd64/libdcpr.so
jdk1.8.0_212/jre/lib/amd64/libj2pkcs11.so
jdk1.8.0_212/jre/lib/amd64/libdeploy.so
jdk1.8.0_212/jre/lib/amd64/libavplugin-ffmpeg-56.so
jdk1.8.0_212/jre/lib/amd64/libhprof.so
jdk1.8.0_212/jre/lib/amd64/libnet.so
jdk1.8.0_212/jre/lib/amd64/libfxplugins.so
jdk1.8.0_212/jre/lib/amd64/libinstrument.so
jdk1.8.0_212/jre/lib/amd64/libt2k.so
jdk1.8.0_212/jre/lib/amd64/libfontmanager.so
jdk1.8.0_212/jre/lib/amd64/libprism_common.so
jdk1.8.0_212/jre/lib/amd64/libattach.so
jdk1.8.0_212/jre/lib/amd64/libavplugin-54.so
jdk1.8.0_212/jre/lib/amd64/server/
jdk1.8.0_212/jre/lib/amd64/server/Xusage.txt
jdk1.8.0_212/jre/lib/amd64/server/libjsig.so
jdk1.8.0_212/jre/lib/amd64/server/libjvm.so
jdk1.8.0_212/jre/lib/amd64/libgstreamer-lite.so
jdk1.8.0_212/jre/lib/amd64/liblcms.so
jdk1.8.0_212/jre/lib/amd64/libawt_xawt.so
jdk1.8.0_212/jre/lib/amd64/jvm.cfg
jdk1.8.0_212/jre/lib/amd64/libavplugin-55.so
jdk1.8.0_212/jre/lib/amd64/libjavafx_font_t2k.so
jdk1.8.0_212/jre/lib/amd64/libprism_es2.so
jdk1.8.0_212/jre/lib/amd64/libdecora_sse.so
jdk1.8.0_212/jre/lib/amd64/libavplugin-53.so
jdk1.8.0_212/jre/lib/amd64/libjfxwebkit.so
jdk1.8.0_212/jre/lib/amd64/libawt.so
jdk1.8.0_212/jre/lib/amd64/libjava_crw_demo.so
jdk1.8.0_212/jre/lib/amd64/libjavafx_font_pango.so
jdk1.8.0_212/jre/lib/amd64/libglassgtk3.so
jdk1.8.0_212/jre/lib/amd64/libsctp.so
jdk1.8.0_212/jre/lib/amd64/libjsound.so
jdk1.8.0_212/jre/lib/amd64/libnio.so
jdk1.8.0_212/jre/lib/amd64/libjavafx_iio.so
jdk1.8.0_212/jre/lib/fontconfig.RedHat.5.properties.src
jdk1.8.0_212/jre/lib/fontconfig.bfc
jdk1.8.0_212/jre/lib/tzdb.dat
jdk1.8.0_212/jre/lib/fonts/
jdk1.8.0_212/jre/lib/fonts/LucidaTypewriterRegular.ttf
jdk1.8.0_212/jre/lib/fonts/LucidaBrightItalic.ttf
jdk1.8.0_212/jre/lib/fonts/LucidaBrightRegular.ttf
jdk1.8.0_212/jre/lib/fonts/LucidaTypewriterBold.ttf
jdk1.8.0_212/jre/lib/fonts/LucidaSansRegular.ttf
jdk1.8.0_212/jre/lib/fonts/LucidaBrightDemiItalic.ttf
jdk1.8.0_212/jre/lib/fonts/LucidaBrightDemiBold.ttf
jdk1.8.0_212/jre/lib/fonts/LucidaSansDemiBold.ttf
jdk1.8.0_212/jre/lib/fonts/fonts.dir
jdk1.8.0_212/jre/lib/management-agent.jar
jdk1.8.0_212/jre/lib/fontconfig.Turbo.bfc
jdk1.8.0_212/jre/lib/logging.properties
jdk1.8.0_212/jre/lib/rt.jar
jdk1.8.0_212/jre/lib/jfr.jar
jdk1.8.0_212/jre/lib/currency.data
jdk1.8.0_212/jre/lib/jsse.jar
jdk1.8.0_212/jre/lib/net.properties
jdk1.8.0_212/jre/lib/applet/
jdk1.8.0_212/jre/lib/flavormap.properties
jdk1.8.0_212/jre/lib/ext/
jdk1.8.0_212/jre/lib/ext/meta-index
jdk1.8.0_212/jre/lib/ext/localedata.jar
jdk1.8.0_212/jre/lib/ext/sunec.jar
jdk1.8.0_212/jre/lib/ext/sunjce_provider.jar
jdk1.8.0_212/jre/lib/ext/nashorn.jar
jdk1.8.0_212/jre/lib/ext/jaccess.jar
jdk1.8.0_212/jre/lib/ext/zipfs.jar
jdk1.8.0_212/jre/lib/ext/jfxrt.jar
jdk1.8.0_212/jre/lib/ext/cldrdata.jar
jdk1.8.0_212/jre/lib/ext/dnsns.jar
jdk1.8.0_212/jre/lib/ext/sunpkcs11.jar
jdk1.8.0_212/jre/lib/deploy.jar
jdk1.8.0_212/jre/lib/cmm/
jdk1.8.0_212/jre/lib/cmm/GRAY.pf
jdk1.8.0_212/jre/lib/cmm/PYCC.pf
jdk1.8.0_212/jre/lib/cmm/CIEXYZ.pf
jdk1.8.0_212/jre/lib/cmm/sRGB.pf
jdk1.8.0_212/jre/lib/cmm/LINEAR_RGB.pf
jdk1.8.0_212/jre/lib/hijrah-config-umalqura.properties
jdk1.8.0_212/jre/lib/fontconfig.properties.src
jdk1.8.0_212/jre/lib/content-types.properties
jdk1.8.0_212/jre/lib/fontconfig.Turbo.properties.src
jdk1.8.0_212/jre/lib/resources.jar
jdk1.8.0_212/jre/lib/sound.properties
jdk1.8.0_212/jre/lib/fontconfig.RedHat.5.bfc
jdk1.8.0_212/jre/lib/jfxswt.jar
jdk1.8.0_212/jre/lib/deploy/
jdk1.8.0_212/jre/lib/deploy/splash@2x.gif
jdk1.8.0_212/jre/lib/deploy/messages_zh_TW.properties
jdk1.8.0_212/jre/lib/deploy/ffjcext.zip
jdk1.8.0_212/jre/lib/deploy/messages_es.properties
jdk1.8.0_212/jre/lib/deploy/messages.properties
jdk1.8.0_212/jre/lib/deploy/messages_fr.properties
jdk1.8.0_212/jre/lib/deploy/messages_pt_BR.properties
jdk1.8.0_212/jre/lib/deploy/cautionshield.icns
jdk1.8.0_212/jre/lib/deploy/messages_de.properties
jdk1.8.0_212/jre/lib/deploy/mixcode_s.png
jdk1.8.0_212/jre/lib/deploy/splash_11-lic.gif
jdk1.8.0_212/jre/lib/deploy/messages_sv.properties
jdk1.8.0_212/jre/lib/deploy/splash.gif
jdk1.8.0_212/jre/lib/deploy/messages_ko.properties
jdk1.8.0_212/jre/lib/deploy/messages_zh_HK.properties
jdk1.8.0_212/jre/lib/deploy/splash_11@2x-lic.gif
jdk1.8.0_212/jre/lib/deploy/MixedCodeMainDialogJs.ui
jdk1.8.0_212/jre/lib/deploy/messages_it.properties
jdk1.8.0_212/jre/lib/deploy/java-icon.ico
jdk1.8.0_212/jre/lib/deploy/MixedCodeMainDialog.ui
jdk1.8.0_212/jre/lib/deploy/messages_zh_CN.properties
jdk1.8.0_212/jre/lib/deploy/messages_ja.properties
jdk1.8.0_212/jre/lib/psfont.properties.ja
jdk1.8.0_212/jre/plugin/
jdk1.8.0_212/jre/plugin/desktop/
jdk1.8.0_212/jre/plugin/desktop/sun_java.png
jdk1.8.0_212/jre/plugin/desktop/sun_java.desktop
jdk1.8.0_212/jre/THIRDPARTYLICENSEREADME.txt
jdk1.8.0_212/jre/COPYRIGHT
jdk1.8.0_212/jre/bin/
jdk1.8.0_212/jre/bin/orbd
jdk1.8.0_212/jre/bin/java
jdk1.8.0_212/jre/bin/keytool
jdk1.8.0_212/jre/bin/pack200
jdk1.8.0_212/jre/bin/ControlPanel
jdk1.8.0_212/jre/bin/rmiregistry
jdk1.8.0_212/jre/bin/jjs
jdk1.8.0_212/jre/bin/servertool
jdk1.8.0_212/jre/bin/rmid
jdk1.8.0_212/jre/bin/policytool
jdk1.8.0_212/jre/bin/javaws
jdk1.8.0_212/jre/bin/unpack200
jdk1.8.0_212/jre/bin/tnameserv
jdk1.8.0_212/jre/bin/jcontrol
jdk1.8.0_212/jre/Welcome.html
jdk1.8.0_212/bin/
jdk1.8.0_212/bin/java-rmi.cgi
jdk1.8.0_212/bin/jinfo
jdk1.8.0_212/bin/xjc
jdk1.8.0_212/bin/orbd
jdk1.8.0_212/bin/javafxpackager
jdk1.8.0_212/bin/javap
jdk1.8.0_212/bin/rmic
jdk1.8.0_212/bin/wsimport
jdk1.8.0_212/bin/jarsigner
jdk1.8.0_212/bin/jcmd
jdk1.8.0_212/bin/jstatd
jdk1.8.0_212/bin/jar
jdk1.8.0_212/bin/jdeps
jdk1.8.0_212/bin/jconsole
jdk1.8.0_212/bin/java
jdk1.8.0_212/bin/keytool
jdk1.8.0_212/bin/jsadebugd
jdk1.8.0_212/bin/jps
jdk1.8.0_212/bin/pack200
jdk1.8.0_212/bin/jmc
jdk1.8.0_212/bin/schemagen
jdk1.8.0_212/bin/extcheck
jdk1.8.0_212/bin/jhat
jdk1.8.0_212/bin/jstack
jdk1.8.0_212/bin/native2ascii
jdk1.8.0_212/bin/jstat
jdk1.8.0_212/bin/ControlPanel
jdk1.8.0_212/bin/rmiregistry
jdk1.8.0_212/bin/appletviewer
jdk1.8.0_212/bin/javadoc
jdk1.8.0_212/bin/jdb
jdk1.8.0_212/bin/jjs
jdk1.8.0_212/bin/servertool
jdk1.8.0_212/bin/idlj
jdk1.8.0_212/bin/rmid
jdk1.8.0_212/bin/javapackager
jdk1.8.0_212/bin/policytool
jdk1.8.0_212/bin/javaws
jdk1.8.0_212/bin/unpack200
jdk1.8.0_212/bin/tnameserv
jdk1.8.0_212/bin/jmc.ini
jdk1.8.0_212/bin/jmap
jdk1.8.0_212/bin/serialver
jdk1.8.0_212/bin/wsgen
jdk1.8.0_212/bin/jrunscript
jdk1.8.0_212/bin/javah
jdk1.8.0_212/bin/javac
jdk1.8.0_212/bin/jvisualvm
jdk1.8.0_212/bin/jcontrol
jdk1.8.0_212/release
```

进入文件夹，并且配置/etc/profile

```bash
[xuhaixing@master java]$ cd jdk1.8.0_212/
[xuhaixing@master jdk1.8.0_212]$ pwd
/usr/local/java/jdk1.8.0_212
[xuhaixing@master jdk1.8.0_212]$ sudo vim /etc/profile
```

在文件末尾添加：

```sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_212
export PATH=${JAVA_HOME}/bin:$PATH
```

最后应用一下profile

```bash
[xuhaixing@master jdk1.8.0_212]$ source /etc/profile
```

输入java -version检查是否配置成功！

```bash
[xuhaixing@master jdk1.8.0_212]$ java -version
java version "1.8.0_212"
Java(TM) SE Runtime Environment (build 1.8.0_212-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.212-b10, mixed mode)
```

ok!让我们来分发一下jdk和profile到slave1和slave2

```bash
[xuhaixing@master jdk1.8.0_212]$ sudo scp -r /usr/local/java root@slave1:/usr/local/
root@slave1's password:
# 省略N行解压的文件
[xuhaixing@master jdk1.8.0_212]$ sudo scp  /etc/profile  root@slave1:/etc/
root@slave1's password:
profile                                                                                                           100% 1901     2.0MB/s   00:00
[xuhaixing@master jdk1.8.0_212]$ sudo scp -r /usr/local/java root@slave2:/usr/local/
root@slave1's password:
# 省略N行解压的文件
[xuhaixing@master jdk1.8.0_212]$ sudo scp  /etc/profile  root@slave2:/etc/
root@slave1's password:
profile                                                                                                           100% 1901     2.0MB/s   00:00
```

然后分别去slave1和slave2执行这两条命令

> source /etc/profile
>
> java -version

如果显示jdk版本号那就配置成功！

<hr />

## 八、 配置docker

注意：这里只需要配置master的即可

安装docker：

```bash
[xuhaixing@master jdk1.8.0_212]$ uname -r
3.10.0-1160.el7.x86_64
[xuhaixing@master jdk1.8.0_212]$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.ustc.edu.cn
 * extras: mirrors.huaweicloud.com
 * updates: mirrors.163.com
Package yum-utils-1.1.31-54.el7_8.noarch already installed and latest version
Resolving Dependencies
--> Running transaction check
---> Package device-mapper-persistent-data.x86_64 0:0.8.5-3.el7 will be updated
---> Package device-mapper-persistent-data.x86_64 0:0.8.5-3.el7_9.2 will be an update
---> Package lvm2.x86_64 7:2.02.187-6.el7 will be updated
---> Package lvm2.x86_64 7:2.02.187-6.el7_9.5 will be an update
--> Processing Dependency: lvm2-libs = 7:2.02.187-6.el7_9.5 for package: 7:lvm2-2.02.187-6.el7_9.5.x86_64
--> Running transaction check
---> Package lvm2-libs.x86_64 7:2.02.187-6.el7 will be updated
---> Package lvm2-libs.x86_64 7:2.02.187-6.el7_9.5 will be an update
--> Processing Dependency: device-mapper-event = 7:1.02.170-6.el7_9.5 for package: 7:lvm2-libs-2.02.187-6.el7_9.5.x86_64
--> Running transaction check
---> Package device-mapper-event.x86_64 7:1.02.170-6.el7 will be updated
---> Package device-mapper-event.x86_64 7:1.02.170-6.el7_9.5 will be an update
--> Processing Dependency: device-mapper-event-libs = 7:1.02.170-6.el7_9.5 for package: 7:device-mapper-event-1.02.170-6.el7_9.5.x86_64
--> Processing Dependency: device-mapper = 7:1.02.170-6.el7_9.5 for package: 7:device-mapper-event-1.02.170-6.el7_9.5.x86_64
--> Running transaction check
---> Package device-mapper.x86_64 7:1.02.170-6.el7 will be updated
--> Processing Dependency: device-mapper = 7:1.02.170-6.el7 for package: 7:device-mapper-libs-1.02.170-6.el7.x86_64
---> Package device-mapper.x86_64 7:1.02.170-6.el7_9.5 will be an update
---> Package device-mapper-event-libs.x86_64 7:1.02.170-6.el7 will be updated
---> Package device-mapper-event-libs.x86_64 7:1.02.170-6.el7_9.5 will be an update
--> Running transaction check
---> Package device-mapper-libs.x86_64 7:1.02.170-6.el7 will be updated
---> Package device-mapper-libs.x86_64 7:1.02.170-6.el7_9.5 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

====================================================================================================================================================
 Package                                         Arch                     Version                                   Repository                 Size
====================================================================================================================================================
Updating:
 device-mapper-persistent-data                   x86_64                   0.8.5-3.el7_9.2                           updates                   423 k
 lvm2                                            x86_64                   7:2.02.187-6.el7_9.5                      updates                   1.3 M
Updating for dependencies:
 device-mapper                                   x86_64                   7:1.02.170-6.el7_9.5                      updates                   297 k
 device-mapper-event                             x86_64                   7:1.02.170-6.el7_9.5                      updates                   192 k
 device-mapper-event-libs                        x86_64                   7:1.02.170-6.el7_9.5                      updates                   192 k
 device-mapper-libs                              x86_64                   7:1.02.170-6.el7_9.5                      updates                   325 k
 lvm2-libs                                       x86_64                   7:2.02.187-6.el7_9.5                      updates                   1.1 M

Transaction Summary
====================================================================================================================================================
Upgrade  2 Packages (+5 Dependent packages)

Total download size: 3.8 M
Downloading packages:
No Presto metadata available for updates
(1/7): device-mapper-event-1.02.170-6.el7_9.5.x86_64.rpm                                                                     | 192 kB  00:00:00
(2/7): device-mapper-event-libs-1.02.170-6.el7_9.5.x86_64.rpm                                                                | 192 kB  00:00:00
(3/7): device-mapper-libs-1.02.170-6.el7_9.5.x86_64.rpm                                                                      | 325 kB  00:00:00
(4/7): lvm2-2.02.187-6.el7_9.5.x86_64.rpm                                                                                    | 1.3 MB  00:00:00
(5/7): lvm2-libs-2.02.187-6.el7_9.5.x86_64.rpm                                                                               | 1.1 MB  00:00:00
(6/7): device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64.rpm                                                              | 423 kB  00:00:01
(7/7): device-mapper-1.02.170-6.el7_9.5.x86_64.rpm                                                                           | 297 kB  00:00:01
----------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                               2.1 MB/s | 3.8 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : 7:device-mapper-libs-1.02.170-6.el7_9.5.x86_64                                                                                  1/14
  Updating   : 7:device-mapper-1.02.170-6.el7_9.5.x86_64                                                                                       2/14
  Updating   : 7:device-mapper-event-libs-1.02.170-6.el7_9.5.x86_64                                                                            3/14
  Updating   : 7:device-mapper-event-1.02.170-6.el7_9.5.x86_64                                                                                 4/14
  Updating   : 7:lvm2-libs-2.02.187-6.el7_9.5.x86_64                                                                                           5/14
  Updating   : device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64                                                                            6/14
  Updating   : 7:lvm2-2.02.187-6.el7_9.5.x86_64                                                                                                7/14
  Cleanup    : 7:lvm2-2.02.187-6.el7.x86_64                                                                                                    8/14
  Cleanup    : 7:lvm2-libs-2.02.187-6.el7.x86_64                                                                                               9/14
  Cleanup    : 7:device-mapper-event-1.02.170-6.el7.x86_64                                                                                    10/14
  Cleanup    : 7:device-mapper-event-libs-1.02.170-6.el7.x86_64                                                                               11/14
  Cleanup    : 7:device-mapper-1.02.170-6.el7.x86_64                                                                                          12/14
  Cleanup    : 7:device-mapper-libs-1.02.170-6.el7.x86_64                                                                                     13/14
  Cleanup    : device-mapper-persistent-data-0.8.5-3.el7.x86_64                                                                               14/14
  Verifying  : 7:device-mapper-event-1.02.170-6.el7_9.5.x86_64                                                                                 1/14
  Verifying  : device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64                                                                            2/14
  Verifying  : 7:lvm2-libs-2.02.187-6.el7_9.5.x86_64                                                                                           3/14
  Verifying  : 7:device-mapper-1.02.170-6.el7_9.5.x86_64                                                                                       4/14
  Verifying  : 7:lvm2-2.02.187-6.el7_9.5.x86_64                                                                                                5/14
  Verifying  : 7:device-mapper-libs-1.02.170-6.el7_9.5.x86_64                                                                                  6/14
  Verifying  : 7:device-mapper-event-libs-1.02.170-6.el7_9.5.x86_64                                                                            7/14
  Verifying  : 7:device-mapper-event-libs-1.02.170-6.el7.x86_64                                                                                8/14
  Verifying  : 7:lvm2-libs-2.02.187-6.el7.x86_64                                                                                               9/14
  Verifying  : 7:device-mapper-libs-1.02.170-6.el7.x86_64                                                                                     10/14
  Verifying  : 7:lvm2-2.02.187-6.el7.x86_64                                                                                                   11/14
  Verifying  : device-mapper-persistent-data-0.8.5-3.el7.x86_64                                                                               12/14
  Verifying  : 7:device-mapper-event-1.02.170-6.el7.x86_64                                                                                    13/14
  Verifying  : 7:device-mapper-1.02.170-6.el7.x86_64                                                                                          14/14

Updated:
  device-mapper-persistent-data.x86_64 0:0.8.5-3.el7_9.2                              lvm2.x86_64 7:2.02.187-6.el7_9.5

Dependency Updated:
  device-mapper.x86_64 7:1.02.170-6.el7_9.5                                   device-mapper-event.x86_64 7:1.02.170-6.el7_9.5
  device-mapper-event-libs.x86_64 7:1.02.170-6.el7_9.5                        device-mapper-libs.x86_64 7:1.02.170-6.el7_9.5
  lvm2-libs.x86_64 7:2.02.187-6.el7_9.5

Complete!
[xuhaixing@master jdk1.8.0_212]$ sudo yum-config-manager --add-repo \
> http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
Loaded plugins: fastestmirror, langpacks
adding repo from: http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
grabbing file http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo to /etc/yum.repos.d/docker-ce.repo
repo saved to /etc/yum.repos.d/docker-ce.repo
[xuhaixing@master jdk1.8.0_212]$ sudo yum -y install docker-ce-18.06.3.ce
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.ustc.edu.cn
 * extras: mirrors.huaweicloud.com
 * updates: mirrors.163.com
docker-ce-stable                                                                                                             | 3.5 kB  00:00:00
(1/2): docker-ce-stable/7/x86_64/updateinfo                                                                                  |   55 B  00:00:00
(2/2): docker-ce-stable/7/x86_64/primary_db                                                                                  | 136 kB  00:00:00
Resolving Dependencies
--> Running transaction check
---> Package docker-ce.x86_64 0:18.06.3.ce-3.el7 will be installed
--> Processing Dependency: container-selinux >= 2.9 for package: docker-ce-18.06.3.ce-3.el7.x86_64
--> Running transaction check
---> Package container-selinux.noarch 2:2.119.2-1.911c772.el7_8 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

====================================================================================================================================================
 Package                             Arch                     Version                                      Repository                          Size
====================================================================================================================================================
Installing:
 docker-ce                           x86_64                   18.06.3.ce-3.el7                             docker-ce-stable                    41 M
Installing for dependencies:
 container-selinux                   noarch                   2:2.119.2-1.911c772.el7_8                    extras                              40 k

Transaction Summary
====================================================================================================================================================
Install  1 Package (+1 Dependent package)

Total download size: 41 M
Installed size: 168 M
Downloading packages:
(1/2): container-selinux-2.119.2-1.911c772.el7_8.noarch.rpm                                                                  |  40 kB  00:00:00
warning: /var/cache/yum/x86_64/7/docker-ce-stable/packages/docker-ce-18.06.3.ce-3.el7.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID 621e9f35: NOKEY
Public key for docker-ce-18.06.3.ce-3.el7.x86_64.rpm is not installed
(2/2): docker-ce-18.06.3.ce-3.el7.x86_64.rpm                                                                                 |  41 MB  00:00:45
----------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                               921 kB/s |  41 MB  00:00:45
Retrieving key from https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
Importing GPG key 0x621E9F35:
 Userid     : "Docker Release (CE rpm) <docker@docker.com>"
 Fingerprint: 060a 61c5 1b55 8a7f 742b 77aa c52f eb6b 621e 9f35
 From       : https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : 2:container-selinux-2.119.2-1.911c772.el7_8.noarch                                                                               1/2
  Installing : docker-ce-18.06.3.ce-3.el7.x86_64                                                                                                2/2
  Verifying  : docker-ce-18.06.3.ce-3.el7.x86_64                                                                                                1/2
  Verifying  : 2:container-selinux-2.119.2-1.911c772.el7_8.noarch                                                                               2/2

Installed:
  docker-ce.x86_64 0:18.06.3.ce-3.el7

Dependency Installed:
  container-selinux.noarch 2:2.119.2-1.911c772.el7_8

Complete!
[xuhaixing@master jdk1.8.0_212]$ sudo systemctl start docker
[xuhaixing@master jdk1.8.0_212]$ sudo systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.

```

配置阿里云镜像加速

```bash
[xuhaixing@master jdk1.8.0_212]$ sudo vi /etc/docker/daemon.json
```

```sh
{
  "registry-mirrors": ["https://xxxxx.mirror.aliyuncs.com"]
}
# 这是我的镜像加速，你们可以自己去阿里云的官网搜索ACR里面就有一个容器镜像服务，点进去就可以配置自己的
```

阿里云官网:

> https://cn.aliyun.com/

```bash
[xuhaixing@master jdk1.8.0_212]$ systemctl daemon-reload
==== AUTHENTICATING FOR org.freedesktop.systemd1.reload-daemon ===
Authentication is required to reload the systemd state.
Authenticating as: xuhaixing
Password:
==== AUTHENTICATION COMPLETE ===
[xuhaixing@master jdk1.8.0_212]$ sudo systemctl restart docker
[xuhaixing@master jdk1.8.0_212]$ sudo docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
[xuhaixing@master jdk1.8.0_212]$ sudo docker pull httpd
Using default tag: latest
latest: Pulling from library/httpd
a2abf6c4d29d: Pull complete
dcc4698797c8: Pull complete
41c22baa66ec: Pull complete
67283bbdd4a0: Pull complete
d982c879c57e: Pull complete
Digest: sha256:0954cc1af252d824860b2c5dc0a10720af2b7a3d3435581ca788dff8480c7b32
Status: Downloaded newer image for httpd:latest
```

<hr />

## 九、 配置hadoop环境

打开文件资源管理器在地址栏输入`ftp://ip`，将`hadoop-3.3.3.tar.gz`移动到刚才pub文件夹内

打开用`finalshell`打开master的bash：

```bash

[xuhaixing@master jdk1.8.0_212]$ cd /opt/
[xuhaixing@master opt]$ sudo mkdir software
[sudo] password for xuhaixing:
[xuhaixing@master opt]$ ll
total 0
drwxr-xr-x. 2 root root 6 Oct 31  2018 rh
drwxr-xr-x. 2 root root 6 Mar 13 18:41 software
[xuhaixing@master opt]$ cd software/
[xuhaixing@master software]$ sudo mkdir hadoop
[xuhaixing@master software]$ sudo tar -zxvf /var/ftp/pub/hadoop-3.3.3.tar.gz -C /opt/software/hadoop/
# 此处省略N行
[xuhaixing@master software]$ cd ..
[xuhaixing@master opt]$ sudo scp -r /opt/software/ root@slave1:/opt/
root@slave1's password:
# 此处省略N行
[xuhaixing@master opt]$ sudo scp -r /opt/software/ root@slave2:/opt/
root@slave2's password:
# 此处省略N行
[xuhaixing@master opt]$ cd software/hadoop/hadoop-3.3.3/
[xuhaixing@master hadoop-3.3.3]$ pwd
/opt/software/hadoop/hadoop-3.3.3
[xuhaixing@master hadoop-3.3.3]$ sudo vim /etc/profile
```

添加：

```sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_212
export HADOOP_HOME=/opt/software/hadoop/hadoop-3.3.3
export PATH=${JAVA_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:$PATH
```

```bash
[xuhaixing@master hadoop-3.3.3]$ source /etc/profile
[xuhaixing@master hadoop-3.3.3]$ hadoop version
Hadoop 3.3.3
Source code repository https://github.com/apache/hadoop.git -r d37586cbda38c338d9fe481addda5a05fb516f71
Compiled by stevel on 2022-05-09T16:36Z
Compiled with protoc 3.7.1
From source with checksum eb96dd4a797b6989ae0cdb9db6efc6
This command was run using /opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-common-3.3.3.jar
```

然后将配置文件分发给slave1和slave2：

```bash

[xuhaixing@master hadoop-3.3.3]$ sudo scp /etc/profile root@slave1:/etc/
root@slave1's password:
profile                                                                                                           100% 1993     3.0MB/s   00:00
[xuhaixing@master hadoop-3.3.3]$ sudo scp /etc/profile root@slave2:/etc/
root@slave2's password:
profile                                                                                                           100% 1993     2.6MB/s   00:00
```

然后分别去slave1和slave2中执行这两条命令

> source /etc/profile
>
> hadoop version

显示版本号就配置成功了

配置software下的所有文件夹的用户权限：

```bash
[xuhaixing@master hadoop-3.3.3]$ cd ..
[xuhaixing@master hadoop]$ cd ..
[xuhaixing@master software]$ cd ..
[xuhaixing@master opt]$ sudo chown -R xuhaixing:xuhaixing ./software/
[xuhaixing@master opt]$ ll
total 0
drwxr-xr-x. 2 root      root       6 Oct 31  2018 rh
drwxr-xr-x. 3 xuhaixing xuhaixing 20 Mar 13 18:42 software
```

接下来就设置一下slave1下的software下的所有文件夹用户权限(我这里用slave1演示，slave2执行同样的操作即可):

```bash

[xuhaixing@slave1 ~]$ cd /opt/
[xuhaixing@slave1 opt]$ sudo chown -R xuhaixing:xuhaixing ./software/
[sudo] password for xuhaixing:
[xuhaixing@slave1 opt]$ ll
total 0
drwxr-xr-x. 2 root      root       6 Oct 31  2018 rh
drwxr-xr-x. 3 xuhaixing xuhaixing 20 Mar 13 18:47 software
```

<hr />

## 十、 `hadoop`伪分布式集群搭建的实现

## 什么是伪分布式集群？

​	顾名思义，伪分布就是假分布式，假就假在__只有一台机器而不是多台机器来完成一个任务,但是模拟了分布式的这个过程__，所以伪分布式下Hadoop也就是虽然在一个机器上配置了`hadoop`的所有节点，但伪分布式完成了所有分布式所必须的事件。伪分布式Hadoop和单机版最大区别就在于需要配置HDFS。

### 既然了解了什么是伪分布式集群那么就跟着我一起来搭建吧！

用`finalshell`打开master的bash

```bash
[xuhaixing@master opt]$ cd software/hadoop/hadoop-3.3.3/etc/hadoop/
[xuhaixing@master hadoop]$ vim ./core-site.xml
[xuhaixing@master hadoop]$ vim ./hadoop-env.sh
[xuhaixing@master hadoop]$ vim ./hdfs-site.xml
```

`core-site.xml`配置

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

`hadoop-env.sh`在文件末尾添加,这里的`xuhaixing`替换成你们自己的初始用户：

```sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_212
export HDFS_NAMENODE_USER=xuhaixing
export HDFS_DATANODE_USER=xuhaixing
export HDFS_SECONDARYNAMENODE_USER=xuhaixing
```

`hdfs-site.xml`配置

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

配置ssh免密登录：

```bash

[xuhaixing@master hadoop]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/xuhaixing/.ssh/id_rsa):
Created directory '/home/xuhaixing/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/xuhaixing/.ssh/id_rsa.
Your public key has been saved in /home/xuhaixing/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vaxZGWsElfHYWtCaQrQCiVb5jhnVNKgQJSd4ViwfjqY xuhaixing@master
The key's randomart image is:
+---[RSA 2048]----+
| .+B*o =+ ++     |
|. B+=.o o+.=.    |
| + * =..o .o+    |
|  o = ...ooo     |
| o   =  S.=      |
|E   o .  o =     |
|          B      |
|         =       |
|        o        |
+----[SHA256]-----+
[xuhaixing@master hadoop]$ ssh-copy-id master
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/xuhaixing/.ssh/id_rsa.pub"
The authenticity of host 'master (200.200.200.10)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
xuhaixing@master's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'master'"
and check to make sure that only the key(s) you wanted were added.
```

<hr />

配置成后我们初始化一下`namenode`

```bash
[xuhaixing@master hadoop]$ cd ..
[xuhaixing@master etc]$ cd ..
[xuhaixing@master hadoop-3.3.3]$ hdfs namenode -format
WARNING: /opt/software/hadoop/hadoop-3.3.3/logs does not exist. Creating.
2024-03-13 19:15:15,943 INFO namenode.NameNode: STARTUP_MSG:
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = master/200.200.200.10
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 3.3.3
STARTUP_MSG:   classpath = /opt/software/hadoop/hadoop-3.3.3/etc/hadoop:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/slf4j-reload4j-1.7.36.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/snappy-java-1.1.8.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jsr305-3.0.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/nimbus-jose-jwt-9.8.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-common-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/hadoop-annotations-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-net-3.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/accessors-smart-2.4.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/j2objc-annotations-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/curator-framework-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/netty-3.10.6.Final.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jersey-servlet-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-databind-2.13.2.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/audience-annotations-0.5.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-servlet-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jsr311-api-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jaxb-impl-2.2.3-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/dnsjava-2.1.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-util-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-xml-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/hadoop-shaded-protobuf_3_7-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-compress-1.21.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/guava-27.0-jre.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-math3-3.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-admin-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jersey-server-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-webapp-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-security-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/paranamer-2.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-beanutils-1.9.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-server-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/listenablefuture-9999.0-empty-to-avoid-conflict-with-guava.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/stax2-api-4.2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jaxb-api-2.2.11.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/metrics-core-3.2.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-xc-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-mapper-asl-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/gson-2.8.9.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-core-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-asn1-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/reload4j-1.2.18.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jsp-api-2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jersey-json-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/avro-1.7.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-client-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-xdr-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/token-provider-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/hadoop-shaded-guava-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-http-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-annotations-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/checker-qual-2.5.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-cli-1.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-identity-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/httpcore-4.4.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/asm-5.0.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-core-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/slf4j-api-1.7.36.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/json-smart-2.4.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-crypto-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/curator-recipes-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-config-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-io-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-pkix-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-core-asl-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-lang3-3.12.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/hadoop-auth-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jakarta.activation-api-1.2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-text-1.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/woodstox-core-5.3.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jettison-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/animal-sniffer-annotations-1.17.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jsch-0.1.55.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/zookeeper-jute-3.5.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jackson-jaxrs-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jul-to-slf4j-1.7.36.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-simplekdc-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-io-2.8.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/curator-client-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-util-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-collections-3.2.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerby-util-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/kerb-server-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/zookeeper-3.5.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/protobuf-java-2.5.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/failureaccess-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jetty-util-ajax-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/re2j-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-codec-1.15.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-configuration2-2.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jcip-annotations-1.0-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-daemon-1.0.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/jersey-core-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/httpclient-4.5.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/javax.servlet-api-3.1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/lib/commons-logging-1.1.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-registry-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-common-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-kms-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-nfs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-common-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/snappy-java-1.1.8.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jsr305-3.0.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/nimbus-jose-jwt-9.8.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-common-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/hadoop-annotations-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-net-3.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/accessors-smart-2.4.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/j2objc-annotations-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/curator-framework-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/netty-3.10.6.Final.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/netty-all-4.1.68.Final.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jersey-servlet-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-databind-2.13.2.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/audience-annotations-0.5.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-servlet-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jsr311-api-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jaxb-impl-2.2.3-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/dnsjava-2.1.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-util-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-xml-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/hadoop-shaded-protobuf_3_7-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-compress-1.21.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/guava-27.0-jre.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-math3-3.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-admin-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jersey-server-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-webapp-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-security-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/paranamer-2.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-beanutils-1.9.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-server-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/listenablefuture-9999.0-empty-to-avoid-conflict-with-guava.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/stax2-api-4.2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jaxb-api-2.2.11.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/okio-1.6.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-xc-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-mapper-asl-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/gson-2.8.9.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-core-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-asn1-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/reload4j-1.2.18.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/json-simple-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jersey-json-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/avro-1.7.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-client-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/okhttp-2.7.5.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-xdr-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/token-provider-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/hadoop-shaded-guava-1.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-http-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-annotations-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/checker-qual-2.5.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-cli-1.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-identity-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/httpcore-4.4.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/asm-5.0.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-core-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/json-smart-2.4.7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-crypto-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/curator-recipes-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-config-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-io-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-pkix-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-core-asl-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-lang3-3.12.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/hadoop-auth-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jakarta.activation-api-1.2.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-text-1.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/woodstox-core-5.3.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jettison-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/animal-sniffer-annotations-1.17.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jsch-0.1.55.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/zookeeper-jute-3.5.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jackson-jaxrs-1.9.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/leveldbjni-all-1.8.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-simplekdc-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-io-2.8.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/curator-client-4.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-util-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-collections-3.2.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerby-util-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/kerb-server-1.0.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/zookeeper-3.5.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/protobuf-java-2.5.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/failureaccess-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jetty-util-ajax-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/re2j-1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-codec-1.15.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-configuration2-2.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jcip-annotations-1.0-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-daemon-1.0.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/jersey-core-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/httpclient-4.5.13.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/javax.servlet-api-3.1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib/commons-logging-1.1.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-client-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-rbf-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-rbf-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-httpfs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-client-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-native-client-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-nfs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/hdfs/hadoop-hdfs-native-client-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.3.3-tests.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-hs-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-common-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-core-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-shuffle-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-nativetask-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-app-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-uploader-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/mapreduce/hadoop-mapreduce-client-hs-plugins-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jetty-plus-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jackson-jaxrs-base-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jetty-jndi-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/java-util-1.9.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/snakeyaml-1.26.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jetty-annotations-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax.websocket-client-api-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/fst-2.50.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-servlet-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jakarta.xml.bind-api-2.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/bcpkix-jdk15on-1.60.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/objenesis-2.6.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/asm-tree-9.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jackson-jaxrs-json-provider-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/metrics-core-3.2.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/aopalliance-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/asm-analysis-9.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jackson-module-jaxb-annotations-2.13.2.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax.websocket-api-1.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax-websocket-client-impl-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax.ws.rs-api-2.1.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jersey-guice-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/mssql-jdbc-6.2.1.jre7.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-common-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-api-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jetty-client-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jersey-client-1.19.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax.inject-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-server-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/bcprov-jdk15on-1.60.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/geronimo-jcache_1.0_spec-1.0-alpha-1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/ehcache-3.3.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/asm-commons-9.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/HikariCP-java7-2.4.12.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jna-5.2.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/swagger-annotations-1.5.4.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/websocket-client-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/guice-4.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/javax-websocket-server-impl-9.4.43.v20210629.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/json-io-2.5.1.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/jline-3.9.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/lib/guice-servlet-4.0.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-tests-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-router-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-applications-unmanaged-am-launcher-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-api-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-client-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-applicationhistoryservice-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-applications-mawo-core-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-common-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-timeline-pluginstorage-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-applications-distributedshell-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-sharedcachemanager-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-registry-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-services-core-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-common-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-nodemanager-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-services-api-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-resourcemanager-3.3.3.jar:/opt/software/hadoop/hadoop-3.3.3/share/hadoop/yarn/hadoop-yarn-server-web-proxy-3.3.3.jar
STARTUP_MSG:   build = https://github.com/apache/hadoop.git -r d37586cbda38c338d9fe481addda5a05fb516f71; compiled by 'stevel' on 2022-05-09T16:36Z
STARTUP_MSG:   java = 1.8.0_212
************************************************************/
2024-03-13 19:15:15,949 INFO namenode.NameNode: registered UNIX signal handlers for [TERM, HUP, INT]
2024-03-13 19:15:15,998 INFO namenode.NameNode: createNameNode [-format]
2024-03-13 19:15:16,218 INFO namenode.NameNode: Formatting using clusterid: CID-5a1f55bb-1756-4ae2-872b-1b951aa0eb0d
2024-03-13 19:15:16,237 INFO namenode.FSEditLog: Edit logging is async:true
2024-03-13 19:15:16,268 INFO namenode.FSNamesystem: KeyProvider: null
2024-03-13 19:15:16,269 INFO namenode.FSNamesystem: fsLock is fair: true
2024-03-13 19:15:16,269 INFO namenode.FSNamesystem: Detailed lock hold time metrics enabled: false
2024-03-13 19:15:16,272 INFO namenode.FSNamesystem: fsOwner                = xuhaixing (auth:SIMPLE)
2024-03-13 19:15:16,272 INFO namenode.FSNamesystem: supergroup             = supergroup
2024-03-13 19:15:16,272 INFO namenode.FSNamesystem: isPermissionEnabled    = true
2024-03-13 19:15:16,272 INFO namenode.FSNamesystem: isStoragePolicyEnabled = true
2024-03-13 19:15:16,272 INFO namenode.FSNamesystem: HA Enabled: false
2024-03-13 19:15:16,297 INFO common.Util: dfs.datanode.fileio.profiling.sampling.percentage set to 0. Disabling file IO profiling
2024-03-13 19:15:16,303 INFO blockmanagement.DatanodeManager: dfs.block.invalidate.limit: configured=1000, counted=60, effected=1000
2024-03-13 19:15:16,304 INFO blockmanagement.DatanodeManager: dfs.namenode.datanode.registration.ip-hostname-check=true
2024-03-13 19:15:16,306 INFO blockmanagement.BlockManager: dfs.namenode.startup.delay.block.deletion.sec is set to 000:00:00:00.000
2024-03-13 19:15:16,306 INFO blockmanagement.BlockManager: The block deletion will start around 2024 Mar 13 19:15:16
2024-03-13 19:15:16,307 INFO util.GSet: Computing capacity for map BlocksMap
2024-03-13 19:15:16,307 INFO util.GSet: VM type       = 64-bit
2024-03-13 19:15:16,307 INFO util.GSet: 2.0% max memory 839.5 MB = 16.8 MB
2024-03-13 19:15:16,307 INFO util.GSet: capacity      = 2^21 = 2097152 entries
2024-03-13 19:15:16,312 INFO blockmanagement.BlockManager: Storage policy satisfier is disabled
2024-03-13 19:15:16,312 INFO blockmanagement.BlockManager: dfs.block.access.token.enable = false
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManagerSafeMode: dfs.namenode.safemode.threshold-pct = 0.999
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManagerSafeMode: dfs.namenode.safemode.min.datanodes = 0
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManagerSafeMode: dfs.namenode.safemode.extension = 30000
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManager: defaultReplication         = 1
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManager: maxReplication             = 512
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManager: minReplication             = 1
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManager: maxReplicationStreams      = 2
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManager: redundancyRecheckInterval  = 3000ms
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManager: encryptDataTransfer        = false
2024-03-13 19:15:16,317 INFO blockmanagement.BlockManager: maxNumBlocksToLog          = 1000
2024-03-13 19:15:16,343 INFO namenode.FSDirectory: GLOBAL serial map: bits=29 maxEntries=536870911
2024-03-13 19:15:16,343 INFO namenode.FSDirectory: USER serial map: bits=24 maxEntries=16777215
2024-03-13 19:15:16,343 INFO namenode.FSDirectory: GROUP serial map: bits=24 maxEntries=16777215
2024-03-13 19:15:16,343 INFO namenode.FSDirectory: XATTR serial map: bits=24 maxEntries=16777215
2024-03-13 19:15:16,350 INFO util.GSet: Computing capacity for map INodeMap
2024-03-13 19:15:16,350 INFO util.GSet: VM type       = 64-bit
2024-03-13 19:15:16,351 INFO util.GSet: 1.0% max memory 839.5 MB = 8.4 MB
2024-03-13 19:15:16,351 INFO util.GSet: capacity      = 2^20 = 1048576 entries
2024-03-13 19:15:16,356 INFO namenode.FSDirectory: ACLs enabled? true
2024-03-13 19:15:16,356 INFO namenode.FSDirectory: POSIX ACL inheritance enabled? true
2024-03-13 19:15:16,356 INFO namenode.FSDirectory: XAttrs enabled? true
2024-03-13 19:15:16,357 INFO namenode.NameNode: Caching file names occurring more than 10 times
2024-03-13 19:15:16,360 INFO snapshot.SnapshotManager: Loaded config captureOpenFiles: false, skipCaptureAccessTimeOnlyChange: false, snapshotDiffAllowSnapRootDescendant: true, maxSnapshotLimit: 65536
2024-03-13 19:15:16,361 INFO snapshot.SnapshotManager: SkipList is disabled
2024-03-13 19:15:16,364 INFO util.GSet: Computing capacity for map cachedBlocks
2024-03-13 19:15:16,364 INFO util.GSet: VM type       = 64-bit
2024-03-13 19:15:16,364 INFO util.GSet: 0.25% max memory 839.5 MB = 2.1 MB
2024-03-13 19:15:16,364 INFO util.GSet: capacity      = 2^18 = 262144 entries
2024-03-13 19:15:16,378 INFO metrics.TopMetrics: NNTop conf: dfs.namenode.top.window.num.buckets = 10
2024-03-13 19:15:16,378 INFO metrics.TopMetrics: NNTop conf: dfs.namenode.top.num.users = 10
2024-03-13 19:15:16,378 INFO metrics.TopMetrics: NNTop conf: dfs.namenode.top.windows.minutes = 1,5,25
2024-03-13 19:15:16,380 INFO namenode.FSNamesystem: Retry cache on namenode is enabled
2024-03-13 19:15:16,380 INFO namenode.FSNamesystem: Retry cache will use 0.03 of total heap and retry cache entry expiry time is 600000 millis
2024-03-13 19:15:16,381 INFO util.GSet: Computing capacity for map NameNodeRetryCache
2024-03-13 19:15:16,381 INFO util.GSet: VM type       = 64-bit
2024-03-13 19:15:16,381 INFO util.GSet: 0.029999999329447746% max memory 839.5 MB = 257.9 KB
2024-03-13 19:15:16,381 INFO util.GSet: capacity      = 2^15 = 32768 entries
2024-03-13 19:15:16,396 INFO namenode.FSImage: Allocated new BlockPoolId: BP-351859838-200.200.200.10-1710328516391
2024-03-13 19:15:16,403 INFO common.Storage: Storage directory /tmp/hadoop-xuhaixing/dfs/name has been successfully formatted.
2024-03-13 19:15:16,423 INFO namenode.FSImageFormatProtobuf: Saving image file /tmp/hadoop-xuhaixing/dfs/name/current/fsimage.ckpt_0000000000000000000 using no compression
2024-03-13 19:15:16,493 INFO namenode.FSImageFormatProtobuf: Image file /tmp/hadoop-xuhaixing/dfs/name/current/fsimage.ckpt_0000000000000000000 of size 404 bytes saved in 0 seconds .
2024-03-13 19:15:16,502 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid >= 0
2024-03-13 19:15:16,514 INFO namenode.FSNamesystem: Stopping services started for active state
2024-03-13 19:15:16,514 INFO namenode.FSNamesystem: Stopping services started for standby state
2024-03-13 19:15:16,516 INFO namenode.FSImage: FSImageSaver clean checkpoint: txid=0 when meet shutdown.
2024-03-13 19:15:16,517 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at master/200.200.200.10
************************************************************/
```

```bash
[xuhaixing@master hadoop-3.3.3]$ ll
total 88
drwxr-xr-x. 2 xuhaixing xuhaixing   203 May 10  2022 bin
drwxr-xr-x. 3 xuhaixing xuhaixing    20 May 10  2022 etc
drwxr-xr-x. 2 xuhaixing xuhaixing   106 May 10  2022 include
drwxr-xr-x. 3 xuhaixing xuhaixing    20 May 10  2022 lib
drwxr-xr-x. 4 xuhaixing xuhaixing   288 May 10  2022 libexec
-rw-rw-r--. 1 xuhaixing xuhaixing 23472 May 10  2022 LICENSE-binary
drwxr-xr-x. 2 xuhaixing xuhaixing  4096 May 10  2022 licenses-binary
-rw-rw-r--. 1 xuhaixing xuhaixing 15217 May 10  2022 LICENSE.txt
drwxrwxr-x. 2 xuhaixing xuhaixing    42 Mar 13 19:15 logs
-rw-rw-r--. 1 xuhaixing xuhaixing 29473 May 10  2022 NOTICE-binary
-rw-rw-r--. 1 xuhaixing xuhaixing  1541 Apr 22  2022 NOTICE.txt
-rw-rw-r--. 1 xuhaixing xuhaixing   175 Apr 22  2022 README.txt
drwxr-xr-x. 3 xuhaixing xuhaixing  4096 May 10  2022 sbin
drwxr-xr-x. 4 xuhaixing xuhaixing    31 May 10  2022 share

```

看到新增的logs了吗？如果看到了就说明初始化成功了！

让我们运行一下

```bash
[xuhaixing@master hadoop-3.3.3]$ sbin/start-dfs.sh
Starting namenodes on [master]
Starting datanodes
localhost: Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
Starting secondary namenodes [master]
[xuhaixing@master hadoop-3.3.3]$ jps
5639 NameNode
6008 SecondaryNameNode
5801 DataNode
6141 Jps
```

ok,伪分布式集群搭建完毕，我们打开自己的浏览器输入ip:9870,显示如下页面就说明成功了！我这里的ip是`200.200.200.10:9870`

![](../images/41.png)

<hr />

__OKKK!坚持到这的同学你真的很棒(๑•̀ㅂ•́)و✧，给你点个赞:grin:__

<hr />

## 十一、 `hadoop`完全分布式的实现

#### 做这一步之前我们先保存三个机子的快照！

用`finalshell`打开master的bash

```bash
[xuhaixing@master hadoop-3.3.3]$ cd
[xuhaixing@master ~]$ cd /opt/software/hadoop/hadoop-3.3.3/etc/hadoop/
[xuhaixing@master hadoop]$ vim ./core-site.xml
[xuhaixing@master hadoop]$ vim ./hdfs-site.xml
[xuhaixing@master hadoop]$ vim ./yarn-site.xml
[xuhaixing@master hadoop]$ vim ./mapred-site.xml
[xuhaixing@master hadoop]$ vim ./workers
```

`core-site.xml`配置

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
                <value>hdfs://master:8020</value>
        </property>
         <property>
                <name>hadoop.tmp.dir</name>
                <value>/opt/software/hadoop/hadoop-3.3.3/tmp/</value>
        </property>
</configuration>
```

`hdfs-site.xml`配置

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
                <name>dfs.namenode.http-address</name>
                <value>master:9870</value>
        </property>
        <property>
                <name>dfs.namenode.secondary.http-address</name>
                <value>slave2:9868</value>
        </property>
         <property>
                <name>dfs.replication</name>
                <value>3</value>
        </property>
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>/opt/software/hadoop/hadoop-3.3.3/tmp/dfs/name</value>
        </property>
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>/opt/software/hadoop/hadoop-3.3.3/tmp/dfs/data</value>
         </property>
        <property>
                <name>dfs.permissions</name>
                <value>false</value>
        </property>
</configuration>

```

`yarn-site.xml`配置

```xml
<?xml version="1.0"?>
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
<configuration>

<!-- Site specific YARN configuration properties -->
 <!-- 指定 MR 走 shuffle -->
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
<!-- 指定 ResourceManager 的地址-->
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>slave1</value>
        </property>
<!-- 环境变量的继承 -->
        <property>
                <name>yarn.nodemanager.env-whitelist</name> <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
        </property>
        <property>
                <name>yarn.nodemanager.vmem-check-enabled</name>
                <value>false</value>
        </property>
</configuration>
```

`mapred-site.xml`配置

```xml
<?xml version="1.0"?>
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
<!-- 指定 MapReduce 程序运行在 Yarn 上 -->
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>master:10020</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>master:19888</value>
        </property>
</configuration>

```

`workers`配置

```sh
master
slave1
slave2
```

接下来我们做一下ssh免密登录，由于我master主机给自己免密过了，接下来就不用给自己免密了

```bash
[xuhaixing@master hadoop]$ ssh-copy-id slave1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/xuhaixing/.ssh/id_rsa.pub"
The authenticity of host 'slave1 (200.200.200.11)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
xuhaixing@slave1's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'slave1'"
and check to make sure that only the key(s) you wanted were added.

[xuhaixing@master hadoop]$ ssh-copy-id slave2
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/xuhaixing/.ssh/id_rsa.pub"
The authenticity of host 'slave2 (200.200.200.12)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
xuhaixing@slave2's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'slave2'"
and check to make sure that only the key(s) you wanted were added.
```

我们把视角转移到slave1

```bash
[xuhaixing@slave1 ~]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/xuhaixing/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/xuhaixing/.ssh/id_rsa.
Your public key has been saved in /home/xuhaixing/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vM8SnPaPTN8B3+FMXbhUOiUf1muYCRt9yaeW0bsuD64 xuhaixing@slave1
The key's randomart image is:
+---[RSA 2048]----+
|            . o+=|
|           o .o@=|
|            + X+*|
|       .   . =+*o|
|       .S.  ..ooo|
|        =.   o+o.|
|       ..o.  .+o.|
|        .=.o.o.o |
|         .=E+.+. |
+----[SHA256]-----+
[xuhaixing@slave1 ~]$ ssh-copy-id slave1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/xuhaixing/.                                                                    ssh/id_rsa.pub"
The authenticity of host 'slave1 (200.200.200.11)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter                                                                     out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompt                                                                    ed now it is to install the new keys
xuhaixing@slave1's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'slave1'"
and check to make sure that only the key(s) you wanted were added.

[xuhaixing@slave1 ~]$ ssh-copy-id slave2
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/xuhaixing/.                                                                    ssh/id_rsa.pub"
The authenticity of host 'slave2 (200.200.200.12)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter                                                                     out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompt                                                                    ed now it is to install the new keys
xuhaixing@slave2's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'slave2'"
and check to make sure that only the key(s) you wanted were added.

[xuhaixing@slave1 ~]$ ssh-copy-id master
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/xuhaixing/.                                                                    ssh/id_rsa.pub"
The authenticity of host 'master (200.200.200.10)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter                                                                     out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompt                                                                    ed now it is to install the new keys
xuhaixing@master's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'master'"
and check to make sure that only the key(s) you wanted were added.
```

我们再把视角转移到slave2

```bash
[xuhaixing@slave2 ~]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/xuhaixing/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/xuhaixing/.ssh/id_rsa.
Your public key has been saved in /home/xuhaixing/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:P1NCb1fiTYrE7RyNcGMwCOvSOVXR3vpMnwvJD7mio/g xuhaixing@slave2
The key's randomart image is:
+---[RSA 2048]----+
|        .. .*++  |
|         ..o *.+ |
|        . o o.=.+|
|       o + o =.B.|
|      . S . = *..|
|       . o +..+ .|
|          +  * +o|
|      .  . +  =.+|
|     ..E..o .. o.|
+----[SHA256]-----+
[xuhaixing@slave2 ~]$ ssh-copy-id slave2
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/xuhaixing/.                                                                    ssh/id_rsa.pub"
The authenticity of host 'slave2 (200.200.200.12)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter                                                                     out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompt                                                                    ed now it is to install the new keys
xuhaixing@slave2's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'slave2'"
and check to make sure that only the key(s) you wanted were added.

[xuhaixing@slave2 ~]$ ssh-copy-id slave1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/xuhaixing/.                                                                    ssh/id_rsa.pub"
The authenticity of host 'slave1 (200.200.200.11)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter                                                                     out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompt                                                                    ed now it is to install the new keys
xuhaixing@slave1's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'slave1'"
and check to make sure that only the key(s) you wanted were added.

[xuhaixing@slave2 ~]$ ssh-copy-id master
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/xuhaixing/.                                                                    ssh/id_rsa.pub"
The authenticity of host 'master (200.200.200.10)' can't be established.
ECDSA key fingerprint is SHA256:3GuZfE/nrlF26oOH6Vv/Dl964VawhyMtrJPRb6V7ld4.
ECDSA key fingerprint is MD5:81:ed:e5:1b:de:76:51:94:f4:40:f7:d2:cd:78:16:ea.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter                                                                     out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompt                                                                    ed now it is to install the new keys
xuhaixing@master's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'master'"
and check to make sure that only the key(s) you wanted were added.
```

这样三台机子的免密都做好了！

接下来分发一下`hadoop`配置

```bash
[xuhaixing@master hadoop]$ sudo scp -r /opt/software/ root@slave1:/opt/
root@slave1's password:
# 省略N行
[xuhaixing@master hadoop]$ sudo scp -r /opt/software/ root@slave2:/opt/
root@slave2's password:
# 省略N行
```

然后我们为两个奴隶机的/opt/software授权给xuhaixing，运行会显示权限不足无法创建文件夹

我们以slave1作为演示，slave2执行相同操作即可

```bash
[xuhaixing@slave1 ~]$ sudo chown -R xuhaixing:xuhaixing /opt/software/
[sudo] password for xuhaixing:
[xuhaixing@slave1 ~]$ cd /opt/
[xuhaixing@slave1 opt]$ ll
total 0
drwxr-xr-x. 2 root      root       6 Oct 31  2018 rh
drwxr-xr-x. 3 xuhaixing xuhaixing 20 Mar 13 18:47 software
```

ok，搭建完毕了，我们来把三台机子的`hadoop`集群全都启动！

master之前搭建了为分布式集群，我们要先停止之前的进程，不然会失败

```bash
[xuhaixing@master ~]$ jps
5639 NameNode
6008 SecondaryNameNode
5801 DataNode
7801 Jps
[xuhaixing@master ~]$ cd /opt/software/hadoop/hadoop-3.3.3/
[xuhaixing@master hadoop-3.3.3]$ sbin/stop-dfs.sh
Stopping namenodes on [master]
Stopping datanodes
Stopping secondary namenodes [master]
[xuhaixing@master hadoop-3.3.3]$ jps
8351 Jps
```

由于我们配置过了`workers`所以我们现在可以群启了，我们先进入master

```bash
[xuhaixing@master hadoop-3.3.3]$ hdfs namenode -format
# 省略一些启动项
[xuhaixing@master hadoop-3.3.3]$ start-dfs.sh
Starting namenodes on [master]
Starting datanodes
Starting secondary namenodes [slave2]
[xuhaixing@master hadoop-3.3.3]$ mapred --daemon start historyserver
```

然后再进入`slave1`:

```
[xuhaixing@slave1 hadoop-3.3.3]$ start-yarn.sh
Starting resourcemanager
Starting nodemanagers
```



我们查看启动后的进程

master:

```bash
[xuhaixing@master hadoop-3.3.3]$ jps
5393 JobHistoryServer
4770 NameNode
5250 NodeManager
4947 DataNode
5453 Jps
```

slave1:

```bash
[xuhaixing@slave1 hadoop-3.3.3]$ jps
3443 Jps
3108 NodeManager
2971 ResourceManager
2781 DataNode
```

slave2:

```bash
[xuhaixing@slave2 hadoop-3.3.3]$ jps
2899 SecondaryNameNode
2775 DataNode
3111 Jps
3006 NodeManager
```

最后我们打开浏览器：输入master的ip:9870

![](../images/42.png)

![](../images/43.png)

我们再输入slave1的ip:8088

![](../images/44.png)

看到这些页面我们的`hadoop`完全分布式也就搭建完毕了！

__你们也做到这了吗?:happy:__



在这里我们按照老师要求，把公共环境变量放在`/etc/profile`里，比如`JDK`。私有环境变量放在`~/.bash_profile`下，比如`HADOOP_HOME`



我这里以master机器为示例，slave1和slave2也执行一样的操作，我就不演示了！

```bash
[xuhaixing@master hadoop]$ sudo vim /etc/profile
[sudo] password for xuhaixing:
[xuhaixing@master hadoop]$ source /etc/profile
[xuhaixing@master hadoop]$ sudo vim ~/.bash_profile
[xuhaixing@master hadoop]$ source ~/.bash_profile
```

`profile`

```sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_212
export PATH=${JAVA_HOME}/bin:$PATH  
```

`bash_profile`

```sh
export HADOOP_HOME=/opt/software/hadoop/hadoop-3.3.3
PATH=$PATH:$HOME/.local/bin:$HOME/bin
export PATH=${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:$PATH
export PATH
```



```bash
[xuhaixing@master hadoop]$ java -version
java version "1.8.0_212"
Java(TM) SE Runtime Environment (build 1.8.0_212-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.212-b10, mixed mode)
[xuhaixing@master hadoop]$ hadoop version
Hadoop 3.3.3
Source code repository https://github.com/apache/hadoop.git -r d37586cbda38c338d9fe481addda5a05fb516f71
Compiled by stevel on 2022-05-09T16:36Z
Compiled with protoc 3.7.1
From source with checksum eb96dd4a797b6989ae0cdb9db6efc6
This command was run using /opt/software/hadoop/hadoop-3.3.3/share/hadoop/common/hadoop-common-3.3.3.jar
```



<hr />

__所有同学向我看齐！__如果你们要重启集群，记得先试用`sbin/stop-all.sh`然后删除hadoop-3.3.3下的生成的tmp文件夹，具体命令可以参考我这里给的示例(这里用master机器做作为示例，两个奴隶机执行一样的操作即可)————此错误由__温志鹏__提出：

```bash
[xuhaixing@master hadoop-3.3.3]$ sbin/stop-all.sh
WARNING: Stopping all Apache Hadoop daemons as xuhaixing in 10 seconds.
WARNING: Use CTRL-C to abort.
Stopping namenodes on [master]
Stopping datanodes
Stopping secondary namenodes [slave1]
Stopping nodemanagers
Stopping resourcemanager
[xuhaixing@master hadoop-3.3.3]$ ll
total 92
drwxr-xr-x. 2 xuhaixing xuhaixing   203 Mar 13 18:48 bin
drwxr-xr-x. 3 xuhaixing xuhaixing    20 Mar 13 18:48 etc
drwxr-xr-x. 2 xuhaixing xuhaixing   106 Mar 13 18:48 include
drwxr-xr-x. 3 xuhaixing xuhaixing    20 Mar 13 18:48 lib
drwxr-xr-x. 4 xuhaixing xuhaixing   288 Mar 13 18:48 libexec
-rw-r--r--. 1 xuhaixing xuhaixing 23472 Mar 13 22:46 LICENSE-binary
drwxr-xr-x. 2 xuhaixing xuhaixing  4096 Mar 13 18:48 licenses-binary
-rw-r--r--. 1 xuhaixing xuhaixing 15217 Mar 13 22:46 LICENSE.txt
drwxrwxr-x. 3 xuhaixing xuhaixing  4096 Mar 15 00:45 logs
-rw-r--r--. 1 xuhaixing xuhaixing 29473 Mar 13 22:46 NOTICE-binary
-rw-r--r--. 1 xuhaixing xuhaixing  1541 Mar 13 22:46 NOTICE.txt
-rw-r--r--. 1 xuhaixing xuhaixing   175 Mar 13 22:46 README.txt
drwxr-xr-x. 3 xuhaixing xuhaixing  4096 Mar 13 18:48 sbin
drwxr-xr-x. 4 xuhaixing xuhaixing    31 Mar 13 18:48 share
drwxrwxr-x. 3 xuhaixing xuhaixing    26 Mar 15 00:49 tmp

[xuhaixing@master hadoop-3.3.3]$ rm -r ./tmp/
[xuhaixing@master hadoop-3.3.3]$ ll
total 92
drwxr-xr-x. 2 xuhaixing xuhaixing   203 May 10  2022 bin
drwxr-xr-x. 3 xuhaixing xuhaixing    20 May 10  2022 etc
drwxr-xr-x. 2 xuhaixing xuhaixing   106 May 10  2022 include
drwxr-xr-x. 3 xuhaixing xuhaixing    20 May 10  2022 lib
drwxr-xr-x. 4 xuhaixing xuhaixing   288 May 10  2022 libexec
-rw-rw-r--. 1 xuhaixing xuhaixing 23472 May 10  2022 LICENSE-binary
drwxr-xr-x. 2 xuhaixing xuhaixing  4096 May 10  2022 licenses-binary
-rw-rw-r--. 1 xuhaixing xuhaixing 15217 May 10  2022 LICENSE.txt
drwxrwxr-x. 3 xuhaixing xuhaixing  4096 Mar 14 16:45 logs
-rw-rw-r--. 1 xuhaixing xuhaixing 29473 May 10  2022 NOTICE-binary
-rw-rw-r--. 1 xuhaixing xuhaixing  1541 Apr 22  2022 NOTICE.txt
-rw-rw-r--. 1 xuhaixing xuhaixing   175 Apr 22  2022 README.txt
drwxr-xr-x. 3 xuhaixing xuhaixing  4096 May 10  2022 sbin
drwxr-xr-x. 4 xuhaixing xuhaixing    31 May 10  2022 share
```

