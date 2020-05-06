---
title: Mybatis经典面试题
date: 2019-12-10 15:59:11
tags: 面试题
---
1.什么是mybatis？
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis是一个半ORM(对象关系映射)框架，它内部封装了JDBC，开发时只需要关注SQL语句本身，不需要花费精力去处理加载驱动、创建连接、创建statement等繁杂的过程。程序员直接编写原生态SQL，可以严格控制SQL执行效能，灵活度高。
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis可以使用XML或注解来配置映射原生信息，将POJO映射成数据库中的记录，避免了几乎所有的JDBC代码和手动设置参数以及获取结果集。
&nbsp;&nbsp;&nbsp;&nbsp;通过XML文件或注解的方式将要执行的各种statement配置起来，并通过java对象和statement中SQL的动态参数进行映射生成最终执行的SQL语句，最后由Mybatis框架执行SQL并将结果映射为java对象并返回。(从执行SQL到返回result的过程)
<!-- more -->
2.Mybatis的优点：
&nbsp;&nbsp;&nbsp;&nbsp;基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除SQL与程序代码的耦合，便于统一管理。
&nbsp;&nbsp;&nbsp;&nbsp;提供XML标签，支持编写动态SQL语句，并可重用。
&nbsp;&nbsp;&nbsp;&nbsp;与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接。
&nbsp;&nbsp;&nbsp;&nbsp;很好的与各种数据库兼容(因为Mybatis使用JDBC来连接数据库，所以只要JDBC支持的数据库Mybatis都支持)。
&nbsp;&nbsp;&nbsp;&nbsp;能够与Spring很好的集成。
&nbsp;&nbsp;&nbsp;&nbsp;提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护。
3.Mybatis的缺点：
&nbsp;&nbsp;&nbsp;&nbsp;SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句功底有一定的要求。
&nbsp;&nbsp;&nbsp;&nbsp;SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。
4.Mybatis框架适用场合：
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis专注于SQL本身，是一个足够灵活的DAO层解决方案。
&nbsp;&nbsp;&nbsp;&nbsp;对性能的要求很高，或者需求变化较多的项目，比如互联网项目,Mybatis将是不错的选择。
5.Mybatis与Hibernate有哪些不同？
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis和Hibernate不同，它不完全是一个ORM框架，因为Mybatis需要程序员自己编写SQL语句。
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis直接编写原生态SQL，可以严格控制SQL执行性能，灵活度高，非常适合对关系型数据模型要求不高的软件开发，因为这类软件需求变化频繁，一旦需求变化要求迅速输出成果。但是灵活的前提是Mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套SQL映射文件，工作量大。
&nbsp;&nbsp;&nbsp;&nbsp;Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用Hibernate开发可以节省很多代码，提高效率。
6.#{}和${}的区别是什么？
&nbsp;&nbsp;&nbsp;&nbsp;#{}是预编译处理，${}是字符串替换。
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis在处理#{}时，会将SQL中的#{}替换为?，调用PreparedStatement的set方法来赋值；
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis在处理${}时，就是把${}替换成变量的值。
&nbsp;&nbsp;&nbsp;&nbsp;使用#{}可以有效的防止SQL注入，提高系统安全性。
7.当实体类中的属性名和表中字段名不一样，怎么办？
&nbsp;&nbsp;&nbsp;&nbsp;第一种：通过查询的SQL语句中定义字段名的别名，让字段名的别名和实体类的属性名一致。
```XML
<select id="getOrder" parameterType="java.lang.String" resultType="com.mybatis.entity.Order">
	select 
		order_id as id, order_no as orderNo, price as price
	from
		orders
	where
		order_id = #{value}
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;第二种：通过<resultMap>来映射字段名和实体类属性名的一一对应的关系。
```XML
<resultMap id="orderResultMap" type="com.mybatis.entity.Order">
	<result property="id" column="order_id" />
	<result property="orderNo" column="order_no" />
	<result property="price" column="price" />
</resultMap>
<select id="getOrder" parameterType="java.lang.String" resultMap="orderResultMap">
	select
		order_id,order_no,price
	from
		orders
	where
		order_id = #{value}
</select>
```
8.模糊查询like语句该怎么写？
&nbsp;&nbsp;&nbsp;&nbsp;第一种：在java代码中添加sql通配符。
```XML
String name = "%" + studentDto.getName() + "%";
List<Student> list = studentDao.getStudentsByName(name);
<select id="getStudentByName" parameterType="java.lang.String" resultMap="studentMap">
	select
		id,name,age
	from
		student
	where
		name like #{value}
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;第二种：在SQL语句拼接通配符，会引起SQL注入
```XML
List<Student> list = studentDao.getStudentByName(studentDto.getName());
<select id="getStudentByName> parameterType="java.lang.String" resultMap="studentMap">
	select
		id,name,age
	from
		student
	where
		name like '%'#{value}'%'
</select>
```
9.通常一个XML映射文件，都会写一个Dao接口预制对应，请问，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？
&nbsp;&nbsp;&nbsp;&nbsp;Dao接口即Mapper接口。接口的全限名，就是映射文件中的namespace的值；接口的方法名，就是映射文件中Mapper的Statement的id值;接口方法内的参数，就是传递给SQL的参数。
&nbsp;&nbsp;&nbsp;&nbsp;Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MapperStatement。在Mybatis中，每一个&lt;select>、&lt;insert>、&lt;update>、&lt;delete>标签，都会被解析为一个MapperStatement对象。
&nbsp;&nbsp;&nbsp;&nbsp;举例：com.mybatis.mappers.StudentDao.findStudentById,可以唯一找到namespace为com.mybatis.mapper.StudentDao下面id为findStudentById的MapperStatement。
&nbsp;&nbsp;&nbsp;&nbsp;Mapper接口里的方法，是不能重载的，因为是使用'全限名+方法名'的保存和寻找策略。Mapper接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Mapper接口生成代理对象proxy，代理对象会拦截接口方法，转而执行MapperStatement所代表的SQL，然后将SQL执行结果返回。
10.Mybatis是如何进行分页的？分页插件的原理是什么？
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在SQL内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。
&nbsp;&nbsp;&nbsp;&nbsp;分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的SQL，然后重写SQL，根据dialect方言，添加对应的物理分页语句和物理分页参数。
11.Mybatis是如何将SQL执行结果封装为目标对象并返回的？都有哪些映射形式？
&nbsp;&nbsp;&nbsp;&nbsp;第一种是使用&lt;resultMap>标签，逐一定义数据库列名和对象属性名之间的映射关系。
&nbsp;&nbsp;&nbsp;&nbsp;第二种是使用SQL列的别名功能，将列的别名书写为对象属性名。
&nbsp;&nbsp;&nbsp;&nbsp;有了列名和属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。
12.如何获取自动生成的(主)键值？
&nbsp;&nbsp;&nbsp;&nbsp;insert方法总是返回一个int值，这个值代表的是插入的行数。
&nbsp;&nbsp;&nbsp;&nbsp;如果采用自增长策略，自动生成的键值在insert方法执行后可以被设置到传入的参数对象中。示例：
```XML
<insert id="insertStudent" usegeneratedkey="true" keyproperty="id">
	insert into student(name,age) values (#{name},#{age})
</insert>
```
13.在mapper.xml中如何传递多个参数？
&nbsp;&nbsp;&nbsp;&nbsp;第一种：
```XML
public List<Student> findStudents(String name, int age);
// #{0}代表接收的是dao层中第一个参数
// #{1}代表接收的是dao层中第二个参数
<select id="findStudents" resultMap="studentMap">
	select id,name,age from students where name=#{0} and age=#{1}
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;第二种：使用@param注解：
```XML
public List<Student> findStudents(@param("name") String name, @param("age") int age);
//推荐封装为一个map，作为单个参数传递给mapper
<select id="findStudents" resultMap="studentMap">
	select id,name,age from students where name=#{name} and age=#{age}
</select>
```
&nbsp;&nbsp;&nbsp;&nbsp;第三种：多个参数封装为map,XML文件写法同第二种
14.Mybatis动态SQL有什么用？执行原理？有哪些动态SQL？
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis动态SQL可以在XML映射文件内，以标签的形式编写动态SQL，执行原理是根据表达式的值完成逻辑判断并动态拼接SQL的功能。
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis提供了9种动态的SQL标签：trim、where、set、foreach、if、choose、when、otherwise、bind。
15.XML映射文件中，除了常见的select、insert、update、delete标签之外，还有哪些标签？
&nbsp;&nbsp;&nbsp;&nbsp;&lt;resultMap>、&lt;parameterMap>、&lt;sql>、&lt;include>、&lt;selectKey>,加上动态SQL的9个标签，其中&lt;sql>为sql片段标签，通过&lt;include>标签引入sql片段，&lt;selectKey>为不支持自增的主键生成策略标签。
16.Mybatis的XML映射文件中，不同的XML映射文件，id是否可以重复？
&nbsp;&nbsp;&nbsp;&nbsp;不同的XML映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复。
&nbsp;&nbsp;&nbsp;&nbsp;原因就是namespace+id是作为Map<Stirng, MapperStatement>的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。
17.为什么说Mybatis是半自动ORM映射工具？它与全自动的区别在哪里？
&nbsp;&nbsp;&nbsp;&nbsp;Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。而Mybatis在查询关联对象或关联集合对象时，需要手动编写SQL来完成，所以，称之为半自动ORM映射工具。
18.一对一，一对多的关联查询
```XML
<mapper namespace="com.mybatis.dao.userDao">
	<resultMap id="classesResultMap" type="com.mybatis.entity.Classes">
		<id property="id" column="c_id" />
		<result property="name" column="c_name" />
		<association property="teacher" javaType="com.mybatis.entity.Teacher">
			<id property="id" column="t_id" />
			<result property="name" column="t_name" />
		</association>
	</resultMap>
	<resultMap id="classesResultMap2" type="com.mybatis.entity.Classes">
		<id property="id" column="c_id" />
		<result property="name" column="c_name" />
		<association property="teacher" javaType="com.mybatis.entity.Teacher">
			<id property="id" column="t_id" />
			<result property="name" column="t_name" />
		</association>
		<collection property="student" ofType="com.mybatis.entity.Student">
			<id property="id" column="s_id" />
			<result property="name" column="s_name" />
		</collection>
	</resultMap>
	<!-- association 一对一关联查询 -->
	<select id="getClass" parameterType="int" resultMap="classesResultMap">
		select * from class c, teacher t where c.teacher_id = t.id and c.c_id =#{id}
	</select>
	<!-- collection 一对多关联查询 -->
	<select id="getClass2" parameterType="int" resultMap="classesResultMap2">
		select * from class s, teacher t, student s where c.teacher_id = t.id and c.c_id = s.class_id and c.c_id = #{id}
	</select>
</mapper>
```
19.Mybatis实现一对一有几种方式？具体怎么操作的？
&nbsp;&nbsp;&nbsp;&nbsp;有联合查询和嵌套查询。
&nbsp;&nbsp;&nbsp;&nbsp;联合查询是几个表联合查询，只查询一次，通过在resultMap里面association节点配置一对一的类就可以完成。
&nbsp;&nbsp;&nbsp;&nbsp;嵌套查询是先查一个表，根据这个表里面的结果的外键id，再去另一个表里面查询数据，也是通过association配置，但另一个表的查询通过select属性配置。
20.Mybatis实现一对多有几种方式？怎么操作的？
&nbsp;&nbsp;&nbsp;&nbsp;有联合查询和嵌套查询。
&nbsp;&nbsp;&nbsp;&nbsp;联合查询是几个表联合查询，只查询一次，通过在resultMap里面collection节点配置一对多的类就可以完成。
&nbsp;&nbsp;&nbsp;&nbsp;嵌套查询是先查一个表，根据这个表里面的结果的外键id，再去另一个表里面查询数据，也是通过配置collection，但另一个表的查询通过select属性配置。
21.Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLodingEnabled=true|false。
&nbsp;&nbsp;&nbsp;&nbsp;它的原理是使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的SQL，把B查询出来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。
&nbsp;&nbsp;&nbsp;&nbsp;当然了，不光是Mybatis，几乎所有的包含Hibernate，支持延迟加载的原理都是一样的。
22.Mybatis的一级、二级缓存：
&nbsp;&nbsp;&nbsp;&nbsp;一级缓存：基于PrepetualCache的HashMap本地缓存，其存储作用域为Session，当Session flush或close之后，该Session中的所有Cache就将清空，默认打开一级缓存。
&nbsp;&nbsp;&nbsp;&nbsp;二级缓存与一级缓存其机制相同，默认也是采用PrepetualCache，HashMap存储，不同在于其储存作用域为Mapper(namespace)，并且可自定义存储源，如Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态)，可在它的映射文件中配置<cache/>。
&nbsp;&nbsp;&nbsp;&nbsp;对于缓存数据更新机制，当某一个作用域(一级缓存Session/二级缓存Namespace)的进行了C/U/D操作后，默认该作用域下所以select中缓存将被clear。
23.什么是Mybatis的接口绑定？有哪些实现方式？
&nbsp;&nbsp;&nbsp;&nbsp;接口绑定就是在Mybatis中任意定义接口，然后把接口里面的方法和SQL语句绑定，我们直接调用接口方法就可以，这样比起原来的SqlSession提供的方法，我们可以更加灵活的选择和设置。
&nbsp;&nbsp;&nbsp;&nbsp;接口绑定有两种实现方式，一种是通过注解绑定，就是在接口的方法上面加上@Select、@Update等注解，里面包含SQL语句来绑定；另外一种就是通过XML里面写SQL来绑定，在这种情况下，要指定XML映射文件里面的namespace必须为接口全路径名。当SQL语句比较简单时候，用注解绑定，当SQL语句比较复杂时候，用XML绑定，一般用XML绑定的比较多。
24.使用Mybatis的mapper接口调用时有哪些要求？
&nbsp;&nbsp;&nbsp;&nbsp;Mapper接口方法名和mapper.xml中定义的每个SQL的id相同。
&nbsp;&nbsp;&nbsp;&nbsp;Mapper接口方法的输入参数类型和mapper.xml中定义的每一个SQL的parameterType的类型相同。
&nbsp;&nbsp;&nbsp;&nbsp;Mapper接口方法的输出参数类型和mapper.xml中定义的每一个SQL的resultType的类型相同。
&nbsp;&nbsp;&nbsp;&nbsp;Mapper.xml文件中namespace即是mapper接口的类路径。
25.Mapper编写有哪几种方式？
&nbsp;&nbsp;&nbsp;&nbsp;第一种：接口实现类继承SqlSessionDaoSupport：使用此方法需要编写mapper接口、mapper接口实现类、mapper.xml文件。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在sqlMapConfig.xml配置mapper.xml的位置
```XML
<mappers>
	<mapper resource="mapper.xml文件的地址" />
	<mapper resource="mapper2.xml文件的地址" />
</mappers>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;定义mapper接口：实现类集成sqlSessionDaoSupport，mapper方法中可以this.getSqlSession()进行数据增删改查。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spring配置
```XML
<bean id="XX" class="mapper接口的实现">
	<property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
```
&nbsp;&nbsp;&nbsp;&nbsp;第二种：使用org.mybatis.spring.mapper.MapperFactoryBean:在sqlMapConfig.xml中配置mapper.xml的位置，如果mapper.xml和mapper接口的名称相同且在同一目录下，这里可以不用配置。
```XML
<mappers>
	<mapper resource="mapper.xml文件的地址" />
</mappsers>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;定义mapper接口：mapper.xml中namespace为mapper接口的地址，mapper接口中的方法名和mapper.xml中定义的statement的id保持一致
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spring中定义：
```XML
<bean id="xx" class="org.mybatis.spring.mapper.MapperFactoryBean" >
	<property name="mapperInterface" value="mapper接口地址" />
	<property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>
```
&nbsp;&nbsp;&nbsp;&nbsp;第三种：使用mapper扫描器：mapper.xml文件编写：mapper.xml中的namespace为mapper接口的地址；mapper接口中的方法名和mapper.xml中定义的statement的id保持一致；如果将mapper.xml和mapper接口的名称保持一致则不用在sqlMapConfig.xml中进行配置。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;定义mapper接口：注意mapper.xml的文件名和mapper的接口名称保持一致，且放在同一目录
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;配置mapper扫描器：
```XML
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="mapper接口包地址" ></property>
	<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
</bean>
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用扫描器后从spring容器中获取mapper的实现对象。
26.简述Mybatis的插件运行原理，以及如何编写一个插件
&nbsp;&nbsp;&nbsp;&nbsp;Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4中接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4中接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。
&nbsp;&nbsp;&nbsp;&nbsp;编写插件：实现Mybatis的Interceptor接口并复写intercept()方法，然后再给插件写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。