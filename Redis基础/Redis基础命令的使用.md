# Redis基础命令的使用

### 字符串string

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

### 列表list

```sql
# 添加键和列表元素，顺序为后插入的放在先插入的元素的前面
LPUSH [key] [value | value...]
# 查看某个范围的列表元素
LRANGE [key] start stop
# 添加键和列表元素，顺序为先插入的元素放在前面后插入的元素放在后面
RPUSH [key] [value | value...]
# 删除列表开头的一个或N个元素
LPOP [key] [步长]
# 删除列表结尾的一个或多个元素
RPOP [key] [步长]
# 查看列表里的元素数量
LLEN [key]
# 删除指定范围以外的元素
LTRIM [key] start stop
```

### 集合set

```sql
# 在集合里添加一个元素或多个元素，集合里的内容如果重复则不添加任何元素
SADD key member [member...]
# 查看集合里的元素
SMEMBERS key
# 查看某个元素是否在集合中
SISMEMBER key member
# 删除集合里的某个元素
SREM key member 
```

### 有序集合zset(SortedSet)

```sql
# 在有序集合内添加元素
ZADD key [key value] [key value...]
# 查询有序集合某个范围内的元素
ZRANGE key start stop
# 查询有序集合某个范围内的分数和元素
ZRANGE key start stop WITHSCORES
# 查询某个元素的分数
ZSCORE key member
# 查询某个元素的排名
ZRANK key member
# 反转有序集合那的元素排名
ZREVRANK key member
```

### 哈希Hash

```sql
# 在哈希中添加一个键值对
HSET key [key value]
# 查询哈希中的某个键值对的值
HGET key [key]
# 查询哈希中的所有键值对
HGETALL key
# 删除哈希中的某个键值对
HDEL key [key]
# 判断哈希中的某个键值对是否存在
HEXISTS key [key]
# 查询哈希中的所有键
HKEYS key
# 查询哈希中的键值对数量
HLEN key
```





