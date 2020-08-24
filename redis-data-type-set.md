# set
## SADD key member [member ...]
添加一个或多个指定的member元素到集合的 key中.指定的一个或者多个元素member 如果已经在集合key中存在则忽略.如果集合key 不存在，则新建集合key,并添加member元素到集合key中.

如果key 的类型不是集合则返回错误.

### 返回值
integer-reply:返回新成功添加到集合里元素的数量，不包括已经存在于集合中的元素.

### 示例
```
> sadd myset "Hello"
(integer) 1

> sadd myset "World"
(integer) 1

> sadd myset "World"
(integer) 0

> smembers myset
1) "World"
2) "Hello"
```

## SCARD key
返回集合存储的key的基数 (集合元素的数量).

### 返回值
integer-reply: 集合的基数(元素的数量),如果key不存在,则返回 0.

### 示例
```
> sadd myset "Hello"
(integer) 1

> sadd myset "World"
(integer) 1

> scard myset
(integer) 2
```

## SDIFF key [key ...]
返回一个集合与给定集合的差集的元素.

### 返回值
array-reply:结果集的元素.

### 示例
```
> sadd key1 "a"
(integer) 1

> sadd key1 "b"
(integer) 1

> sadd key1 "c"
(integer) 1

> sadd key2 "c"
(integer) 1

> sadd key2 "d"
(integer) 1

> sadd key2 "e"
(integer) 1

> sdiff key1 key2
1) "a"
2) "b"
```

## SDIFFSTORE destination key [key ...]
该命令类似于 SDIFF, 不同之处在于该命令不返回结果集，而是将结果存放在destination集合中.

如果destination已经存在, 则将其覆盖重写.

### 返回值
integer-reply: 结果集元素的个数.

### 示例
```
> sadd key1 "a"
(integer) 1

> sadd key1 "b"
(integer) 1

> sadd key1 "c"
(integer) 1

> sadd key2 "c"
(integer) 1

> sadd key2 "d"
(integer) 1

> sadd key2 "e"
(integer) 1

> sdiffstore key key1 key2
(integer) 2

> smembers key
1) "a"
2) "b"
```

## SINTER key [key ...]
返回指定所有的集合的成员的交集.

### 返回值
array-reply: 结果集成员的列表.

### 示例
```
> sadd key1 "a"
(integer) 1

> sadd key1 "b"
(integer) 1

> sadd key1 "c"
(integer) 1

> sadd key2 "c"
(integer) 1

> sadd key2 "d"
(integer) 1

> sadd key2 "e"
(integer) 1

> sinter key1 key2
1) "c"
```

## SINTERSTORE destination key [key ...]
这个命令与SINTER命令类似, 但是它并不是直接返回结果集,而是将结果保存在 destination集合中.

如果destination 集合存在, 则会被重写.

### 返回值
integer-reply: 结果集中成员的个数.

### 示例
```
> sinterstore key key1 key2
(integer) 1

> smembers key
1) "c"
```

## SISMEMBER key member
返回成员 member 是否是存储的集合 key的成员.

### 返回值
integer-reply,详细说明:

- 如果member元素是集合key的成员，则返回1
- 如果member元素不是key的成员，或者集合key不存在，则返回0

### 示例
```
> sadd myset "one"
(integer) 1

> sismember myset "one"
(integer) 1

> sismember myset "two"
(integer) 0
```

## SMEMBERS key
返回key集合所有的元素.

该命令的作用与使用一个参数的SINTER 命令作用相同.

### 返回值
array-reply:集合中的所有元素.

### 示例
```
> sadd myset "Hello"
(integer) 1

> sadd myset "World"
(integer) 1

> smembers myset
1) "World"
2) "Hello"
```

## SMOVE source destination member
将member从source集合移动到destination集合中. 对于其他的客户端,在特定的时间元素将会作为source或者destination集合的成员出现.

如果source 集合不存在或者不包含指定的元素,这smove命令不执行任何操作并且返回0.否则对象将会从source集合中移除，并添加到destination集合中去，如果destination集合已经存在该元素，则smove命令仅将该元素充source集合中移除. 如果source 和destination不是集合类型,则返回错误.

### 返回值
integer-reply

- 如果该元素成功移除,返回1
- 如果该元素不是 source集合成员,无任何操作,则返回0.

### 示例
```
> sadd myset "one"
(integer) 1

> sadd myset "two"
(integer) 1

> sadd myotherset "three"
(integer) 1

> smembers myset
1) "one"
2) "two"

> smembers myotherset
1) "three"

> smove myset myotherset "two"
(integer) 1

> smembers myset
1) "one"

> smembers myotherset
1) "two"
2) "three"
```

## SPOP key [count]

从存储在key的集合中移除并返回一个或多个随机元素。

此操作与SRANDMEMBER类似，它从一个集合中返回一个或多个随机元素，但不删除元素。

如果count大于集合内部的元素数量，此命令将会返回整个集合，不会有额外的元素。

### 返回值
bulk-string-reply：被删除的元素，或者当key不存在时返回nil。

### 示例
```
> sadd myset "one"
(integer) 1

> sadd myset "two"
(integer) 1

> sadd myset "three"
(integer) 1

> smembers myset
1) "one"
2) "three"
3) "two"

> spop myset
"one"

> smembers myset
1) "three"
2) "two"

> sadd myset "four"
(integer) 1

> sadd myset "five"
(integer) 1

> smembers myset
1) "four"
2) "five"
3) "three"
4) "two"

> spop myset 3
1) "four"
2) "five"
3) "three"

> smembers myset
1) "two"
```

## SRANDMEMBER key [count]
仅提供key参数时，该命令作用类似于SPOP命令，不同的是SPOP命令会将被选择的随机元素从集合中移除，而SRANDMEMBER仅仅是返回该随记元素，而不做任何操作.

当传递了一个值为正数的count参数，返回的元素就好像从集合中移除了每个选中的元素一样。但是元素不会从集合中移除。所以基本上：

- 不会返回重复的元素。
- 如果count参数的值大于集合内的元素数量，此命令将会仅返回整个集合，没有额外的元素。

相反，当count参数的值为负数时，此命令的行为将发生改变，并且提取操作就像在每次提取后，重新将取出的元素放回包里一样，因此，可能返回重复的元素，以及总是会返回我们请求的数量的元素，因为我们可以一次又一次地重复相同的元素，除了当集合为空（或者不存在key）的时候，将总是会返回一个空数组。

### 返回值
bulk-string-reply: 不使用count 参数的情况下该命令返回随机的元素，如果key不存在则返回nil。

array-reply: 使用count参数,则返回一个随机的元素数组，如果key不存在则返回一个空的数组。

### 示例
```
> sadd myset "one" "two" "three"
(integer) 3

> smembers myset
1) "one"
2) "three"
3) "two"

> srandmember myset
"one"

> smembers myset
1) "one"
2) "three"
3) "two"

> srandmember myset 2
1) "two"
2) "three"

> smembers myset
1) "one"
2) "three"
3) "two"

> srandmember myset -5
1) "one"
2) "one"
3) "one"
4) "three"
5) "one"
```

## SREM key member [member ...]
在key集合中移除指定的元素. 如果指定的元素不是key集合中的元素则忽略 如果key集合不存在则被视为一个空的集合，该命令返回0.

如果key的类型不是一个集合,则返回错误.

### 返回值
integer-reply:从集合中移除元素的个数，不包括不存在的成员.

### 示例
```
> sadd myset "one" "two" "three"
(integer) 3

> srem myset "one"
(integer) 1

> smembers myset
1) "three"
2) "two"

> srem myset "four"
(integer) 0

> smembers myset
1) "three"
2) "two"
```

## SUNION key [key ...]
返回给定的多个集合的并集中的所有成员.

### 返回值
array-reply:并集的成员列表

### 示例
```
> sadd key1 "a" "b" "c"
(integer) 3

> sadd key2 "c" "d" "e"
(integer) 3

> sunion key1 key2
1) "b"
2) "c"
3) "d"
4) "a"
5) "e"
```

## SUNIONSTORE destination key [key ...]
该命令作用类似于SUNION命令,不同的是它并不返回结果集,而是将结果存储在destination集合中.

如果destination 已经存在,则将其覆盖.

### 返回值
integer-reply:结果集中元素的个数.

### 示例
```
> sadd key1 "a" "b" "c"
(integer) 3

> sadd key2 "c" "d" "e"
(integer) 3

> sunionstore key key1 key2
(integer) 5

> smembers key
1) "b"
2) "c"
3) "d"
4) "a"
5) "e"
```
