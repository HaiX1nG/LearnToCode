# React基础事件绑定

语法：on + 事件名称 = {事件处理程序}，整体上遵循驼峰命名法。

```tsx
function App() {
  const clickHandler = () => {
    console.log('button按钮点击了')
  }
  return (
    <button onClick={clickHandler}></button>
  )
}
```

### 使用事件对象参数

语法：在事件回调函数中设置形参`e`。

```tsx
function App() {
  const clickHandler = (e) => {
    console.log('button按钮点击了', e)
  }
  return <button onClick={clickHandler}>click me!</button>
}
```

### 传递自定义参数

语法：事件绑定的位置_改造成箭头函数_的写法，在执行clickHandler实际处理业务函数的时候传递实参。

```tsx
function App() {
  const clickHandler = (name) => {
    console.log('button按钮点击了', name)
  }
  return <button onClick={() => clickHandler('Kato Megumi')}>click me!</button>
}
```

注意：不能直接写函数调用，这里事件绑定需要一个__函数引用__。

### 同时传递事件对象参数和自定义参数

语法：在事件绑定的位置传递实参`e`和自定义参数，clickHandler中声明形参，注意顺序对应。

```tsx
function App() {
  const clickHandler = (name, e) => {
    console.log('button按钮点击了', name, e)
  }
  return <button onClick={(e) => clickHandler('Kato Megumi', e)}>click me!</button>
}
```

