# redis 常用命令

## 查看当前连接数

```bash
> info clients
# Clients
connected_clients:67
client_recent_max_input_buffer:2
client_recent_max_output_buffer:0
blocked_clients:0
```

## 查看最大连接数

```bash
> config get maxclients
1) "maxclients"
2) "10000"
```

## 清空数据库

- ```flushdb```: 删除当前(或指定)数据库中的所有 key
- ```flushall```: 删除所有数据库的所有 key

## 批量删除

- 根据前缀批量删除：

    ```bash
    redis-cli -h [ip] -p [port] -a [password] -n [database] --scan --pattern "prefix:*" | xargs -r -n1 -t redis-cli -h [ip] -p [port] -a [password] -n [database] del
    ```
- 批量删除所有：

    ```bash
    redis-cli -h [ip] -p [port] -a [password] -n [database] --scan --pattern "*" | xargs -r -n1 -t redis-cli -h [ip] -p [port] -a [password] -n [database] del
    ```

```xargs``` 参数说明:

- ```-r```: 不加的话，如果 keys 的数量为 0，就会报错 ```(error) ERR wrong number of arguments for 'del' command```
- ```-n1```: 不加的话，如果集群环境下 keys 的数量大于 1 时，可能会报错 ```(error) CROSSSLOT Keys in request don’t hash to the same slot```
- ```-t```: 输出每次删除的内容
