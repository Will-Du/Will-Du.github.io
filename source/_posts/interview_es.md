---
title: 谈谈对ES搜索引擎的理解
date: 2019-12-17 10:57:00
tags: 面试题
---
### 面试官心里分析 ###
&nbsp;&nbsp;&nbsp;&nbsp;问这个，其实面试官就是要看看你了不了解ES的一些基本原理，因为用ES无非就是写入数据、搜索数据。你要是不明白你发起一个写入和搜索请求的时候，ES在干什么，那你是对ES基本就是个黑盒，你还能干什么？你唯一能干的就是用ES的api读写数据。要是出点什么问题，你啥都不知道，那还能指望你什么呢？
<!-- more -->
### 面试题剖析 ###
##### 1.ES写数据过程 #####
&nbsp;&nbsp;&nbsp;&nbsp;客户端选择一个node发送请求过去，这个node就是coordinating node(协调节点)。
&nbsp;&nbsp;&nbsp;&nbsp;coordination node对document进行路由，将请求转发给对应的node(有primary shard)。
&nbsp;&nbsp;&nbsp;&nbsp;实际上node上的primary shard处理请求，然后将数据同步到replica node。
&nbsp;&nbsp;&nbsp;&nbsp;coordination node如果发现primary node和所有replica node都搞定之后，就返回响应结果给客户端。
##### 2.ES读数据过程 #####
&nbsp;&nbsp;&nbsp;&nbsp;可以通过doc id来查询，会根据doc id进行hash，判断出来当时把doc id分配到了哪个shard上面去，从哪个shard去查询。
&nbsp;&nbsp;&nbsp;&nbsp;客户端发送请求到任意一个node，成为coordination node。
&nbsp;&nbsp;&nbsp;&nbsp;coordination node对doc id进行哈希路由，将请求转发到相应的node，此时会使用round-robin随机轮询算法，在primary shard以及其所有replica中随机选择一个，让读请求负载均衡。
&nbsp;&nbsp;&nbsp;&nbsp;接收请求的node返回document给coordination node。
&nbsp;&nbsp;&nbsp;&nbsp;coordination node返回document给客户端。
##### 3.ES搜索数据过程 #####
&nbsp;&nbsp;&nbsp;&nbsp;ES最强大的是做全文检索，就是比如你有三条数据：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;java真好玩啊
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;java真难学啊
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;j2ee特别牛
&nbsp;&nbsp;&nbsp;&nbsp;你根据java关键词来搜索，将包含java的document给搜索出来。ES就会给你返回：java真好玩啊，java真难学啊。
&nbsp;&nbsp;&nbsp;&nbsp;客户端发送请求到一个coordination node。
&nbsp;&nbsp;&nbsp;&nbsp;协调节点将搜索请求转发到所有的shard对应的primary shard或replica shard，都可以。
&nbsp;&nbsp;&nbsp;&nbsp;query phase:每个shard将自己的搜索结果(其实就是一些doc id)返回给协调节点，有协调节点进行数据的合并、排序、分页等操作，产出最终结果。
&nbsp;&nbsp;&nbsp;&nbsp;fetch phase:接着由协调节点根据doc id去各个节点上拉取实际的document数据，最终返回给客户端。
&nbsp;&nbsp;&nbsp;&nbsp;写请求是写入primary shard，然后同步给所有的replica shard；读请求可以从primary shard或replica shard读取，采用的是随机轮询算法。
##### 4.写数据底层原理 #####
&nbsp;&nbsp;&nbsp;&nbsp;先写入内存buffer，在buffer里的时候数据是搜索不到的；同时将数据写入translog日志文件。
&nbsp;&nbsp;&nbsp;&nbsp;如果buffer快满了，或者到一定时间，就会将内存buffer数据refresh到一个新的segment file中，但是此时数据不是直接进入segment file磁盘文件，而是先进入os cache。这个过程就是refresh。
&nbsp;&nbsp;&nbsp;&nbsp;每隔1秒钟，ES将buffer中的数据写入一个新的segment file，每秒钟会产生一个新的磁盘文件segment file，这个segment file中就存储最近1秒内buffer中写入的数据。
&nbsp;&nbsp;&nbsp;&nbsp;但是如果buffer里面此时没有数据，那当然不会执行refresh操作，如果buffer里面有数据，默认1秒钟执行一次refresh操作，刷入一个新的segment file中。
&nbsp;&nbsp;&nbsp;&nbsp;操作系统里面，磁盘文件其实都有一个东西，叫做os cache，即操作系统缓存，就是说数据写入磁盘文件之前，会先进入os cache，先进入操作系统级别的一个内存缓存中去。只要buffer中的数据被refresh操作刷入os cache中，这个数据就可以被搜索到了。
&nbsp;&nbsp;&nbsp;&nbsp;为什么叫ES是准实时的？NRT，全称near real-time。默认是每隔1秒refresh一次的，所以ES是准实时的，因为写入的数据1秒之后才能被看到。可以通过ES的restful api或java api，手动执行一次refresh操作，就是手动将buffer中的数据刷入os cache中，让数据立马就可以被搜索到。只要数据被输入os cache中，buffer就会被清空了，因为不需要保留buffer了，数据在translog里面已经持久化到磁盘一份了。
&nbsp;&nbsp;&nbsp;&nbsp;重复上面的步骤，新的数据不断进入buffer和translog，不断将buffer数据写入一个又一个新的segment file中去，每次refresh完buffer被清空，translog保留。随着这个进程推进，translog会变得越来越大。当translog达到一定长度的时候，就会触发commit操作。
&nbsp;&nbsp;&nbsp;&nbsp;commit操作发生第一步，就是将buffer中现有数据refresh到os cache中去，清空buffer。然后将一个commit point写入磁盘文件，里面标识着这个commit point对应的所有segment file，同时强行将os cache中目前所有的数据都fsync到磁盘文件中去。最后清空现有translog日志文件，重启一个translog，此时commit操作完成。
&nbsp;&nbsp;&nbsp;&nbsp;这个commit操作叫做flush。默认30分钟自动执行一次flush，但如果translog过大，也会触发flush。flush操作就对应着commit的全过程，我们可以通过ES api，手动执行flush操作，手动将os cache中的数据fsync强刷到磁盘上去。
&nbsp;&nbsp;&nbsp;&nbsp;translog日志文件的作用是什么？你执行commit操作之前，数据要么是停留在buffer中，要么是停留在os cache中，无论是buffer还是os cache都是内存，一旦这台机器死了，内存中的数据就全丢了。所以需要将数据对应的操作写入一个专门的日志文件translog中，一旦此时机器宕机，再次重启的时候，ES会自动读取translog日志文件中的数据，恢复到内存buffer和os cache中去。
&nbsp;&nbsp;&nbsp;&nbsp;translog其实也是先写入os cache的，默认每隔5秒刷一次到磁盘中去，所以默认情况下，可能有5秒的数据会仅仅停留在buffer或者translog文件的os cache中，如果此时机器挂了，会丢失5秒钟的数据。但是这样性能比较好，最多丢5秒的数据。也可以将translog设置成每次写操作必须是fsync到磁盘，但是性能会差很多。
&nbsp;&nbsp;&nbsp;&nbsp;实际上你在这里，如果面试官没有问你ES丢数据的问题，你可以在这里给面试官炫一把，你说，其实ES第一是准实时的，数据写入1秒后可以搜索到；可能会丢失数据的。有5秒的数据，停留在buffer、translog os cache、segment file os cache中，而不在磁盘上，如果此时宕机，会导致5秒的数据丢失。
### 总结 ###
&nbsp;&nbsp;&nbsp;&nbsp;数据先写入内存buffer，然后每隔1s，将数据refresh到os cache，到了os cache数据就能被搜索到(所以我们才说ES从写入到能被搜索到，中间有1s的延迟)，每隔5s，将数据写入translog文件(这样如果机器宕机，内存数据全没。最多会有5s的数据丢失)，translog大到一定程度，或者默认每隔30mins，会触发commit操作，将缓存区的数据都flush到segment file磁盘文件中。
&nbsp;&nbsp;&nbsp;&nbsp;数据写入segment file之后，同时就建立好了倒排索引。
##### 1.删除/更新数据底层原理 #####
&nbsp;&nbsp;&nbsp;&nbsp;如果是删除操作，commit的时候会生成一个.del文件，里面将某个doc标识为deleted状态，那么搜索的时候根据.del文件就知道这个doc是否被删除了。
&nbsp;&nbsp;&nbsp;&nbsp;如果是更新操作，就是将原来的doc标识为deleted状态，然后新写入一条数据。
&nbsp;&nbsp;&nbsp;&nbsp;buffer没refresh一次，就会产生一个segment file，所以默认情况下是1s一个segment file，这样下来segment file会越来越多，此时会定期执行merge。每次merge的时候，会将多个segment file合并成一个，同时这里会将标识为deleted的doc给物理删除掉，然后将新的segment file写入磁盘，这里会写一个commit point，标识所有新的segment file，然后打开segment file供搜索使用，同时删除旧的segment file。
##### 2.底层lucene #####
&nbsp;&nbsp;&nbsp;&nbsp;简单来说，lucene就是一个jar包，里面包含了封装好的各种建立倒排索引的算法代码。我们用java开发的时候，引入lucene.jar，然后基于lucene的api去开发就可以了。
&nbsp;&nbsp;&nbsp;&nbsp;通过lucene，我们可以将已有的数据建立索引，lucene会在本地磁盘上面，给我们组织索引的数据结构。
##### 3.倒排索引 #####
&nbsp;&nbsp;&nbsp;&nbsp;在搜索引擎中，每个文档都有一个相应的文档id，文档内容被表示为一系列关键字的集合。例如，文档1经过分词，提取了20个关键字，每个关键字都会记录它在文档中出现的次数和出现的位置。那么，倒排索引就是关键词到文档ID的映射，每个关键词都对应一系列的文件，这些文件中都出现了关键词。
&nbsp;&nbsp;&nbsp;&nbsp;案例：有以下文档

|<font color='#0A0A0A'>DocId</font>|<font color='#0A0A0A'>Doc</font>|
|:-----  |:-----|
|1| 谷歌地图之父跳槽Facebook|
|2| 谷歌地图之父加盟Fcaebook|
|3| 谷歌地图创始人拉斯离开谷歌加盟Facebook|
|4| 谷歌地图之父跳槽Facebook与Wave项目取消有关|
|5| 谷歌地图之父拉斯加盟社交网站Facebook|
&nbsp;&nbsp;&nbsp;&nbsp;对文档进行分词之后，得到以下倒排索引。

|<font color='#0A0A0A'>WordId</font>|<font color='#0A0A0A'>Word</font>|<font color='#0A0A0A'>DocIds</font>|
|:-----|:-----|:-----|
|1| 谷歌|1，2，3，4，5|
|2| 地图|1，2，3，4，5|
|3| 之父|1，2，4，5|
|4| 跳槽|1，4|
|5| Facebook|1，2，3，4，5|
|6| 加盟|2，3，5|
|7| 创始人|3|
|8| 拉斯|3，5|
|9| 离开|3|
|10| 与|4|
|...|...|...|
&nbsp;&nbsp;&nbsp;&nbsp;另外，实用的倒排索引还可以记录更多的信息，比如文档频率信息，表示在文档集合中有多少个文档包含某个单词。
&nbsp;&nbsp;&nbsp;&nbsp;那么，有了倒排索引，搜索引擎可以很方便地响应用户的查询，比如用户输入查询Facebook，搜索系统查找倒排索引，从中读出包含这个单词的文档，这些文档就是提供给用户的搜索结果。
&nbsp;&nbsp;&nbsp;&nbsp;要注意倒排索引的两个重要细节：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.倒排索引中的所有词汇对应一个或多个文档
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.倒排索引中的词汇根据字典顺序升序排列