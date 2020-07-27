# String
## get / set

- 当 key 不存在时，设置新的 key/value
- 当 key 存在时，覆盖原有的 key/value

```
> set mykey somevalue
OK

> get mykey
"somevalue"

> set mykey newvalue
OK

> get mykey
"newvalue"
```

删除 key
```
> del mykey
```

## nx | xx

- nx – 仅当key不存在时，set才生效
- xx – 仅当key存在时，set才生效

### nx
```
> set mykey somevalue
OK

> get mykey
"somevalue"

> set mykey newvalue nx
(nil)

> get newkey
(nil)

> set newkey newvalue nx
OK

> get newkey
"newvalue"
```

删除 key
```
> del mykey newkey
```

### xx
```
> set mykey somevalue xx
(nil)

> set mykey somevalue
OK

> get mykey
"somevalue"

> set mykey newvalue xx
OK

> get mykey
"newvalue"
```

删除 key
```
> del mykey
```

## incr / incrby / decr / decrby

incr / incrby / decr / decrby 只能作用于 integer 数据。

- incr - 递增 1
- incrby - 根据指定的步长递增
- decr - 递减 1
- decrby - 根据指定的步长递减

```
> set counter 100
OK

> incr counter
(integer) 101

> incr counter
(integer) 102

> incrby counter 50
(integer) 152

> decr counter
(integer) 151

> decr counter
(integer) 150

> decrby counter 50
(integer) 100
```

删除 key
```
> del counter
```

## mset / mget

- mset - 同时设置一个或多个 key-value
- mget - 同时获取多个 key 的值

```
> mset a 10 b 20 c 30
OK

> mget a b c
1) "10"
2) "20"
3) "30"
```

删除 key
```
> del a b c
```

## exists
判断 key 是否存在。

```
> set mykey hello
OK

> exists mykey
(integer) 1

> del mykey
(integer) 1

> exists mykey
(integer) 0
```

## type
判断指定的 key 所存储的 value 的数据类型。

```
> set mykey x
OK

> type mykey
string

> del mykey
(integer) 1

> type mykey
none
```

删除 key
```
> del mykey
```

## expire
设置 key 的有效期。

```
> set key some-value
OK

> expire key 5
(integer) 1

> get key (immediately)
"some-value"

> get key (after 5 seconds)
(nil)
```

## ttl
剩余的有效期时长。

```
> set key 100 ex 10
OK

> ttl key
(integer) 9

> get key
"100"

> ttl key
(integer) 2

> ttl key
(integer) 0

> ttl key
(integer) -2

> get key
(nil)
```

# List
## lpush
从链表的左边（头部）添加一个元素。

```
> lpush mylist 1
(integer) 1

> lpush mylist 2
(integer) 2

> lpush mylist 3 4 5
(integer) 5
```

## lrange
返回列表中指定区间内的元素。

- start - 第一个元素的索引
- end - 最后一个元素的索引
   - -1：最后一个元素
   - -2：倒数第二个元素

```
> lrange mylist 0 -1
1) "5"
2) "4"
3) "3"
4) "2"
5) "1"

> lrange mylist 0 -2
1) "5"
2) "4"
3) "3"
4) "2"
```

## rpush
从链表的右边（尾部）添加一个元素。

```
> rpush mylist a
(integer) 6

> rpush mylist b
(integer) 7

> rpush mylist c d e
(integer) 10

> lrange mylist 0 -1
 1) "5"
 2) "4"
 3) "3"
 4) "2"
 5) "1"
 6) "a"
 7) "b"
 8) "c"
 9) "d"
10) "e"
```

删除 key
```
> del mykey
```

## lpop / rpop
- lpop - 从链表的左边（头部）删除一个元素
- rpop - 从链表的右边（尾部）删除一个元素

```
> rpush mylist a b c
(integer) 3

> rpop mylist
"c"

> rpop mylist
"b"

> rpop mylist
"a"

> rpop mylist
(nil)
```

如果链表里没有元素可以被删除的时候，redis 返回 NULL。



