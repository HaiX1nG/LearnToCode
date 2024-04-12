# gin请求重定向

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()
	r.GET("/index", func(c *gin.Context) {
		// 请求重定向
		c.Redirect(http.StatusMovedPermanently, "https://yandex.eu")
	})
	r.GET("/a", func(c *gin.Context) {
		c.Request.URL.Path = "/b" // 修改请求路径
		r.HandleContext(c)        //保存并转发
	})
	r.GET("/b", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"message": "This is /b",
		})
	})
	r.Run(":9090")
}

```

