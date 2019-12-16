---
title: 面试题-Redis
date: 2019-10-29 10:00:49
tags: 面试题
---
1.Redis支持的数据类型
&nbsp;&nbsp;&nbsp;&nbsp;String字符串、Hash（哈希）、List（列表）、Set（集合）、zset(sorted set：有序集合)
2.什么是Redis持久化
&nbsp;&nbsp;&nbsp;&nbsp;持久化就是把内存的数据写到磁盘中去，防止服务宕机了内存数据丢失。
<!-- more -->
3.Redis常用命令：
&nbsp;&nbsp;&nbsp;&nbsp;Keys pattern \* 查看Exists  key是否存在
&nbsp;&nbsp;&nbsp;&nbsp;Set 设置 key 对应的值为 string 类型的 value。
&nbsp;&nbsp;&nbsp;&nbsp;setnx 设置 key 对应的值为 string 类型的 value。如果 key 已经存在，返回 0，nx 是 not exist 的意思。
&nbsp;&nbsp;&nbsp;&nbsp;Expire 设置过期时间（单位秒）
&nbsp;&nbsp;&nbsp;&nbsp;TTL 查看剩下多少时间 返回负数则key失效，key不存在了
&nbsp;&nbsp;&nbsp;&nbsp;Setex 设置 key 对应的值为 string 类型的 value，并指定此键值对应的有效期。
&nbsp;&nbsp;&nbsp;&nbsp;Mset 一次设置多个 key 的值，成功返回 ok 表示所有的值都设置了，失败返回 0 表示没有任何值被设置。
&nbsp;&nbsp;&nbsp;&nbsp;Getset 设置 key 的值，并返回 key 的旧值。
4.使用过Redis分布式锁么，它是怎么实现的？
&nbsp;&nbsp;&nbsp;&nbsp;先拿setnx来争抢锁，抢到之后，再用expire给锁加一个过期时间防止锁忘记了释放。
5.如果在setnx之后执行expire之前进程意外crash或者要重启维护了，那会怎么样？
&nbsp;&nbsp;&nbsp;&nbsp;set指令有非常复杂的参数，这个应该是可以同时把setnx和expire合成一条指令来用的！（Setex）
6.使用过Redis做异步队列么，你是怎么用的？有什么缺点
&nbsp;&nbsp;&nbsp;&nbsp;一般使用list结构作为队列，rpush生产消息，lpop消费消息。当lpop没有消息的时候，要适当sleep一会再重试。
&nbsp;&nbsp;&nbsp;&nbsp;缺点：在消费者下线的情况下，生产的消息会丢失，得使用专业的消息队列如rabbitmq等。
7.能不能生产一次消费多次呢？
&nbsp;&nbsp;&nbsp;&nbsp;使用pub/sub主题订阅者模式，可以实现1:N的消息队列。
8.缓存穿透
&nbsp;&nbsp;&nbsp;&nbsp;原理：一般的缓存系统都是按照KEY去缓存查询，如果不存在对应的value，就应该去后端系统查询(比如DB)。一些恶意的请求会故意查询不存在的KEY，请求量很大，就会对后端系统造成很大压力。
&nbsp;&nbsp;&nbsp;&nbsp;如何避免：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.对查询结果为空的情况也进行缓存，缓存时间设置短一点，或者该KEY对应的数据insert了之后清理缓存
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.对一定不存在的key进行过滤。可以把所有的可能存在的KEY放到一个大的BigMap中，查询时通过该BigMap过滤
9.缓存雪崩
&nbsp;&nbsp;&nbsp;&nbsp;原理：当缓存服务器重启或大量缓存集中在某一个时间段失效，这样在失效的时候，会给后端系统带来很大压力，导致系统崩溃
&nbsp;&nbsp;&nbsp;&nbsp;如何避免：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个KEY只允许一个线程查询数据和写缓存，其他线程等待
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.做二级缓存，A1为原始缓存，A2为拷贝缓存，A1失效时，可以访问A2，A1缓存失效时间设置为短期，A2设置为长期
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.不同的KEY设置不同的过期时间，让缓存失效的时间点尽量均匀