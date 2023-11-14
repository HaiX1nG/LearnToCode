### 6.6.2 Date对象

日期对象：用来表示时间的对象

作用：可以得到当前的系统时间

1. 实例化日期对象

```javascript
//直接使用构造函数创建一个Date对象，则会封装为当前代码执行的时间
const date = new Date();
console.log(date);  
 
//创建一个指定的时间对象，需要在构造函数中传递一个表示时间的字符串作为参数
//日期的格式：年份-月份-日 时:分:秒
const date = new Date("2023-06-22 12:35:46");
console.log(date);
```



1. 日期对象常用方法

- getDate()：获取当前月份中的每一天

- - 不同月份取值也不相同

- getDay()：获取当前的日期对象是周几

- - 返回一个0-6的值，表示周日—周六

- getMonth()：获取当前的日期对象是几月

- - 返回一个0-11的值，0表示1月，1表示2月，...，11表示12月

- getFullYear()：获取当前的日期对象的年份（四位数字）

- getHours()：获取小时

- - 返回一个0-23的值

- getMinutes()：获取分钟

- - 返回一个0-59的值

- getSecond()：获取秒

- - 返回一个0-59的值

- getTime()：获取当前日期对象的时间戳

- - 时间戳：指的是从格林威治标准时间的1970年1月1日 0时0分0秒到当前日期所花费的毫秒数（1秒=1000毫秒）

- Date.now()：获取当前执行代码的时间戳



需求：将当前时间以：YYYY-MM-DD HH:mm 形式显示在页面 2008-08-08 08:08

分析：

①：调用日期对象方法进行转换

②：记得数字要补0

③：字符串拼接后，通过 innerText 给 标签

```html
<!DOCTYPE html>
<html lang="en">
 
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    div {
      width: 300px;
      height: 40px;
      border: 1px solid pink;
      text-align: center;
      line-height: 40px;
    }
  </style>
</head>
 
<body>
  <div></div>
  <script>
    const div = document.querySelector('div')
    function getMyDate() {
      const date = new Date()
      let h = date.getHours()
      let m = date.getMinutes()
      let s = date.getSeconds()
      h = h < 10 ? '0' + h : h
      m = m < 10 ? '0' + m : m
      s = s < 10 ? '0' + s : s
      return `今天是: ${date.getFullYear()}年${date.getMonth() + 1}月${date.getDate()}号 ${h}:${m}:${s}`
    }
 
    div.innerHTML = getMyDate()
    setInterval(function () {
      div.innerHTML = getMyDate()
    }, 1000)
  </script>
</body>
 
</html>
```

方法二：

const date = new Date()

date.toLocaleString()

date.toLocaleDateString()

date.toLocaleTimeString()

```html
<!DOCTYPE html>
<html lang="en">
 
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    div {
      width: 300px;
      height: 40px;
      border: 1px solid pink;
      text-align: center;
      line-height: 40px;
    }
  </style>
</head>
 
<body>
  <div></div>
  <script>
    const div = document.querySelector('div')
    // 得到日期对象
    const date = new Date()
    div.innerHTML = date.toLocaleString()  // 2022/4/1 09:41:21
    setInterval(function () {
      const date = new Date()
      div.innerHTML = date.toLocaleString()  // 2022/4/1 09:41:21
 
    }, 1000)
    // div.innerHTML = date.toLocaleDateString()  // 2022/4/1
    // div.innerHTML = date.toLocaleTimeString()  // 2022/4/1
  </script>
</body>
 
</html>
```



3.时间戳

是指1970年01月01日00时00分00秒起至现在的毫秒数，它是一种特殊的计量时间的方式



- 获取时间戳的方式：

- - 只能得到当前的时间戳
  - 可以得到当前的时间戳，也可以返回指定时间的时间戳
  - 可以得到当前的时间戳，也可以返回指定时间的时间戳
  - getTime()
  - +new Date()
  - Date.now()


```javascript
const date = new Date()
 
// 方法一
console.log(date.getTime())
 
// 方法二
console.log(+new Date())
// 得到指定时间的时间戳
console.log(+new Date('2023-10-24 09:50:00'))
 
// 方法三
console.log(Date.now())
```



小tip：

​	获取星期几的方式：date.getDay() + 1

​	可用数组加以改进

```javascript
const arr = ['周日', '周一', '周二', '周三', '周四', '周五', '周六']
console.log(arr[new Date().getDay()])
```