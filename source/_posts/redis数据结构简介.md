---
title: redis数据结构简介
date: 2018-03-22 14:00:07
tags: 
  - redis
categories: 编程学习
---
# redis中的数据结构
redis中可以存储键与5种不同的数据结构，这5中数据结构分别是：  
 
 - `STRING`(字符串)
 - `LIST`(列表)
 - `SET`(集合)
 - `HASH`(散列
 - `ZSET`(有序集合)

## redis中的字符串
redis中的存储都是`k-v`形式的，存储字符串(STRING)类型的值，也需要设置一个键名。对于redis中的字符串的操作，后续会有更详细的说明。  
简单的字符串操作如下：

```bash
set hello world # 设置键为hello的值为world，返回OK则设置成功
get hello       # 获取键为hello的值，返回其值，不存在则返回nil
del hello       # 删除键为hello的值，成功则返回int类型的1
```
## redis中的列表
redis中的列表可以有序的存储多个字符串。redis中的列表操作和很多编程语言中的列表操作相似。简单的操作如下所示：

```bash
rpush list-key item    # 将值item推入列表list-key的右端
rpush list-key item2   # 将值item2推入列表list-key的右端
rpop list-key          # 从列表的左端弹出一个元素，并返回被弹出的值
lrange list-key 0 -1   # 获取列表在给定范围上的所有值
lindex list-key 1      # 获取列表在给定位置上的单个元素
```
## redis的集合

redis的集合和列表都可以存储多个字符串，它们之间的区别在于：列表可以存储多个相同的字符串，而集合则通过散列值来保证自己存储的每个字符串都是各不相同的，常见的集合操作如下所示：

```bash
sadd set-key item     # 将给定元素添加到集合
smembers set-key      # 返回集合包含的所有元素
sismembers set-key item3     # 检查给定元素是否存在于集合中
srem set-key item     # 如果给定的元素存在于集合中，那么移除这个元素
```

## redis的散列

redis的散列可以存储多个键值对之间的映射。散列存储的值即可以是字符串，又可以是数字值。简单的散列操作如下所示：

```bash
hset hash-key sub-key1 value1   # 将键值对sub-key1 value1添加到散列hash-key中
hgetall hash-key                # 获取散列中所有的键值对
hdel hash-key sub-key1          # 删除散列中指定键值对
```

## redis的有序集合

有序集合和散列一样，都是存储键值对的，有序集合的键被称为成员(member)，每个成员都是独一无二的；有序集合的值被称为分值(score)，**分值必须为浮点数**。*有序集合是redis中唯一一个即可以根据成员访问元素，又可以根据分值及分值的排列顺序来访问元素的结构*。  
简单的有序集合的操作如下所示：

```bash
zadd zset-key 728 member1      # 将一个带有给定分值的成员添加到有序集合里面
zrange zset-key 0 -1 withscores   # 根据有序元素在有序排列中所处的位置，从有序集合里面获取多个元素
zrangebyscore zset-key 0 800 withscores    # 获取有序集合在给定分值范围内的所有元素
zrem zset-key member1        # 如果给定成员存在于有序集合，那么移除这个成员
```

以上内容简单介绍了redis中的5中数据结构，关于redis中对于这几种数据结构的操作，后续有更详尽的介绍。
