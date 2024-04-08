# 初识gin框架

## gin框架的安装

我的`golang`版本为`v1.17.0`，安装gin框架的时候别忘了注意自己的golang版本，否则会出现依赖错误。

```bash
    $ go get -u github.com/gin-gonic/gin@[版本号]
```

下面是一个简单的demo:

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func sayHello(c *gin.Context) {
	c.JSON(200, gin.H{
		"message": "Hello gin!",
	})
}

func main() {
	r := gin.Default() // 使用gin默认路由设置
	r.GET("/hello", sayHello) // 这里面有两个参数，一个是状态码，另一个则是函数
    r.Run(":9000") // 这里是启动gin框架，Run方法内可以传参，参数为":端口号",默认不传参是8080
}
```



## 认识RESTful风格API

### 什么是RESTful风格API？

### REST是Representational State Transfer的简称，中文翻译为“表征状态转移”或“表现层状态转化”，通俗一点的话就是RESTful是一种风格，通过四个HTTP动词GET,POST,PUT,DELETE对服务器资源进行操作，用传统API和RESTful API做个对比图：

| 传统请求方式 | 传统URL      | `RESTful 请求方式` | `RESTful URL` | 描述说明     |
| ------------ | ------------ | ------------------ | ------------- | ------------ |
| GET          | /user        | `GET`              | `/user`       | 查询用户信息 |
| POST         | /create_user | `POST`             | `/user`       | 创建用户信息 |
| POST         | /update_user | `PUT`              | `/user`       | 更新用户信息 |
| POST         | /delete_user | `DELETE`           | `/user`       | 删除用户信息 |

这里有RESTful风格示例：

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func sayHello(c *gin.Context) {
	c.JSON(200, gin.H{
		"message": "Hello gin!",
	})
}

func main() {
	r := gin.Default()
	r.GET("/hello", sayHello)
	
	r.GET("/test", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "GET",
		})
	})
	
	r.POST("/test", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "POST",
		})
	})
	
	r.PUT("/test", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "PUT",
		})
	})
	
	r.DELETE("/test", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "DELETE",
		})
	})
	
	r.Run(":9000")
}
```

__由于浏览器只能识别GET和POST请求，所以我们可以使用`postman`这款免费的接口工具来测试接口__
