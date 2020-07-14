# 安装
~~~
# yum -y install redis
~~~

# 开启守护进程模式
~~~
# vim /etc/redis/6379.conf
daemonize yes
~~~

# 启动
## 方法1
~~~
# systemctl start redis

# ps -ef | grep redis
redis    16021     1  0 14:10 ?        00:00:00 /usr/bin/redis-server 127.0.0.1:6379
root     16074  3247  0 14:13 pts/2    00:00:00 grep --color=auto redis
~~~

## 方法2
~~~
# /usr/local/bin/redis-server /etc/redis/6379.conf &
~~~

# 允许远程访问
    若要支持远程访问调试，除了需要开放服务器端口号6379，还需要将配置文件中的bind 127.0.0.1注释掉
~~~
# vim /etc/redis/6379.conf
# bind 127.0.0.1

protected-mode no
~~~

# 重启
~~~
# systemctl restart redis
~~~

# 链接redis服务端
~~~
# redis-cli -h 192.168.x.x -p 6379
192.168.x.x:6379>
~~~

# 测试
~~~
192.168.x.x:6379> set name zhy
OK
192.168.x.x:6379> get name
"zhy"
~~~
