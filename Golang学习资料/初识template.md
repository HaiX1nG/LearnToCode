# 初识template

<hr />

在golang中template主要有8个知识点:

1. 模板语法：Go语言的模板使用双大括号`{{}}`来标记模板中的动作和变量。模板语法支持条件语句、循环语句、变量输出等功能。
2. 模板变量：在模板中，可以使用`{{.}}`来表示当前的数据对象。您可以通过传递不同的数据对象来渲染不同的模板。
3. 模板函数：Go语言的模板支持自定义函数。您可以使用`{{funcName}}`来调用自定义函数，并将其结果用于模板渲染。
4. 模板注释：您可以使用`{{/* 注释内容 */}}`来添加注释，这些注释在渲染模板时会被忽略。
5. 模板嵌套：模板可以相互嵌套，通过使用`{{template "name" .}}`来调用其他模板。
6. 模板文件解析：使用`template.ParseFiles`函数可以解析模板文件。您可以传递一个或多个模板文件路径作为参数。
7. 模板渲染：使用`template.Execute`方法可以将数据对象应用于模板，并将结果写入指定的输出流（如`http.ResponseWriter`）。
8. 模板缓存：Go语言的模板引擎会自动缓存已解析的模板，以提高性能。您可以使用`template.ParseFiles`或`template.ParseGlob`函数来解析模板文件，并使用缓存的模板进行渲染。

是一个简单的示例：

`main.go`

```go
package main

import (
	"fmt"
	"net/http"
	"text/template"
)

type Data struct {
	Name string
	Msg  string
}

func sayHello(w http.ResponseWriter, r *http.Request) {
	// 解析模版
	t, err := template.ParseFiles("./hello.tmpl")
	if err != nil {
		fmt.Printf("Parse template failed err:%v", err)
		return
	}
	// 渲染模版
	data := Data{
		Name: "小王子",
		Msg:  "吃饭了吗？",
	}
	err = t.Execute(w, data)
	if err != nil {
		fmt.Printf("rander template failed,err:%v", err)
		return
	}
}

func main() {
	http.HandleFunc("/", sayHello)
	err := http.ListenAndServe(":9090", nil)
	if err != nil {
		fmt.Printf("HTTP server start failed, err:%v\n", err)
		return
	}
}
```

`hello.tmpl`

```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <title>Hello</title>
    </head>
    <body>
        <p>Hello {{ .Name }}</p>
        <p>{{ .Msg }}</p>
    </body>
</html>
```

<hr />

## 自定义模版函数

示例：

`main.go`

```go
package main

import (
	"fmt"
	"net/http"
	"text/template"
)

func f1(w http.ResponseWriter, r *http.Request) {
	// 定义一个函数kua
	// 要么只有一个返回值，要么有两个返回值，第二个返回值必须是error类型
	k := func(name string) (string, error) {
		return name + "你真帅！", nil
	}
	// 定义模版
	// 解析模版
	t := template.New("f.tmpl") // 创建一个名字为f的模版对象，名字一定要与模版的名字能对应上
	// 告诉模版引擎，现在多了一个自定义函数kua99
	t.Funcs(template.FuncMap{
		"kua99": k,
	})
	// 解析模版
	_, err := t.ParseFiles("./lesson01/f.tmpl")
	if err != nil {
		fmt.Printf("Parse template failed,err:%v\n", err)
		return
	}

	name := "小王子"
	// 渲染模版
	err = t.Execute(w, name)
	if err != nil {
		fmt.Printf("rander template failed,err:%v\n", err)
		return
	}
}

func demo1(w http.ResponseWriter, r *http.Request) {
	t, err := template.ParseFiles("./lesson01/t.tmpl", "./lesson01/ul.tmpl")
	if err != nil {
		fmt.Printf("Parse template failed,err:%v\n", err)
		return
	}
	name := "小王子"
	err = t.Execute(w, name)
	if err != nil {
		fmt.Printf("rander template failed.err:%v\n", err)
		return
	}
}

func main() {
	http.HandleFunc("/", f1)
	http.HandleFunc("/tmplDemo", demo1)
	err := http.ListenAndServe(":9090", nil)
	if err != nil {
		fmt.Printf("HTTP server start failed,err:%v\n", err)
		return
	}
}
```

`f.tmpl`

```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <title>自定义模版函数</title>
    </head>
    <body>
        <p>{{ kua99 . }}</p>
    </body>
</html>
```

`t.html`

```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <title>嵌套template语法</title>
    </head>
    <body>
        <h1>测试嵌套template语法</h1>
        <hr>
        {{/*嵌套了另外一个单独的模版文件*/}}
        {{ template "ul.tmpl" }}
        <hr>
        {{/*嵌套另一个define定义的模版*/}}
        {{ template "ol.tmpl" }}
        <div>
            你好，{{ . }}
        </div>
    </body>
</html>
{{/*通过define定义一个模版*/}}
{{ define "ol.tmpl" }}
    <ol>
        <li>吃饭</li>
        <li>睡觉</li>
        <li>打豆豆</li>
    </ol>
{{ end }}
```

`ul.html`

```html
<ul>
    <li>注释</li>
    <li>日志</li>
    <li>测试</li>
</ul>
```

<hr />

## 模版继承

示例:

`main.go`

```go
package main

import (
	"fmt"
	"net/http"
	"text/template"
)

func index(w http.ResponseWriter, r *http.Request) {
	t, err := template.ParseFiles("./lesson02/index.tmpl")
	if err != nil {
		fmt.Printf("Parse template failed,err:%v\n", err)
		return
	}
	msg := "这里是index页面"
	err = t.Execute(w, msg)
	if err != nil {
		fmt.Printf("rander template failed,err:%v\n", err)
		return
	}
}

func home(w http.ResponseWriter, r *http.Request) {
	t, err := template.ParseFiles("./lesson02/home.tmpl")
	if err != nil {
		fmt.Printf("Parse template failed,err:%v\n", err)
		return
	}
	msg := "这里是home页面"
	err = t.Execute(w, msg)
	if err != nil {
		fmt.Printf("rander template failed,err:%v\n", err)
		return
	}
}

func index1(w http.ResponseWriter, r *http.Request) {
	t, err := template.ParseFiles("./lesson02/templates/base.tmpl", "./lesson02/templates/index.tmpl")
	if err != nil {
		fmt.Printf("Parse template failed,err:%v\n", err)
	}

	name := "小王子"
	err = t.ExecuteTemplate(w, "index.tmpl", name)
}

func home1(w http.ResponseWriter, r *http.Request) {
	t, err := template.ParseFiles("./lesson02/templates/base.tmpl", "./lesson02/templates/home.tmpl")
	if err != nil {
		fmt.Printf("Parse template failed,err:%v\n", err)
	}
	name := "Kato Megumi"
	err = t.ExecuteTemplate(w, "home.tmpl", name)
}

func main() {
	http.HandleFunc("/index", index)
	http.HandleFunc("/home", home)
	http.HandleFunc("/index1", index1)
	http.HandleFunc("/home1", home1)
	err := http.ListenAndServe(":9090", nil)
	if err != nil {
		fmt.Printf("HTTP server start failed,err:%v\n", err)
		return
	}
}
```

`index.tmpl`

```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <title>模版继承</title>
        <style>
            * {
                margin: 0;
            }
            .nav {
                width: 100%;
                height: 50px;
                position: fixed;
                top: 0;
                background-color: lightskyblue;
            }
            .main{
                margin-top: 50px;
            }
            .menu {
                width: 20%;
                height: 100%;
                position: fixed;
                left: 0;
                background-color: antiquewhite;
            }
            .center {
                text-align: center;
            }
        </style>
    </head>
    <body>
        <div class="nav"></div>
        <div class="main">
            <div class="menu"></div>
            <div class="content center">
                <h1>这里是index</h1>
                <p>Hello {{ . }}</p>
            </div>
        </div>
    </body>
</html>
```

`home.tmpl`

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <title>模版继承</title>
    <style>
        * {
            margin: 0;
        }
        .nav {
            width: 100%;
            height: 50px;
            position: fixed;
            top: 0;
            background-color: lightskyblue;
        }
        .main{
            margin-top: 50px;
        }
        .menu {
            width: 20%;
            height: 100%;
            position: fixed;
            left: 0;
            background-color: antiquewhite;
        }
        .center {
            text-align: center;
        }
    </style>
</head>
<body>
<div class="nav"></div>
<div class="main">
    <div class="menu"></div>
    <div class="content center">
        <h1>这里是home</h1>
        <p>Hello {{ . }}</p>
    </div>
</div>
</body>
</html>
```

`templates/base.tmpl`

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <title>模版继承</title>
    <style>
        * {
            margin: 0;
        }
        .nav {
            width: 100%;
            height: 50px;
            position: fixed;
            top: 0;
            background-color: lightskyblue;
        }
        .main{
            margin-top: 50px;
        }
        .menu {
            width: 20%;
            height: 100%;
            position: fixed;
            left: 0;
            background-color: antiquewhite;
        }
        .center {
            text-align: center;
        }
    </style>
</head>
<body>
<div class="nav"></div>
<div class="main">
    <div class="menu"></div>
    <div class="content center">
        {{ block "content" . }}

        {{ end }}
    </div>
</div>
</body>
</html>
```

`templates/index.tmpl`

```html
{{/*继承根模版*/}}
{{ template "base.tmpl" . }}
{{/*重新定义块模版*/}}
{{ define "content" }}
    <h1>这里是index页面</h1>
    <p>Hello {{ . }}</p>
{{ end }}
```

`templates/home.tmpl`

```html
{{/*继承根模版*/}}
{{ template "base.tmpl" . }}
{{/*重新定义块模版*/}}
{{ define "content" }}
    <h1>这里是home页面</h1>
    <p>Hello {{ . }}</p>
{{ end }}
```

<hr />

## 修改默认的标识符

Go标准库的模版引擎使用花括号`{{ }}`作为标识符，，而许多前端框架（如`Vue`和`React`等）也使用`{{ }}`作为标识符，所以我们同时使用Go语言模版引擎和前端框架时就会出现冲突，这个时候我们修改前端的或者修改Go语言模版引擎默认的标识符：

```go
template.New("test").Delins("{[", "]}").ParseFiles("./t.tmpl")
```

## text/template和html/template的区别

`text/template`和`html/template`的最大区别就是一个可以解析html标签并渲染，而一个不能。

