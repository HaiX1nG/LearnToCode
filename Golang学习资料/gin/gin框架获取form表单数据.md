

# gin框架获取form表单数据

project目录：

> D:.
> │  go.mod
> │  go.sum
> │  index.html
> │  login.html
> │  main.go
> │
> └─.idea
>         .gitignore
>         gin-web05.iml
>         modules.xml
>         workspace.xml

`mian.go`

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

// 获取form表单提交的参数
func main() {
	r := gin.Default()
	r.LoadHTMLFiles("./login.html", "./index.html")
	r.GET("/login", func(c *gin.Context) {
		c.HTML(http.StatusOK, "login.html", nil)
	})

	r.POST("/login", func(c *gin.Context) {
		// 获取form表单提交的数据
		//username := c.PostForm("username")
		//password := c.PostForm("password")
		// 取到就返回值，没取到就返回空字符串
		username := c.DefaultPostForm("username", "somebody")
		password := c.DefaultPostForm("password", "***")
		c.HTML(http.StatusOK, "index.html", gin.H{
			"Name":     username,
			"Password": password,
		})
	})
	r.Run(":9090")
}
```

`index.html`

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
</head>
<body>
<h1>Login Successful!</h1>
 <p style="color: crimson">Hello {{ .Name }}!</p>
<p>Yours password is: {{ .Password }}</p>
</body>
</html>
```

`login.html`

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>login</title>
</head>
<body>
    <form action="/login" method="post" novalidate autocomplete="off">
        <div>
            <label for="username">username:</label>
            <input type="text" name="username" id="username">
        </div>
        <div>
            <label for="password">password:</label>
            <input type="password" name="password" id="password">
        </div>
        <div>
            <input type="submit" value="登录">
        </div>
    </form>
</body>
</html>
```

