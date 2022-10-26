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
