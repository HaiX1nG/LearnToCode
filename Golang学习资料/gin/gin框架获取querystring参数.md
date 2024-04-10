# gin框架获取querystring参数

`main.go`

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

// querystring
// GET请求URL？后面的是querystring参数
// key=value格式，多个key-value用&连接
// eq: /web3?query=你好&age=20

func main() {
	r := gin.Default()
	r.GET("/web", func(c *gin.Context) {
		// 获取浏览器那边发送请求携带的querystring参数
		querystring := c.Query("query") // 通过Query获取请求中携带的querystring参数
		c.JSON(http.StatusOK, gin.H{
			"querystring": querystring,
		})
	})

	r.GET("/web1", func(c *gin.Context) {
		// 获取浏览器那边发送请求携带的querystring参数
		querystring := c.DefaultQuery("query", "somebody") // 通过DefaultQuery获取请求中携带的querystring参数,如若获取不到则返回默认值
		c.JSON(http.StatusOK, gin.H{
			"querystring": querystring,
		})
	})

	r.GET("/web2", func(c *gin.Context) {
		// 获取浏览器那边发送请求携带的querystring参数
		querystring, ok := c.GetQuery("query") // 通过GetQuery获取请求中携带的querystring参数,如若获取不到则返回boolean值
		if !ok {
			// 获取不到
			querystring = "somebody"
		}
		c.JSON(http.StatusOK, gin.H{
			"querystring": querystring,
		})
	})

	r.GET("/web3", func(c *gin.Context) {
		// 获取浏览器那边发送请求携带的querystring参数
		querystring := c.Query("query") // 通过DefaultQuery获取请求中携带的querystring参数
		age := c.Query("age")
		c.JSON(http.StatusOK, gin.H{
			"querystring": querystring,
			"age":         age,
		})
	})
	r.Run(":9090")
}
```

