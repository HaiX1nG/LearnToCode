# *gin框架的参数绑定

project项目结构:

> D:.
> │  go.mod
> │  go.sum
> │  index.html
> │  main.go
> │
> └─.idea
>         .gitignore
>         gin-web07.iml
>         modules.xml
>         workspace.xml

`main.go`

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"net/http"
)

type UserInfo struct {
	Username string `form:"username"`
	Password string `form:"password"`
}

func main() {
	r := gin.Default()
	r.LoadHTMLFiles("./index.html")

	r.GET("/user", func(c *gin.Context) {
		var u UserInfo
		err := c.ShouldBind(&u)
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"Error": err.Error(),
			})
		} else {
			fmt.Printf("%#v\n", u)
			c.JSON(http.StatusOK, gin.H{
				"Status": "OK",
			})
		}
	})

	r.GET("/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index.html", nil)
	})

	r.POST("/form", func(c *gin.Context) {
		var u UserInfo
		err := c.ShouldBind(&u)
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"Error": err.Error(),
			})
		} else {
			fmt.Printf("%#v\n", u)
			c.JSON(http.StatusOK, gin.H{
				"Status": "OK",
			})
		}
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
    <title>Title</title>
</head>
<body>
<form action="/form" method="post">
    <div>
        <label for="username">username:</label>
        <input type="text" name="username" id="username">
    </div>
    <div>
        <label for="password">password:</label>
        <input type="text" name="password" id="password">
    </div>
    <div>
        <input type="submit" value="Login">
    </div>
</form>
</body>
</html>
```

