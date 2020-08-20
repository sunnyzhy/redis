# String
## APPEND key value
如果 key 已经存在，并且值为字符串，那么这个命令会把 value 追加到原来值（value）的结尾。 如果 key 不存在，那么它将首先创建一个空字符串的key，再执行追加操作，这种情况 APPEND 将类似于 SET 操作。

### 返回值
Integer reply：返回append后字符串值（value）的长度。

### 示例
```
> exists mykey
(integer) 0

> append mykey "Hello"
(integer) 5

> append mykey " World"
(integer) 11

> get mykey
"Hello World"
```

## DECR key
对key对应的数字做减1操作。如果key不存在，那么在操作之前，这个key对应的值会被置为0。如果key有一个错误类型的value或者是一个不能表示成数字的字符串，就返回错误。这个操作最大支持在64位有符号的整型数字。

### 返回值
数字：减小之后的value

### 示例
```
> set mykey "10"
OK

> decr mykey
(integer) 9

> get mykey
"9"

> set mykey "234293482390480948029348230948"
OK

> decr mykey
(error) ERR value is not an integer or out of range
```

## DECRBY key decrement
将key对应的数字减decrement。如果key不存在，操作之前，key就会被置为0。如果key的value类型错误或者是个不能表示成数字的字符串，就返回错误。这个操作最多支持64位有符号的正型数字。

### 返回值
返回一个数字：减少之后的value值。

### 示例
```
> set mykey "10"
OK

> decrby mykey 5
(integer) 5

> get mykey
"5"
```

## GET key
返回key的value。如果key不存在，返回特殊值nil。如果key的value不是string，就返回错误，因为GET只处理string类型的values。

### 返回值
simple-string-reply:key对应的value，或者nil（key不存在时）。

## GETRANGE key start end
在小于2.0的Redis版本中叫SUBSTR。 返回key对应的字符串value的子串，这个子串是由start和end位移决定的（两者都在string内）。可以用负的位移来表示从string尾部开始数的下标。所以-1就是最后一个字符，-2就是倒数第二个，以此类推。

### 返回值
bulk-reply

### 示例
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

### 返回值
bulk-string-reply: 返回之前的旧值，如果之前Key不存在将返回nil。

### 示例
```
> incr mycounter
(integer) 1

> get mycounter
"1"

> getset mycounter "0"
"1"

> get mycounter
"0"
```

### 应用场景
GETSET可以和INCR一起使用实现支持重置的计数功能。举个例子：每当有事件发生的时候，一段程序都会调用INCR给key mycounter加1，但是有时我们需要获取计数器的值，并且自动将其重置为0。这可以通过GETSET mycounter "0"来实现。

## INCR key
对存储在指定key的数值执行**原子**的加1操作。

如果指定的key不存在，那么在执行incr操作之前，会先将它的值设定为0。

如果指定的key中存储的值不是字符串类型（fix：）或者存储的字符串类型不能表示为一个整数，那么执行这个命令时服务器会返回一个错误(eq:(error) ERR value is not an integer or out of range)。

**这个操作仅限于64位的有符号整型数据。**

注意: 由于redis并没有一个明确的类型来表示整型数据，所以这个操作是一个字符串操作。

执行这个操作的时候，key对应存储的字符串被解析为10进制的64位有符号整型数据。

### 返回值
integer-reply:执行递增操作后key对应的值。

### 示例
```
> set mykey "10"
OK

> incr mykey
(integer) 11

> get mykey
"11"

> set mykey "a"
OK

> incr mykey
(error) ERR value is not an integer or out of range
```

### 应用场景
#### 计数器
Redis的原子递增操作最常用的使用场景是计数器。

使用思路是：每次有相关操作的时候，就向Redis服务器发送一个incr命令。

例如这样一个场景：我们有一个web应用，我们想记录每个用户每天访问这个网站的次数。

web应用只需要通过拼接用户id和代表当前时间的字符串作为key，每次用户访问这个页面的时候对这个key执行一下incr命令。

这个场景可以有很多种扩展方法:

- 通过结合使用INCR和EXPIRE命令，可以实现一个只记录用户在指定间隔时间内的访问次数的计数器
- 客户端可以通过GETSET命令获取当前计数器的值并且重置为0
- 通过类似于DECR或者INCRBY等原子递增/递减的命令，可以根据用户的操作来增加或者减少某些值 比如在线游戏，需要对用户的游戏分数进行实时控制，分数可能增加也可能减少。

#### 限速器
限速器是一种可以限制某些操作执行速率的特殊场景。

传统的例子就是限制某个公共api的请求数目。

假设要解决如下问题：限制某个api每秒每个ip的请求次数不超过10次。

可以通过incr命令来实现两种方法解决这个问题。

## INCRBY key increment
将key对应的数字加decrement。如果key不存在，操作之前，key就会被置为0。如果key的value类型错误或者是个不能表示成数字的字符串，就返回错误。这个操作最多支持64位有符号的正型数字。

### 返回值
integer-reply： 增加之后的value值。

### 示例
```
> set mykey "10"
OK

> incrby mykey 5
(integer) 15
```

## INCRBYFLOAT key increment
通过指定浮点数key来增长浮点数(存放于string中)的值. 当键不存在时,先将其值设为0再操作.下面任一情况都会返回错误:

- key 包含非法值(不是一个string).
- 当前的key或者相加后的值不能解析为一个双精度的浮点值.(超出精度范围了)

### 返回值
Bulk-string-reply: 当前key增加increment后的值。

### 示例
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

### 返回值
array-reply: 指定的key对应的values的list。

### 示例
```
> set key1 "hello"
OK

> set key2 "world"
OK

> mget key1 key2 nonexisting
1) "hello"
2) "world"
3) (nil)
```

## MSET key value [key value ...]
批量替换指定key的value。MSET会用新的value替换已经存在的value，就像普通的SET命令一样。如果你不想覆盖已经存在的values，请参看命令MSETNX。

***MSET是原子的，所以所有给定的keys是一次性set的。***

### 返回值
simple-string-reply：总是OK，因为MSET不会失败。

### 示例
```
> mset key1 "Hello" key2 "World"
OK

> get key1
"Hello"

> get key2
"World"
```

## MSETNX key value [key value ...]
批量替换指定key的value。只要有一个key已经存在，MSETNX一个操作都不会执行。 由于这种特性，MSETNX可以实现要么所有的操作都成功，要么一个都不执行，这样可以用来设置不同的key，来表示一个唯一的对象的不同字段。

***MSETNX是原子的，所以所有给定的keys是一次性set的。***

### 返回值
integer-reply，只有以下两种值：
- 1: 如果所有的key被set
- 0: 如果没有key被set(至少其中有一个key是存在的)

### 示例
```
> mget key1 key2
1) (nil)
2) (nil)

> msetnx key1 "Hello" key2 "three"
(integer) 1

> mget key1 key2
1) "Hello"
2) "three"

> msetnx key2 "three" key3 "world"
(integer) 0

> mget key1 key2 key3
1) "Hello"
2) "three"
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

### 返回值
simple-string-reply:如果SET命令正常执行那么回返回OK，否则如果加了NX 或者 XX选项，但是没有设置条件。那么会返回nil。

### 示例
```
> set mykey "hello"
OK

> get mykey
"hello"

> set mykey "world" nx
(nil)

> set mykey "world" xx
OK

> get mykey
"world"

> set myotherkey "world" xx
(nil)

> set myotherkey "world" nx
OK

> get myotherkey
"world"

> set mykey "hello" ex 10
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

## SETRANGE key offset value
这个命令的作用是覆盖key对应的string的一部分，从指定的offset处开始，覆盖value的长度。如果offset比当前key对应string还要长，那这个string后面就补0以达到offset。不存在的keys被认为是空字符串，所以这个命令可以确保key有一个足够大的字符串，能在offset处设置value。

注意，offset最大可以是229-1(536870911),因为redis字符串限制在512M大小。如果你需要超过这个大小，你可以用多个keys。

### 返回值
integer-reply：该命令修改后的字符串长度

### 示例
```
> set key1 "Hello World"
OK

> setrange key1 6 "Redis"
(integer) 11

> get key1
"Hello Redis"

> setrange key2 6 "Redis"
(integer) 11

> get key2
"\x00\x00\x00\x00\x00\x00Redis"
```

## STRLEN key
返回key的string类型value的长度。如果key对应的非string类型，就返回错误。

### 返回值
integer-reply：key对应的字符串value的长度，或者0（key不存在）

### 示例
```
> set mykey "Hello World"
OK

> strlen mykey
(integer) 11

> strlen nonexisting
(integer) 0
```

## exists key [key ...]
判断 key 是否存在。

### 示例
```
> set mykey "hello"
OK

> exists mykey
(integer) 1

> del mykey
(integer) 1

> exists mykey
(integer) 0
```

## type key
判断指定的 key 所存储的 value 的数据类型。

### 示例
```
> set mykey "x"
OK

> type mykey
string

> del mykey
(integer) 1

> type mykey
none
```

## expire key seconds
设置 key 的有效期。

### 示例
```
> set key "some-value"
OK

> expire key 5
(integer) 1

> get key (immediately)
"some-value"

> get key (after 5 seconds)
(nil)
```

## ttl key
剩余的有效期时长。

### 示例
```
> set key "100" ex 10
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
