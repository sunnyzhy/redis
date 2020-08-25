# Hash
## HDEL key field [field ...]
从 key 指定的哈希集中移除指定的域。在哈希集中不存在的域将被忽略。

如果 key 指定的哈希集不存在，它将被认为是一个空的哈希集，该命令将返回0。

### 返回值

integer-reply： 返回从哈希集中成功移除的域的数量，不包括指出但不存在的那些域

### 示例
```
> hset myhash field1 "foo"
(integer) 1

> del myhash field1
(integer) 1

> del myhash field2
(integer) 0
```

## HEXISTS key field
返回hash里面field是否存在

### 返回值
integer-reply, 含义如下：

- 1: hash里面包含该field。
- 0: hash里面不包含该field或者key不存在。

### 示例
```
> hset myhash field1 "foo"
(integer) 1

> hexists myhash field1
(integer) 1

> hexists myhash field2
(integer) 0
```

## HGET key field
返回 key 指定的哈希集中该字段所关联的值

### 返回值
bulk-string-reply：该字段所关联的值。当字段不存在或者 key 不存在时返回nil。

### 示例
```
> hset myhash field1 "foo"
(integer) 1

> hget myhash field1
"foo"

> hget myhash field2
(nil)
```

## HGETALL key
返回 key 指定的哈希集中所有的字段和值。返回值中，每个字段名的下一个是它的值，所以返回值的长度是哈希集大小的两倍

### 返回值
array-reply：哈希集中字段和值的列表。当 key 指定的哈希集不存在时返回空列表。

### 示例
```
> hset myhash field1 "Hello"
(integer) 1

> hset myhash field2 "World"
(integer) 1

> hgetall myhash
1) "field1"
2) "World"
3) "field2"
4) "World"
```

## HINCRBY key field increment
增加 key 指定的哈希集中指定字段的数值。如果 key 不存在，会创建一个新的哈希集并与 key 关联。如果字段不存在，则字段的值在该操作执行前被设置为 0

HINCRBY 支持的值的范围限定在 64位 有符号整数

### 返回值
integer-reply：增值操作执行后的该字段的值。

### 示例
```
> hset myhash field 5
(integer) 1

> hincrby myhash field 1
(integer) 6

> hincrby myhash field -1
(integer) 5

> hincrby myhash field -10
(integer) -5

> hget myhash field
"-5"
```

## HINCRBYFLOAT key field increment
为指定key的hash的field字段值执行float类型的increment加。如果field不存在，则在执行该操作前设置为0.如果出现下列情况之一，则返回错误：

- field的值包含的类型错误(不是字符串)。
- 当前field或者increment不能解析为一个float类型。

### 返回值
bulk-string-reply： field执行increment加后的值

### 示例
```
> hset myhash field 10.5
(integer) 1

> hincrbyfloat myhash field 0.1
"10.6"

> hget myhash field
"10.6"

> hset myhash field 5.0e3
(integer) 0

> hincrbyfloat myhash field 2.0e2
"5200"
```

## HKEYS key
返回 key 指定的哈希集中所有字段的名字。

### 返回值
array-reply：哈希集中的字段列表，当 key 指定的哈希集不存在时返回空列表。

### 示例
```
> hset myhash field1 "Hello"
(integer) 1

> hset myhash field2 "World"
(integer) 1

> hkeys myhash
1) "field1"
2) "field2"
```

## HLEN key
返回 key 指定的哈希集包含的字段的数量。

### 返回值
integer-reply： 哈希集中字段的数量，当 key 指定的哈希集不存在时返回 0

### 示例
```
> hset myhash field1 "Hello"
(integer) 1

> hset myhash field2 "World"
(integer) 1

> hlen myhash
(integer) 2
```

## HMGET key field [field ...]
返回 key 指定的哈希集中指定字段的值。

对于哈希集中不存在的每个字段，返回 nil 值。因为不存在的keys被认为是一个空的哈希集，对一个不存在的 key 执行 HMGET 将返回一个只含有 nil 值的列表

### 返回值
array-reply：含有给定字段及其值的列表，并保持与请求相同的顺序。

### 示例
```
> hset myhash field1 "Hello"
(integer) 1

> hset myhash field2 "World"
(integer) 1

> hmget myhash field1 field2 nofield
1) "Hello"
2) "World"
3) (nil)
```

## HMSET key field value [field value ...]
设置 key 指定的哈希集中指定字段的值。该命令将重写所有在哈希集中存在的字段。如果 key 指定的哈希集不存在，会创建一个新的哈希集并与 key 关联

### 返回值
simple-string-reply

### 示例
```
> hmset myhash field1 "Hello" field2 "World"
OK

> hget myhash field1
"Hello"

> hget myhash field2
"World"
```

## HSET key field value
设置 key 指定的哈希集中指定字段的值。

如果 key 指定的哈希集不存在，会创建一个新的哈希集并与 key 关联。

如果字段在哈希集中存在，它将被重写。

### 返回值
integer-reply：

- 1: 如果field是一个新的字段
- 0: 如果field原来在map里面已经存在

### 示例
```
> hset myhash field1 "Hello"
(integer) 1

> hget myhash field1
"Hello"
```

## HSETNX key field value
只在 key 指定的哈希集中不存在指定的字段时，设置字段的值。如果 key 指定的哈希集不存在，会创建一个新的哈希集并与 key 关联。如果字段已存在，该操作无效果。

### 返回值
integer-reply：

- 1：如果字段是个新的字段，并成功赋值
- 0：如果哈希集中已存在该字段，没有操作被执行

### 示例
```
> hsetnx myhash field "Hello"
(integer) 1

> hsetnx myhash field "World"
(integer) 0

> hget myhash field
"Hello"
```

## HSTRLEN key field
返回hash指定field的value的字符串长度，如果hash或者field不存在，返回0.

### 返回值
integer-reply:返回hash指定field的value的字符串长度，如果hash或者field不存在，返回0.

### 示例
```
> hmset myhash f1 "Hello World" f2 99 f3 -256
OK

> hstrlen myhash f1
(integer) 11

> hstrlen myhash f2
(integer) 2

> hstrlen myhash f3
(integer) 4
```

## HVALS key
返回 key 指定的哈希集中所有字段的值。

### 返回值
array-reply：哈希集中的值的列表，当 key 指定的哈希集不存在时返回空列表。

### 示例
```
> hset myhash field1 "Hello"
(integer) 1

> hset myhash field2 "World"
(integer) 1

> hvals myhash
1) "Hello"
2) "World"
```
