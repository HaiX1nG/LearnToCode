# 创建一个Vue实例

## Vue的引入

````html
<script src="PATH"></script>
//PATH内填写地址vue存放的地址
//也可以是网络链接
````

## 准备工作

- 创建一个容器

  ``````html
  <div id="app">
  
  </div>
  ``````

- 创建一个Vue实例

  ``````vue
      <script>
      // 一旦引入 VueJS 包,在全局环境中就有了 Vue 构造函数
          const app = new Vue({
              // 通过 el 配置选择器,指定 Vue 管理的是哪个盒子
              el:'#app',
              data: {
                  msg: 'hello vue2',
                  count:666
              }
          });
      </script>
  ``````

  - 往容器内填装vue语句

    ``````vue
        <div id="app">
            <!-- 这里将来会编写一用于渲染的代码逻辑 -->
            <h1>{{ msg }}</h1>
            <a href="#">{{ count }}</a>
        </div>
    ``````

    