# redis

## redis 可视化工具

[RedisInsight](https://redis.com/redis-enterprise/redis-insight/ 'RedisInsight')

## 设置 redis 密码
```bash
# vim /etc/redis/6379.conf
requirepass your_password

# systemctl restart redis
```

## 缓存原理

![缓存原理](./images/redis-01.png '缓存原理')

流程如下:

1. 查询 mysql
2. 返回结果
3. 把返回的结果写入 redis
4. 下次再有相同的查询，就先从 redis 里查询，如果有，就直接返回结果；如果没有，就从第 1 步开始执行后续流程

## 缓存穿透

![缓存穿透](./images/redis-02.png '缓存穿透')

缓存穿透的特点:

1. 查询的一定是不存在的数据，比如 id = -1 的数据
2. redis 不会命中
3. 请求被转发给 mysql
4. mysql 也不会命中
5. 因为 mysql 不会命中，所以不会有结果写入 redis
6. 从第 1 步开始重复执行

解决方法: 布隆过滤器 BloomFilter

## 缓存击穿

![缓存击穿](./images/redis-03.png '缓存击穿')

缓存穿透的特点:

1. 某一个缓存数据失效，从而被删除
2. 随后又请求了该数据
3. 因为刚刚从 redis 里被删除，所以redis不会命中
4. 请求被转发给 mysql

## 缓存雪崩

![缓存雪崩](./images/redis-04.png '缓存雪崩')

缓存雪崩的特点:

1. 大量的缓存数据同时失效，从而被大面积删除
2. 随后又请求了该批量的数据
3. 因为刚刚从 redis 里被删除，所以redis不会命中
4. 所有的请求都被转发给了 mysql

解决方法: 针对不同的数据，设置不同的有效期，避免给大批量的缓存数据设置相同的有效期。

缓存击穿 vs 缓存雪崩:

- 相同点: 都给缓存数据设置了有效期
- 不同点：缓存击穿针对的是某一个 key，而缓存雪崩针对的是某一批 key

## Cache Aside Pattern
### 读

1. 先读 cache，再读 db

2. 如果 cache hit，就直接返回数据

3. 如果 cache miss，就访问 db，并将数据 set 进缓存

### 写

1. 写数据库

2. 淘汰缓存

3. 缓存采用 delete 淘汰，而不是 set 更新

### 策略

- Cache Aside Pattern为什么建议淘汰缓存，而不是更新缓存？
   ```
   如果更新缓存，在并发写时，可能出现数据不一致。
   ```

- Cache Aside Pattern为什么建议先操作数据库，再操作缓存？
   ```
   如果先写缓存，在读写并发时，可能出现数据不一致。
   ```
