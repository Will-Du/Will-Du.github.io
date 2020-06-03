---
title: Redis 6.0新特性
date: 2020-06-02 17:45:58
tags: Redis
---
<b style="color: orangered">1.Redis6.0之前的版本真是单线程吗？</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis处理客户端的请求时，包括获取(socket 读)、解析、执行、内容返回(socket 写)等都由一个顺序串行的主线程处理，这就是所谓的“单线程”。但如果严格来讲从Redis4.0之后并不是单线程，除了主线程外，他也有后台线程在处理一些较为缓慢的操作，例如清理脏数据、无用连接的释放、大key的删除等等。<!-- more -->
<b style="color: orangered">2.Redis6.0之前为什么一直不使用多线程？</b>
&nbsp;&nbsp;&nbsp;&nbsp;官方曾做过类似问题的回复：使用Redis时，几乎不存在CPU成为瓶颈的情况，Redis主要受限于内存和网络。例如在一个普通的Linux系统上，Redis通过使用pipelinling每秒可以处理100万个请求，所以如果应用程序主要使用O(N)或O(log(N))的命令，它几乎不会占用太多CPU。
&nbsp;&nbsp;&nbsp;&nbsp;使用了单线程后，可维护性高。多线程模型虽然在某些方面表现优异，但是它却引入了程序执行顺序的不确定性，带来了并发读写的一系列问题，增加了系统复杂度、同时可能存在线程切换、甚至加锁解锁、死锁造成的性能损耗。Redis通过AE事件模型以及IO多路复用等技术，处理性能非常高，因此没有必要使用多线程。单线程机制使得Redis内部实现的复杂度大大降低，Hash的惰性Rehash、Lpush等等“线程不安全”的命令都可以无锁进行。
<b style="color: orangered">3.Redis为什么要引入多线程呢？</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis将所有数据放在内存中，内存的响应时长大约为100纳秒，对于小数据包，Redis服务器可以处理80000到100000 QPS，这也是Redis处理的极限了，对于80%的公司来说，单线程Redis已经足够使用了。
&nbsp;&nbsp;&nbsp;&nbsp;但随着越来越复杂的业务场景，有些公司动不动就上亿的交易量，因此需要更大的QPS。常见的解决方案是在分布式架构中对数据进行分区并采用多个服务器，但该方案有非常大的缺点，例如要管理的Redis服务器太多，维护代价大；某些适用于单个Redis服务器的命令不适用于数据分区；数据分区无法解决热点读、写问题；数据偏斜，重新分配和放大/缩小变得更加复杂等等。
&nbsp;&nbsp;&nbsp;&nbsp;从Redis自身角度来说，因为读写网络的read/write系统调用占用了Redis执行期间大部分CPU时间，瓶颈主要在于网络的IO消耗，优化主要有两个方向：
- 提高网络IO性能，典型的实现比如使用DPDK来替代内核网络栈的方式
- 使用多线程充分利用多核，典型的实现比如Memcached
&nbsp;&nbsp;&nbsp;&nbsp;协议栈优化的这种方式跟Redis关系不大，支持多线程是一种最有效最便捷的操作方式。所以总结起来，Redis支持多线程主要就是两个原因：
- 可以充分利用服务器CPU资源，目前主线程只能利用一个核
- 多线程任务可以分摊Redis同步IO读写负荷
<b style="color: orangered">4.Redis6.0默认是否开启了多线程？</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis6.0的多线程默认是禁用的，只使用主线程。如需开启需要修改redis.conf配置文件：io-threads-do-reads yes
<b style="color: orangered">5.Redis6.0多线程开启时，线程数如何设置？</b>
&nbsp;&nbsp;&nbsp;&nbsp;开启多线程后，还需要设置线程数，否则是不生效的。同样修改redis.conf配置文件：io-threads 4
&nbsp;&nbsp;&nbsp;&nbsp;关于线程数的设置，官方有一个建议：4核的机器建议设置2或3个线程，8核的建议设置为6个线程，线程数一定要小于机器核数。但需要注意的是，线程数并不是越大越好，官方认为超过8个基本就没什么意义了。
<b style="color: orangered">6.Redis6.0采用多线程后，性能的提升效果如何？</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redis作者antirez在RedisConf2019分享时曾提到：Redis 6引入的多线程IO特性对性能的提升至少是一倍以上。国内也有大牛曾使用unstable版本在阿里云esc进行测试，GET/SET命令在4线程IO性能相比单线程是几乎翻倍了。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">说明1：</b>这些性能的测试并没有针对严谨的延时控制和不同并发的场景进行压测。数据仅供验证参考而不能作为线上指标。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">说明2：</b>如果开启多线程，至少要4核的机器，且Redis实例已经占用相当大的CPU耗时的时候才建议使用，否则使用多线程没有意义。所以估计80%的公司开发人员看看就好。
<b style="color: orangered">7.Redis6.0多线程的实现机制？</b>
![多线程实现机制](Redis6.0新特性/redis1.png)
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">流程简述如下：</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.主线程负责接收建立连接请求，获取scoket放入全局等待读处理队列
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.主线程处理完读事件之后，通过RR(Round Robin)将这些连接分配给这些IO线程
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.主线程阻塞等待IO线程读取socket完毕
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.主线程通过单线程的方式执行请求命令，请求数据读取并解析完成，但并不执行
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.主线程阻塞等待IO线程将数据回写socket完毕
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.解除绑定，清空等待队列
![流程图](Redis6.0新特性/redis2.png)
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">该设计有如下特点：</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.IO线程要么同时在读socket，要么同时在写，不会同时读或写
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.IO线程只负责读写socket解析命令，不负责命令处理
<b style="color: orangered">8.开启多线程后，是否会存在线程并发安全问题？</b>
&nbsp;&nbsp;&nbsp;&nbsp;从上面的实现机制可以看出，Redis的多线程部分只是用来处理网络数据的读写和协议解析，执行命令仍然是单线程顺序执行。所以我们不需要去考虑控制key、lua、事务，LPUSH/LPOP等等的并发和线程安全问题。
<b style="color: orangered">9.Redis6.0的多线程和Memcached多线程模型进行对比</b>
&nbsp;&nbsp;&nbsp;&nbsp;前些年Memcached是各大互联网公司常用的缓存方案，因此redis和memcached的区别基本成了面试官缓存方面必问的面试题，最近几年memcached用的少了，基本都是redis。不过随着Redis6.0加入了多线程特性，类似的问题可能还会出现，接下来我们只针对多线程模型来简单比较一下。
![memcached多线程模型](Redis6.0新特性/redis3.png)
&nbsp;&nbsp;&nbsp;&nbsp;如上图所示：Mecached服务器采用master-worker模式进行工作，服务端采用socket与客户端通信。主线程、工作线程采用pipe管道进行通讯。主线程采用libevent监听listen、accept的读事件，事件响应后将连接信息的数据结构封装起来，根据算法选择合适的工作线程，将连接任务携带链连接信息分发出去，相应的线程利用连接描述符建立与客户端的socket连接并进行后续的存取数据操作。
&nbsp;&nbsp;&nbsp;&nbsp;redis6.0与memcached多线程模型对比：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;相同点：都采用了master线程和worker线程的模型
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;不同点：memcached执行主逻辑也是在worker线程，模型更加简单，实现了真正的线程隔离，符合我们对线程隔离的常规理解。而redis把处理逻辑交还给master线程，虽然一定程度上增加了模型复杂度，但也解决了线程并发安全等问题。