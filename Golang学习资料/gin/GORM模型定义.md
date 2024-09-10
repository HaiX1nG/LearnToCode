# GORM Model定义

为了方便模型定义，GORM内置了一个`gorm.Model`结构体。`gorm.Model`是一个包含了`ID`,`CreatedAt`,`UpdatedAt`,`DeletedAt`四个字段的Golang结构体。

```go
// gorm.Model定义
type Model struct {
    ID			uint `gorm:"primary_key"`
    CreatedAt 	time.Time
    UpdatedAt 	time.Time
    DeletedAt 	*time.Time
}
```

你可以将它嵌入到你自己的模型中：

```go
// 将ID,CreatedAt,UpdatedAt,DeletedAt字段注入到User模型中
type User struct {
    gorm.Model
    Name	string
}
```

当然你也可以完全自己定义模型:

```go
type User struct {
    ID		int
    Name	string
}
```

模型定义示例：

```go
type User struct {
	gorm.Model
	Name         string
	Age          sql.NullInt64
	Birthday     *time.Time
	Email        string  `gorm:"type:varchar(100);unique_index"`
	Role         string  `gorm:"size:255"`        // 设置字段大小为255
	MemberNumber *string `gorm:"unique;not null"` // 设置会员号（menber number）唯一且不能为空
	Num          int     `gorm:"AUTO_INCREMENT"`  // 设置num为自增类型
	Address      string  `gorm:"index:addr"`      // 给address字段创建名为addr的索引
	IgnoreMe     int     `gorm:"-"`               // 忽略本字段
}
```

