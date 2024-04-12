# gin中间件

## Gin中间件

Gin框架允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数。这个钩子函数就叫中间件，中间件适合处理一些公共的业务逻辑，比如登录认证，权限校验，数据分页，记录日志，统计耗时等

示例：

`main.go`

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"net/http"
	"time"
)

func indexHandler(c *gin.Context) {
	fmt.Println("index")
	c.JSON(http.StatusOK, gin.H{
		"msg": "index",
	})
}

// 定义一个中间件m1，统计请求处理函数耗时
func m1(c *gin.Context) {
	fmt.Println("m1 in ...")
	start := time.Now()
	c.Next() // 调用后续处理函数
	// c.About() // 阻止调用后续的处理函数
	cost := time.Since(start)
	fmt.Printf("cosgt:%v\n", cost)
}

func main() {
	r := gin.Default()
	r.Use(m1) // 全局注册中间件m1，里面可以注册多个中间件
	r.GET("/index", indexHandler)
	r.GET("/video", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"msg": "video",
		})
	})
	r.Run(":9090")
}
```

