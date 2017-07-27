# redis 是什么

一个把数据存储在内存中的高速缓存

# redis 优势

1. Key-Value内存数据库
2. 读写性能强悍 
3. 支持丰富的数据结构:`List`,`Hash`,`Map`, `Set`, `Sorted set (排序的集合)`,`消息订阅与发布(pub/sub)`
4. 可持久化存储 （memchaced不可持久化）,通过一种机制把存储在内存上的数据，也存储在硬盘中.


优点：`高速`，`安全(可持续化)`，`丰富的数据结构`
缺点：消耗内存(内存使用固定)，效率较低（持久化以磁盘存储）


> redis 和 memcached区别

都是内存高速缓存数据库，但是redis比memcached支持更多数据，且redis可持久化.


![clipboard.png](/img/bVJ3Q0)




# redis 使用范围

> 端口 

redis端口：`6379`


> redis使用范围

1. 计数器. `incr count`
2. SNS社区服务
3. 内存高速缓存
4. 利用redis的集合及数据过期策略，做一个防攻系统
5. 利用消息订阅与发布功能 可以做聊天系统
6. 利用list可以做消息队列服务

`List`把一串数据连在一起。


# 编译安装

```
git clone https://github.com/antirez/redis.git
cd redis
make
```

修改redis配置文件
```
vim redis.conf
daemonize yes // 控制前后台运行
```
启动redis
```
./redis-server reids.conf
./reids-cli
```



> 文件下的可执行文件

```
redis-server: redis服务器的daemon启动程序
redis-cli: redi命令行操作工具
redis-benchmark: redis性能检测工具，测试redis在当前系统及配置下的读写性能
redis-stat: redis 状态检测工具，可以检测reids当前状态参数及延迟状况
```

# 初步使用

```
set key value // 设置
get key // 获取
incr key // 计数
key * // 获取全部
```

> redis持久化的两种方式

- 将数据按照规定的频率保存到硬盘上  原子操作（速度快）
    尽量不要使用保存数据文件方式，会把以前的数据从新保存一遍 （mv）
- 将对数据有修改的操作的命令 保存到文件中， 文件后缀为`.aof`中

```
appendonly on // 开启操作命令保存到文件中的方式
appendsync everysec // 保存的频率
appendfilename appendonly.aof // 保存文件的名字
```

# 配置文件 

```
daemonize yes // 控制前后台运行
bind ip // 连接的ip
timeout 0 // 超过多长时间没返回就超时
loglevel notice // 日志级别
databases 16 // 可以指定存储位置，默认是0

save 900 1  // 900秒 有1次数据操作，就同步到硬盘中去
save 300 10 // 300秒 之内有10次操作，就同步到硬盘中去
save 60 10000 // 60秒之内有10000次操作，就同步到硬盘中去

dbfilename dump.rdb // 硬盘存储的数据

slaveof <masterip> <msterport> // 主从服务器

```


