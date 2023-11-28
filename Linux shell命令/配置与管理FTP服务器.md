# 配置与管理FTP服务器

__项目实施前提条件:虚拟机与物理机能够相互`ping`通__

## 1.1 安装,启动与停止vsftpd服务

1. 安装vsftpd服务

​	安装过程如下:

```powershell
[root@Server02 ~]# rpm -q vsftpd
未安装软件包 vsftpd 
[root@Server02 ~]# mount /dev/cdrom /media
mount: /media: WARNING: device write-protected, mounted read-only.
[root@Server02 ~]# dnf clean all
Updating Subscription Management repositories.
This system is registered to Red Hat Subscription Management, but is not receiving updates. You can use subscription-manager to assign subscriptions.
21 文件已删除
[root@Server02 ~]# dnf install vsftpd -y
Updating Subscription Management repositories.
This system is registered to Red Hat Subscription Management, but is not receiving updates. You can use subscription-manager to assign subscriptions.
Red Hat Enterprise Linux 8 for x86_64 - BaseOS  2.1 MB/s |  63 MB     00:30    
Red Hat Enterprise Linux 8 for x86_64 - AppStre 5.8 MB/s |  58 MB     00:09    
上次元数据过期检查：0:00:06 前，执行于 2023年11月28日 星期二 07时13分46秒。
依赖关系解决。
================================================================================
 软件包    架构      版本             仓库                                 大小
================================================================================
安装:
 vsftpd    x86_64    3.0.3-36.el8     rhel-8-for-x86_64-appstream-rpms    181 k

事务概要
================================================================================
安装  1 软件包

总下载：181 k
安装大小：347 k
下载软件包：
vsftpd-3.0.3-36.el8.x86_64.rpm                   42 kB/s | 181 kB     00:04    
--------------------------------------------------------------------------------
总计                                             41 kB/s | 181 kB     00:04     
警告：/var/cache/dnf/rhel-8-for-x86_64-appstream-rpms-9d3886b51bb367d7/packages/vsftpd-3.0.3-36.el8.x86_64.rpm: 头V3 RSA/SHA256 Signature, 密钥 ID fd431d51: NOKEY
Red Hat Enterprise Linux 8 for x86_64 - AppStre 4.9 MB/s | 5.0 kB     00:00    
导入 GPG 公钥 0xFD431D51:
 Userid: "Red Hat, Inc. (release key 2) <security@redhat.com>"
 指纹: 567E 347A D004 4ADE 55BA 8A5F 199E 2F91 FD43 1D51
 来自: /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
导入公钥成功
导入 GPG 公钥 0xD4082792:
 Userid: "Red Hat, Inc. (auxiliary key) <security@redhat.com>"
 指纹: 6A6A A7C9 7C88 90AE C6AE BFE2 F76F 66C3 D408 2792
 来自: /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
导入公钥成功
运行事务检查
事务检查成功。
运行事务测试
事务测试成功。
运行事务
  准备中  :                                                                 1/1 
  安装    : vsftpd-3.0.3-36.el8.x86_64                                      1/1 
  运行脚本: vsftpd-3.0.3-36.el8.x86_64                                      1/1 
  验证    : vsftpd-3.0.3-36.el8.x86_64                                      1/1 
Installed products updated.

已安装:
  vsftpd-3.0.3-36.el8.x86_64                                                    

完毕！
[root@Server02 ~]# dnf install ftp -y
Updating Subscription Management repositories.
This system is registered to Red Hat Subscription Management, but is not receiving updates. You can use subscription-manager to assign subscriptions.
上次元数据过期检查：0:01:23 前，执行于 2023年11月28日 星期二 07时13分46秒。
依赖关系解决。
================================================================================
 软件包  架构       版本             仓库                                  大小
================================================================================
安装:
 ftp     x86_64     0.17-78.el8      rhel-8-for-x86_64-appstream-rpms      70 k

事务概要
================================================================================
安装  1 软件包

总下载：70 k
安装大小：112 k
下载软件包：
ftp-0.17-78.el8.x86_64.rpm                       22 kB/s |  70 kB     00:03    
--------------------------------------------------------------------------------
总计                                             22 kB/s |  70 kB     00:03     
运行事务检查
事务检查成功。
运行事务测试
事务测试成功。
运行事务
  准备中  :                                                                 1/1 
  安装    : ftp-0.17-78.el8.x86_64                                          1/1 
  运行脚本: ftp-0.17-78.el8.x86_64                                          1/1 
  验证    : ftp-0.17-78.el8.x86_64                                          1/1 
Installed products updated.

已安装:
  ftp-0.17-78.el8.x86_64                                                        

完毕！
[root@Server02 ~]# rpm -qa|grep vsftpd
vsftpd-3.0.3-36.el8.x86_64
```

2. 启动,重启,随系统启动,停止vsftpd服务

    若要重新启动vsftpd服务,随系统启动,开放防火墙,开放SELinux和停止vsftpd服务,则输入以下命令

```powershell
[root@Server02 ~]# systemctl restart vsftpd
[root@Server02 ~]# systemctl enable vsftpd
Created symlink /etc/systemd/system/multi-user.target.wants/vsftpd.service → /usr/lib/systemd/system/vsftpd.service.
[root@Server02 ~]# firewall-cmd --permanent --add-service=ftp
success
[root@Server02 ~]# firewall-cmd --reload
success
[root@Server02 ~]# setsebool -P ftpd_full_access=on
[root@Server02 ~]# systemctl stop vsftpd
```

__执行最后一句关闭`vsftpd`服务后记得执行第一行命令将它重启这里我就不演示了__

<hr />

## 1.2 认识vsftpd的配置文件

```powershell
[root@Server02 ~]# mv /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf.bak
[root@Server02 ~]# grep -v "#" /etc/vsftpd/vsftpd.conf.bak > /etc/vsftpd/vsftpd.conf
[root@Server02 ~]# cat /etc/vsftpd/vsftpd.conf -n
     1	anonymous_enable=NO
     2	local_enable=YES
     3	write_enable=YES
     4	local_umask=022
     5	dirmessage_enable=YES
     6	xferlog_enable=YES
     7	connect_from_port_20=YES
     8	xferlog_std_format=YES
     9	listen=NO
    10	listen_ipv6=YES
    11	
    12	pam_service_name=vsftpd
    13	userlist_enable=YES
```

1. 配置匿名用户登录FTP服务器实例

- 新建测试文件,编辑/etc/vsftpd/vsftpd.conf

    ```powershell
    [root@Server02 ~]# touch /var/ftp/pub/sample.tar
    [root@Server02 ~]# vim /etc/vsftpd/vsftpd.conf
    ```

    vsftpd.conf文件输入添加一下内容:

    ```sh
    anonymous_enable=YES
    # 允许匿名用户登录
    anon_root=/var/ftp
    # 设置匿名用户的根目录为/var/ftp
    anon_upload_enable=YES
    # 允许匿名用户上传文件
    anon_mkdir_write_enable=YES
    # 允许匿名用户创建目录
    ```

2. 允许SELinux,让防火墙放行ftp服务,重启vsftpd服务.

    ```powershell
    [root@Server02 ~]# setenforce 0
    [root@Server02 ~]# firewall-cmd --permanent --add-service=ftp
    Warning: ALREADY_ENABLED: ftp
    success
    [root@Server02 ~]# firewall-cmd --reload
    success
    [root@Server02 ~]# firewall-cmd --list-all
    public (active)
      target: default
      icmp-block-inversion: no
      interfaces: ens160
      sources: 
      services: cockpit dhcpv6-client ftp ssh
      ports: 
      protocols: 
      masquerade: no
      forward-ports: 
      source-ports: 
      icmp-blocks: 
      rich rules: 
    	
    [root@Server02 ~]# systemctl restart vsftpd
    ```

    在windows11客户端的资源管理器中输入`ftp://<你Linux的IP地址>`,打开pub目录,新建一个文件夹,结果出错了,如书上265页14-2所示:

    __什么原因呢?系统的本地权限没有设置!__

3. 设置本地系统权限,将属主设置为`ftp`,或者为pub目录赋予其他用户写权限.

    ```powershell
    [root@Server02 ~]# systemctl restart vsftpd
    [root@Server02 ~]# ll -ld /var/ftp/pub
    drwxr-xr-x. 2 root root 24 11月 28 07:31 /var/ftp/pub
    [root@Server02 ~]# chown ftp /var/ftp/pub
    [root@Server02 ~]# chmod o+w /var/ftp/pub
    [root@Server02 ~]# ll -ld /var/ftp/pub
    drwxr-xrwx. 2 ftp root 24 11月 28 07:31 /var/ftp/pub
    [root@Server02 ~]# systemctl restart vsftpd
    ```

    这样就能创建文件夹了,但是?好像也就只能创建文件夹了,没办法更改文件夹的名称?

    那就在vsftpd.conf中添加一行`anon_other_write_enable=YES`吧!

    这行参数的作用是__允许匿名用户修改目录名称或删除目录__

```powershell
[root@Server02 ~]# vim /etc/vsftpd/vsftpd.conf

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
```

```powershell
[root@Server02 ~]# systemctl restart vsftpd
```

重启vsftpd服务后就能修改刚才没能修改的文件目录的名称了,我将它修改为test了,修改成功!

<hr />

### 你们也成功了吗?:v: