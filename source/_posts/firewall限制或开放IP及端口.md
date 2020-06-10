---
title: firewall限制或开放IP及端口
date: 2020-06-09 10:40:48
tags: Linux
---
<b style="color: orangered">一.查看防火墙状态</b>
&nbsp;&nbsp;&nbsp;&nbsp;首先查看防火墙是否开启，如未开启，需要先开启防火墙并作开机自启。
```
systemctl status firewalld
```
&nbsp;&nbsp;&nbsp;&nbsp;开启防火墙并设置开机自启
```
systemctl start firewalld
systemctl enable firewalld
```
&nbsp;&nbsp;&nbsp;&nbsp;一般需要重启一下机器，不然后面做得设置可能不会生效<!-- more -->
<b style="color: orangered">二.开放或限制端口</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2.1.开放端口</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.1 如我们需要开启xshell连接时需要使用的22端口
```
firewall-cmd --zone=public --add-port=22/tcp --permanent
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其中--permanent的作用是使设置永久生效，不加的话机器重启之后失效。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.2 重新载入一下防火墙设置，使设置生效
```
firewall-cmd --reload
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.3 可通过如下命令查看是否生效
```
firewall-cmd --zone=public --query-port=22/tcp
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.4 如下命令可查看当前系统打开的所有端口
```
firewall-cmd --zone=public --list-ports
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2.2.限制端口</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;比如我们现在需要关掉刚刚打开的22端口
```
firewall-cmd --zone=public --remove-port=22/tcp --permanent
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;后续操作同2.1.2-2.1.4
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2.3.批量开放或限制端口</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;批量开放端口，如从100到500这之间的端口我们全部都要打开
```
firewall-cmd --zone=public --add-port=100-500/tcp --permanent
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;后续操作同2.1.2、2.1.4
<b style="color: orangered">三.开放或限制IP</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">3.1.限制IP地址访问</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.1.1 比如限制IP为192.168.0.200的地址禁止访问80端口即禁止访问机器
```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0.200" port protocol="tcp" port="80" reject"
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.1.2 重新载入一下防火墙，是设置生效
```
firewall-cmd --reload
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.1.3 查看已经设置的规则
```
firewall-cmd --zone=public --list-rich-rules
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2.解除IP地址限制</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.2.1 解除刚才被限制的192.168.0.200
```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0.200" port protocol="tcp" port="80" accept"
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;后续操作同3.1.2、3.1.3。如设置未生效，可尝试直接编辑规则文件，删掉原来的设置规则，重新载入一下防火墙即可
```
vi /etc/firewalld/zones/public.xml
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">3.限制IP地址段</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.3.1 如我们需要限制10.0.0.0-10.0.0.255这一整个段的IP，禁止他们访问
```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.0.0.0/24" port protocol="tcp" port="80" reject"
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其中10.0.0.0/24表示为从10.0.0.0这个IP开始，24代表子网掩码为255.255.255.0，共包含256个地址，即从0-255共256个IP，即正好限制了这一整段的ip地址，具体的设置规则可参考下表

|<font color='#0A0A0A'></font>|<font color='#0A0A0A'>IP总数</font>|<font color='#0A0A0A'>子网掩码</font>|<font color='#0A0A0A'>C段个数</font>|
|:-----|:-----|:-----|:-----|
| /30 | 4 | 255.255.255.252 | 1/64 |
| /29 | 8 | 255.255.255.248 | 1/32 |
| /28 | 16 | 255.255.255.240 | 1/16 |
| /27 | 32 | 255.255.255.224 | 1/8 |
| /26 | 64 | 255.255.255.192 | 1/4 |
| /24 | 256 | 255.255.255.0 | 1 |
| /23 | 512 | 255.255.254.0| 2 |
| /22 | 1024 | 255.255.252.0 | 4 |
| /21 | 2048| 255.255.248.0| 8 |
| /20 | 4096 | 255.255.240.0| 16 |
| /19 | 8192 | 255.255.224.0| 32 |
| /18 | 16384 | 255.255.192.0| 64 |
| /17 | 32768 | 255.255.128.0| 128 |
| /16| 65536 | 255.255.0.0 | 256 |
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;后续操作同3.1.2、3.1.3。同理，打开限制为:
```
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.0.0.0/24" port protocol="tcp" port="80" accept"
```