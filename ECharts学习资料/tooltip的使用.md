# tooltip提示框组件的使用

主要属性：`tooptip.show`、`tooptip.trigger`、`tooptip.triggerOn`、`tooptip.formatter`

<hr />

```js
xAxis: {
    type: 'category',
    data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
},
yAxis: {
    type: 'value'
},
series: [
    {
      data: [150, 230, 224, 218, 135, 147, 260],
      type: 'line'
    }
]
```



## 1. `tooltip.show`

`boolean`类型， 默认值为：true

**作用：用于控制提示框组件的显示**

用法：

```js
tooltip: {
	show: true
}
```

<hr />

## 2. `tooltip.trigger`

`string`触发类型, 默认值为：item
可选值：

`item`: 数据项图形触发，主要在散点图，饼图等无类目轴的图表中使用。

`axis`: 坐标轴触发，主要在柱状图，折线图等会使用类目轴的图表中使用。

`none`: 什么都不触发

用法：

```js
tooltip: {
	trigger: 'item'
}
```



<hr />



## 3. `tooltip.triggerOn`

`string`触发类型，默认值为: mousemove|click

可选值:

`mousemove`: 鼠标移动触发。

`click`: 鼠标点击触发。

`mousemove|click`: 鼠标移动和点击时都能触发。

用法：

```js
tooltip: {
    triggerOn: 'mousemove'
}
```

<hr />

## 4. `tooltip.formatter`

`string`、`Function`

提示框浮层内容格式器，支持字符串模板和回调函数两种形式，主要用来处理数据格式。

**1. 字符串模板**

模板变量有 `{a}`, `{b}`，`{c}`，`{d}`，`{e}`，分别表示系列名，数据名，数据值等。 在 `trigger`为 `'axis'` 的时候，会有多个系列的数据，此时可以通过 `{a0}`, `{a1}`, `{a2}` 这种后面加索引的方式表示系列的索引。 不同图表类型下的 `{a}`，`{b}`，`{c}`，`{d}` 含义不一样。 其中变量`{a}`, `{b}`, `{c}`, `{d}`在不同图表类型下代表数据含义为：

- 折线（区域）图、柱状（条形）图、K线图 : `{a}`（系列名称），`{b}`（类目值），`{c}`（数值）, `{d}`（无）
- 散点图（气泡）图 : `{a}`（系列名称），`{b}`（数据名称），`{c}`（数值数组）, `{d}`（无）
- 地图 : `{a}`（系列名称），`{b}`（区域名称），`{c}`（合并数值）, `{d}`（无）
- 饼图、仪表盘、漏斗图: `{a}`（系列名称），`{b}`（数据项名称），`{c}`（数值）, `{d}`（百分比）

**示例：**

```js
formatter: '{b0}: {c0}<br />{b1}: {c1}'
```

**2. 回调函数**

回调函数格式：

```js
(params: Object|Array, ticket: string, callback: (ticket: string, html: string)) => string | HTMLElement | HTMLElement[]
```

**下面是简单的示例**：

```js
    var option = {
      xAxis: {
        type: 'category',
        data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
      },
      yAxis: {
        type: 'value'
      },
      series: [
        {
          data: [150, 230, 224, 218, 135, 147, 260],
          type: 'line'
        }
      ],
      tooltip: {
        show: true,
        trigger: 'item',
        tiggerOn: 'click',
        formatter: function(arg) {
          let day = arg.name
          let value = arg.value
          if (day == 'Mon') {
            day = '星期一'
          } else if(day == 'Tue') {
            day = '星期二'
          } else if(day == 'Wed') {
            day = '星期三'
          } else if(day == 'Thu') {
            day = '星期四'
          } else if(day == 'Fri') {
            day = ' 星期五'
          } else if(day == 'Sat') {
            day = '星期六'
          } else if(day == 'Sun') {
            day = '星期天'
          }
          return `<h1 style="color: red;">${day}</h1> 访问人数为： ${value}人`     
        }
      }
    }
```

有意思的是当`trigger`设置为item或axis返回的值是不一样的

**设置为item是返回的是一个Object，设置为axis是返回的是一个Array[Object]**

