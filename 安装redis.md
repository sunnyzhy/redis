# 安装 redis

## 安装

```bash
# yum -y install redis
```

## 开启守护进程模式

```bash
# vim /etc/redis/6379.conf
daemonize yes
```

## 启动

### 方法1

```bash
# systemctl start redis

# ps -ef | grep redis
redis    16021     1  0 14:10 ?        00:00:00 /usr/bin/redis-server 127.0.0.1:6379
root     16074  3247  0 14:13 pts/2    00:00:00 grep --color=auto redis
```

### 方法2

```bash
# /usr/local/bin/redis-server /etc/redis/6379.conf &
```

## 允许远程访问

    若要支持远程访问调试，除了需要开放服务器端口号6379，还需要将配置文件中的bind 127.0.0.1注释掉

```bash
# vim /etc/redis/6379.conf
# bind 127.0.0.1

protected-mode no
```

## 设置密码

```bash
masterauth 123456

requirepass 123456
```

注:

- masterauth: 用于集群间的节点相互访问时的认证
- requirepass: 用于单独连接时的认证

## 重启

```bash
# systemctl restart redis
```

## 连接 redis 服务端

```bash
# redis-cli -h 192.168.x.x -p 6379
192.168.x.x:6379>
```

## 测试

```bash
192.168.x.x:6379> set name zhy
OK
192.168.x.x:6379> get name
"zhy"
```
