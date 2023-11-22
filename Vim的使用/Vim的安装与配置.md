# Vim安装

想学Vim的使用，起码得先会安装吧╮(￣▽￣””)╭。

前文说到，Vim跨平台，接下来就教大家在三大平台上怎么安装。

> Vim其实是可以用GUI的，但是一般很少用GUI版本。主要讲命令行版本。

## Linux

其实，一般的Linux发行版本和服务器厂商提供的镜像，是自带Vim的。比如[腾讯云轻量应用服务器的Debian系统](https://cloud.tencent.com/act/cps/redirect?redirect=30206&cps_key=825b2fa50ccba7d3668554b568acab71)，均自带Vim：

[![腾讯云轻量应用服务器](https://imagehost.mintimate.cn/post_vim/6d7163efee994e943e2590cb6988fa23.png)](https://imagehost.mintimate.cn/post_vim/6d7163efee994e943e2590cb6988fa23.png)

腾讯云轻量应用服务器



[![腾讯云自带的Vim](https://imagehost.mintimate.cn/post_vim/d6499eb4239c9a125b5ca3dbd9e2d6f1.png)](https://imagehost.mintimate.cn/post_vim/d6499eb4239c9a125b5ca3dbd9e2d6f1.png)

腾讯云自带的Vim



如果你使用其他镜像，或者没安装Vim。使用软件包管理器即可：

```
# CentOS
yum install vim
# Ubuntu/Debian
apt-get install vim
# Archlinux
pacman -S vim

CMAKE
```

[![软件包安装Vim](https://imagehost.mintimate.cn/post_vim/6f79179770da989bb6f3d4177b4a0358.png)](https://imagehost.mintimate.cn/post_vim/6f79179770da989bb6f3d4177b4a0358.png)

软件包安装Vim



当然，你也可以源码编译，[官方源码下载](https://github.com/vim/vim/releases)、[Gitee源码镜像](https://gitee.com/mirrors/vim)

编译参数参考：

```
# 依赖安装
sudo apt install build-essentials git make
sudo apt install libncurses-dev clang
# 当前在源码内
./configure --prefix=/usr/local/vim
make && make install

BASH
```

如果你使用的CentOS，那么需要使用yum安装：`gcc`、`make`和`ncurses-devel`。
之后不要忘记配置vim，到环境变量内，最终效果：
[![编译安装](https://imagehost.mintimate.cn/post_vim/f5f4bc0f4d3e2c15dcc67e68f99692dc.png)](https://imagehost.mintimate.cn/post_vim/f5f4bc0f4d3e2c15dcc67e68f99692dc.png)

编译安装



## macOS

macOS一般自带Vim，但是如果你想自己安装。可以和上文一样手动编译，也可以使用Homebrew进行安装：

```
brew install vim

MIPSASM
```

Homebrew版本vim优先级高于系统自带版本。如果不知道怎么安装Homebrew：[一篇文章，多种方法安装Homebrew](https://www.mintimate.cn/2020/04/05/Homebrew/)

## Windows

没错，Vim是垮系统的，Windows也可以安装。（这不比系统自带的**文本编辑器**好用？）

### 1. 下载Vim

首先我们进入Vim项目地址，下载Windows版本Vim：[vim-win32-installer](https://github.com/vim/vim-win32-installer/releases)

[![我的Windows为x86_x64](https://imagehost.mintimate.cn/post_vim/64efc0fed971aab9787289867124020f.png)](https://imagehost.mintimate.cn/post_vim/64efc0fed971aab9787289867124020f.png)

我的Windows为x86_x64



### 2. 创建&解压

之后，我们在电脑的合适位置，创建一个目录，并解压Vim到其中：

[![效果](https://imagehost.mintimate.cn/post_vim/ca9dd624912a3f5fa41c74667d3a8fa3.png)](https://imagehost.mintimate.cn/post_vim/ca9dd624912a3f5fa41c74667d3a8fa3.png)

效果



### 3. 环境变量

最好，我们设置环境变量即可：

[![设置环境变量](https://imagehost.mintimate.cn/post_vim/962ff0f9dc3998e621af679e5ac03c23.png)](https://imagehost.mintimate.cn/post_vim/962ff0f9dc3998e621af679e5ac03c23.png)

设置环境变量



这个时候，我们使用`CMD`、`Powershell`和`Windwos Terminal`就可以使用Vim了：

[![Windows Terminal](https://imagehost.mintimate.cn/post_vim/6f2cb5d4176b22db5e887805a37ef739.png)](https://imagehost.mintimate.cn/post_vim/6f2cb5d4176b22db5e887805a37ef739.png)

Windows Terminal



# Vim基础使用

首先，Vim的具体使用，可以参考官方文档：[Vim help](https://vimhelp.org/)

Vim基本分为三个基础模式：

- **命令模式（Command mode）**
- **输入模式（Insert mode）**
- **底线命令模式（Last line mode）**

## 1. 命令模式

在使用vim打开或创建文件时，就是命令模式模式，比如：

[![命令模式](https://imagehost.mintimate.cn/post_vim/0330b5d1e39ac2fc45f5264ed0f23aa2.png)](https://imagehost.mintimate.cn/post_vim/0330b5d1e39ac2fc45f5264ed0f23aa2.png)

命令模式



这个时候，使用方向键，就是基础的上下行移动。其他的按键：

| 键盘上的组合按键              | 说明                                                         |
| :---------------------------- | :----------------------------------------------------------- |
| h 或方向键盘(←)               | 光标向左移动一个字符                                         |
| j 或方向键盘(↓)               | 光标向下移动一个字符                                         |
| k 或方向键盘(↑)               | 光标向上移动一个字符                                         |
| l 或方向键盘(→)               | 光标向右移动一个字符                                         |
| 【Ctrl + f】 或 【Page Down】 | 屏幕『向下』移动一页 (**常用**)                              |
| 【Ctrl + b】或 【Page Up】    | 屏幕『向上』移动一页 (**常用**)                              |
| G                             | 直达文件末尾(**非常常用**)                                   |
| gg                            | 移动到这个档案的第一行，相当于 1G (**非常常用**)             |
| n                             | n 为数字。光标向下移动 n 行(**常用**)                        |
| u                             | 撤销操作(**常用**)                                           |
| nG                            | n 为数字。移动到这个档案的第 n 行。例如 20G 则会移动到这个档案的第 20 行(可配合 :set nu) |
| n<空格>                       | 那个 n 表示『数字』，例如 20 。按下数字后再按空格键，光标会向右移动这一行的 n 个字符。例如 20 则光标会向后面移动 20 个字符距离。 |
| 0 或功能键【Home】            | 这是数字『 0 』：移动到这一行的最前面字符处                  |
| $ 或功能键【End】             | 移动到这一行的最后面字符处                                   |
| H                             | 光标移动到这个屏幕的最上方那一行的第一个字符                 |
| M                             | 光标移动到这个屏幕的中央那一行的第一个字符                   |
| L                             | 光标移动到这个屏幕的最下方那一行的第一个字符                 |
| Ctrl + d                      | 屏幕『向下』移动半页                                         |
| Ctrl + u                      | 屏幕『向上』移动半页                                         |
| +                             | 光标移动到非空格符的下一行                                   |
| -                             | 光标移动到非空格符的上一行                                   |

同时，如果你想格式化代码，可以按：`gg=G`，即可格式化代码。

## 2. 输入模式

在**命令模式**基础下，按规定按键即可。规定的按键（默认）：

| 输入按键 | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| i, I     | 进入输入模式(Insert mode)：i 为『从目前光标所在处输入』， I 为『在目前所在行的第一个非空格符处开始输入』。 (**常用**) |
| a, A     | 进入输入模式(Insert mode)：a 为『从目前光标所在的下一个字符处开始输入』， A 为『从光标所在行的最后一个字符处开始输入』。 |
| o, O     | 进入输入模式(Insert mode)：这是英文字母 o 的大小写。o 为在目前光标所在的下一行处输入新的一行； O 为在目前光标所在的上一行处输入新的一行！ |
| r, R     | 进入取代模式(Replace mode)：r 只会取代光标所在的那一个字符一次；R会一直取代光标所在的文字，直到按下 ESC 为止。 |

举个例子，在在**命令模式**基础下：

[![处于命令模式](https://imagehost.mintimate.cn/post_vim/82b1da9857155e3d056ac8de2cdd662c.png)](https://imagehost.mintimate.cn/post_vim/82b1da9857155e3d056ac8de2cdd662c.png)

处于命令模式



按`I`即可进入输入模式（编辑模式）：

[![进入编辑模式](https://imagehost.mintimate.cn/post_vim/fb168258c6cba0327e3bda45af3d34a6.png)](https://imagehost.mintimate.cn/post_vim/fb168258c6cba0327e3bda45af3d34a6.png)

进入编辑模式



如果你使用的是`i`，那么按完后，再右移一个字符即可和图片效果一致。

> 我日常使用，为一般都是按i后，再右移一字符……主要是，按要按一个Shift切换大小写，有点麻烦……

在编辑好内容后，我们按`ESC`键盘，即可回到**命令模式**：

[![回到梦里模式](https://imagehost.mintimate.cn/post_vim/6b4fd8dfbb77898e3bf97ec79b70664a.png)](https://imagehost.mintimate.cn/post_vim/6b4fd8dfbb77898e3bf97ec79b70664a.png)

回到梦里模式



## 3. 指令行模式

在**命令模式**下，输入`:`即可进入指令行模式：

[![进入指令行模式](https://imagehost.mintimate.cn/post_vim/2a7ed7b7dc8f545e39f601153e0329bd.png)](https://imagehost.mintimate.cn/post_vim/2a7ed7b7dc8f545e39f601153e0329bd.png)

进入指令行模式



一些指令：

| 指令行指令            | 描述                                                         |
| :-------------------- | :----------------------------------------------------------- |
| :w                    | 将编辑的数据写入硬盘档案中(不得不用系列)                     |
| :w!                   | 若文件属性为『只读』时，强制写入该档案。不过，到底能不能写入， 还是跟你对该档案的档案权限有关啊！ |
| :q                    | 离开 vim (不得不用系列)                                      |
| :q!                   | 若曾修改过档案，又不想储存，使用 ! 为强制离开不储存档案。    |
| :wq                   | 储存后离开，若为 **:wq!** 则为强制储存后离开 (不得不用系列)  |
| ZZ                    | 这是大写的 Z 喔！如果修改过，保存当前文件，然后退出！效果等同于(保存并退出) |
| ZQ                    | 不保存，强制退出。效果等同于 **:q!**。                       |
| :w 【filename】       | 将编辑的数据储存成另一个档案（类似另存新档）                 |
| :r 【filename】       | 在编辑的数据中，读入另一个档案的数据。亦即将 『filename』 这个档案内容加到游标所在行后面 |
| :n1,n2 w 【filename】 | 将 n1 到 n2 的内容储存成 filename 这个档案。                 |
| :! command            | 暂时离开 vi 到指令行模式下执行 command 的显示结果！例如『:! ls ~/Downloads』即可在临时察看 Download底下以 ls 输出的档案信息！ |

举个例子：我们在当前目录下编辑文件，但是想看看外面目录具体有什么文件，进而方便编写文件

[![输入命令](https://imagehost.mintimate.cn/post_vim/4612b00a387d1d324a57094bd4276b26.png)](https://imagehost.mintimate.cn/post_vim/4612b00a387d1d324a57094bd4276b26.png)

输入命令



回车执行，即可看到效果：

[![使用ls命令](https://imagehost.mintimate.cn/post_vim/dae797ba1d8f88cf89ffb256f3ef0386.png)](https://imagehost.mintimate.cn/post_vim/dae797ba1d8f88cf89ffb256f3ef0386.png)

使用ls命令



最好，按任意按键即可回到vim内。

# Vim分屏

这里简单说一下分屏。

分屏的方法很多，比较简单的是打开文件时候进行分屏：

```
vim -o/O[n] file1 [file2 ...]

CSS
```

其中：

- o：垂直分屏
- O：水平分屏
- n：可以省略，省略后，由后面file参数决定分屏个数

如：
[![同时打开两个文件](https://imagehost.mintimate.cn/post_vim/b103c119170d7448a5c4b446f5fde3d9.png)](https://imagehost.mintimate.cn/post_vim/b103c119170d7448a5c4b446f5fde3d9.png)

同时打开两个文件

[![结果](https://imagehost.mintimate.cn/post_vim/66edd3ee93a071633d53cf112135a48b.png)](https://imagehost.mintimate.cn/post_vim/66edd3ee93a071633d53cf112135a48b.png)

结果



这个时候，如果想切换激活窗口。可以按`Ctrl+w`+`w`，即可顺时针切换。

# Vim安装插件

没错，Vim是可以安装插件的，一般插件需要修改文件`syntax`、`filetype.vim`等等，也正因如此，Vim的上手门槛可以说是相当大。但是，在Vim8之后，安装Vim插件变得十分简单。我们可以直接克隆插件到：`~/.vim/pack/plugins/start`目录内，之后Vim即可生效插件。当然，也很推荐使用一些Vim插件管理器：

- [vim-plug：https://github.com/junegunn/vim-plug](https://github.com/junegunn/vim-plug)
- [Dein.vim：https://github.com/Shougo/dein.vim](https://github.com/Shougo/dein.vim)
- [Vundle.vim：https://github.com/VundleVim/Vundle.vim](https://github.com/VundleVim/Vundle.vim)
- [Pathogen.vim：https://github.com/tpope/vim-pathogen](https://github.com/tpope/vim-pathogen)

每个插件用的人都很多，这边就介绍我平常使用的`vim-plug`。

## 1. vim-plug

[vim-plug](https://github.com/junegunn/vim-plug)的安装方法很简单，我们下载[plug.vim](https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim)，并放到用户下的**autoload文件夹内**。

[![腾讯云轻量应用服务器Debian](https://imagehost.mintimate.cn/post_vim/21b88fe2685fe92324b823f49d1efe6a.png)](https://imagehost.mintimate.cn/post_vim/21b88fe2685fe92324b823f49d1efe6a.png)

腾讯云轻量应用服务器Debian



[![Windwos操作](https://imagehost.mintimate.cn/post_vim/480c41217b540f3793e3309b41b48a15.png)](https://imagehost.mintimate.cn/post_vim/480c41217b540f3793e3309b41b48a15.png)

Windwos操作



之后编辑`~/.vimrc`（也就是用户目录下的vimrc文件），添加内容：

```
call plug#begin('~/.vim/plugged')
call plug#end()

LEAF
```

[![腾讯云轻量应用服务器](https://imagehost.mintimate.cn/post_vim/8b45864df709cceb1d595c7bed4f9a3a.png)](https://imagehost.mintimate.cn/post_vim/8b45864df709cceb1d595c7bed4f9a3a.png)

腾讯云轻量应用服务器



[![Windwos](https://imagehost.mintimate.cn/post_vim/ce416406f53cbf280abd82d959a978cf.png)](https://imagehost.mintimate.cn/post_vim/ce416406f53cbf280abd82d959a978cf.png)

Windwos



> 记得保存文件嗷

## 1.1 Plug换源

因为vim-plug默认使用GitHub作为插件的抓去地址，但是因为一些原因，中国大陆连接GitHub有点困难大家可以根据自己网络环境，选择换源。
换源的方法很简单，编辑我们刚刚下载的`plug.vim`文件：

```
# 当前在autoload文件夹内
vim plug.vim

NGINX
```

[![编辑plug.vim](https://imagehost.mintimate.cn/post_vim/vimPlugVim.png)](https://imagehost.mintimate.cn/post_vim/vimPlugVim.png)

编辑plug.vim

搜索`plug_url_format`和`git::@github`,更改：



```
let fmt = get(g:, 'plug_url_format', 'https://git::@github.com/%s.git')
\ '^https://git::@githubfast\.com', 'https://githubfast.com', '')

NGINX
```

为

```
let fmt = get(g:, 'plug_url_format', 'https://git::@hub.fastgit.org/%s.git')
\ '^https://git::@githubfast\.com', 'https://githubfast.com', '')

NGINX
```

[![修改内容](https://imagehost.mintimate.cn/post_vim/changePart1.png)](https://imagehost.mintimate.cn/post_vim/changePart1.png)

修改内容

[![修改后](https://imagehost.mintimate.cn/post_vim/changePart2.png)](https://imagehost.mintimate.cn/post_vim/changePart2.png)

修改后

之后，保存并推出即可。接下来，就可以安装插件了。



## 2. 安装插件

这里我演示安装[nginx.vim](https://github.com/chr4/nginx.vim)这个插件，用来格式化（代码高亮）Nginx的Conf文件。在没安装插件前，查看Nginx配置：

[![查看Nginx配置](https://imagehost.mintimate.cn/post_vim/fad32d74064c5611a73d615df6999ae4.png)](https://imagehost.mintimate.cn/post_vim/fad32d74064c5611a73d615df6999ae4.png)

查看Nginx配置



之后，我们到`.vimrc`内添加[nginx.vim](https://github.com/chr4/nginx.vim)这个插件：

```
call plug#begin('~/.vim/plugged')
Plug 'chr4/nginx.vim'
call plug#end()

LEAF
```

[![在刚刚的Plug内添加](https://imagehost.mintimate.cn/post_vim/ed5a1b423696b1b722174c72128e83b1.png)](https://imagehost.mintimate.cn/post_vim/ed5a1b423696b1b722174c72128e83b1.png)

在刚刚的Plug内添加



之后，保存该文件并退出(`:wq`)，重新进入后，输入`:PlugInstall`:

[![PlugInstall](https://imagehost.mintimate.cn/post_vim/7190f6ba8dd9dc696046d4301edb4300.png)](https://imagehost.mintimate.cn/post_vim/7190f6ba8dd9dc696046d4301edb4300.png)

PlugInstall



回车即可自动开始安装：

[![安装成功](https://imagehost.mintimate.cn/post_vim/0081f4fc1e12567b45c71d1372ce9f0c.png)](https://imagehost.mintimate.cn/post_vim/0081f4fc1e12567b45c71d1372ce9f0c.png)

安装成功



> 原理很简单，其实就是vim-plug插件，自动git clone插件到插件文件夹内

这个时候，我们输入`:q`退出，再看看Nginx的配置文件：

[![插件已经生效](https://imagehost.mintimate.cn/post_vim/640bf8fcdfbf53acb9ba0a3ecabed700.png)](https://imagehost.mintimate.cn/post_vim/640bf8fcdfbf53acb9ba0a3ecabed700.png)

插件已经生效



其他实用插件，大家可以到网上自己获取嗷。（比如：自动格式化代码？）

## 3. 卸载插件

最后，如何卸载插件呢？其实很简单，在`~/.vimrc`内，删除插件，之后执行`:PlugClean`即可。具体可以参考[官网文档](https://github.com/junegunn/vim-plug/blob/master/README.md)：

| Command            | Description                                                  |
| :----------------- | :----------------------------------------------------------- |
| PlugInstall name … | Install plugins                                              |
| PlugUpdate name …  | Install or update plugins                                    |
| PlugClean!         | Remove unlisted plugins (bang version will clean without prompt) |
| PlugUpgrade        | Upgrade vim-plug itself                                      |
| PlugStatus         | Check the status of plugins                                  |
| PlugDiff           | Examine changes from the previous update and the pending changes |
| PlugSnapshot!      | Generate script for restoring the current snapshot of the plugins |

# Vim番外

其实，Vim也可以愉快地写Markdown，使用项目：[Markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim)

[![安装Markdown插件](https://imagehost.mintimate.cn/post_vim/7c00912829993cb86c4477d64da53fea.png)](https://imagehost.mintimate.cn/post_vim/7c00912829993cb86c4477d64da53fea.png)

安装Markdown插件



之后，编辑`.md`结尾的Markdown文件，可以调用浏览器进行预览：

[![使用效果](https://imagehost.mintimate.cn/post_vim/bacae542b4f6ac9b60f633126d79d470.png)](https://imagehost.mintimate.cn/post_vim/bacae542b4f6ac9b60f633126d79d470.png)

使用效果



# END

其实，Vim最为一个强大的文本编辑器，又经过几十年的迭代，已经进入8.x版本，并且即将9.x版本。受限于篇幅，只能讲这么多，但是这些操作已经饱含入门和插件，总体来说够用了。