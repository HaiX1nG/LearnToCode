### 6.6.1 Math

Math对象是JavaScript提供的一个“数学”对象

**作用：**提供了一系列做数学运算的方法

 

Math对象包含的方法有：

​	random：生成0-1之间的随机数（包含0不包括1）

​	ceil：向上取整

​	floor：向下取整

​	max：找最大数

​	min：找最小数

​	pow：幂运算

​	abs：绝对值



如何生成0-10的随机数呢？

Math.floor(Math.random() * (10 + 1))



如何生成5-10的随机数？

Math.floor(Math.random() * (5 + 1)) + 5



如何生成N-M之间的随机数

Math.floor(Math.random() * (M - N + 1)) + N



如何随机抽取数组中的数

Math.floor(Math.random() *  arr.length) 



需求：请把 [‘赵云’, ‘黄忠’, ‘关羽’, ‘张飞’, ‘马超’, ‘刘备’, ‘曹操’] 随机显示一个名字到页面中，且不能重复显示

```javascript
let arr = ['赵云', '黄忠', '关羽', '张飞', '马超', '刘备', '曹操'];
 
let index = Math.floor(Math.random() *  arr.length);
 
document.write(arr[index]);
 
// splice(起始位置, 删除几个元素)
arr.splice(index,1);
 
console.log(arr);
```