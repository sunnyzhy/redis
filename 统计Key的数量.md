# 统计Key的数量
## 1. info
统计所有数据库里 key 的数量。

- 方法1:

   ```bash
   # redis-cli -h host_ip -p port -a auth_password info keyspace
   Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
   # Keyspace
   db0:keys=1397,expires=76,avg_ttl=21309000
   db1:keys=28350,expires=0,avg_ttl=0
   db2:keys=5,expires=0,avg_ttl=0
   ```

- 方法2:

   ```bash
   # redis-cli -h host_ip -p port
   
   > auth auth_password
   OK
   
   > info keyspace
   # Keyspace
   db0:keys=1397,expires=64,avg_ttl=26714878
   db1:keys=28350,expires=0,avg_ttl=0
   db2:keys=5,expires=0,avg_ttl=0
   ```

## 2. dbsize
统计指定数据库里 key 的数量。

- 方法1:

   ```bash
   # redis-cli -h host_ip -p port -n db_index -a auth_password dbsize
   Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
   (integer) 1397
   ```

- 方法2:

   ```bash
   # redis-cli -h host_ip -p port
   
   > auth auth_password
   OK
   
   > select 0
   OK

   > dbsize
   (integer) 1397
   ```

## 3. keys 
统计包含关键字的 Key 的数量。

- 方法:

   ```bash
   # redis-cli -h host_ip -p port -n db_index -a auth_password keys "abc:e:*" | wc -l
   Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
   1103
   ```
