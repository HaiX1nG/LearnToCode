# useState修改状态的规则

### 状态不可变

​	在React中，状态被认为是只读的，我们应该始终__替换它而不是修改它__，直接修改状态不能引发视图更新。



下面是两个例子：

1. 直接修改状态

   ```react
   import React, { useState } from 'react'
   
   const UseState = () => {
       let [count, setCount] = useState(0)
       
       const handleClick = () => {
           count++
         	console.log(count)
       }
   
       return (
           <>
               <button onClick={handleClick}>点我加1</button>
               <span>&nbsp;我被点了：{count}下</span>
           </>
       )
   }
   
   export default UseState
   ```
   
   这里我们可以看到，控制台中的count点击后是有在自增的，但是视图并没有更新。
   
1. 替换状态

   ```react
   import React, { useState } from 'react'
   
   const UseState = () => {
       // 1.调用useState添加一个状态变量
       // count 状态变量
       // setCount 修改状态变量的方法
       const [count, setCount] = useState(0)
       // 2.点击事件回调
       const handleClick = () => {
           // 作用：1.用传入的新值修改count
           // 2.重新使用新的count渲染UI
           setCount(count + 1)
       }
   
       return (
           <>
               <button onClick={handleClick}>点我加1</button>
               <span>&nbsp;我被点了：{count}下</span>
           </>
       )
   }
   
   export default UseState
   ```
   
   这里我们可以看到，不但count自增了，而且视图中也跟着更新了。

### 修改对象状态

规则：对于对象类型的状态变量，应该始终传给set方法一个_全新的对象_来进行修改。

```react
import React, { useState } from 'react'

const UseStateObject = () => {
    const [form, setForm] = useState({ name: "John" })

    const changeForm = () => {
        setForm({
            ...form,
            name: "Kato Megumi"
        })
    }

    return (
        <div>
            <button onClick={changeForm}>click me!</button>
            <span>{form.name}</span>
        </div>
    )
}

export default UseStateObject
```

