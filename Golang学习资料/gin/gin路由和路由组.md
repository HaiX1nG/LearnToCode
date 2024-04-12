# gin路由和路由组

`main.go`

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()
	r.GET("/get", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "GET",
		})
	})
	r.POST("/post", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "POST",
		})
	})
	r.PUT("/put", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "PUT",
		})
	})
	r.DELETE("/del", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "DELETE",
		})
	})
	r.Any("/any", func(c *gin.Context) {
		switch c.Request.Method {
		case http.MethodGet:
			c.JSON(http.StatusOK, gin.H{
				"method": "GET",
			})
		case http.MethodPost:
			c.JSON(http.StatusOK, gin.H{
				"method": "POST",
			})
			// ..
		}
	})
	r.NoRoute(func(c *gin.Context) {
		c.JSON(http.StatusNotFound, gin.H{"message": "404 Not Found"})
	})
	// 路由组
	videoGroup := r.Group("/video")
	{
		videoGroup.GET("/1", func(c *gin.Context) {
			c.JSON(http.StatusOK, gin.H{
				"message": "1",
			})
		})
		videoGroup.GET("/2", func(c *gin.Context) {
			c.JSON(http.StatusOK, gin.H{
				"message": "2",
			})
		})
		videoGroup.GET("/3", func(c *gin.Context) {
			c.JSON(http.StatusOK, gin.H{
				"message": "3",
			})
		})
		videoGroup.GET("/4", func(c *gin.Context) {
			c.JSON(http.StatusOK, gin.H{
				"message": "4",
			})
		})
	}
	r.Run(":9090")
}

```

