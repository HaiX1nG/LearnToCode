# GORM连接MySQL示例

## 什么是ORM?

ORM全称为Object Relational Mapping(对象关系映射)

## ORM优缺点

优点：

- 提高开发效率

缺点：

- 牺牲执行性能
- 牺牲灵活性
- 弱化SQL能力

<hr />

## 什么时候使用？

当你的项目对性能要求不高，为了快速开发的时候建议使用ORM。对性能要求高的项目建议手写SQL。



下面是一个GORM框架连接MYSQL的示例：

首先我们先下载所需要的库：

```bash
go get -u github.com/jinzhu/gorm
go get github.com/jinzhu/gorm/dialects/mysql@v1.9.16
```

`mian.go`

```go
package main

import (
	"fmt"
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

type UserInfo struct {
	ID     uint
	Name   string
	Gender string
	Hobby  string
}

func main() {
	// 连接MySQL数据库
	db, err := gorm.Open("mysql", "root:123456@(127.0.0.1:3306)/db1?charset=utf8mb4&parseTime=True&loc=Local")
	if err != nil {
		panic(err)
	}
	defer db.Close()

	// 创建表，自动迁移（把结构体和数据表进行对应）
	db.AutoMigrate(&UserInfo{})
	// 创建数据行
	u1 := UserInfo{1, "Kato Megumi", "girl", "sex"}
	db.Create(&u1)
	// 查询
	var u UserInfo
	db.First(&u)
	fmt.Printf("u:%#v\n", u)
	// 更新
	db.Model(&u).Update("hobby", "双色球")
	// 删除
	db.Delete(&u)
}
```

