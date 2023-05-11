# Redis集群-启停&master和slave的切换

[TOC]

## 杀掉Redis集群中的所有节点的进程

- 分别获取redis节点的pid**(注：redis集群中的节点的pid一般是不一致的)**

```shell
ps -ef|grep redis
```

- 获取到redis节点的进程例如：

```shell
sandy    745 38725  0 15:56 pts/3    00:00:00 grep --color=auto redis
sandy  46077     1  1  2018 ?        5-19:34:34 /home/sandy/redis4/bin/redis-server 【本机IP与bind一致】:6379 [cluster]
```

- 最好先杀掉从节点的pid，以保证集群主从不变，获取到redis节点的进程pid例如46077并杀掉redis进程(其余redis节点也是如此)

```shell
kill 46077
```

- 在此查看redis各节点的进程例如：(表示已经杀掉redis节点的进程)

```shell
ps -ef|grep redis

sandy    2020 38725  0 15:56 pts/3    00:00:00 grep --color=auto redis
```

## 恢复Redis集群

- 启动所有机器的redis实例

```shell
/home/sandy/redis4/bin/redis-server  /home/sandy/redis4/conf/redis.conf
```

- 在各个机器查看redis是否启动成功(查看到redis节点的进程例如)

```shell
ps -ef|grep redis

sandy  23468 10573  0 15:58 pts/1    00:00:00 grep --color=auto redis
sandy  40029     1  0  2019 ?        03:59:15 /home/sandy/redis4/bin/redis-server 【本机IP与bind一致】:6379 [cluster]
```

- 任意连接redis一个客户端，查看集群信息(查看到所有redis节点在集群的主从关系表示Redis集群已恢复)

```shell
/home/sandy/redis4/bin/redis-cli -c -h 【连接机器IP】 -p 6379  -a 【登录密码】
CLUSTER NODES

4e042e0ca7e513646ef3fefa30a71fe4a51d1956 【节点3IP】:6379@16379 master - 0 1538966585556 3 connected 10923-16383
88df1952ebce427eb29ea1259b79264f94bed6c7 【节点5IP】:6379@16379 slave e50853129701300ab2f6c2bdc23acc3eae2319ed 0 1538966586557 5 connected
0062d412dcc2826d31ef2644f221dcb50d0cce7b 【节点1IP】:6379@16379 myself,master - 0 1538966585000 1 connected 0-5460
4b3afe6dd443495edd03c8889b764912905517dc 【节点4IP】:6379@16379 slave 0062d412dcc2826d31ef2644f221dcb50d0cce7b 0 1538966586000 4 connected
e50853129701300ab2f6c2bdc23acc3eae2319ed 【节点2IP】:6379@16379 master - 0 1538966585556 2 connected 5461-10922
ad23800ce0e4c5438abc049b9b97d3b36fe8d689 【节点6IP】:6379@16379 slave 4e042e0ca7e513646ef3fefa30a71fe4a51d1956 0 1538966586757 6 connected
```

## 注：

此redis集群停止方案，是为了方便自动恢复redis集群以及保留存储在redis中的数据，若直接reboot了redis节点机器，则集群可能不能自动恢复，存储在redis中的数据也会丢失

## 主从切换

- 登录slave节点

```
/home/sandy/redis4/bin/redis-cli -c -h 【连接机器IP】 -p 6379  -a 【登录密码】
cluster failover #显示ok后查看集群状态
```



