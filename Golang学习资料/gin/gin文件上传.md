# gin文件上传

project项目目录:

> D:.
> │  go.mod
> │  go.sum
> │  index.html
> │  main.go
> │
> └─.idea
>         .gitignore
>         gin-web08.iml
>         modules.xml
>         workspace.xml

`main.go`

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
	"path"
)

func main() {
	r := gin.Default()
	r.LoadHTMLFiles("./index.html")
	r.GET("/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index.html", nil)
	})
	r.POST("/upload", func(c *gin.Context) {
		// 从请求中读取文件
		f, err := c.FormFile("f1")
		// 将读取到的文件保存到本地(服务端本地)
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"Error": err.Error(),
			})
		} else {
			//dst := fmt.Sprintf("./%s", f.Filename)
			dst := path.Join("./", f.Filename)
			c.SaveUploadedFile(f, dst)
			c.JSON(http.StatusOK, gin.H{
				"Status": "OK",
			})
		}
	})
	r.Run(":9090")
}

```

`index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
</head>
<body>
    <form action="/upload" enctype="multipart/form-data" method="post">
        <input type="file" name="f1" id="f1">
        <input type="submit" value="上传">
    </form>
</body>
</html>
```



