# gin模版渲染

下面是一些简单的示例：

> D:.
> │  go.mod
> │  go.sum
> │  main.go
> │
> ├─.idea
> │      .gitignore
> │      gin-web02.iml
> │      modules.xml
> │      workspace.xml
> │
> ├─statics
> │      index.css
> │      index.js
> │
> └─templates
>     ├─posts
>     │      index.tmpl
>     │
>     └─users
>             index.tmpl

`main.go`

```go
package main

import (
	"github.com/gin-gonic/gin"
	"html/template"
	"net/http"
)

func main() {
	r := gin.Default() // gin框架默认路由管理模式
	r.Static("/xxx", "./statics")
	// gin框架中给模版添加自定义函数
	r.SetFuncMap(template.FuncMap{
		"safe": func(str string) template.HTML {
			return template.HTML(str)
		},
	})
	//r.LoadHTMLFiles("templates/index.tmpl", "templates/users/index.tmpl") // 模版解析
	r.LoadHTMLGlob("templates/**/*")
	r.GET("/posts/index", func(c *gin.Context) {
		// HTTP请求
		c.HTML(http.StatusOK, "posts/index.tmpl", gin.H{ // 模版渲染
			"title": "posts/index",
		})
	})

	r.GET("/users/index", func(c *gin.Context) {
		// HTTP请求
		c.HTML(http.StatusOK, "users/index.tmpl", gin.H{ // 模版渲染
			"title": `<a href="https:www.github.com">github</a>`,
		})
	})

	r.Run(":9090") // 启动server
}
```

`posts/index.tmpl`

```html
{{define "posts/index.tmpl"}}
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8"/>
        <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
        <meta http-equiv="x-ua-compatible" content="ie=edge"/>
        <title>posts/index</title>
    </head>
    <body>
    {{.title}}
    </body>
    </html>
{{end}}
```

`users/index.tmpl`

```html
{{define "users/index.tmpl"}}
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8"/>
        <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
        <meta http-equiv="x-ua-compatible" content="ie=edge"/>
        <link rel="stylesheet" href="/xxx/index.css">
        <title>users/index</title>
    </head>
    <body>
    {{.title | safe}}
    <script src="/xxx/index.js"></script>
    </body>
    </html>
{{end}}
```

