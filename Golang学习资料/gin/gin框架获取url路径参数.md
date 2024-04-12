# gin框架获取url路径参数

示例：

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

// 获取请求的path（URL）参数，返回的都是字符串类型
// 注意URL的匹配不要冲突
func main() {
	r := gin.Default()
	r.GET("/user/:name/:age", func(c *gin.Context) {
		// 获取URL路径参数
		name := c.Param("name") // string类型
		age := c.Param("age")
		c.JSON(http.StatusOK, gin.H{
			"name": name,
			"age":  age,
		})
	})

	r.GET("/blog/:year/:month", func(c *gin.Context) {
		year := c.Param("year")
		month := c.Param("month")
		c.JSON(http.StatusOK, gin.H{
			"year":  year,
			"month": month,
		})
	})
	r.Run(":9090")
}
```

