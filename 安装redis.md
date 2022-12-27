# 安装 redis

## 安装

[redis](https://redis.io/download/ 'redis')

```bash
wget https://github.com/redis/redis/archive/7.0.7.tar.gz -P /usr/local/

cd /usr/local

tar -zxvf redis-7.0.7.tar.gz

cd redis-7.0.7

make

make install PREFIX=/usr/local/redis
```

## 启动

### 直接启动

```bash
mkdir -p /etc/redis

cp /usr/local/redis-7.0.7/redis.conf /etc/redis/6379.conf

vim /etc/redis/6379.conf
```

```conf
bind 0.0.0.0
```

```bash
/usr/local/redis/bin/redis-server /etc/redis/6379.conf
```

### 通过守护进程方式启动

```bash
mkdir -p /etc/redis

cp /usr/local/redis-7.0.7/redis.conf /etc/redis/6379.conf

vim /etc/redis/6379.conf
```

```conf
bind 0.0.0.0

daemonize yes
```

```bash
/usr/local/redis/bin/redis-server /etc/redis/6379.conf
```

### 设置开机启动

```bash
vim /usr/local/redis-7.0.7/utils/redis_init_script
```

```
REDISPORT=6379
EXEC=/usr/local/redis/bin/redis-server
CLIEXEC=/usr/local/redis/bin/redis-cli

PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="/etc/redis/${REDISPORT}.conf"
```

```bash
cp /usr/local/redis-7.0.7/utils/redis_init_script /etc/init.d/redis

chkconfig --add redis

chkconfig redis on
```

其他命令:

```bash
systemctl enable redis

systemctl start redis

systemctl status redis

systemctl stop redis

systemctl disabled redis
```

## 配置环境变量

```bash
vim /etc/profile
```

```
export REDIS_HOME=/usr/local/redis
export PATH=$REDIS_HOME/bin:$PATH
```

```bash
source /etc/profile
```

## 设置密码

```bash
vim /usr/local/redis/bin/redis.conf
```

```conf
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
