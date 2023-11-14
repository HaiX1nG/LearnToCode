# Vue指令

### Vue指令会根据不同的`[指令]`,针对标签实现不同的`[功能]`.

##### 指令:带有`v-前缀`的特殊`标签属性`

示例: 

``````vue
<!--Vue指令:v-前缀的标签属性-->
<div v-html="str"></div>
``````

v-html:

作用:设置元素的`innerHTML`用于`解析`标签

语法: v-html="`表达式`"

示例:

``````vue
    <div id="app">
        <div v-html="msg"></div>
    </div>

    <script>
        const app = new Vue({
            el: '#app',
            data:{
                msg:`<a href="https://space.bilibili.com/586771752?spm_id_from=333.1007.0.0">
                        我的bilibili主页
                    </a>
                    `
            }
        })
    </script>
``````

