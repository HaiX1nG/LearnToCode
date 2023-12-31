### 6.6.4 正则表达式对象

正则表达式（Regular Expression）是用于匹配字符串中字符组合的模式。在 JavaScript中，正则表达式也是对象，通常用来查找、替换那些符合正则表达式的文本。

使用场景：

1.验证表单：用户名表单只能输入英文字母、数字或者下划线， 昵称输入框中可以输入中文(匹配)

2.过滤掉页面内容中的一些敏感词(替换)   字符串.replace(正则表达式, '替换的文本')

3.从字符串中获取我们想要的特定部分(提取)

...



#### 6.6.4.1 语法

我们想要查找是否有戴眼镜的人, 怎么做呢？

1. 定义规则： 戴眼镜的
2. 根据规则去查找：找到则返回



![image-20230626210829182.png](https://p.ananas.chaoxing.com/star3/origin/16cb5e12ef1559f4879e3a3f7def72b4.jpg)





正则同样道理，我们分为两步：

1. 定义规则
2. 查找



**1. 定义正则表达式语法：**

```javascript
Plain Text

const 变量名 = /表达式/
```

其中 / / 是正则表达式字面量

```javascript
Plain Text

const reg = /javascript/
```



**2.判断是否有符合规则的字符串：**

**test()** 方法：用来查看正则表达式与指定的字符串是否匹配

如果正则表达式与指定的字符串匹配 ，返回true，否则false

```javascript
const str = '我们在学习前端，希望能用心学习前端'
// 1. 定义规则
const reg = /前端/
// 2. 是否匹配
console.log(reg.test(str))  //true
```



**3.检索（查找）符合规则的字符串：**

**exec()** 方法 在一个指定字符串中执行一个搜索匹配

如果匹配成功，exec() 方法返回一个数组，否则返回null

```javascript
const str = '我们在学习前端，希望能用心学习前端'
const reg = /前端/
console.log(reg.exec(str))
```

![image-20230626212033752]()



**4.重新改变RegExp实例：**

**complie()** 方法 用于在脚本执行过程中重新改变RegExp实例，并编译正则表达式为内部格式

```javascript
const str = '我们在学习前端，希望能用心学习前端'
const reg = /前端/
document.writeln(reg.exec(str))
reg.compile('学习','g')
document.writeln(reg.exec(str))
```



#### 6.6.4.2 元字符

**普通字符:**

大多数的字符仅能够描述它们本身，这些字符称作普通字符，例如所有的字母和数字。

也就是说普通字符只能够匹配字符串中与它们相同的字符。

**元字符(特殊字符）**

是一些具有特殊含义的字符，可以极大提高了灵活性和强大的匹配功能。

比如，规定用户只能输入英文26个英文字母，普通字符的话 abcdefghijklm….. 

但是换成元字符写法： [a-z] 



- 元字符的分类：

- - 边界符（表示位置，开头和结尾，必须用什么开头，用什么结尾）
  - 量词 （表示重复次数）
  - 字符类 （比如 \d 表示 0~9）



**1. 边界符**

正则表达式中的边界符（位置符）用来提示字符所处的位置，主要有两个字符



| 边界符 | 说明^                          |
| ------ | ------------------------------ |
| ^      | 表示匹配行首的文本（以谁开始） |
| $      | 表示匹配行尾的文本（以谁结束） |



如果 ^ 和 $ 在一起，表示必须是精确匹配。



**2. 量词**

量词用来 设定某个模式出现的次数



| 量词  | 说明                                           | 实例                        |
| ----- | ---------------------------------------------- | --------------------------- |
| *     | 前一个字符0次或无限次扩展                      | /ab*/表示a、ab、abb、abbb等 |
| +     | 前一个字符1次或无限次扩展                      | /ab+/表示ab、abb、abbb等    |
| ?     | 前一个字符0次或1次，可理解为出现或者不出现     | /ab?/表示a、ab              |
| {n}   | 前一个字符n次，例如\d{5}，和\d\d\d\d\d效果一致 | /ab{2}c/表示abbc            |
| {n,}  | 前一个字符n次或更多次                          | /ab{2,}/表示abb、abbb等     |
| {n,m} | 前一个字符n到m次                               | /ab{1,2}c/表示abc、abbc     |



逗号左右两侧千万不要出现空格



**3. 字符类**



| 操作符 | 说明                                       | 实例                                               |
| ------ | ------------------------------------------ | -------------------------------------------------- |
| .      | 匹配除了换行符以外的任意单个字符           |                                                    |
| \w     | 匹配字母或数字或下划线，等价于[a-zA-Z0-9_] |                                                    |
| \s     | 匹配任意的空白符，例如空格、换行等         |                                                    |
| \d     | 匹配数字，等价于[0-9]                      |                                                    |
| \n     | 匹配一个换行符                             |                                                    |
| \t     | 匹配一个制表符                             |                                                    |
|        |                                            |                                                    |
| \W     | 匹配非字母或数字或下划线                   |                                                    |
| \D     | 匹配非数字                                 |                                                    |
| \S     | 匹配非空白符                               |                                                    |
| \|     | 或                                         | [a\|b]表示a或b                                     |
| ()     | 分组标记，内部只能使用\|操作符             | (abc)表示abc，(abc\|bcd)表示abc或bcd (ab)*  ababab |
| [...]  | 字符集，对**单个字符**给出取值范围         | [abc]表示a或b或c，[a-z]表示a-z的单个字符           |
| [^...] | 非字符集，对**单个字符**给出排除范围       | [^abc]表示非a或b或c的单个字符                      |





在线测试正则表达式：http://tool.oschina.net/regex/