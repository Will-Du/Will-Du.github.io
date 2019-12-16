---
title: git生成ssh key
date: 2019-12-10 10:20:18
tags:
---
&nbsp;&nbsp;&nbsp;&nbsp;1.首先在git设置身份的名称和邮箱
```git
git config --global user.name "Will"
git config --global user.email "XXX@XX.com"
```
<!-- more -->
&nbsp;&nbsp;&nbsp;&nbsp;2.删除.ssh文件夹(一般在C盘的用户里)下的known_hosts(手动删除即可)
&nbsp;&nbsp;&nbsp;&nbsp;3.git输入命令
```git
ssh-keygen -t rsa -C "XXX@XX.com"
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;接着出现：
```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;请直接按下回车，然后系统会自动在.ssh文件夹生成两个文件，id_rsa和id_rsa.pub,打开id_rsa.pub,将全部内容复制。
&nbsp;&nbsp;&nbsp;&nbsp;4.登录github，进入设置->ssh设置，新增SSH KEY，将复制的key粘贴上后保存。
&nbsp;&nbsp;&nbsp;&nbsp;5.在git中输入命令：
```git
ssh -T git@github.com
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;然后遇到有yes的地方输入yes，其余一路回车，最后会提示成功。