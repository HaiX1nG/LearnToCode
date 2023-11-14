# Vue 核心特性:响应式

## 数据改变,试图会自动更新



数据 => 修改数据 => 监听到数据修改 => 自动更新视图(Dom)操作 => 视图界面



聚焦于数据--->数据驱动视图

使用Vue开发,关注业务核心逻辑,根据业务修改数据即可

``````vue
    <div id="app">
        <p>{{msg}}</p>
        <p>{{count}}</p>
    </div>

    <script>
        const app = new Vue({
            el: '#app',
            data:{
                //响应式数据
                msg:'hello vue2',
                count: 100
            }
        })
        //data中的数据,是会被添加到实例上
        //1.访问数据: 实例.属性名
        //2.修改数据: 实例.属性名 = 新值
``````

![image-20231107164302195](C:\Users\14654\AppData\Roaming\Typora\typora-user-images\image-20231107164302195.png)