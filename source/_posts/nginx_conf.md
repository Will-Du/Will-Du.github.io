---
title: Nginx配置参数说明
date: 2019-12-28 15:03:25
tags: nginx
---
Nginx配置参数中文详细说明:
<!-- more -->
```JAVA
# 定义Nginx运行的用户和用户组
user www www;

# Nginx进程数，建议设置为等于CPU总核心数 
worker_processes 8;

# 全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log /var/log/nginx/error.log info;

# 进程文件
pid /var/run/nginx.pid;

# 一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数(系统的值ulimit -n)与nginx进程数相除，但是nginx分配请求并不均匀，所以建议与ulimit -n的值保持一致。
worker_rlimit_nofile 65535;

# 工作模式与连接数上限
events
{
    # 参考事件模型，use [ kqueue | rtsug | epoll | /dev/poll | select | poll ]; epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型，如果跑在FreeBSD上面，就用kqueue模型。
    use epoll;
    # 单个进程最大连接数(最大连接数=连接数*进程数)
    worker_connections 65535;
}

#设定http服务器
http
{
   include mime.types; # 文件扩展名与文件类型映射表
   default_type application/octet-stream; # 默认文件类型
   #charset utf-8; # 默认编码
   server_names_hash_bucket_size 128; # 服务器名字的hash表大小
   client_header_buffer_size 32k; # 上传文件大小限制
   large_client_header_buffers 4 64k; # 设定请求缓存中header大小
   client_max_body_size 8m; # 设定请求缓存中body大小

   # 开启目录列表访问，核实下载服务器，默认关闭
   autoindex on; # 显示目录
   autoindex_exact_size on; # 显示文件大小 默认为on，显示出文件的确切大小，单位是bytes 改为off后，显示出文件的大概大小，单位是KB或者MB或者GB
   autoindex_localtime on; # 显示文件时间 默认为off，显示的文件时间为GMT时间 改为on后，显示的文件时间为文件的服务器时间

   sendfile on; # 开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普遍应用设为on，如果用了下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘和网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成off。
}
```