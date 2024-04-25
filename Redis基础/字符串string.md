# 字符串string

```sql
# 设置键和值
SET [key] [value]
# 获取键和值
GET [key]
# 删除某个键
DEL [key]
# 查询某个键是是否存在
EXISTS [key]
# 查询所有键
KEYS *
# 查询所有以某个字母开头的键
KEYS [key]*
# 查询所有以某个字母结尾的键
KEYS *[key]
# 设置某个键的过期时间
EXPIRE [key] [time] [value]
# 查看某个键的过期时间
TTL [key]
# 设置某个键的过期时间，如果该键存在则不进行任何操作，反之则设置某个键的过期时间
SETNX [key] [time] [value]
```

# 列表list

```sql
# 添加键和列表元素，顺序为后插入的放在先插入的元素的前面
LPUSH [key] [value | ...value]
# 查看某个范围的列表元素
LRANGE [key] start stop
# 添加键和列表元素，顺序为先插入的元素放在前面后插入的元素放在后面
RPUSH [key] [value | ...value]
# 删除列表开头的一个或N个元素
LPOP [key] [步长]
# 删除列表结尾的一个或多个元素
RPOP [key] [步长]
# 查看列表里的元素数量
LLEN [key]
# 删除指定范围以外的元素
LTRIM [key] start stop
```





