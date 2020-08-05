# List
## LPUSH key value [value ...]
向列表的头部插入多个 value 。如果 key 不存在，那么会创建一个空的列表然后再进行 push 操作。 

```
> lpush mylist 1
(integer) 1

> lpush mylist 2
(integer) 2

> lrange mylist 0 -1
1) "2"
2) "1"
```

## LPUSHX key value
- 仅当 key 存在的时候，才会在列表的头部插入一个 value
- 当 key 不存在的时候，不会进行任何操作

```
> lpush mylist 1
(integer) 1

> lpushx mylist 2
(integer) 2

> lpushx myotherlist 2
(integer) 0

> lrange mylist 0 -1
1) "2"
2) "1"

> lrange myotherlist 0 -1
(empty list or set)
```

## LPOP key
移除并返回列表的第一个元素。当 key 不存在时返回 nil 。

```
> lpush mylist 1 2 3
(integer) 3

> lpop mylist
"3"

> lrange mylist 0 -1
1) "2"
2) "1"
```

## LRANGE key start stop
返回列表中指定范围内的元素。

start 和 stop 偏移量都是基于 0 的下标，即列表的第一个元素下标是 0（列表的表头），第二个元素下标是 1 ，以此类推。

偏移量也可以是负数，表示从列表的尾部开始计数。 例如， -1 表示列表的最后一个元素，-2 是倒数第二个，以此类推。

当下标超过列表范围的时候不会产生 error 。

```
> lpush mylist 1 2 3
(integer) 3

> lrange mylist 0 -1
1) "3"
2) "2"
3) "1"

> lrange mylist 0 0
1) "3"

> lrange mylist -3 2
1) "3"
2) "2"
3) "1"

> lrange mylist -100 100
1) "3"
2) "2"
3) "1"

> lrange mylist 5 10
(empty list or set)
```

## RPUSH key value [value ...]
向列表的尾部插入多个 value 。如果 key 不存在，那么会创建一个空的列表然后再进行 push 操作。  

```
> rpush mylist a
(integer) 1

> rpush mylist b
(integer) 2

> lrange mylist 0 -1
1) "a"
2) "b"
```

## RPUSHX key value
- 仅当 key 存在的时候，才会在列表的尾插入一个value
- 当 key 不存在的时候，不会进行任何操作

```
> rpush mylist b
(integer) 2

> rpushx myotherlist b
(integer) 0

> lrange mylist 0 -1
1) "a"
2) "b"

> lrange myotherlist 0 -1
(empty list or set)
```

## RPOP key
移除并返回列表的最后一个元素。当 key 不存在时返回 nil 。

```
> rpush mylist a b c
(integer) 3

> rpop mylist
"c"

> lrange mylist 0 -1
1) "a"
2) "b"
```

## LTRIM key start stop
删除列表中指定范围以外的元素，这样列表就会只包含指定范围的元素。

```
> rpush mylist 1 2 3 4 5
(integer) 5

> lrange mylist 0 -1
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"

> ltrim mylist 0 2
OK

> lrange mylist 0 -1
1) "1"
2) "2"
3) "3"

> ltrim mylist 1 -1
OK

> lrange mylist 0 -1
1) "2"
2) "3"
```

## RPOPLPUSH source destination
原子性地返回并移除存储在 source 的列表的最后一个元素（列表尾部元素）， 并把该元素放入存储在 destination 的列表的第一个元素位置（列表头部）。

如果 source 不存在，那么会返回 nil 值，并且不会执行任何操作。 如果 source 和 destination 是同样的，那么这个操作等同于移除列表最后一个元素并且把该元素放在列表头部， 所以这个命令也可以当作是一个旋转列表的命令。

```
> rpush mylist one
(integer) 1

> rpush mylist two
(integer) 2

> rpush mylist three
(integer) 3

> rpoplpush mylist myotherlist
"three"

> lrange mylist 0 -1
1) "one"
2) "two"

> lrange myotherlist 0 -1
1) "three"
```

## BLPOP key [key ...] timeout
BLPOP 是阻塞式列表的弹出原语。 它是命令 LPOP 的阻塞版本，这是因为当给定列表内没有任何元素可供弹出的时候， 连接将被 BLPOP 命令阻塞。 当给定多个 key 参数时，按参数 key 的先后顺序依次检查各个列表，弹出第一个非空列表的头元素。

timeout 参数表示的是一个指定阻塞的最大秒数的整型值。当 timeout 为 0 是表示阻塞时间无限制。

### 非阻塞行为
当 BLPOP 被调用时，如果给定 key 内至少有一个非空列表，那么弹出遇到的第一个非空列表的头元素，并和被弹出元素所属的列表的名字 key 一起，组成结果返回给调用者。

当存在多个给定 key 时， BLPOP 按给定 key 参数排列的先后顺序，依次检查各个列表。 假设 list1 不存在，而 list2 和 list3 都是非空列表。使用以下命令：

客户端1
```
> blpop list1 list2 list3 0
```

客户端2
```
> lpush list2 b
(integer) 1
```

客户端1
```
1) "list2"
2) "b"
```

BLPOP 保证返回一个存在于 list2 里的元素（因为它是从 list1 –> list2 –> list3 这个顺序查起的第一个非空列表）。

### 阻塞行为
如果所有给定 key 都不存在或包含空列表，那么 BLPOP 命令将阻塞连接， 直到有另一个客户端对给定的这些 key 的任意一个执行 LPUSH 或 RPUSH 命令为止。

一旦有新的数据出现在其中一个列表里，那么这个命令会解除阻塞状态，并且返回 key 和弹出的元素值。

客户端1
```
> blpop mylist 0
```

客户端2
```
> lpush mylist b
(integer) 1
```

客户端1
```
1) "mylist"
2) "b"
```

## BRPOP key [key ...] timeout
BRPOP 是一个阻塞的列表弹出原语。 它是 RPOP 的阻塞版本，因为这个命令会在给定list无法弹出任何元素的时候阻塞连接。 该命令会按照给出的 key 顺序查看 list，并在找到的第一个非空 list 的尾部弹出一个元素。

BRPOP 和 BLPOP 基本是完全一样的，除了它们一个是从尾部弹出元素，而另一个是从头部弹出元素。

## BRPOPLPUSH source destination timeout
BRPOPLPUSH 是 RPOPLPUSH 的阻塞版本。 当 source 包含元素的时候，这个命令表现得跟 RPOPLPUSH 一模一样。 当 source 是空的时候，Redis将会阻塞这个连接，直到另一个客户端 push 元素进入或者达到 timeout 时限。 timeout 为 0 能用于无限期阻塞客户端。

## LINDEX key index
返回列表里的元素的索引 index 存储在 key 里面。 下标是从0开始索引的，所以 0 是表示第一个元素， 1 表示第二个元素，并以此类推。 负数索引用于指定从列表尾部开始索引的元素。在这种方法下，-1 表示最后一个元素，-2 表示倒数第二个元素，并以此往前推。

```
> lpush mylist world
(integer) 1

> lpush mylist hello
(integer) 2

> lindex mylist 0
"hello"

> lindex mylist -1
"world"

> lindex mylist 3
(nil)
```

## LINSERT key BEFORE|AFTER pivot value
把 value 插入存于 key 的列表中在基准值 pivot 的前面或后面。

当 key 不存在时，这个list会被看作是空list，任何操作都不会发生。

当 key 存在，但保存的不是一个list的时候，会返回error。

```
> rpush mylist hello
(integer) 1

> rpush mylist world
(integer) 2

> linsert mylist before world three
(integer) 3

> lrange mylist 0 -1
1) "hello"
2) "three"
3) "world"
```

## LLEN key
返回存储在 key 里的list的长度。 如果 key 不存在，那么就被看作是空list，并且返回长度为 0。 当存储在 key 里的值不是一个list的话，会返回error。

```
> lpush mylist world
(integer) 1

> lpush mylist hello
(integer) 2

> llen mylist
(integer) 2
```

## LREM key count value
从列表里移除前 count 次出现的值为 value 的元素。 这个 count 参数通过下面几种方式影响这个操作：

- count > 0: 从头往尾移除值为 value 的元素。
- count < 0: 从尾往头移除值为 value 的元素。
- count = 0: 移除所有值为 value 的元素。

```
> rpush mylist hello
(integer) 1

> rpush mylist hello
(integer) 2

> rpush mylist foo
(integer) 3

> rpush mylist hello
(integer) 4

> lrange mylist 0 -1
1) "hello"
2) "hello"
3) "foo"
4) "hello"

> lrem mylist 2 hello
(integer) 2

> lrange mylist 0 -1
1) "foo"
2) "hello"
```

```
> rpush mylist hello
(integer) 1

> rpush mylist hello
(integer) 2

> rpush mylist foo
(integer) 3

> rpush mylist hello
(integer) 4

> lrange mylist 0 -1
1) "hello"
2) "hello"
3) "foo"
4) "hello"

> lrem mylist -2 hello
(integer) 2

> lrange mylist 0 -1
1) "hello"
2) "foo"
```

## LSET key index value
设置 index 位置的list元素的值为 value。 当index超出范围时会返回一个error。

```
> rpush mylist one
(integer) 1

> rpush mylist two
(integer) 2

> rpush mylist three
(integer) 3

> lrange mylist 0 -1
1) "one"
2) "two"
3) "three"

> lset mylist 0 four
OK

> lset mylist -2 five
OK

> lrange mylist 0 -1
1) "four"
2) "five"
3) "three"
```
