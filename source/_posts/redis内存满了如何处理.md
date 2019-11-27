---
title: redis内存满了如何处理
date: 2019-11-27 15:23:20
tags: 面试题
---
&nbsp;&nbsp;&nbsp;&nbsp;Redis是基于内存的key-value数据库，因为系统的内存大小有限，所以我们在使用Redis的时候可以配置Redis能使用的最大的内存大小。
<!-- more -->
** 1.通过修改配置文件 **
&nbsp;&nbsp;&nbsp;&nbsp;通过Redis安装目录下面的redis.conf配置文件中添加以下配置设置内存大小(Redis的配置文件不一定使用的是安装目录下面的redis.conf文件，启动redis服务的时候是可以传一个参数指定redis的配置文件)
```
// 设置Redis最大占用内存大小为100M
maxmemory 100mb
```
** 2.通过命令修改 **
&nbsp;&nbsp;&nbsp;&nbsp;Redis支持运行时通过命令动态修改内存大小(如果不设置最大内存大小或者设置最大内存大小为0，在64位操作系统系下不限制内存大小，在32位操作系统下最多使用3GB内存)
```
// 设置Redis最大占用内存大小为100M
127.0.0.1:6379>config set maxmemory 100mb
// 查看设置的Redis能使用的最大内存大小
127.0.0.1:6379>config get maxmemory
```
** 3.Redis的内存淘汰 **
&nbsp;&nbsp;&nbsp;&nbsp;既然可以设置Redis最大占用内存大小，那么配置的内存就有用完的时候。那在内存用完的时候，还继续往Redis里面添加数据不就没有内存可用吗？实际上Redis定义了几种策略来处理这种情况：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>noeviction(默认策略)：</b>对于写请求不再提供服务，直接返回错误(DEL请求和部分特殊请求除外)。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>allkeys-lru:</b>从所有key中使用LRU算法进行淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>volatile-lru:</b>从设置过期时间的key中使用LRU算法进行淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>allkeys-random:</b>从所有key中随机淘汰数据。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>volatile-random:</b>从设置了过期时间的key中随机淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>volatile-ttl:</b>在设置了过期时间的key中，根据key的过期时间进行淘汰，越早过期的越优先被淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;当使用volatile-lru、volatile-random、volatile-ttl这三个策略时，如果没有key可以被淘汰，则和noveiction一样返回错误。
&nbsp;&nbsp;&nbsp;&nbsp;<b>如何获取及设置内存淘汰策略：</b>
```
// 获取当前内存淘汰策略
127.0.0.1:6379>config get maxmemory-policy
// 通过配置文件设置淘汰策略(修改redis.conf文件)
maxmemory-policy allkeys-lru
// 通过命令修改淘汰策略
127.0.0.1:6379>config set maxmemory-policy allkeys-lru
```
