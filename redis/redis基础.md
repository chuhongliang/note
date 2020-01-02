### 基本使用（常见数据结构的命令）

#### redis的数据结构

redis的数据存储结构：**redis本身采用key-value的方式存储数据**

- key的类型是string                
- value的类型包括5种：string  set  list  zset(可排序，打分)  hash

> 实际上Redis并**没有直接使用**这些数据结构来实现`key-value`数据库，而是**基于**这些数据结构创建了一个**对象系统**，redis数据库里面的每个键值对都是由对象组成的，其中
>
> 1. 数据库的键总是一个字符串对象
> 2. 数据库键对应的值可以是字符串对象，列表对象，哈希对象，集合对象，有序集合对象这五种对象中的一个
>
> 也就是说：当我们执行了 `set username zhangsan` 命令之后，在redis内部会生成两个字符串对象，分别做 key 和 value 存储数据

#### redis的命令

在命令行客户端中写入命令可以用来操作redis，常见的命令如下：

> Redis 命令参考：http://doc.redisfans.com/
>
> try Redis(不用安装Redis即可体验Redis命令)：http://try.redis.io/

 

##### redis常用命令汇总 

value的数据结构不同，对应支持的命令不同

###### string

| 命令   | 示例             | 作用                                           |
| ------ | ---------------- | ---------------------------------------------- |
| set    | set k v          | 添加一个key-value                              |
| get    | get k            | 根据key获取value                               |
| exists | exists k         | 判断key是否存在                                |
| del    | del k            | 根据key删除对应数据该命令适用于其他4个数据结构 |
| mset   | mset k1 v1 k2 v2 | 一次性设置多个key-value                        |
| mget   | mget k1 k2       | 一次性根据多个key获取多个value                 |
| incr   | incr k1          | 对该key对应的value(数字)，进行加1              |
| decr   | decr k1          | 对该key对应的value(数字)，进行减1              |

应用场景：缓存查询结果(json或者序列化) 用户访问记录 例如：通过记录ip-访问次数来显示ip的访问 统计粉丝数、点击次数

 ###### set 

| 命令     | 示例         | 作用                                     |
| -------- | ------------ | ---------------------------------------- |
| sadd     | sadd k v     | 向key对应的set集合中添加一个元素         |
| smembers | smembers k   | 获取set集合中所有元素                    |
| scard    | scard k      | 获取set集合中元素个数                    |
| spop     | spop k       | 删除set集合中任意一个元素                |
| srem     | srem k v     | 删除set集合中指定元素                    |
| sdiff    | sdiff k1 k2  | 返回k1对应集合中去重和k2对应集合重复部分 |
| sinter   | sinter k1 k2 | 获取k1对应set集合和k2对应set集合交集     |
| sunion   | sunion k1 k2 | 获取k1对应set集合和k2对应set集合的并集   |

应用场景：查找共同好友

###### zset (sorted_set)

| 命令      | 示例                   | 作用                                  |
| --------- | ---------------------- | ------------------------------------- |
| zadd      | zadd k 分数 v          | 在key对应的有序集合中添加一个新元素   |
| zrevrange | zrevrange k start stop | 获取下标区间的元素，按照分数正向排名  |
| zcard     | zcard k                | 返回key对应的zset集合元素个数         |
| zrem      | zrem k v               | 删除key对应集合中value元素            |
| zrevrank  | zrevrank k v           | 获取value在key对应集合中排名(从0开始) |
| zscore    | zscore k v             | 获取value在key对应的zset集合中的分数  |
| zincrby   | zincrby k n v          | 对key对应的set集合中的v进行加分操作   |

应用场景：排行榜

###### hash

| 命令    | 示例         | 作用                                |
| ------- | ------------ | ----------------------------------- |
| hset    | hset k k1 v1 | 向hash中添加一个k-v                 |
| hget    | hget k k1    | 根据k获取hash,在根据k1从hash中获取v |
| hgetall | hgetall k    | 获取k对应的hash中所有的k-v          |
| hdel    | hdel k k1    | 删除k对应的hash中的k1数据           |
| hkeys   | hkeys k      | 获取k对应hash中所有key              |
| hvals   | hvals k      | 获取k对应hash中所有的value          |
| hexists | hexists k k1 | 判断hash中是否存在对应的k           |

应用场景：存储对象，便于修改 分区缓存

###### list

| 命令   | 示例                | 作用                          |
| ------ | ------------------- | ----------------------------- |
| rpush  | rpush k v           | 从list集合右侧添加一个元素    |
| rpop   | rpop                | 删除list集合右侧的元素        |
| lpush  | lpush k v           | 从list集合左侧添加一个元素    |
| lpop   | lpop                | 删除list集合左侧的元素        |
| llen   | llen k              | 获取list集合长度              |
| lrange | lrange k start stop | 获取liist集合下标中范围的元素 |
| lindex | lindex k 下标       | 获取list集合中某个下标的元素  |

应用场景：关注列表、消息队列

###### 其他

| 命令                   | 示例        | 作用                                       |
| ---------------------- | ----------- | ------------------------------------------ |
| seletct database的编号 | select 0    | redis默认16个库选择指定库操作，默认从0开始 |
| flushdb                | flushdb     | 清空当前操作的库                           |
| flushall               | flushall    | 清空redis所有库                            |
| expire key             | expire key  | 设置key存活时间的秒                        |
| ttl key                | ttl key     | 查看key对应的数据的存活时间                |
| pexpire key            | pexpire key | 存活时间的毫秒                             |
| pttl key               | pttl key    | 查看key对应的数据的存活时间,毫秒单位       |
| keys *                 | keys *      | 打印所有key                                |

