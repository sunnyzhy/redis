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
