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
<b style="color: orangered">18.Redis是单进程单线程的</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis利用队列技术将并发访问变为串行访问，消除了传统数据库串行控制的开销。
<b style="color: orangered">19.Redis的并发竞争问题如何解决？</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis为单进程单线程模式，采用队列模式将并发访问变为串行访问。Redis本身没有锁的概念，Redis对于多个客户端连接并不存在竞争，但是在Jedis客户端对Redis进行并发访问时会发生连接超时、数据转换错误、阻塞、客户端关闭连接等问题，这些问题均是由于客户端连接混乱造成。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">对此有2种解决方法：</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.客户端角度，为保证每个客户端正常有序与Redis进行通信，对连接进行池化，同时对客户端读写Redis操作采用内部锁synchronized。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.服务器角度，利用setnx实现锁。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">注：对于第一种，需要应用程序自己处理资源的同步，可以利用的方法比较通俗，可以使用synchronized也可以使用lock；第二种需要用到Redis的setnx命令，但是需要注意一些问题。</b>
<b style="color: orangered">20.Redis事务的了解CAS(check-and-set操作实现乐观锁)</b>
&nbsp;&nbsp;&nbsp;&nbsp;和众多其他数据库一样，Redis作为NoSQL数据库也同样提供了事务机制。在Redis中，MULTI/EXEC/DISCARD/WATCH这四个命令是我们实现事务的基石。
&nbsp;&nbsp;&nbsp;&nbsp;相信对有关系型数据库开发经验的开发者而言这一概念并不陌生，即便如此，我们还是会简要的列出Redis中事务的实现特征：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.在事务中的所有命令都将会被串行化的顺序执行，事务执行期间，Redis不会再为其他客户端的请求提供任何服务，从而保证了事务中的所有命令被原子的执行。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.和关系型数据库中的事务相比，在Redis事务中如果有某一条命令执行失败，其后的命令仍然会被继续执行。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.我们可以通过MULTI命令开启一个事务，有关系型数据库开发经验的人可以将其理解为“BEGIN TRANSACTION”语句。在该语句之后执行的命令都将被视为事务之内的操作，最后我们可以通过执行EXEC/DISCARD命令来提交/回滚该事务内的所有操作。这两个Redis命令可被视为等同于关系型数据库中的COMMIT/ROLLBACK语句。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.在事务开启之前，如果客户端与服务器之间出现通讯故障并导致网络断开，其后所有待执行的语句都将不会被服务器执行。然而如果网络中断事件是发生在客户端执行EXEC命令之后，那么该事务中的所有命令都会被服务器执行。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.当使用Append-Only模式时，Redis会通过调用系统函数write将该事务内的所有写操作在本次调用中全部写入磁盘。然而如果在写入的过程中出现系统崩溃，如电源故障导致的宕机，那么此时也许只有部分数据被写入磁盘，而另外一部分数据却已经丢失。
&nbsp;&nbsp;&nbsp;&nbsp;Redis服务器会在重新启动时执行一系列必要的一致性检测，一旦发现类似问题，就会立即退出并给出相应的错误提示。
&nbsp;&nbsp;&nbsp;&nbsp;此外，我们就要充分利用Redis工具包中提供的redis-check-aof工具，该工具可以帮助我们定位到数据不一致的错误，并将已经写入的部分数据进行回滚。修复之后我们就可以再次重新启动Redis服务器了。
<b style="color: orangered">21.WATCH命令和基于CAS的乐观锁</b>
&nbsp;&nbsp;&nbsp;&nbsp;在Redis的事务中，WATCH命令可用于提供CAS(check-and-set)功能。假设我们通过WATCH命令在事务执行之前监控了多个keys，倘若在WATCH之后有任何key的值发生变化，EXEC命令执行的事务都将被放弃，同时返回NULL multi-bulk应答以通知调用者事务执行失败。例如，我们再次假设Redis中并未提供incr命令来完成键值	的原子性递增，如果要实现该功能，我们只能自行编写相应的代码。其伪码如下：
```shell
val = GET mykey
val = val +1
SET mykey $val
```
&nbsp;&nbsp;&nbsp;&nbsp;以上代码只有在单连接的情况下才可以保证执行结果是正确的，因为如果在同一时刻有多个客户端在同时执行该段代码，那么就会出现多线程程序中经常出现的一种错误--竞态争用(race condition)。
&nbsp;&nbsp;&nbsp;&nbsp;比如，客户端A和B都在同一时刻读取了mykey的原有值，假设该值为10，此后两个客户端又均将该值+1后set回Redis服务器，这样会导致mykey的结果为11，而不是我们认为的12。为了解决类似的问题，我们需要借助WATCH命令的帮助，见如下代码：
```shell
WATCH mykey
val = GET mykey
val = val + 1
MULTI
SET mykey $val
EXEC
```
&nbsp;&nbsp;&nbsp;&nbsp;和此前代码不同的是，新代码在获取mykey的值之前先通过WATCH命令监控了该键，此后又将set命令包围在事务中，这样就可以有效的保证每个连接在执行EXEC之前，如果当前连接获取的mykey的值被其他连接的客户端修改，那么当前连接的EXEC命令将执行失败。这样调用者在判断返回值后就可以获悉val是否被重新设置成功。
<b style="color: orangered">22.Redis持久化的几种方式</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">1. 快照(snapshots)</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;缺省情况下，Redis把数据快照存放在磁盘上的二进制文件中，文件名为dump.rdb。你可以配置Redis的持久化策略，例如数据集中每N秒钟有超过N次更新，就将数据写入磁盘；或者你可以手动调用命令SAVE或BGSAVE。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">工作原理：</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Redis forks.子进程开始将数据写到临时RDB文件中。当子进程完成写RDB文件，用新文件替换老文件。这种方式可以是Redis使用copy-on-write技术。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">2. AOF</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;快照模式并不十分健壮，当系统停止，或者无意中Redis被kill掉，最后写入Redis的数据就会丢失。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这对某些应用也许不是大问题，但对于要求高可靠性的应用来说，Redis就不是一个合格的选择。Append-only文件模式是另一种选择。你可以在配置文件中打开AOF模式。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">3. 虚拟内存方式</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当你的key很小而value很大时，使用VM的效果会比较好，因为这样节约的内存比较大。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当你的key不小时，可以考虑使用一些非常方法将很大的key编程很大的value,比如你可以考虑将key，value组合成一个新的value。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vm-max-threads这个参数，可以设置访问swap文件的线程数，设置最好不要超过机器的核数，如果设置为0，那么所有对swap文件的操作都是串行的。可能会造成比较长时间的延迟，但是对数据完整性有很好的的保证。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;自己测试的时候发现虚拟内存性能也不错。如果数据量很大，可以考虑分布式或者其他数据库。
<b style="color: orangered">23.Redis的缓存失效策略和主键失效机制</b>
&nbsp;&nbsp;&nbsp;&nbsp;作为缓存系统都要定期清理无效数据，就需要一个主键失效和淘汰策略。
&nbsp;&nbsp;&nbsp;&nbsp;在Redis当中，有生存期的key被称为volatile。在创建缓存时，要为给定的key设置生存期，当key过期的时候(生存期为0)，它可能会被删除。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">1. 影响生存时间的一些操作</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;生存时间可以通过使用EDL命令来删除整个key来移除，或者被SET和GETSET命令覆盖原来的数据，也就是说，修改key对应的value和使用另外相同的key和value来覆盖以后，当前数据的生存时间不同。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;比如说，对一个key执行INCR命令，对一个列表进行LPUSH命令，或者对一个哈希表执行HSET命令，这类操作都不会修改key本身的生存时间。另一方面，如果使用RENAME对一个key进行改名，那么改名后的key的生存时间和改名前一样。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;RENAME命令的另一种可能是，尝试将一个带有生存时间的key该名成另一个带生存时间的another_key，这时旧的another_key(以及它的生存时间)会被删除，然后旧的key会改名为another_key，因此，新的another_key的生存时间也和原本的key一样。使用PERSIST命令可以在不删除key的情况下，移除key的生存时间，让key重新成为一个persistent key。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">2. 如何更新生存时间</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;可以对一个已经带有生存时间的key执行EXPIRE命令，新指定的生存时间会取代旧的生存时间。过期时间的精度已经被控制在1ms之内，主键失效的时间复杂度是O(1)，EXPIRE和TTL命令搭配使用，TTL可以查看key当前生存时间。设置成功返回1；当key不存在或者不能为key设置生存时间时，返回0。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">3. 最大缓存配置：</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在Redis中，允许用户设置最大使用内存大小，server.maxmemory默认为0，没有指定最大缓存，如果有新的数据添加，超过最大内存，则会使redis崩溃，所以一定要设置。Redis内存数据集大小上升到一定大小的时候，就会实行数据淘汰策略(<b style="color: #6A6AFF">详细参考16点</b>)。
<b style="color: orangered">24.Redis最适合的场景</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis最适合所有数据in-memory的场景，虽然Redis也提供持久化功能，但实际更多的是一个disk-backed的功能，跟传统意义上的持久化有较大的差别。那么可能大家就会有疑问，似乎Redis更像一个加强版的Memcached，那么何时使用Memcached，何时使用Redis呢？
&nbsp;&nbsp;&nbsp;&nbsp;如果简单得比较Redis与Memcached的区别，大多数会得到以下观点：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.Redis不仅支持简单的k/v类型的数据，同时还提供list,set,zset,hash等数据结构的存储。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.Redis支持数据的备份，即master-slave模式的数据备份。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载运行使用。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1. 会话缓存(Session Cache)</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最常用的一种使用Redis的情景是会话缓存(session cache)。用Redis缓存会话比其他存储(如Memcached)的优势在于：Redis提供持久化。当维护一个不是严格要求一致性的缓存时，如果用户的购物车信息全部丢失，大部分人都会不高兴的，现在，他们还会这样吗？
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;幸运的是，随着Redis这些年的改进，很容易找到怎么恰当的使用Redis来缓存会话的文档。甚至广为人知的商业平台Magento也提供Redis的插件。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2. 全页缓存(FPC)</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;除基本的会话token之外，Redis还提供很简便的FPC平台。回到一致性问题，即使重启了Redis实例，因为有磁盘的持久化，用户也不会看到页面加载速度的下降，这是一个极大改进，类似PHP本地FPC。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;再次以Memcached为例，Megento提供了一个插件来使用Redis作为全页缓存后端。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此外，对WordPress的用户来说，Pantheon有一个非常好的插件wp-redis，这个插件能帮助你以最快速度加载你曾浏览过的页面。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">3. 队列</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Redis在内存存储引擎领域的一大优点是提供list和set操作，这使得Redis能作为一个很好的消息队列平台来使用。Redis作为队列使用的操作，就类似于本地程序语言(如Python)对list的push/pop操作。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果你快速的在Google中搜索“Redis queues”,你马上就能找到大量的开源项目，这些项目的目的就是利用Redis创建非常好的后端工具，以满足各种队列需求。例如，Celery有一个后台就是使用Redis作为broker，你可以从这里去查看。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">4. 排行榜/计数器</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Redis在内存中对数字进行递增或递减的操作实现的非常好。集合(Set)和有序集合(Sorted set)也使得我们在执行这些操作的时候变得非常简单，Redis只是正好提供了这两种数据结构。所以，我们要从排序集合中获取到排名靠前的10个用户-我们称之为“user_scores”,我们只需要下面一样执行即可，当然，这是假定你是根据你用户的分数做递增的排序。如果你想返回用户及用户分数，你需要这样执行：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ZRANGE user_scores 0 10 WITHSCORES
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Agora Games就是一个很好的例子，用Ruby实现的，它的排行榜就是使用Redis来存储数据的。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">5. 发布/订阅</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最后(但肯定不是最不重要的)是Redis的发布/订阅功能。发布/订阅的使用场景确实非常多。我已看见人们在社交网络连接中使用，还可以作为基于发布/订阅的脚本触发器，甚至用Redis的发布/订阅功能来建立聊天系统!