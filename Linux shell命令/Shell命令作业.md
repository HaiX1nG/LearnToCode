# 作业`sh01.sh~sh19.sh`:page_with_curl:

- 注意:Linux系统复制是`Ctrl`+`insert`,粘贴是`Ctrl`+`insert`

- 进入文本编辑器是:

    > vim 文件名.文件后缀名

    - 编辑完成后保存并退出是`Esc`后按`:`+`wq`

        不保存直接退出是`Esc`后按`:`+`q`

## sh01.sh

```powershell
[root@Server02 ~]# cd /root
[root@Server02 ~]# ls
公共  视频  文档  音乐  anaconda-ks.cfg       scripts
模板  图片  下载  桌面  initial-setup-ks.cfg
[root@Server02 ~]# cd ;mkdir /root/scripts; cd/root/scripts
[root@Server02 ~]# vim sh01.sh
```

```sh
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

执行sh文件[后续都是这样调用我不再过多赘述]:

```powershell
[root@Server02 ~]# sh sh01.sh
Hello World!  

```

## sh02.sh

```powershell
[root@Server02 ~]# vim sh02.sh
```

```sh
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH


read -p "Please input your first name:" firstname
read -p "Please input your last name:" lastname
echo -e "\nYour full name is: $firstname $lastname"
exit 0
~
```

```powershell
[root@Server02 ~]# sh sh02.sh
Please input your first name:Kato
Please input your last name:Megumi

Your full name is: Kato Megumi
```

## sh03.sh

```powershell
[root@Server02 ~]# vim sh03.sh
```

```sh
powershellPATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo -e "I will use 'touch'command to create 3 files."
read -p "Please input your filename:" fileuser
filename=${fileuser:-"filename"}

date1=$(date --date='2 days ago' +%Y%m%d)
date2=$(date --date='1 days ago' +%Y%m%d)
date3=$(date +%Y%m%d)

file1=${filename}${date1}
file2=${filename}${date2}
file3=${filename}${date3}

touch "$file1"
touch "$file2"
touch "$file3"

exit 0
~      
```

```powershell
[root@Server02 ~]# sh sh03.sh
I will use 'touch'command to create 3 files.
Please input your filename:test2
[root@Server02 ~]# ll
总用量 44
drwxr-xr-x. 2 root root    6 10月 25 09:06 公共
drwxr-xr-x. 2 root root    6 10月 25 09:06 模板
drwxr-xr-x. 2 root root    6 10月 25 09:06 视频
drwxr-xr-x. 2 root root    6 10月 25 09:06 图片
drwxr-xr-x. 2 root root    6 10月 25 09:06 文档
drwxr-xr-x. 2 root root    6 10月 25 09:06 下载
drwxr-xr-x. 2 root root    6 10月 25 09:06 音乐
drwxr-xr-x. 2 root root    6 10月 25 09:06 桌面
-rw-------. 1 root root 1833 10月 25 08:43 anaconda-ks.cfg
-rw-r--r--. 1 root root    0 11月 14 04:08 filename
-rw-r--r--. 1 root root    0 11月 14 04:08 filename20231114
-rw-r--r--. 1 root root 1988 10月 25 09:04 initial-setup-ks.cfg
drwxr-xr-x. 2 root root    6 11月 14 03:11 scripts
-rw-r--r--. 1 root root  132 11月 14 03:21 sh01.sh
-rw-r--r--. 1 root root  244 11月 14 03:57 sh02.sh
-rw-r--r--. 1 root root  461 11月 14 04:09 sh03.sh
-rw-r--r--. 1 root root  312 11月 14 04:17 sh04.sh
-rw-r--r--. 1 root root  675 11月 14 04:32 sh05.sh
-rw-r--r--. 1 root root  352 11月 14 05:24 sh06-2.sh
-rw-r--r--. 1 root root  329 11月 14 05:27 sh06-3.sh
-rw-r--r--. 1 root root  328 11月 14 05:06 sh06.sh
-rw-r--r--. 1 root root  308 11月 14 05:15 sh09.sh
-rw-r--r--. 1 root root    0 11月 14 04:08 test1
-rw-r--r--. 1 root root    0 11月 14 04:09 test120231112
-rw-r--r--. 1 root root    0 11月 14 04:09 test120231113
-rw-r--r--. 1 root root    0 11月 14 04:09 test120231114
-rw-r--r--. 1 root root    0 11月 14 05:40 test220231112
-rw-r--r--. 1 root root    0 11月 14 05:40 test220231113
-rw-r--r--. 1 root root    0 11月 14 05:40 test220231114
```

## sh04.sh

```powershell
[root@Server02 ~]# vim sh04.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo -e "You SHOULD input 2 numbers, I will cross them! \n"
read -p "first number:" firstnu
read -p "second number:" secnu
total=$(($firstnu*$secnu))
echo -e "\nThe result of $firstnu*$secnu is ==> $total"
exit 0
```

```powershell
[root@Server02 ~]# sh sh04.sh
You SHOULD input 2 numbers, I will cross them! 

first number:22
second number:11

The result of 22*11 is ==> 242
```

## sh05.sh

```powershell
[root@Server02 ~]# vim sh05.sh
```

```sh
#!/bin/bash
#2023/11/14
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo -e "Please input a filename, I will check the filename's type and permission. \n\n"
read -p "Input a filename:" filename
test -z $filename && echo "You MUST input a filename." && exit 0
test ! -e $filename && echo "The filename '$filename' DO NOT exist" && exit 0
test -f $filename && filetype="regulare file"
test -d $filename && filetype="directory"
test -r $filename && perm="readable"
test -w $filename && perm="$perm writable"
test -x $filename && perm="$perm executable"

echo "The filename: $filename is a $filetype"
echo "And permissions are : $perm"
~
```

```powershell
[root@Server02 ~]# sh sh05.sh
Please input a filename, I will check the filename's type and permission. 


Input a filename:test1
The filename: test1 is a regulare file
And permissions are : readable writable
```

## sh06.sh

```powershell
[root@Server02 ~]# vim sh06.sh
```

```sh
#!/bin/bash
#2023/11/14
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input (Y/N)" yn
[ "$yn" == "Y" -o "$yn" == "y" ] && echo "OK, continue" && exit 0
[ "$yn" == "N" -o "$yn" == "n" ] && echo "Oh, interrupt!" && exit 0
echo "I don't know what your choice is" && exit 0
```

```powershell
[root@Server02 ~]# vim sh06.sh
[root@Server02 ~]# sh sh06.sh
Please input (Y/N)y
OK, continue
```

## sh06-2.sh

```powershell
[root@Server02 ~]# cp sh06.sh sh06-2.sh
[root@Server02 ~]# vim sh06-2.sh
```

```sh
#!/bin/bash
#2023/11/14
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input (Y/N)" yn
if [ "$yn" == "Y" ] || [ "$yn" == "y" ]; then
        echo "Ok,continue"
        exit 0
fi
if [ "$yn" == "N" ] || [ "$yn" == "n" ]; then
        echo "Oh, interrupt!"
        exit 0
fi
echo "I don't know what your choice is" && exit 0
```

```powershell
[root@Server02 ~]# sh sh06-2.sh
Please input (Y/N)y
Ok,continue
```

## sh06-3.sh

```powershell
[root@Server02 ~]# cp sh06-2.sh sh06-3.sh
[root@Server02 ~]# vim sh06-3.sh
```

```sh
#!/bin/bash
#2023/11/14
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input (Y/N)" yn
if [ "$yn" == "Y" ] || [ "$yn" == "y" ]; then
        echo "Ok,continue"
elif [ "$yn" == "N" ] || [ "$yn" == "n" ]; then
        echo "Oh, interrupt!"
else
        echo "I don't know what your choice is"
fi
```

```powershell
[root@Server02 ~]# sh sh06-3.sh
Please input (Y/N)y
Ok,continue
```

## sh09.sh

```powershell
[root@Server02 ~]# vim sh09.sh
```

```sh
#!/bin/bash
#2023/11/14
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

if [ "$1" == "hello" ]; then
        echo "Hello, who are you?"
elif [ "$1" == "" ]; then
        echo "You MUST input parameters, ex> {$0 someword}"
else
        echo "The only parameter is 'hello', ex> {$0 hello}"
fi
```

```powershell
[root@Server02 ~]# sh sh09.sh
You MUST input parameters, ex> {sh09.sh someword}
[root@Server02 ~]# sh sh09.sh hello
Hello, who are you?
```

## sh10.sh

```powershell
[root@Server02 ~]# vim sh10.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "Now, I will detect your Linux server's services!"
echo -e "The www, ftp, ssh, and mail will be detect! \n"

testing=$(netstat -tuln | grep ":80")
if [ "$testing" != "" ]; then
        echo "WWW is running in your system."
fi
testing=$(netstat -tuln | grep ":22")
if [ "$testing" != "" ]; then
        echo "SSH is running in your system."
fi
testing=$(netstat -tuln | grep ":21")
if [ "$testing" != "" ]; then
        echo "FTP is running in your system."
fi
testing=$(netstat -tuln | grep ":25")
if [ "$testing" != "" ]; then
        echo "Mail is running in your system."
fi
```

```powershell
[root@Server02 ~]# sh sh10.sh
Now, I will detect your Linux server's services!
The www, ftp, ssh, and mail will be detect! 

SSH is running in your system.
```

## sh09-2.sh

```powershell
[root@Server02 ~]# vim sh09-2.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

case $1 in
        "hello")
        echo "Hello, how are you ?"
        ;;
        "")
        echo "You MUST input parameters, ex> {$0 someword}"
        ;;
*)
        echo "Usage $0"
        ;;
esac
```

```powershell
[root@Server02 ~]# sh sh09-2.sh
You MUST input parameters, ex> {sh09-2.sh someword}
[root@Server02 ~]# sh sh09-2.sh smile
Usage sh09-2.sh
[root@Server02 ~]# sh sh09-2.sh hello
Hello, how are you ?
```

## sh12.sh

```powershell
root@Server02 ~]# vim sh12.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "This program will print your selection !"

case $1 in
        "one")
                echo "Your choice is ONE"
                ;;
        "two")  
                echo "Your choice is TWO"
                ;;
        "three")
                echo "Your choice is THREE"
                ;;
        *)
                echo "Usage $0 {one|two|three}"
                ;;
esac 
```

```powershell
[root@Server02 ~]# sh sh12.sh two
This program will print your selection !
Your choice is TWO
[root@Server02 ~]# sh sh12.sh test
This program will print your selection !
Usage sh12.sh {one|two|three}
```

## sh13.sh

```powershell
[root@Server02 ~]# vim sh13.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

while [ "$yn" != "yes" -a "$yn" != "YES" ]
do
        read -p "Please input yes/YES to stop this program:" yn
done
echo "OK! you input the correct answer."
```

```powershell
[root@Server02 ~]# sh sh13.sh
Please input yes/YES to stop this program:yes
OK! you input the correct answer.
[root@Server02 ~]# sh sh13.sh
Please input yes/YES to stop this program:YES
OK! you input the correct answer.
```

## sh13-2.sh

```powershell
[root@Server02 ~]# vim sh13-2.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

until [ "$yn" == "yes" -o "$yn" == "YES" ]
do
        read -p "Please input yes/YES to stop this program:" yn
done
echo "OK! you input the correct answer."
```

```powershell
[root@Server02 ~]# sh sh13-2.sh
Please input yes/YES to stop this program:yes
OK! you input the correct answer.
[root@Server02 ~]# sh sh13-2.sh
Please input yes/YES to stop this program:YES
OK! you input the correct answer.
```

## sh14.sh

```powershell
[root@Server02 ~]# vim sh14.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

s=0
i=0
while [ "$i" != "100" ]
do
        i=$(($i+1))
        s=$(($s+$i))
done    
echo "The result of '1+2+3+...+100' is ==> $s"
```

```powershell
[root@Server02 ~]# sh sh14.sh
The result of '1+2+3+...+100' is ==> 5050
```

## sh15.sh

```powershell
[root@Server02 ~]# vim sh15.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

for animal in dog cat elephant
do      
        echo "There are ${animal}s..."
done
```

```powershell
[root@Server02 ~]# sh sh15.sh
There are dogs...
There are cats...
There are elephants...
```

## sh16.sh

```powershell
[root@Server02 ~]# vim sh16.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

users=$(cut -d ':' -f1 /etc/passwd)
for username in $users
do      
        id $username
done
```

```powershell
[root@Server02 ~]# sh sh16.sh
uid=0(root) gid=0(root) 组=0(root)
uid=1(bin) gid=1(bin) 组=1(bin)
uid=2(daemon) gid=2(daemon) 组=2(daemon)
uid=3(adm) gid=4(adm) 组=4(adm)
uid=4(lp) gid=7(lp) 组=7(lp)
uid=5(sync) gid=0(root) 组=0(root)
uid=6(shutdown) gid=0(root) 组=0(root)
uid=7(halt) gid=0(root) 组=0(root)
uid=8(mail) gid=12(mail) 组=12(mail)
uid=11(operator) gid=0(root) 组=0(root)
uid=12(games) gid=100(users) 组=100(users)
uid=14(ftp) gid=50(ftp) 组=50(ftp)
uid=65534(nobody) gid=65534(nobody) 组=65534(nobody)
uid=81(dbus) gid=81(dbus) 组=81(dbus)
uid=999(systemd-coredump) gid=997(systemd-coredump) 组=997(systemd-coredump)
uid=193(systemd-resolve) gid=193(systemd-resolve) 组=193(systemd-resolve)
uid=59(tss) gid=59(tss) 组=59(tss)
uid=998(polkitd) gid=996(polkitd) 组=996(polkitd)
uid=997(geoclue) gid=995(geoclue) 组=995(geoclue)
uid=172(rtkit) gid=172(rtkit) 组=172(rtkit)
uid=171(pulse) gid=171(pulse) 组=171(pulse)
uid=107(qemu) gid=107(qemu) 组=107(qemu),36(kvm)
uid=113(usbmuxd) gid=113(usbmuxd) 组=113(usbmuxd)
uid=996(unbound) gid=991(unbound) 组=991(unbound)
uid=32(rpc) gid=32(rpc) 组=32(rpc)
uid=995(gluster) gid=990(gluster) 组=990(gluster)
uid=994(chrony) gid=989(chrony) 组=989(chrony)
uid=993(libstoragemgmt) gid=987(libstoragemgmt) 组=987(libstoragemgmt)
uid=992(setroubleshoot) gid=986(setroubleshoot) 组=986(setroubleshoot)
uid=991(pipewire) gid=985(pipewire) 组=985(pipewire)
uid=990(saslauth) gid=76(saslauth) 组=76(saslauth)
uid=984(dnsmasq) gid=984(dnsmasq) 组=984(dnsmasq)
uid=75(radvd) gid=75(radvd) 组=75(radvd)
uid=983(clevis) gid=982(clevis) 组=982(clevis)
uid=982(cockpit-ws) gid=980(cockpit-ws) 组=980(cockpit-ws)
uid=981(sssd) gid=979(sssd) 组=979(sssd)
uid=980(colord) gid=978(colord) 组=978(colord)
uid=42(gdm) gid=42(gdm) 组=42(gdm)
uid=29(rpcuser) gid=29(rpcuser) 组=29(rpcuser)
uid=979(gnome-initial-setup) gid=977(gnome-initial-setup) 组=977(gnome-initial-setup)
uid=74(sshd) gid=74(sshd) 组=74(sshd)
uid=70(avahi) gid=70(avahi) 组=70(avahi)
uid=72(tcpdump) gid=72(tcpdump) 组=72(tcpdump)
uid=1000(haixing) gid=1000(haixing) 组=1000(haixing),10(wheel)
```

## sh17.sh

```powershell
[root@Server02 ~]# vim sh17.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

network="192.168.10"
for sitenu in $(seq 1 100)
do      
        ping -c 1 -w 1 ${network}.${sitenu} &> /dev/null && result=
0 || result=1
        if [ "$result" == 0 ]; then
                echo "Server ${network}.${sitenu} is UP."
        else    
                echo "Server ${network}.${sitenu} is DOWN."
        fi      
done
```

```powershell
[root@Server02 ~]# sh sh17.sh
Server 192.168.10.1 is DOWN.
Server 192.168.10.2 is DOWN.
Server 192.168.10.3 is DOWN.
Server 192.168.10.4 is DOWN.
Server 192.168.10.5 is DOWN.
Server 192.168.10.6 is UP.
Server 192.168.10.7 is DOWN.
Server 192.168.10.8 is DOWN.
Server 192.168.10.9 is DOWN.
Server 192.168.10.10 is UP.
Server 192.168.10.11 is DOWN.
Server 192.168.10.12 is DOWN.
Server 192.168.10.13 is DOWN.
Server 192.168.10.14 is DOWN.
Server 192.168.10.15 is DOWN.
Server 192.168.10.16 is DOWN.
Server 192.168.10.17 is DOWN.
Server 192.168.10.18 is DOWN.
Server 192.168.10.19 is DOWN.
Server 192.168.10.20 is DOWN.
Server 192.168.10.21 is DOWN.
Server 192.168.10.22 is DOWN.
Server 192.168.10.23 is DOWN.
Server 192.168.10.24 is DOWN.
Server 192.168.10.25 is DOWN.
Server 192.168.10.26 is DOWN.
Server 192.168.10.27 is DOWN.
Server 192.168.10.28 is DOWN.
Server 192.168.10.29 is DOWN.
Server 192.168.10.30 is DOWN.
Server 192.168.10.31 is DOWN.
Server 192.168.10.32 is DOWN.
Server 192.168.10.33 is DOWN.
Server 192.168.10.34 is DOWN.
Server 192.168.10.35 is DOWN.
Server 192.168.10.36 is DOWN.
Server 192.168.10.37 is DOWN.
Server 192.168.10.38 is DOWN.
Server 192.168.10.39 is DOWN.
Server 192.168.10.40 is DOWN.
Server 192.168.10.41 is DOWN.
Server 192.168.10.42 is DOWN.
Server 192.168.10.43 is DOWN.
Server 192.168.10.44 is DOWN.
Server 192.168.10.45 is DOWN.
Server 192.168.10.46 is UP.
Server 192.168.10.47 is DOWN.
Server 192.168.10.48 is DOWN.
Server 192.168.10.49 is DOWN.
Server 192.168.10.50 is DOWN.
Server 192.168.10.51 is DOWN.
Server 192.168.10.52 is DOWN.
Server 192.168.10.53 is DOWN.
Server 192.168.10.54 is DOWN.
Server 192.168.10.55 is DOWN.
Server 192.168.10.56 is DOWN.
Server 192.168.10.57 is DOWN.
Server 192.168.10.58 is DOWN.
Server 192.168.10.59 is DOWN.
Server 192.168.10.60 is DOWN.
Server 192.168.10.61 is DOWN.
Server 192.168.10.62 is DOWN.
Server 192.168.10.63 is DOWN.
Server 192.168.10.64 is DOWN.
Server 192.168.10.65 is DOWN.
Server 192.168.10.66 is DOWN.
Server 192.168.10.67 is DOWN.
Server 192.168.10.68 is DOWN.
Server 192.168.10.69 is DOWN.
Server 192.168.10.70 is DOWN.
Server 192.168.10.71 is DOWN.
Server 192.168.10.72 is DOWN.
Server 192.168.10.73 is DOWN.
Server 192.168.10.74 is DOWN.
Server 192.168.10.75 is DOWN.
Server 192.168.10.76 is DOWN.
Server 192.168.10.77 is DOWN.
Server 192.168.10.78 is DOWN.
Server 192.168.10.79 is UP.
Server 192.168.10.80 is DOWN.
Server 192.168.10.81 is DOWN.
Server 192.168.10.82 is DOWN.
Server 192.168.10.83 is DOWN.
Server 192.168.10.84 is DOWN.
Server 192.168.10.85 is DOWN.
Server 192.168.10.86 is DOWN.
Server 192.168.10.87 is DOWN.
Server 192.168.10.88 is DOWN.
Server 192.168.10.89 is DOWN.
Server 192.168.10.90 is DOWN.
Server 192.168.10.91 is UP.
Server 192.168.10.92 is DOWN.
Server 192.168.10.93 is DOWN.
Server 192.168.10.94 is DOWN.
Server 192.168.10.95 is UP.
Server 192.168.10.96 is DOWN.
Server 192.168.10.97 is DOWN.
Server 192.168.10.98 is DOWN.
Server 192.168.10.99 is UP.
Server 192.168.10.100 is DOWN.
```

## sh18.sh

```powershell
[root@Server02 ~]# vim sh18.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin                
export PATH

read -p "Please input a directory: " dir
if [ "$dir" == "" -o ! -d "$dir" ]; then
        echo "The $dir is NOT exist in your system."
        exit 1
fi

filelist=$(ls $dir)
for filename in $filelist
do
        perm=""
        test -r "$dir/$filename" && perm="$perm readable"
        test -w "$dir/$filename" && perm="$perm writable"
        test -x "$dir/$filename" && perm="$perm executable"
        echo "The file $dir/$filename's permission is $perm "
done
```

```powershell
[root@Server02 ~]# sh sh18.sh
Please input a directory: /var 
The file /var/account's permission is  readable writable executable 
The file /var/adm's permission is  readable writable executable 
The file /var/cache's permission is  readable writable executable 
The file /var/crash's permission is  readable writable executable 
The file /var/db's permission is  readable writable executable 
The file /var/empty's permission is  readable writable executable 
The file /var/ftp's permission is  readable writable executable 
The file /var/games's permission is  readable writable executable 
The file /var/gopher's permission is  readable writable executable 
The file /var/kerberos's permission is  readable writable executable 
The file /var/lib's permission is  readable writable executable 
The file /var/local's permission is  readable writable executable 
The file /var/lock's permission is  readable writable executable 
The file /var/log's permission is  readable writable executable 
The file /var/mail's permission is  readable writable executable 
The file /var/nis's permission is  readable writable executable 
The file /var/opt's permission is  readable writable executable 
The file /var/preserve's permission is  readable writable executable 
The file /var/run's permission is  readable writable executable 
The file /var/spool's permission is  readable writable executable 
The file /var/tmp's permission is  readable writable executable 
The file /var/yp's permission is  readable writable executable 
```

## sh19.sh

```powershell
[root@Server02 ~]# vim sh19.sh
```

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin      
export PATH

read -p "Please input a number, I will count for 1+2+...your_input" nu

s=0
for(( i=1; i<=$nu; i=i+1 ))
do      
        s=$(($s+$i))
done    
echo "The result of '1+2+3...+$nu' is ==> $s"
```

```powershell
[root@Server02 ~]# sh sh19.sh
Please input a number, I will count for 1+2+...your_input: 1000
The result of '1+2+3...+1000' is ==> 500500
```





### __*顺利完成!*__:tada:

