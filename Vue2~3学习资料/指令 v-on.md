# Vue指令`v-on`

1. 作用: 注册事件 = 添加监听+提供处理逻辑

2. 语法:

    - v-on:事件名="内联语句"

        ```````html
        <button v-on:click="count++">按钮</button>
        ```````

        

    - v-on:事件名="methods中的函数名"

    - 简写:@事件名

        ``````html
        <button @click="count++">按钮</button>
        ``````

## Vue指令`v-on`调用传参



