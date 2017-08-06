# redis 介绍

一个把数据存储在内存中的高速缓存

可以用来存储字符串，哈希结构，链表，因此常用来提供数据结构服务

# redis 优势

1. Key-Value内存数据库
2. 读写性能强悍 
3. 支持丰富的数据结构:`List`,`Hash`,`Map`, `Set`, `Sorted set (排序的集合)`,`消息订阅与发布(pub/sub)`
4. 可持久化存储 （memchaced不可持久化）,通过一种机制把存储在内存上的数据，也存储在硬盘中.


优点：`高速`，`安全(可持续化)`，`丰富的数据结构`
缺点：消耗内存(内存使用固定)，效率较低（持久化以磁盘存储）


> redis 和 memcached区别

- redis可以用来做存储（`storge`）,memcached用来做缓存（`cache`）
- 存储的数据有“结构”，对于memcacehd来说，存储的数据，只有一种类型:字符串，而reids则可以存字符串，链表，hash结构，集合，有序集合。

都是内存高速缓存数据库，但是redis比memcached支持更多数据类型，且redis可持久化.


|  //   | memcached   |  redis |
| -------- |:-----  | :---- |
|  类型    | 内存数据库     |   内存数据库    |
|  数据类型     | 在定义value是就要固定数据类型 | 不需要，有字符串，链表，集合和有序集合  |
|  虚拟内存  | 不支持    |  支持    |
| 过期策略    | 支持     |  支持    |
|  分布式    | magent     | master-slave, 一主一从，一主多从  |
|  存储数据安全    |  不支持  |  使用 save存储到dump.rdb中   |
|   灾难恢复   | 不支持    |   append only file (aof)用户数据恢复   |

> 文件目录

```
redis-benchmark  reids性能测试工具
redis-check-aof  检查aof日志工具
redis-check-dump 查看rdb日志工具
redis-cli 连接用的客户端
redis-server  redis服务进程
```


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

> 通用key操作

[redis命令手册](http://doc.redisfans.com/)


**key**
```
keys pattern // 查询相应的key
```
允许模糊查询key
有3个通配符`*`,`?`,`[]`
`*`任意通配符
`?`单个通配符

```
randomkey // 获取随机的key
type key  // 判断key值的类型
exists key // 判断是否存在key
```
----
```
del key // 删除key
rename oldKey newKey // 修改key名字 （如果原有已经存在key，则覆盖）
renamenx oldKey newKey // 修改已经存在的key值，失败
```
-----
```
keys * // 查看所有key的值
del key // 删除指定的key
exists key  // 判断指定key 是否存在，不存在返回0，存在返回1
ttl key // 剩余时间 (秒) key存在，永久有效返回-1，不存在返回-2，其它返回剩余时间
pexpire key time // 设置过期时间 (毫秒)
expire key time // 设置过期时间（秒）
persist key // 设置永久有效
```

> 基础类型

**String**
```
set key value [ex 生命周期的秒数]/[px 毫秒数] [nx|xx]
set site sf.com ex 10 
```
`ex`和`px`不要同时存在，同时存在报错
`ng`表示key不存在时，执行操作
`xx`表示key存在时，执行操作
```
flusdb // 冲刷db
```
-----
```
mset key value key value // 一次性设置多个键值
mget key1 key2 key3 // 一次性获取多个值
```
-----
```
setrange key offset value // 把字符串的offset偏移字节，改成value
```
如果**偏移量>字符串长度**该字符自动补`\x00`
```
appned key value // 把value追加到key的原值上
getrange key start stop // 获取字符串[start, stop]范围的值 // 对于字符串下标左数从0开始，右数从-1开始
getset key newValue  // 获取旧值设置新值 // 返回旧值
```
-----
```
strlen key // 字符串长度
get key // 获取字符串
set key value // 设置字符串
mset key value key value // 同时设置多个key值
mget key key // 同时获取多个key值
incr key // 存储值加一
decr key // 存储值减一
incrby key number // 存储值加number
decrby key number // 存储值减number
incrbyfloat key number // 存储值加number（number为浮点数）
decrbyfloat key number // 存储值减number(number为浮点数)
```

# 配置文件 


# 配置文件 

```
daemonize yes // 控制前后台运行
bind ip // 连接的ip
timeout 0 // 超过多长时间没返回就超时
loglevel notice // 日志级别

save 900 1  // 900秒 有1次数据操作，就同步到硬盘中去
save 300 10 // 300秒 之内有10次操作，就同步到硬盘中去
save 60 10000 // 60秒之内有10000次操作，就同步到硬盘中去

dbfilename dump.rdb // 硬盘存储的数据

slaveof <masterip> <msterport> // 主从服务器

```

```
databases 16 // 可以指定存储位置，默认使用的是`0`
```
使用`1`号数据库
![](./_image/1111.png)

```
move key 数据库编号 // 移动数据到其它库中
move key 1 
```

# 数据结构


`String`, `List`（链表）,`Set`,`Hash`，`SortedSet`（有序集合）,`Pub/Sub`(发布/订阅)

## List

`List`多个元素链在一起的一串东西.

特点：在任何位置增加或删除元素都很快
缺点：不支持随机存取

是每个元素都是string类型的双向链表
`头指针` --> `data` --> `尾指针`

基本组成：空间，头指针，指向下一个元素的指针。

```
lpush key value1 value2 // 从left写入链表 
rpush  key value1 value2 // 从right写入链表
lrenge key start top  // 返回列表 key 中指定区间内的元素，区间以偏移量 start 和 stop 指定 // lrange key 0 -1
lpop key // 移除并返回列表 key 的头元素。
rpop key // 移除并返回right的最后一个单元值
lindex key index // 获取指定index的值
llen key // 获取List的长度
ltrim key start stop // 从List中截取指定的开始和结束，包括开始和结束字符
lrem key count value // 从List链表中删除 // lrem answer 1 b // 通过值来删除，指定count个数
```

![](./_image/310180808-59856e3dd58d7_articlex.png)


```
linsert key after|before search value // 沿着List找值，然后在值之前或之后插入新值. // linsert num after b hh // 没查询到，返回-1，插入失败。
```
-----
```
lpoprpush source dest // 把srouce的尾部拿出来，放在dest的头部
```
`lpoprpush`是一个原子性操作，如果自己实现通过`lpop`，`rpush`来操作，中间过程中如果有其它操作，就破坏原子性

场景：task + bak 双链表完成安全队列

业务逻辑：
1：rpoplpush task job
2: 接收返回值，并做业务处理
3: 如果成功，rpop job清除任务，彻底删除；如果不成功，下次从bak表里取任务,继续执行

![](./_image/143639739-59857430080cf_articlex.png)

```
brpop key timeout // 等待弹出key的尾元素
blpop key timeout // 等待弹出key的头元素
```
`timeout` 为等待超时时间，如果`timeout`为0，则一直等待。

场景: 长轮询Ajax，在线聊天

> 位图法统计活跃用户

1：记录用户登陆：
每天按日期生成一个位图，用户登陆后，把user_id位上的bit位置1

2： 把1周的位图 and 计算，
位上位1的，即使连续登陆的用户

```
setbit mon 100000000 0
setbit mon 3 1
setbit mon 5 1
setbit mon 7 1
setbit thur 100000000 0
setbit thur 3 1
setbit thur 5 1
setbit thur 8 1
setbit wen 100000000 0
setbit wen 3 1
setbit wen 4 1
setbit wen 6 1
bitop and res mon thur wen
```

优点：
1. 节约空间，1亿人每天的登陆情况，用1亿bit，约10M的字符就能表示
2. 计算快，计算方便

## Set 

特点：

- 无序性
- 确定性 （描述是确定的）
- 唯一性 （Set的值是唯一，不重复，独一无二）


```
sadd key value1 value2 // 写入集合中
smembers key // 获取集合所有元素
sismember key value // 判断value是否存在key中
srem key // 移除指定key
spop key // 返回并删除集合key的1个随机元素
srendmember key // 返回随机的元素
scard key // 返回集合中的长度
```
-----
```
smove srouce dest value // 把srouce中的value删除，并添加到dest集合中
sunion key1 key2 key3... // 集合的并集
sinter key1 key2... // 集合的交集
sdiff key1 key2 // 集合的差集

sinterstoure result key1 key2 kye3  // 把集合中的结果保存起来
```


## SortedSet

`Order Set` 有序集合
集合本来是无序，既然是有序集合，必然是需要一个排序的`因子`(排序的一句，使用什么来排序)，每个元素都需要一个`score`

每一个元素都带有一个权重score，加入到有序集合里的所有元素都根据score进行了排序

```
zadd key score1 value1 score2 value2 // 写入有序集合中
zadd key website 10 sf.gg 9 google.com 
zrange key 0 -1 [withscores] // 取出所有 // 默认升序 // 指定widthsrouces，一并取出指定的srouce
zrangebyscoure key min max [withscores] limit offset N // 取srouce的[min, max]之内的元素，并跳过offset，取出N个 // 指定widthsrouces，一并取出指定的srouce
```
-----
```
zrank key member // 查询member在集合中的位置
zrevrank key member // 倒序查看member在集合中的位置

zrem  key value1 value2 // 根据value来删除指定元素
zremrangebyscore key min max  // 根据score来删除指定[min, max]范围的元素
zcard key // 返回元素个数
zcount key min max // 返回[min, max]区间内的元素数量
zinterstore destination numbers key1 key2 [WEIGHTS weight] aggregate [sum|min|max] // key1，key2的交集，权重是weight。聚合方法是sum|min|max， 聚合的结果放入des中
```


## Hash

`Hash`类似PHP的`关联数组`.
很多单元不是通过索引，而是通过键来标记.
Hash是一个复合结构，每一个值都是有一个独特的键，指向Hash结构

```
hset key field value // Hash设置
hget key field // 获取Hash给定的key值
hmset key filed1 value  field2 value  // 哈希表 key 中，一个或多个给定域的值。
hmget key field1 field2 // 返回哈希表 key 中，一个或多个给定域的值。
hgetall key // 返回给定key的所有field
hdel key field // 删除key中field域
hlen key // 返回中的元素数量
hexists key field // 判断key中有没有field域 
hkeys key // 返回key的所有键
hvals key // 返回key的所有值
```



## 事务

一组操作在同一时间执行，其中有一个失败了，可以回滚到原来操作。
redis与mysql事务的对比：

|  //   | mysql    |  redis |
| -------- |:-----  | :---- |
|  开启     | start transaction     |   mutil    |
|  语句     | 普通sql     |   普通命令    |
|  失败     | rollback 回滚    |   discard取消   |
|  成功     |  commit   |   exec   |

注：rollback与discard的区别
如果已经成功执行了2条语句，第3条语句错误
rollback后，前2条的语句影响消失
discard姿势结束本次事务，前2条语句造成的影响仍然还在

注：在mutil后的语句中，语句出错可能有2中情况
1： 语法就有问题。
这种exec时，报错，所有语句得不到执行
2： 语法本身没错，但使用对象有问题。比如 zadd操作Link对象exec之后，会执行正确的语句，并跳过不适当的语句。


```
multi
    命令1
    命令2
exec
```
放入队列中，管道里暂时不执行
```
multi
incr count
incr count
incr count
exec
```

![](./_image/2222.png)

## Pub/Sub


```
subscribe channel // 订阅
publish channel value // 发布
```


# PHP操作redis


安装`phpredis`，以`.so` 文件扩展形式存在

> 安装

- 进入`phpredis`源码目录，执行`phpize`
- 配置，`./configuer --with-php-config=/phpconfig所在目录`
- make && make install
- 修改php配置文件
    [reids]
    extension="reids.so"
- 重启php


> 使用php操作redis

```
<?php
	$redis = new Redis();
	$connect_result = $reids->connect('127.0.0.1');
	if ($connect_result) {
		$members = array('red', 'tan', 'pink', 'cyan');
		$redis->sadd('setname', 'red', 'tan', 'pink', 'cyan');
		$getmem = $reids->smembers('setname');
		var_dump($getmem);
		$redis->close();
	}
```

> 工具

`nomn`查看系统资源使用率

```
nmon -s 1 -c 20 -f -m ./

-s 间隔多长时间，去抓取系统资源利用率的快照，包括系统资源，网络，硬盘
-c 执行多长时间
-f 结果存文件
-m 文件路径
```

把生成文件，使用树型结构展示，使用`nmon analysre`（windows）工具


`redis-benchmark` reids性能测试

模拟1000个客户端发起1万次测试请求，并统计linux系统资源使用情况。
```
redis-benchmark -c 1000 –n 10000 –csv
```




