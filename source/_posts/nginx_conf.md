---
title: Nginx配置参数说明
date: 2019-12-28 15:03:25
tags: nginx
---
Nginx配置参数中文详细说明:
<!-- more -->
```JAVA
#定义Nginx运行的用户和用户组
user www www;

#Nginx进程数，建议设置为等于CPU总核心数 
worker_processes 8;

#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log /var/log/nginx/error.log info;

#进程文件
pid /var/run/nginx.pid;

#一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数(系统的值ulimit -n)与nginx进程数相除，但是nginx分配请求并不均匀，所以建议与ulimit -n的值保持一致。
worker_rlimit_nofile 65535;

#工作模式与连接数上限
events
{
    #参考事件模型，use [ kqueue | rtsug | epoll | /dev/poll | select | poll ]; epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型，如果跑在FreeBSD上面，就用kqueue模型。
    use epoll;
    #单个进程最大连接数(最大连接数=连接数*进程数)
    worker_connections 65535;
}

#设定http服务器
http
{
   include mime.types; #文件扩展名与文件类型映射表
   default_type application/octet-stream; #默认文件类型
   #charset utf-8; #默认编码
   server_names_hash_bucket_size 128; #服务器名字的hash表大小
   client_header_buffer_size 32k; #上传文件大小限制
   large_client_header_buffers 4 64k; #设定请求缓存
}
```