---
title: 面试题-oracle
date: 2019-11-07 19:53:05
tags: 面试题
---
1.delete与truncate区别
&nbsp;&nbsp;&nbsp;&nbsp;truncate是DDL语句(create、drop、alter、truncate)，delete是DML语句(insert、uodate、delete、select)；DCL语句(grant、revoke)
&nbsp;&nbsp;&nbsp;&nbsp;truncate的速度快于delete
&nbsp;&nbsp;&nbsp;&nbsp;delete数据可以进行rollback进行数据回滚，truncate是永久删除不能回滚
&nbsp;&nbsp;&nbsp;&nbsp;truncate不会触发表上的delete触发器，而delete会正常触发
&nbsp;&nbsp;&nbsp;&nbsp;truncate语句不能带where条件意味着只能全部数据删除，而delete可带where条件进行删除
&nbsp;&nbsp;&nbsp;&nbsp;truncate操作会重置表的高水位线，而delete不会
<!-- more -->
2.冷备份和热备份的不同点及优缺点
&nbsp;&nbsp;&nbsp;&nbsp;热备份针对归档模式的数据库，在数据库仍旧处于运行状态时进行备份。
&nbsp;&nbsp;&nbsp;&nbsp;冷备份是指在数据库关闭后，进行备份，适用于所有模式的数据库。
&nbsp;&nbsp;&nbsp;&nbsp;热备份的优点在于当备份时，数据库仍旧可以被使用并且可以将数据库恢复到任一时间点。而冷备份的优点在于备份和恢复相当简单，并且由于冷备份的数据库可以工作在非归档模式下，数据库性能会比归档模式稍好。
3.分页语句
&nbsp;&nbsp;&nbsp;&nbsp;oracle：select * from (select rownum rn, name from employee where rownum <= 20) temp where temp.rn > 10;
&nbsp;&nbsp;&nbsp;&nbsp;mysql: select * from employee limit 10,10;--第一个10是起始数，第二个是是每页条数
4.sql优化
&nbsp;&nbsp;&nbsp;&nbsp;1.对查询进行优化，应尽量避免全表扫描，首先应考虑在where及order by 涉及的列上建立索引
&nbsp;&nbsp;&nbsp;&nbsp;2.应尽量避免在where子句中对字段进行null判断,可以设默认值为0，判断是否为0
&nbsp;&nbsp;&nbsp;&nbsp;3.应尽量避免在where子句中使用!=或<>操作符
&nbsp;&nbsp;&nbsp;&nbsp;4.应尽量避免在where子句中使用or来连接条件，可以使用union all
&nbsp;&nbsp;&nbsp;&nbsp;5.in和not in也要慎用，能用between就用，或可以使用exist和not exist代替
&nbsp;&nbsp;&nbsp;&nbsp;6.模糊查询时，前模糊也会导致索引失效
&nbsp;&nbsp;&nbsp;&nbsp;7.应尽量避免在where子句中对字段进行表达式或函数操作
&nbsp;&nbsp;&nbsp;&nbsp;8.在使用索引字段作为查询条件时，如果该索引是复合索引，那么必须使用到该索引中的第一个字段作为条件时才能保证系统引用该索引，否则索引将不会使用，并且应尽可能的让字段的顺序与索引顺序相一致
&nbsp;&nbsp;&nbsp;&nbsp;9.不要写一些没有意义的查询，如where 1=1
&nbsp;&nbsp;&nbsp;&nbsp;10.并不是所有索引对查询都有效，SQL是根据表中的数据来进行查询优化的，当索引列有大量数据重复时，SQL查询可能就不会去利用索引，如sex字段
&nbsp;&nbsp;&nbsp;&nbsp;11.索引并不是越多越好，索引固然可以提高相应的select效率，但同时也降低了update及insert的效率。一张表的索引数最好不要超过6个
&nbsp;&nbsp;&nbsp;&nbsp;12.尽量使用数字型字段
&nbsp;&nbsp;&nbsp;&nbsp;13.尽可能的使用varchar代替char，因为首先变长字段存储空间小，可以节省存储空间，其次相对于查询，在一个相对小的字段内搜索效率显然高点
&nbsp;&nbsp;&nbsp;&nbsp;14.用具体字段代替*
&nbsp;&nbsp;&nbsp;&nbsp;15.避免频繁创建和删除临时表，以减少系统表资源的消耗
&nbsp;&nbsp;&nbsp;&nbsp;16.临时表并不是不可用，适当地使用他们可以使某些例程更有效，如：当需要重复引用大型表或常用表中的某个数据集时，但是对于一次性事件，最好使用导出表
&nbsp;&nbsp;&nbsp;&nbsp;17.在新建临时表时，如果一次性插入数据量很大，那么可以使用select into 代替create table，避免造成大量log，以提高速度；如果数据量不大，为了缓和系统表的资源，应先create table，再insert
&nbsp;&nbsp;&nbsp;&nbsp;18.如果使用到了临时表，在存储过程的最后务必将所有的临时表显式删除，先truncate table，再drop table，这样可以避免系统表较长时间锁定
&nbsp;&nbsp;&nbsp;&nbsp;19.尽量避免使用游标，因为游标的效率较差，如果游标操作的数据超过1W行，那么应该考虑改写
&nbsp;&nbsp;&nbsp;&nbsp;20.使用基于游标的方法或临时表的方法之前，应先寻找基于集的解决方案来解决问题，基于集的方法通常更有效
&nbsp;&nbsp;&nbsp;&nbsp;21.与临时表一样，游标并不是不可用。对于小型数据集使用FAST_FORWARD游标通常优于其他逐行处理方法，尤其是在必须引用几个表才能获得所需的数据时
&nbsp;&nbsp;&nbsp;&nbsp;22.尽量避免使用大事务操作，提高系统并发能力
&nbsp;&nbsp;&nbsp;&nbsp;23.尽量避免向客户端返回大数据，若数据量过大，应当考虑相应需求是否合理