## 12.2 screen对象

screen对象是由浏览器中的脚本运行时引擎自动创建的，可以显示有关浏览器屏幕的信息，这些信息只能读取不能设置。

screen对象的属性



| 属性        | 描述                                      |
| ----------- | ----------------------------------------- |
| availHeight | 返回显示屏幕的高度（除Windows任务栏之外） |
| availWidth  | 返回显示屏幕的宽度（除Windows任务栏之外） |
| colorDepth  | 返回目标设备或缓冲器上的调色板的比特深度  |
| height      | 返回显示屏幕的高度                        |
| width       | 返回显示屏幕的宽度                        |





```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
        console.log(screen.availHeight,screen.availWidth,screen.height,screen.width,screen.colorDepth)
        // 792 1260 840 1260 24
    </script>
</body>
</html>
```



## 12.3 location对象

location 对象又称为网址对象，是窗口对象的子对象，包含了窗口对象的web地址。

**location对象的属性**



| 属性     | 描述                              |
| -------- | --------------------------------- |
| hostname | 设置或返回当前URL的主机名         |
| port     | 设置或返回当前URL的端口号         |
| host     | 设置或返回当前URL的主机名和端口号 |
| pathname | 设置或返回当前URL的路径部分       |
| hash     | 设置或返回从#号开始的URL          |
| search   | 设置或返回从问号开始的URL         |
| href     | 设置或返回完整的URL               |





**location对象的方法**



| 方法      | 描述                   |
| --------- | ---------------------- |
| assign()  | 加载新的文档           |
| reload()  | 重新加载当前文档       |
| replace() | 用新的文档替换当前文档 |





5秒钟之后跳转的页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body onload　="setTimeout(newdoc,3000)">
    <p>5s后自动加载新页面</p>
    <script>
        function newdoc(){
            location.assign('http://www.baidu.com')
            // location.reload()
        }
    </script>
</body>
</html>
```