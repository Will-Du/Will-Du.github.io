---
title: 如何干掉SQL注入
date: 2020-01-28 18:32:43
tags: Java
---
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">简介</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;文章主要内容包括：
- java持久层技术/框架简单介绍
- 不同场景/框架下易导致SQL注入的写法
- 如何避免和修复SQL注入
<!-- more -->
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">JDBC</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">介绍</b>
- 全称Java Database Connectivity
- 是java访问数据库的API，不依赖于特定的数据库(database-independent)
- 所有java持久层技术都是基于JDBC
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">说明</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;直接使用JDBC的场景，如果代码中存在拼接SQL语句，那么很可能会产生注入，如：
```java
// concat sql
String sql = "SELECT * FROM USERS WHERE NAME = '" + name + "'";
Statement stmt = connection.createStatement();
ResultSet rs = stmt.executeQuery(sql);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;安全的写法是使用<b style="color: #00FFFF">参数化查询(parameterized queries)</b>,即SQL语句中使用参数绑定(? 占位符)和PreparedStatement,如：
```java
// use ? to bind variables
String sql = "SELECT * FROM USERS WHERE NAME = ? ";
PreparedStatement ps = connection.preparedStatement(sql);
// 参数index从1开始
ps.setString(1, name);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;还有一些情况，比如order by、column name,不能使用参数绑定，此时需要手工过滤，如通常order by 的字段名是有限的，因此可以使用白名单的方式来限制参数值。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里需要注意的是，使用了PreparedStatement<b style="color: #00FFFF">并不意味着不会产生注入，</b>如果在使用PreparedStatement之前，存在拼接SQL语句，那么仍然会导致注入，如：
```java
// 拼接SQL
String sql = "SELECT * FROM USERS WHERE NAME = '" + name + "'";
PreparedStatement ps = connection.preparedStatement(sql);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;看到这里，大家肯定会好奇PreparedStatement是如何防止SQL注入的，来了解一下。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;正常情况下，用户的输入是作为参数值的，而在SQL注入中，用户的输入是作为SQL指令的一部分，会被数据库进行编译/解释执行。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当使用了PreparedStatement，带占位符(?)的SQL语句只会被编译一次，之后执行只是将占位符替换为用户输入，并不会再次编译/解释，因此从根本上防止了SQL注入问题。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">MyBatis</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">介绍</b>
- 首个class persistence framework
- 介于JDBC(raw SQL)和Hibernate(ORM)
- 简化绝大部分JDBC代码、手工设置参数和获取结果
- 灵活、使用者能够完全控制SQL、支持高级映射
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">说明</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在Mybatis中，使用XML文件或Annotation来进行配置和映射，将interfaces和java POJOs(Plain Old Java Objects)映射到database records。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">XML例子</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Mapper Interface
```java
@Mapper
public interface UserMapper {
    User getById(int id);
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;XML配置文件
```XML
<select id="getById" resultType="org.example.User">
    SELECT * FROM USERS WHERE ID = #{id}
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Annotation例子
```java
@Mapper
public interface UserMapper {
    @Select("SELECT * FROM USERS WHERE ID = ?")
    User getById(@Param("id") int id);
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;可以看到，使用者需要自己编写SQL语句，因此当使用不当时，会导致注入问题。与使用JDBC不同的是，MyBatis使用#{}和${}来进行参数值替换。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用#{}语法时，MyBatis会自动生成PreparedStatement，使用参数绑定(?)的方式来设置值，上述两个例子等价的JDBC查询代码如下：
```java
String sql = "SELECT * FROM USERS WHERE ID = ?";
PreparedStatement ps = connection.preparedStatement(sql);
ps.setInt(1, id);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因此#{}可以有效防止SQL注入。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;而使用${}语法时，MyBatis会直接注入原始字符串，即相当于拼接字符串，因此会导致SQL注入，如
```XML
<select id="getByName" resultType="org.example.User">
    SELECT * FROM USERS WHERE NAME = '${name}' limit 1
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;name值为' or '1'='1，实际执行的语句为
```
SELECT * FROM USERS WHERE NAME = '' or '1'='1' limit 1
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因此建议尽量使用#{}，但有些时候，如order by 语句，使用#{}会导致出错，如
```
ORDER BY #{sortBy}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sortBy参数值为name，，替换后会成为
```
ORDER BY 'name'
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;即以字符串“name”来排序，而非按照name字段排序，这种情况下就需要使用${}
```
ORDER BY ${sortBy}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用了${}，使用者需要自行过滤输入，方法有：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;代码层使用白名单的方式，限制sortBy允许的值，如只能为name，email字段，异常情况则设置为默认值name
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在XML配置文件中，使用if标签进行判断
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Mapper接口方法
```java
List<User> getUserListsortBy(@Param("sortBy") String sortBy);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;XML配置文件
```XML
<select id="getUserListsortBy" resultType="org.example.User">
    SELECT * FROM USERS
    <if test="sortBy == 'name' or sortBy == 'email'">
        order by ${sortBy}
    </if>
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为MyBatis不支持else，需要默认值的情况，可以使用choose(when,otherwise)
```XML
<select id="getUserListsortBy" resultType="org.example.User">
    SELECT * FROM USERS
    <choose>
        <when test="sortBy == 'name' or sortBy == 'email'">
            order by ${sortBy}
        </when>
        <otherwise>
            order by name
        </otherwise>
    </choose>
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;除了order by之外，还有一些可能会使用到${}情况，可以使用其他方法避免，如：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">like语句</b>
- 如需要使用通配符(wildcard characters % 和 _)可以在代码层，在参数值两边加上%，然后再使用#{}
- 使用bind标签来构造新参数，然后再使用#{}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Mapper接口方法
```java
List<User> getUserListLike(@Param("name") String name);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;XML配置文件
```XML
<select id="getUserListLike" resultType="org.example.User">
    <bind name="pattern" value="'%' + value + "'%'" />
		SELECT * FROM USERS WHERE NAME LIKE #{pattern}
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<bind>语句内的value为OGNL expression.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;bind部分使用SQL concat()函数
```XML
<select id="getUserListLikeConcat" resultType="org.example.User">
    SELECT * FROM USERS WHERE NAME LIKE CONCAT('%',#{name},'%')
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;除了注入问题之外，这里还需要对用户的输入进行过滤，不允许有通配符，否则在表中数据量较多的时候，假设用户输入为%%，会进行全部模糊查询，严重情况下可导致DOS。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">IN条件</b>
- 使用<foreach>和#{}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Mapper接口方法
```java
List<User> getUserListIn(@Param("nameList") List<STring> nameList);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;XML配置文件
```XML
<select id="getUserListIn" resultType="org.example.User">
    SELECT * FROM USERS WHERE NAME IN
    <foreach item="name" collection="nameList" open="(" separator="," close=")">
        #{name}
    </foreach>
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">limit语句</b>
- 直接使用#{}即可
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Mapper接口方法
```java
List<User> getUserListLimit(@Param("offset") int offset, @Param("limit") int limit);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;XML配置文件
```XML
<select id="getUserListLimit" resultType="org.example.User">
    SELECT * FROM USERS LIMIT #{offset}, #{limit}
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">JPA & Hibernate</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">介绍</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">JPA:</b>
- 全称Java Persistence API
- ORM(object-relational mapping)持久层API，需要有具体的实现
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">Hibernate:</b>
- JPA PRM实现
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">说明</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里有一种错误的认识，使用了ORM框架，就不会有SQL注入。而实际上，在Hibernate中，支持HQL(Hibernate Query Language)和native SQL查询，前者存在HQL注入，后者和之前JDBC存在相同的注入问题，来具体看一下。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">HQL</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;HQL查询例子
```java
Query<User> query = session.createQuery("from User where name = '" + name + "'", User.class);
User user = query.getSingleResult();
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里的User为类名，和原生SQL类似，拼接会导致注入。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;正确用法
- 位置参数(Positional parameter)
```java
Query<User> query = session.createQuery("from User where name = ?", User.class);
query.setParameter(0, name);
```
- 命名参数(named parameter)
```java
Query<User> query = session.createQuery("form User where name = :name", User.class);
query.setParameter("name", name);
```
- 命名参数list(named parameter list)
```java
Query<User> query = session.createQuery("from User where name in (:nameList)", User.class);
query.setParameterList("nameList", Arrays.asList("lisi","zhangsan"));
```
- 类实例(JavaBean)
```java
User user = new User();
user.setName("zhangsan");
Query<User> query = session.createQuery("from User where name = :name", User.calss);
// User类需要有getName()方法
query.setProperties(user);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">Native SQL</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;存在SQL注入
```java
String sql = "select * from user where name = '" + name + "'";
Query query = session.createNativeQuery(sql);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用参数绑定来设置参数值
```java
String sql = "select * from user where name = :name";
Query query = session.createNativeQuery(sql);
query.setParameter("name", name);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">JPA</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;JPA中使用JPQL(Java Persistence Query Language),同时也支持native SQL，因此和Hibernate存在类似的问题。