# Golang的安装与配置

## 1. Golang的下载

我们可以去`Golang`的官网下载我们所需的版本:

> https://golang.google.cn/dl/

我们这里下载的是Golang `1.16.15`的版本



## 2. Golang的安装与环境变量的配置

### 一. 安装Golang到电脑上:evergreen_tree:

1. 进入安装界面,点击`next`进入下一步

![image-20231119180516384](C:\Users\14654\AppData\Roaming\Typora\typora-user-images\image-20231119180516384.png)

2. 然后选择一个Golang的安装路径,点击`next`进入下一步

![image-20231119180650215](C:\Users\14654\AppData\Roaming\Typora\typora-user-images\image-20231119180650215.png)

3. 然后点击`Install`完成安装

![image-20231119180928226](C:\Users\14654\AppData\Roaming\Typora\typora-user-images\image-20231119180928226.png)

这样我们的Golang就在电脑上安装好了:smiley:



### 二. 配置Golang的环境变量:evergreen_tree:

添加以下变量:

- `GOROOT` --->这里是Golang的安装目录

    > GOROOT	D:\Golang1.16.15\Go

- `GOPATH`--->这里是Golang的工作路径

    > GOPATH	D:\Golang1.16.15\GoWorks

在Golang的安装目录的同级目录下创建一个`GoWorks`的文件夹,然后在该文件夹下再创建三个文件夹分别为`src`-`pkg`-`bin`.这里不做过多赘述,后面会讲到.



最后我们运行`cmd`来检测我们的环境变量是否配置成功!

![image-20231119182019605](C:\Users\14654\AppData\Roaming\Typora\typora-user-images\image-20231119182019605.png)

Oh!看来已经配置成功了!:+1: