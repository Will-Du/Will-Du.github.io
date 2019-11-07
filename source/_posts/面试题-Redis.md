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
&nbsp;&nbsp;&nbsp;&nbsp;Keys pattern * 查看Exists  key是否存在
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