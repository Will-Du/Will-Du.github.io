---
title: centos8中nmcli常用命令总结
date: 2020-06-10 16:03:13
tags: Linux
---
查看ip(类似于ifconfig、ip addr)
```
nmcli
```
<!-- more -->创建connection，配置静态ip(等同于配置ifcfg，其中BOOTPROTO=none，并ifup启动)
```
nmcli c add type ethernet con-name ethX ifname ethX ipv4.addr 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.method manual
```
创建connection，配置动态ip(等同于配置ifcfg，其中BOOTPROTO=dhcp，并ifup启动)
```
nmcli c add type ethernet con-name ethX ifname ethX ipv4.method auto
```
修改ip(非交互式)
```
nmcli c modify ethX ipv4.addr '192.168.1.200/24'
nmcli c up ethX
```
启用connection(相当于ifup)
```
nmcli c up ethX
```
停止connection(相当于ifdown)
```
nmcli c down
```
查看connection列表
```
nmcli c show
```
查兰connection详细信息
```
nmcli c show ethX
```
重载所有ifcfg或route到connection(不会立即生效)
```
nmcli c reload
```
立即生效connection，有3种方法
```
nmcli c up ethX
nmcli d reapply ethX
nmcli d connect ethX
```
查看device列表
```
nmcli d
```
查看所有device详细信息
```
nmcli d show
```
查看指定device的详细信息
```
nmcli d show ethX
```
激活网卡
```
nmcli d connect ethX
```