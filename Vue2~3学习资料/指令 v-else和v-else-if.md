# Vue指令 `v-esle`和`v-else-if`

### 1. 作用:辅助`v-if`进行判断渲染

### 2. 语法:`v-else`  `v-else-if`="`表达式`"

### 3. 注意:需要紧挨着`v-if`一起使用

``````vue
    <div id="app">
        <p v-if="gender===1">性别:♂男</p>
        <p v-else>性别:♀女</p>
        <hr>
        <p v-if="score >= 90">成绩判定A:奖励电脑一台</p>
        <p v-else-if="score >= 70">成绩判定B:奖励周末郊游</p>
        <p v-else-if="score >= 60">成绩判定C:奖励零食礼包</p>
        <p v-else>成绩判定D:惩罚一周不准打胶</p>
    </div>

    <script>
        const app = new Vue({
            el: '#app',
            data:{
                gender: 1,
                score: 95
            }
        })
    </script>
``````

