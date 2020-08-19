# String
## DECR key
对key对应的数字做减1操作。如果key不存在，那么在操作之前，这个key对应的值会被置为0。如果key有一个错误类型的value或者是一个不能表示成数字的字符串，就返回错误。这个操作最大支持在64位有符号的整型数字。

```
> set mykey 10
OK

> decr mykey
(integer) 9

> get mykey
"9"

> set mykey 234293482390480948029348230948
OK

> decr mykey
(error) ERR value is not an integer or out of range
```

## DECRBY key decrement
将key对应的数字减decrement。如果key不存在，操作之前，key就会被置为0。如果key的value类型错误或者是个不能表示成数字的字符串，就返回错误。这个操作最多支持64位有符号的正型数字。

```
> set mykey 10
OK

> decrby mykey 5
(integer) 5

> get mykey
"5"
```

## GET key
返回key的value。如果key不存在，返回特殊值nil。如果key的value不是string，就返回错误，因为GET只处理string类型的values。

## GETRANGE key start end
在小于2.0的Redis版本中叫SUBSTR。 返回key对应的字符串value的子串，这个子串是由start和end位移决定的（两者都在string内）。可以用负的位移来表示从string尾部开始数的下标。所以-1就是最后一个字符，-2就是倒数第二个，以此类推。

```
> set mykey "This is a string"
OK

> getrange mykey 0 3
"This"

> getrange mykey -3 -1
"ing"

> getrange mykey 0 -1
"This is a string"

> getrange mykey 10 100
"string"
```

## GETSET key value
把key对应的值修改为value，并且返回原来key对应的value。

```
> incr mycounter
(integer) 1

> get mycounter
"1"

> getset mycounter 0
"1"

> get mycounter
"0"
```

## INCR key
对存储在指定key的数值执行**原子**的加1操作。

如果指定的key不存在，那么在执行incr操作之前，会先将它的值设定为0。

如果指定的key中存储的值不是字符串类型（fix：）或者存储的字符串类型不能表示为一个整数，那么执行这个命令时服务器会返回一个错误(eq:(error) ERR value is not an integer or out of range)。

**这个操作仅限于64位的有符号整型数据。**

注意: 由于redis并没有一个明确的类型来表示整型数据，所以这个操作是一个字符串操作。

执行这个操作的时候，key对应存储的字符串被解析为10进制的64位有符号整型数据。

```
> set mykey 10
OK

> incr mykey
(integer) 11

> get mykey
"11"

> set mykey a
OK

> incr mykey
(error) ERR value is not an integer or out of range
```

## INCRBY key increment
将key对应的数字加decrement。如果key不存在，操作之前，key就会被置为0。如果key的value类型错误或者是个不能表示成数字的字符串，就返回错误。这个操作最多支持64位有符号的正型数字。

```
> set mykey 10
OK

> incrby mykey 5
(integer) 15
```

## INCRBYFLOAT key increment
通过指定浮点数key来增长浮点数(存放于string中)的值. 当键不存在时,先将其值设为0再操作.下面任一情况都会返回错误:

- key 包含非法值(不是一个string).
- 当前的key或者相加后的值不能解析为一个双精度的浮点值.(超出精度范围了)

```
> set mykey 10.50
OK

> incrbyfloat mykey 0.1
"10.6"

> get mykey
"10.6"

> set mykey 5.0e3
OK

> get mykey
"5.0e3"

> incrbyfloat mykey 2.0e2
"5200"

> get mykey
"5200"
```

## MGET key [key ...]
返回所有指定的key的value。对于每个不对应string或者不存在的key，都返回特殊值nil。

```
> set key1 hello
OK

> set key2 world
OK

> mget key1 key2 nonexisting
1) "hello"
2) "world"
3) (nil)
```

## SET key value [EX seconds] [PX milliseconds] [NX|XX]
如果 key 已经保存了一个值，那么这个操作会直接覆盖原来的值，并且忽略原始类型。

当set命令执行成功之后，之前设置的过期时间都将失效。

### 选项

- EX seconds – 设置键key的过期时间，单位时秒
- PX milliseconds – 设置键key的过期时间，单位时毫秒
- NX – 只有键key不存在的时候才会设置key的值
- XX – 只有键key存在的时候才会设置key的值

注意: 由于SET命令加上选项已经可以完全取代SETNX, SETEX, PSETEX的功能，所以在将来的版本中，redis可能会不推荐使用并且最终抛弃这几个命令。

```
> set mykey hello
OK

> get mykey
"hello"

> set mykey world nx
(nil)

> set mykey world xx
OK

> get mykey
"world"

> set myotherkey world xx
(nil)

> set myotherkey world nx
OK

> get myotherkey
"world"

> set mykey hello ex 10
OK

> ttl mykey
(integer) 7

> ttl mykey
(integer) 1

> ttl mykey
(integer) -2

> get mykey
(nil)
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
