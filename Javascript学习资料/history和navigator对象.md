## 12.4 navigator对象

navigator对象即浏览器信息对象，主要包括浏览器及用户使用的计算机操作系统的有关信息，这些信息只能读取不能设置。

navigator对象的属性



| 属性        | 描述                                       |
| ----------- | ------------------------------------------ |
| appCodeName | 返回浏览器的代码名                         |
| appName     | 返回浏览器的名称                           |
| appVersion  | 返回浏览器的平台和版本信息                 |
| platform    | 返回运行浏览器的操作系统平台               |
| userAgent   | 返回由客户机发送服务器的user-agent头部的值 |





```javascript
// 检测 userAgent（浏览器信息）
!(function () {
    const userAgent = navigator.userAgent
    // 验证是否为Android或iPhone
    const android = userAgent.match(/(Android);?[\s\/]+([\d.]+)?/)
    const iphone = userAgent.match(/(iPhone\sOS)\s([\d_]+)/)
    // 如果是Android或iPhone，则跳转至移动站点
    if (android || iphone) {
        location.href = 'http://m.itcast.cn' }
})()
```



## 12.5 history对象

history 对象为浏览器访问过的历史页面，又称为历史记录对象，主要管理历史记录。

通过该对象的属性和方法实现前进和后退的功能。



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <a href="demoone.html">跳转</a>
    <script>
        console.log(history.length)
    </script>
    
</body>
</html>
```

demoone.html

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
        function goback(){
            history.back()
        }
    </script>
    <button onclick　="goback()">回退</button>
</body>
</html>
```