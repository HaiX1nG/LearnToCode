# React中获取DOM

在React组件中获取/操作DOM，需要使用useRef钩子函数，分为两步：

1. 使用useRef创建ref对象，并与JSX绑定

   ```tsx
   const inputRef = useRef(null)
   <input type="text" ref={inputRef} />
   ```

2. 在DOM可用时，通过inputRef.current拿到DOM对象

   ```tsx
   console.log(inputRef.current)
   ```

<hr />

```tsx
import React, { useRef } from 'react'

const GetReactDOM = () => {
    const inputRef = useRef(null)
    const showDOM = () => {
        console.dir(inputRef.current)
    }
    return (
        <div>
            <input type="text" ref={inputRef} />
            <button onClick={showDOM}>获取DOM</button>
        </div>
    )
}

export default GetReactDOM
```

