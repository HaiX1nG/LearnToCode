# jsx实现列表渲染

_TypeScript是JavaScript的超集，所有的JS语句都可以是TS语句，所以所有jsx都是tsx。_

```tsx
import React from 'react'

const list = [
    {id: 1, name: 'Vue'},
    {id: 2, name: 'React'},
    {id: 3, name: 'TypeScript'}
]

const List = () => {
  return (
    <>
        <ul>
            {list.map(item => (<li key={item.id}>{item.name}</li>))}
        </ul>
    </>
  )
  
}

export default List
```

__注意__：map()循环哪个结构就return哪个结构，key是独一无二的，可以是string｜number｜id等类型。

key的作用是提升组件更新性能，因为实在React内部执行的。