# 什么是web的本质？

##  概念: 简单的来说，一个动态的网页页面，一个客户端请求(request)对应着一个服务器响应(response)

让我们写一个简单的web吧！

我们这里用到`golang`自带的库`net/http`来实现这个demo

```go
package main

import (
	"fmt"
	"net/http"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
	_, _ = fmt.Fprint(w, "Hello Golang!")
}

func main() {
	http.HandleFunc("/hello", sayHello)
	err := http.ListenAndServe(":9000", nil)
	if err != nil {
		fmt.Printf("http server failed, err:%v\n\n", err)
		return
	}
}
```

1. 包引入：使用`import`语句导入需要使用的包。在这个例子中，导入了`fmt`和`net/http`包。
2. 函数定义：使用`func`关键字定义函数。在这个例子中，定义了一个名为`sayHello`的函数，它接受`http.ResponseWriter`和`http.Request`作为参数。
3. `http.HandleFunc`函数：这个函数用于注册一个处理器函数来处理指定路径的HTTP请求。它接受两个参数，第一个参数是路径字符串，第二个参数是处理器函数。在这个例子中，使用`"/hello"`作为路径，将`sayHello`函数作为处理器函数。
4. `http.ListenAndServe`函数：这个函数用于监听指定的TCP地址并提供HTTP服务。它接受两个参数，第一个参数是监听的地址和端口号，第二个参数是处理HTTP请求的处理器。在这个例子中，使用`":9000"`表示监听在9000端口，使用`nil`表示使用默认的路由器。
5. `http.ResponseWriter`接口：这个接口用于向客户端发送HTTP响应。可以使用其方法来设置响应头、写入响应体等。
6. `http.Request`结构体：这个结构体表示HTTP请求的信息。可以通过它来获取请求的方法、URL、头部信息等。
7. 错误处理：使用`err`变量来接收函数返回的错误值，并通过条件语句判断是否发生了错误。在这个例子中，如果`ListenAndServe`函数返回了错误，则打印错误信息并退出程序。