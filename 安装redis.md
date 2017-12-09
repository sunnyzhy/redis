# 安装
~~~
# yum -y install redis
~~~

# 配置
~~~
# vim /etc/redis.conf
daemonize yes
~~~

# 启动
~~~
# systemctl start redis

# ps -ef | grep redis
redis    16021     1  0 14:10 ?        00:00:00 /usr/bin/redis-server 127.0.0.1:6379
root     16074  3247  0 14:13 pts/2    00:00:00 grep --color=auto redis
~~~

# 允许远程访问
    若要支持远程访问调试，除了需要开放服务器端口号6379，还需要将配置文件中的bind 127.0.0.1注释掉
~~~
# vim /etc/redis.conf
# bind 127.0.0.1
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
