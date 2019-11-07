---
title: 集群/分布式环境下5种共享session处理策略
date: 2019-11-07 08:11:45
tags: 面试题
---
1.粘性session：将用户锁定到某个服务器上
&nbsp;&nbsp;&nbsp;&nbsp;优点：简单，不需要对session进行处理
&nbsp;&nbsp;&nbsp;&nbsp;缺点：缺乏容错性，如果当前访问的服务器发生故障，用户被转移到另一个服务器上时，他的session信息会失效
&nbsp;&nbsp;&nbsp;&nbsp;使用场景：发生故障对客户产生的影响较小的时候，毕竟服务器故障是小概率事件
&nbsp;&nbsp;&nbsp;&nbsp;实现方式：nginx在upstream模块配置ip_hash属性即可实现粘性session
<!-- more -->
2.服务器session复制：任何一个服务器上的session发生改变，该节点会把这个session的所有内容序列化，然后广播给所有其他节点，不管其他服务器需不需要session，以此来保证session同步
&nbsp;&nbsp;&nbsp;&nbsp;优点：可容错，每个服务器间的session能够实时响应
&nbsp;&nbsp;&nbsp;&nbsp;缺点：会对网络负荷造成一定压力，如果session量大的话可能造成网络堵塞，拖慢服务器性能
&nbsp;&nbsp;&nbsp;&nbsp;实现方式：①设置tomcat，server.xml开启Tomcat集群功能（address：填写本机ip即可，设置端口号，预防端口冲突）②在应用里增加信息：通知应用当前处于集群环境中，支持分布式（在web.xml中添加选项<distributable>）
3.session共享机制：使用分布式缓存方案比如memcached、Redis，但要求memcached/Redis必须是集群，分为以下两种情况
&nbsp;&nbsp;&nbsp;&nbsp;①粘性session处理方式：不同的Tomcat指定访问不同的memcached。多个memcached之间通信是同步的，能主从备份和高可用。用户访问是首先在Tomcat创建session，然后将session复制一份放到他对应的memcached上。memcached只起备份作用，读写都在Tomcat上。当某一个Tomcat挂掉后，集群将用户访问定位到备用tomcat上，然后根据cookie中存储的sessionid找session，找不到时，再去相应的memcached上取session，找到之后将其复制到备用的tomcat上。
&nbsp;&nbsp;&nbsp;&nbsp;②非粘性session处理方式：memcached做主从复制，写入session都往memcached服务上写，读取都从主memcached读取，tomcat本身不存储session
&nbsp;&nbsp;&nbsp;&nbsp;优点：可容错，session实时响应
&nbsp;&nbsp;&nbsp;&nbsp;实现方式：用开源的msm（memcached_session_manager）插件解决tomcat之间的session共享
4.session持久化到数据库：拿出一个数据库，专门用来存储session信息，保证session持久化
&nbsp;&nbsp;&nbsp;&nbsp;优点：服务器出现问题，session也不会丢失
&nbsp;&nbsp;&nbsp;&nbsp;缺点：如果网站访问量很大，把session存储到数据库中，会对数据库造成很大压力，还需要增加额外的开销维护数据库。
5.terracotta实现session复制：terracotta的基本原理是对于集群件共享的数据，当在一个节点发生变化的时候，terracotta只把变化的部分发送给terracotta服务器，然后由服务器把它转发给真正需要这个数据的节点。可以看成是第二个方案的优化
&nbsp;&nbsp;&nbsp;&nbsp;优点：对网络的压力就非常小，各个节点也不必浪费CPU时间和内存进行大量的序列化操作。把这种集群间数据共享的机制应用在session同步上，既避免了对数据库的依赖，又能达到负载均衡和灾难恢复的效果
