### 6.5.3 Array 对象

数组可以存储任意数据类型的数据

1.声明语法

let arr = [数据1,数据2,数据3,...,数据n]

let arr = new Array(数据1,数据2,数据3,...,数据n)

2.使用

数组名[索引]

索引又称为下标，从0开始



- 一些常用概念：

- - 元素：数组中保存的每个数据叫数组元素
  - 下标：即索引，数组中数据的编号
  - 长度：数组中数据的个数，通过数组的length属性获得



#### 6.5.3.1 数组增删改查

1.比较数组

（1）找到数组最大值

```javascript
let arr = [1,4,7,3,8];
let max = arr[0];
 
for (let i = 1; i < arr.length; i++) {
    if (max < arr[i]) {
        max = arr[i];
    }
}
 
console.log(max)
```



2.修改数组

arr[下标] = 新值

需求：给数组中的所有元素后加同学

```javascript
let arr = ['a','b','c'];
 
for (let i = 0; i < arr.length; i++) {
    arr[i] = arr[i] + '同学';
}
 
console.log(arr);
```



3.添加数据

（1）数组.push() 方法将一个或多个元素添加到数组的末尾，并返回该数组的新长度

arr.push(数据1,数据2,数据3,...,数据n)

```javascript
let arr = ['a','b','c'];
 
arr.push('d','e');
 
console.log(arr.push('d','e'))  // 5
console.log(arr);
// ['a','b','c','d','e']
```



（2）arr.unshift(新增的内容) 方法将一个或多个元素添加到数组的**开头**，并返回该数组的新长度



- 数组筛选

需求：将数组 [2, 0, 6, 1, 77, 0, 52, 0, 25, 7] 中大于等于 10 的元素选出来，放入新数组

分析：

①：声明一个新的数组用于存放新数据newArr

②：遍历原来的旧数组， 找出大于等于 10 的元素

③：依次追加给新数组 newArr

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
        let arr = [2, 0, 6, 1, 77, 0, 52, 0, 25, 7];
        let newArr = [];
        for (let i = 0; i < arr.length; i++) {
            if (arr[i] > 10) {
                newArr.push(arr[i]);
            };
        };
        console.log(newArr);
    </script>
</body>
</html>
```



4.删除数据

（1）数组. pop() 方法从数组中删除最后一个元素，并返回该元素的值

```javascript
let arr = ['a','b','c'];
 
console.log(arr.pop()); // c
 
console.log(arr);  // ['a','b']
```



（2）数组. shift() 方法从数组中删除第一个元素，并返回该元素的值



（3）数组. splice() 方法 删除指定元素，并返回被删除元素的值

- 语法：

- - arr.splice(start,deletecount)

- 参数：

- - start 起始位置：指定修改的开始位置（从0计数）
  - deleteCount：表示要移除的数组元素的个数。可选的。 如果省略则默认从指定的起始位置删除到最后

```javascript
let arr = ['a','b','c'];
arr.splice(1,1)
 
console.log(arr);  // ['a','c']
```



#### 6.5.3.2 其他常用方法



![image-20230629134914152.png](https://p.ananas.chaoxing.com/star3/origin/9ab3fc6bc2dea63c6f32009242ed2f98.jpg)







1.map()



![image-20230626151324385-1696851797400.png](https://p.ananas.chaoxing.com/star3/origin/816cd9734f62a7c21a082eb36601faf9.jpg)





使用场景：map可以遍历数组处理数据，**并返回新的数组**

map也称为映射，指两个元素的集之间元素相对应的关系

```javascript
const arr = ['a', 'b', 'c']
const newarr = arr.map(function(ele,index){
        console.log(ele)
        console.log(index)
        return ele + '同学'
})
console.log(newarr)
```



![image-20230626151151177.png](https://p.ananas.chaoxing.com/star3/origin/3fa4b5b28bfc5bcc6229d7bb7bec5814.jpg)











2.join()

join() 方法用于把数组中的所有元素转换一个字符串

参数：数组元素是通过参数里面指定的分隔符进行分隔的，小括号为空，则用逗号分隔

```javascript
const arr = ['a', 'b', 'c']
console.log(arr.join(''))  // abc
console.log(arr.join(','))  // a,b,c
console.log(arr.join())  // a,b,c
```



3.foreach方法

forEach() 方法用于遍历数组的每个元素，即对数组的每个元素执行一次提供的函数。无返回值

语法：

```javascript
被遍历的数组.forEach(function (当前数组元素,当前元素索引号,当前数组) {
    函数体
})
const arr = ['a', 'b', 'c']
const result = arr.forEach(function (item, index) {
    console.log(item)   // a b c
    console.log(index)  // 0 1 2
})
 
console.log(result) // unifined
```

参数中当前数组元素是必须要写的， 索引号可选。

forEach() 方法仅用于遍历，无返回值



4.filter()

filter() 方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素

主要使用场景： **筛选数组符合条件的元素，并返回筛选之后元素的新数组**

语法：

```javascript
数组.filter(function (item, index) {
    return 筛选条件
})
```



筛选数组中大于30的元素

```javascript
const arr = [10, 40, 50]
 
const newArr = arr.filter(function (item) {
    return item > 30
})
 
console.log(newArr) // [40, 50]
```

箭头函数写法

```javascript
const arr = [10, 40, 50]
 
const newArr = arr.filter(item => item > 30)
 
console.log(newArr) // [40, 50]
```



5.reduce()

返回函数累计处理的结果，经常用于求和等

语法：

```javascript
Plain Text

arr.reduce(function(){}, 起始值)
```

起始值可以省略，如果写就作为第一次累计的起始值



需求：求数组各元素之和

```javascript
const arr = [1, 3, 6]
 
// 无初始值
const sum = arr.reduce(function(prev, current) {
    return prev + current
})
console.log(sum)    // 10
 
// 有初始值
const sum = arr.reduce(function(prev, current) {
    return prev + current
}, 2)
console.log(sum)    // 12
```

注：

1. 如果有起始值，则以起始值为准开始累计， 累计值 = 起始值
2. 如果没有起始值， 则累计值以数组的第一个数组元素作为起始值开始累计
3. 后面每次遍历就会用后面的数组元素 累计到 累计值 里面 （类似求和里面的 sum ）



3名员工当月薪资之和

```html
<!DOCTYPE html>
<html lang="en">
 
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
 
<body>
  <script>
    const arr = [{
      name: '张三',
      salary: 9000
    }, {
      name: '李四',
      salary: 10000
    }, {
      name: '王五',
      salary: 20000
    },
    ]
    const money = arr.reduce(function (prev, current) {
      return prev + current.salary
    }, 0)
    console.log(money)
  </script>
</body>
 
</html>
```

给员工每人涨薪 30%，计算需要支出的费用

```javascript
// 修改第24-26行语句
const money = arr.reduce(function (prev, current) {
    return prev + current.salary * 1.3
}, 0)
```





![image-20230629162054841.png](https://p.ananas.chaoxing.com/star3/origin/f967c9257ff237e11319ff5db400323c.jpg)







1. 伪数组转换为真数组

静态方法 Array.from()