# 作业`sh01.sh~sh09.sh`

- 注意:Linux系统复制是`Ctrl`+`insert`,粘贴是`Ctrl`+`insert`

- 进入文本编辑器是:

    > vim 文件名.文件后缀名

    - 编辑完成后保存并退出是`Esc`后按`:`+`wq`

        不保存直接退出是`Esc`后按`:`+`q`

## sh01.sh

```shell
[root@Server02 ~]# cd /root
[root@Server02 ~]# ls
公共  视频  文档  音乐  anaconda-ks.cfg       scripts
模板  图片  下载  桌面  initial-setup-ks.cfg
[root@Server02 ~]# cd ;mkdir /root/scripts; cd/root/scripts
[root@Server02 ~]# vim sh01.sh
```

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

执行sh文件[后续都是这样调用我不再过多赘述]:

```shell
[root@Server02 ~]# sh sh01.sh
Hello World!  

```

## sh02.sh

```shell
[root@Server02 ~]# vim sh02.sh
```

```shell
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH


read -p "Please input your first name:" firstname
read -p "Please input your last name:" lastname
echo -e "\nYour full name is: $firstname $lastname"
exit 0
~
```

```shell
[root@Server02 ~]# sh sh02.sh
Please input your first name:Kato
Please input your last name:Megumi

Your full name is: Kato Megumi
```

## sh03.sh

```shell
[root@Server02 ~]# vim sh03.sh
```

```shell
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
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

```shell
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

```shell
[root@Server02 ~]# vim sh04.sh
```

```shell
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

```shell
[root@Server02 ~]# sh sh04.sh
You SHOULD input 2 numbers, I will cross them! 

first number:22
second number:11

The result of 22*11 is ==> 242
```

## sh05.sh

```shell
[root@Server02 ~]# vim sh05.sh
```

```shell
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

```shell
[root@Server02 ~]# sh sh05.sh
Please input a filename, I will check the filename's type and permission. 


Input a filename:test1
The filename: test1 is a regulare file
And permissions are : readable writable
```

## sh06.sh

```shell
[root@Server02 ~]# vim sh06.sh
```

```shell
#!/bin/bash
#2023/11/14
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input (Y/N)" yn
[ "$yn" == "Y" -o "$yn" == "y" ] && echo "OK, continue" && exit 0
[ "$yn" == "N" -o "$yn" == "n" ] && echo "Oh, interrupt!" && exit 0
echo "I don't know what your choice is" && exit 0
```

```shell
[root@Server02 ~]# vim sh06.sh
[root@Server02 ~]# sh sh06.sh
Please input (Y/N)y
OK, continue
```

## sh06-2.sh

```shell
[root@Server02 ~]# cp sh06.sh sh06-2.sh
[root@Server02 ~]# vim sh06-2.sh
```

```shell
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

```shell
[root@Server02 ~]# sh sh06-2.sh
Please input (Y/N)y
Ok,continue
```

## sh06-3.sh

```shell
[root@Server02 ~]# cp sh06-2.sh sh06-3.sh
[root@Server02 ~]# vim sh06-3.sh
```

```shell
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

```shell
[root@Server02 ~]# sh sh06-3.sh
Please input (Y/N)y
Ok,continue
```

## sh09.sh

```shell
[root@Server02 ~]# vim sh09.sh
```

```shell
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

```shell
[root@Server02 ~]# sh sh09.sh
You MUST input parameters, ex> {sh09.sh someword}
[root@Server02 ~]# sh sh09.sh hello
Hello, who are you?
```



*顺利完成!*