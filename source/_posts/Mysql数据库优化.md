---
title: Mysql数据库优化
date: 2019-11-25 21:22:23
tags: 数据库
---
** 前言 **
&nbsp;&nbsp;&nbsp;&nbsp;数据库优化一方面是找出系统的瓶颈，提高MySQL数据库的整体性能，而另一方面需要合理的结构设计和参数调整，以提高用户的相应速度，同时还尽可能的节约系统资源，以便让系统提供更大的负荷。
** 优化 **
&nbsp;&nbsp;&nbsp;&nbsp;优化分为两大类：软优化和硬优化，软优化一般是操作数据库即可，而硬优化是操作服务器硬件及参数配置。
<!-- more -->
*** 软优化 ***
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">①.查询语句优化：</label>我们可以用EXPLAIN或DESCRIBE(简写：DESC)命令分析一条查询语句的执行信息，例如：DESC SELECT * FROM USER;其中会显示索引和查询数据读取数据条数等信息。
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">②.优化子查询：</label>在MySQL中，尽量使用JOIN来代替子查询。因为子查询需要嵌套查询，嵌套查询时会建立一张临时表，临时表的建立和删除都会有较大的系统开销，而连接查询不会创建临时表，因此效率比嵌套子查询高。
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">③.使用索引：</label>索引是提高数据库查询速度最重要的方法之一。使用索引的三大注意事项：
- LIKE关键字匹配'%'开头的字符串，不会使用索引。
- OR关键字的两个字段必须都是用了索引，该查询才会使用索引。
- 使用多列索引必须满足最左匹配。
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">④.分解表：</label>对于字段多的表，如果某些字段使用频率较低，此时应当将其分离出来从而形成新表。
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">⑤.中间表：</label>对于将大量连接查询的表可以创建中间表，从而减少在查询时造成的连接耗时。
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">⑥.增加冗余字段：</label>类似于创建中间表，增加冗余也是为了减少连接查询。
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">⑦.分析表、检查表、优化表：</label>分析表主要是分析表中关键字的分布，检查表主要是检查表中是否存在错误，优化表主要是消除删除或更新造成的表空间浪费。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.分析表：使用ANALYZE关键字，如:ANALYZE TABLE USER;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Op:表示执行的操作。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msg_type:信息类型，有status,info,note,warning,error。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Mst_text:显示信息。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.检查表：使用CHECK关键字，如：CHECK TABLE USER[option]
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;option只对MyISAM有效，共5个参数值：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;QUICK:不扫描行，不检查错误的连接。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FAST:只检查没有正确关闭的表。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CHANGED:只检查上次检查后被更改的表和没正确关闭的表。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;MEDIUM:扫描行，以验证被删除的连接是否是有效的，也可以计算各行关键字的校验和。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;EXTENDED:最全面的检查，对每行关键字全面查找。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.优化表：使用OPTIMIZE关键字，如：OPTIMIZE [LOCAL|NO_WRITE_TO_BINLOG] TABLE USER;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;LOCAL|NO_WRITE_TO_BINLOG都是表示不写入日志。优化表只对VARCHAR,BLOB和TEXT有效，通过OPTIMIZE TABLE语句可以消除文件碎片，在执行过程中会加上只读锁。
*** 硬优化***
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">①.硬件三件套：</label>
- 配置多核心和频率高的CPU，多核心可以执行多个线程。
- 配置大内存，提高内存，即可提高缓存区容量，因此能减少磁盘I/O时间，从而提高响应速度。
- 配置高速磁盘或合理分布磁盘，高速磁盘可以提高I/O，分布磁盘能提高并行操作的能力。
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">②.优化数据库参数：</label>优化数据库参数可以提高资源利用率，从而提高MySQL服务器性能。MySQL服务的配置参数都在my.cnf或my.ini，下面列出性能影响比较大的几个参数：
- key_buffer_size:索引缓存区大小
- table_cache:能同时打开表的个数
- query_cache_size和query_cache_type:前者是查询缓存区大小。后者是前面参数的开关，0表示不使用缓存区，1表示使用缓存区，但可以在查询中使用SQL_NO_CACHE表示不要使用缓存区，2表示在查询中明确指出使用缓存区才用缓存区，即SQL_CACHE.
- sort_buffer_size:排序缓存区
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">③.分库分表：</label>因为数据库压力过大，首先一个问题就是高峰期系统性能可能会降低，因为数据库负载过高对性能会有影响。另一方面，压力过大把数据库给搞挂了。所以此时你必须对系统做分库分表+读写分离，也就是把一个库拆分为多个库，部署多个数据库服务上，这时作为主库承载写请求。然后每一个主库挂载至少一个从库，由从库承载读请求。
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">④.缓存集群：</label>如果用户量越来越大，此时你可以不停的加机器，比如说系统层面不停加机器，就可以承载更高的并发请求。然后数据库层面如果写入并发越来越高，就扩容加数据库服务器，通过分库分表是可以支持扩容机器的，如果数据库层面的读并发越来越高，就扩容更多的从库。但是这里有一个很大的问题：数据库其实本身不是用来承载高并发请求的，所以通常说，数据库单机每秒承载的并发就在几千的数量级。而且数据库使用的机器都是较高配置、比较昂贵的机器，成本很高。如果你就是简单的不同的加机器，其实是不对的。所以高并发架构里通常都有缓存这个环节，缓存系统的设计就是为了承载高并发而生。所以单机承载的并发量都是在每秒几万，甚至每秒数十万，对高并发的承载能力比数据库系统要高出一到两个数量级。所以你完全可以根据系统的业务特性，对那种写少读多的请求，引入缓存集群。具体来说，<label style="color:red">就是在写数据库的时候同时写一份数据到缓存集群里，然后用缓存集群来承载大部分的读请求。</label>这样通过缓存集群，就可以用更少的机器资源承载更高的并发。