# FAQ

## redis启动失败

```bash
Can't handle RDB format version 9
Fatal error loading the DB: Invalid argument. Exiting.
```

- 原因
   当前 redis 版本无法处理 version 为 9 的 rdb 文件，原因是此 rdb 文件为其他版本 redis 生成的，当前版本 redis 无法兼容。

- 解决方法
   删除 dump.rdb 文件，再启动 redis-server 。

## /var/run/redis_6379.pid exists, process is already running or crashed

### 1 删除 pid 文件

```bash
# rm -rf /var/run/redis_6379.pid
```

### 2 删除dump.rdb

```bash
# cd /var/lib/redis/6379

# ls
dump.rdb

# rm -rf dump.rdb
```

## MISCONF Redis is configured to save RDB snapshots, but it is currently not able to persist on disk.

### 1 修改redis.conf

```bash
# vim /etc/redis/6379.conf
stop-writes-on-bgsave-error no

# service redis_6379 restart
```

### 2 修改sysctl.conf

```bash
# vim /etc/sysctl.conf
vm.overcommit_memory=1

# sudo sysctl -p /etc/sysctl.conf
```

## 停止 redis 服务报错：(error) NOAUTH Authentication required.

```bash
# service redis_6379 stop
Stopping ...
(error) NOAUTH Authentication required.
Waiting for Redis to shutdown ...
Waiting for Redis to shutdown ...
Waiting for Redis to shutdown ...
Waiting for Redis to shutdown ...
Waiting for Redis to shutdown ...
```

解决方法：

- 使用 ```ps -ef | grep redis``` 查看 pid，然后 ```kill -9 pid```，如果在 deamon 下还需要删除 pid 文件
- ```redis-cli -a <password> -p <port> shutdown```，***【推荐】***
