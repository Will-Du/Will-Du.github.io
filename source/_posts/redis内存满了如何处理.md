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
config set maxmemory 100mb
// 查看设置的Redis能使用的最大内存大小
config get maxmemory
```
** 3.Redis的内存淘汰 **