---
title: oracle如何查看当前有哪些用户连接到数据库
date: 2019-12-09 15:17:55
tags: sql
---
```sql
-- 查询用户会话
SELECT USERNAME,SERIAL#,SID,STATUS FROM V$SESSION;
SELECT 
	A.SID,B.SPID,A.SERIAL#,A.MACHINE,A.PREV_EXEC_START,A.STATUS,C.SQL_TEXT
FROM
	V$SESSION A,V$PROCESS B,V$SQL C
WHERE
	A.PADDR = B.ADDR
	AND A.PREV_SQL_ID = C.SQL_ID
ORDER BY A.PREV_EXEC_START;
```
<!-- more -->
```sql
-- 删除相关用户会话(SERIAL#,SID为上方查询结果)
ALTER SYSTEM KILL SESSION 'SERIAL#, SID';
-- 查询oracle连接数
SELECT COUNT(*) FROM V$SESSION;
-- 查询oracle最大连接数
SELECT VALUE FROM V$PARAMETER WHERE NAME = 'processes';
-- 查询oracle的并发连接数
SELECT CPUNT(*) FROM V$SESSION WHERE STATUS = 'ACTIVE';
-- 查看不同用户的连接数
SELECT USERNAME,COUNT(USERNAME) FROM V$SESSION WHERE USERNAME IS NOT NULL GROUP BY USERNAME;
-- 查看所有用户
SELECT * FROM ALL_USERS;
-- 查看用户或角色系统权限(直接赋值给用户或角色的系统权限)
SELECT * FROM DBA_SYS_PRIVS;
SELECT * FROM USER_SYS_PRIVS;
-- 查看角色(只能查看登录用户拥有的角色)所包含的权限
SELECT * FROM ROLE_SYS_PRIVS;
-- 查看用户对象权限
SELECT * FROM DBA_ROLE_PRIVS;
SELECT * FROM USER_ROLE_PRIVS;
-- 查看哪些用户有sysdba或sysoper系统权限(查询时需要相应权限)
SELECT * FROM V$PWFILE_USERS;
```
### oracle查看并修改最大连接数 ###
&nbsp;&nbsp;&nbsp;&nbsp;1.首先通过sqlplus登录数据库(cmd命令窗口中输入, sqlplus / as sysdba),如遇到提示输入口令，则输入口令即可。
&nbsp;&nbsp;&nbsp;&nbsp;2.查看当前数据库进程的连接数：select count(*) from v$processes;
&nbsp;&nbsp;&nbsp;&nbsp;3.查询数据库当前会话的连接数：select count(*) from v$session;
&nbsp;&nbsp;&nbsp;&nbsp;4.查看数据库设置的最大连接数和最大session数量：show parameter processes命令查看的是汇总的消息，也可以直接select value from v￥parameter where name = 'processes';语句查看最大进程连接数。
&nbsp;&nbsp;&nbsp;&nbsp;5.当数据库连接数需要调整时，可以用alter system set processes = 4000 scope = spfile;修改连接数。
&nbsp;&nbsp;&nbsp;&nbsp;6.修改processes和session值必须重启oracle服务器才能生效。shutdown immediate;关闭实例。 startup启动服务
&nbsp;&nbsp;&nbsp;&nbsp;7.重启后再次查看，是否生效。