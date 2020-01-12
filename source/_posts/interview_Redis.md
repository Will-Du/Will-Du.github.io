---
title: 面试题-Redis
date: 2019-10-29 10:00:49
tags: 面试题
---
<b style="color: orangered">1.Redis支持的数据类型</b>
&nbsp;&nbsp;&nbsp;&nbsp;String字符串、Hash（哈希）、List（列表）、Set（集合）、zset(sorted set：有序集合)
<b style="color: orangered">2.什么是Redis持久化</b>
&nbsp;&nbsp;&nbsp;&nbsp;持久化就是把内存的数据写到磁盘中去，防止服务宕机了内存数据丢失。
<!-- more -->
<b style="color: orangered">3.Redis常用命令：</b>
&nbsp;&nbsp;&nbsp;&nbsp;Keys pattern \* 查看Exists  key是否存在
&nbsp;&nbsp;&nbsp;&nbsp;Set 设置 key 对应的值为 string 类型的 value。
&nbsp;&nbsp;&nbsp;&nbsp;setnx 设置 key 对应的值为 string 类型的 value。如果 key 已经存在，返回 0，nx 是 not exist 的意思。
&nbsp;&nbsp;&nbsp;&nbsp;Expire 设置过期时间（单位秒）
&nbsp;&nbsp;&nbsp;&nbsp;TTL 查看剩下多少时间 返回负数则key失效，key不存在了
&nbsp;&nbsp;&nbsp;&nbsp;Setex 设置 key 对应的值为 string 类型的 value，并指定此键值对应的有效期。
&nbsp;&nbsp;&nbsp;&nbsp;Mset 一次设置多个 key 的值，成功返回 ok 表示所有的值都设置了，失败返回 0 表示没有任何值被设置。
&nbsp;&nbsp;&nbsp;&nbsp;Getset 设置 key 的值，并返回 key 的旧值。
<b style="color: orangered">4.使用过Redis分布式锁么，它是怎么实现的？</b>
&nbsp;&nbsp;&nbsp;&nbsp;先拿setnx来争抢锁，抢到之后，再用expire给锁加一个过期时间防止锁忘记了释放。
<b style="color: orangered">5.如果在setnx之后执行expire之前进程意外crash或者要重启维护了，那会怎么样？</b>
&nbsp;&nbsp;&nbsp;&nbsp;set指令有非常复杂的参数，这个应该是可以同时把setnx和expire合成一条指令来用的！（Setex）
<b style="color: orangered">6.使用过Redis做异步队列么，你是怎么用的？有什么缺点</b>
&nbsp;&nbsp;&nbsp;&nbsp;一般使用list结构作为队列，rpush生产消息，lpop消费消息。当lpop没有消息的时候，要适当sleep一会再重试。
&nbsp;&nbsp;&nbsp;&nbsp;缺点：在消费者下线的情况下，生产的消息会丢失，得使用专业的消息队列如rabbitmq等。
<b style="color: orangered">7.能不能生产一次消费多次呢？</b>
&nbsp;&nbsp;&nbsp;&nbsp;使用pub/sub主题订阅者模式，可以实现1:N的消息队列。
<b style="color: orangered">8.缓存穿透</b>
&nbsp;&nbsp;&nbsp;&nbsp;原理：一般的缓存系统都是按照KEY去缓存查询，如果不存在对应的value，就应该去后端系统查询(比如DB)。一些恶意的请求会故意查询不存在的KEY，请求量很大，就会对后端系统造成很大压力。
&nbsp;&nbsp;&nbsp;&nbsp;如何避免：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.对查询结果为空的情况也进行缓存，缓存时间设置短一点，或者该KEY对应的数据insert了之后清理缓存
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.对一定不存在的key进行过滤。可以把所有的可能存在的KEY放到一个大的BigMap中，查询时通过该BigMap过滤
<b style="color: orangered">9.缓存雪崩</b>
&nbsp;&nbsp;&nbsp;&nbsp;原理：当缓存服务器重启或大量缓存集中在某一个时间段失效，这样在失效的时候，会给后端系统带来很大压力，导致系统崩溃
&nbsp;&nbsp;&nbsp;&nbsp;如何避免：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个KEY只允许一个线程查询数据和写缓存，其他线程等待
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.做二级缓存，A1为原始缓存，A2为拷贝缓存，A1失效时，可以访问A2，A1缓存失效时间设置为短期，A2设置为长期
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.不同的KEY设置不同的过期时间，让缓存失效的时间点尽量均匀
<b style="color: orangered">10.什么是redis？</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis是一个基于内存的高性能key-value数据库
<b style="color: orangered">11.Redis的特点</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis本质上是一个key-value类型的内存数据库，很像memcached，整个数据库统统加载在内存中进行操作，定期通过异步操作把数据库数据flush到硬盘上进行保存。
&nbsp;&nbsp;&nbsp;&nbsp;因为纯内存操作，Redis的性能非常出色，每秒可以处理超过10万次读写操作，是已知性能最快的key-value DB。
&nbsp;&nbsp;&nbsp;&nbsp;Redis的出色之处不仅仅是性能，Redis最大的魅力是支持保存多种数据结构，此外单个value的最大限制是1GB，不像memcached只能保存1MB的数据，因此Redis可以用来实现很多有用的功能。
&nbsp;&nbsp;&nbsp;&nbsp;比方说用它的list来做FIFO双向链表，实现一个轻量级的高性能消息队列服务，用他的Set可以做高性能的tag系统等等。另外Redis也可以对存入的key-value设置expire时间，因此也可以被当做一个功能加强版的memcached来用。
&nbsp;&nbsp;&nbsp;&nbsp;Redis的主要缺点是数据库容量受到物理内存的限制，不能用作海量数据的高性能读写，因此Redis适合的场景主要局限在较小数据量的高性能操作和运算上。
<b style="color: orangered">12.使用Redis有哪些好处？</b>
&nbsp;&nbsp;&nbsp;&nbsp;1.速度快，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)。
&nbsp;&nbsp;&nbsp;&nbsp;2.支持丰富数据类型，支持String，list，set，sorted set，hash。
&nbsp;&nbsp;&nbsp;&nbsp;3.支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行。
&nbsp;&nbsp;&nbsp;&nbsp;4.丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除
<b style="color: orangered">13.Redis相比Memcached有哪些优势？</b>
&nbsp;&nbsp;&nbsp;&nbsp;1.memcached所有的值均是简单的字符串，Redis作为其替代者，支持更为丰富的数据类型。
&nbsp;&nbsp;&nbsp;&nbsp;2.Redis的速度比memcached快很多。
&nbsp;&nbsp;&nbsp;&nbsp;3.Redis可以持久化其数据。
<b style="color: orangered">14.Memcached与Redis的区别都有哪些？</b>
&nbsp;&nbsp;&nbsp;&nbsp;1.储存方式：Memcached把数据全部存在内存之中，断电后会挂掉，数据不能超过内存大小。Redis有部分存在硬盘上，这样能保证数据的持久性。
&nbsp;&nbsp;&nbsp;&nbsp;2.数据支持类型：Mecached对数据类型支持相对简单，Redis有复杂的数据类型。
&nbsp;&nbsp;&nbsp;&nbsp;3.使用底层模型不同，他们之间底层实现方式以及与客户端之间通信的应用协议不一样。Redis直接自己构建了VM机制，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求。
<b style="color: orangered">15.Redis常见性能问题和解决方案：</b>
&nbsp;&nbsp;&nbsp;&nbsp;1.Master写内存快照，save命令调度rdbSave函数，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务，所以Master最好不要写内存快照。
&nbsp;&nbsp;&nbsp;&nbsp;2.Master AOF持久化，如果不重写AOF文件，这个持久化方式对性能的影响是最小的，但是AOF文件会不断增大，AOF文件过大会影响Master重启的恢复速度。Master最好不要做任何持久化工作，包括内存快照和AOF日志文件，特别是不要启用内存快照做持久化，如果数据比较关键，某个Slave开启AOF备份，策略是每秒同步一次。
&nbsp;&nbsp;&nbsp;&nbsp;3.Master调用BGREWRITEAOF重写AOF文件，AOF在重写的时候会占大量的CPU和内存资源，导致服务load过高，出现短暂服务暂停现象。
&nbsp;&nbsp;&nbsp;&nbsp;4.Redis主从复制的性能问题，为了主从复制的速度和连接的稳定性，Slave和Master最好在同一个局域网内。
<b style="color: orangered">16.mySQL里有2000W数据，Redis中只存20W的数据，如何保证Redis中的数据都是热点数据</b>
&nbsp;&nbsp;&nbsp;&nbsp;相关知识：Redis内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略(回收策略)。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">Redis提供6种数据淘汰策略：</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.volatile-lru:从已设置过期时间的数据集(server.db[i].expires)中挑选最少使用的数据淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.volatile-ttl:从已设置过期时间的数据集(server.db[i].expires)中挑选将要过期的数据淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.volatile-random:从已设置过期时间的数据集(server.db[i].expires)中任意选择数据淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.allkeys-lru:从数据集(server.db[i].dict)中挑选最少使用的数据淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.allkeys-random:从数据集(server.db[i].dict)中任意选择数据淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.no-enviction(驱逐):禁止驱逐数据。
<b style="color: orangered">17.为什么Redis需要把所有数据放到内存中？</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis为了达到最快的读写速度将数据都读到内存中，并通过异步的方式将数据写入磁盘。所以Redis具有快速和数据持久化的特征。如果不将数据放在内存中，磁盘I/O速度严重影响Redis的性能。在内存越来越便宜的今天，Redis将会越来越受欢迎。
&nbsp;&nbsp;&nbsp;&nbsp;如果设置了最大使用的内存，则数据已有记录数达到内存限值后不能继续插入新值。