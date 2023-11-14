# 指令`v-show` vs `v-if`

## v-show

1. 作用:控制元素显示隐藏
2. 语法: v-show="`表达式`" 表达式值`true`显示, `false`隐藏
3. 原理: 切换`display:none`控制显示隐藏
4. 场景: 频繁切换显示隐藏的场景

## v-if

1. 作用:控制元素显示隐藏(条件渲染)

2. 语法: v-if="`表达式`" 表达式`true`显示, `false`隐藏
3. 基于条件判断,是否`创建`或`移除`元素节点



``````vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="../Vue2/vue.js"></script>
    <style>
        .box{
            border:1px solid #000;
            width: 200px;
            height: auto;
            padding: 30px;
            margin: 5px;
            box-shadow: 2px 2px 2px #ccc;
        }
    </style>
    <title>Document</title>
</head>
<body>
    <div id="app">
        <div v-show="flag" class="box">我是v-show控制的盒子</div>
        <div v-if="flag" class="box">我是v-if控制的盒子</div>
    </div>

    <script>
        
        const app = new Vue({
            el: '#app',
            data:{
                flag: true
            }
        })

    </script>
</body>
</html>
``````

