# gin框架返回json

`main.go`

```go
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

func main() {
    r := gin.Default()
    r.GET("/json", func(c *gin.Context) {
       //data := map[string]interface{}{
       // "name":    "Kato Megumi",
       // "message": "Hello",
       // "age":     18,
       //}
       data := gin.H{
          "name":    "Kato Megumi",
          "message": "Hello",
          "age":     18,
       }
       c.JSON(http.StatusOK, data)
    })

    type msg struct {
       Name    string
       Message string
       Age     int
    }

    r.GET("/another_json", func(c *gin.Context) {
       data := msg{
          "Kato Megumi",
          "hello!",
          20,
       }
       c.JSON(http.StatusOK, data)
    })

    r.Run(":9090")
}
```