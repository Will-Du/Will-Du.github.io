---
title: 面试题-spring
date: 2019-11-07 22:16:53
tags: 面试题
---
1.spring五个事务隔离级别和7个事务传播行为
&nbsp;&nbsp;&nbsp;&nbsp;脏读：脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时另一个事务也访问这个数据，然后使用了这个数据。
&nbsp;&nbsp;&nbsp;&nbsp;不可重复读：在一个事务还没有结束时，另一个事务也访问了该同一数据，那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的数据可能是不一样的。这样就发生了在同一个事务内两次读到的数据是不一样的。
&nbsp;&nbsp;&nbsp;&nbsp;幻读：第一个事务对一个表中的数据进行修改，这种修改涉及到表中的全部数据行，同时，第二个事务也修改了表中的数据，这种修改是向表中新增一行数据。那么，就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好像发生幻觉一样。
&nbsp;&nbsp;&nbsp;&nbsp;可参考：https://www.cnblogs.com/ubuntu1/p/8999403.html
<!-- more -->
&nbsp;&nbsp;&nbsp;&nbsp;Spring在TransactionDefinition接口中定义了五个事务隔离级别：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.ISOLATION_DEFAULT:这是一个platfromTranscationManager默认的隔离级别，使用数据库默认的隔离级别。另外四个与JDBC的隔离级别相对应
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.ISOLATION_READ_UNCOMMITED:这是事务最低的隔离级别，他允许另外一个事务可以看到这个事务未提交的数据。这种隔离级别会产生脏读、不可重复读和幻读
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.ISOLATION_READ_COMMITED:保证一个事务修改的数据提交后才能被另外一个事务读取，另一个事务不能读取该事务未提交的数据。这种隔离级别可以避免脏读的出现，但是可能会出现不可重复读和幻读
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.ISOLATION_REPEATABLE_READ:这种事务隔离级别可以防止脏读、不可重复读。但是可能出现幻读。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.ISOLATION_SERIALIZABLE:这是花费最高代价但是最可靠的事务隔离等级。事务被处理为顺序执行，可以防止脏读、不可重复读、幻读
&nbsp;&nbsp;&nbsp;&nbsp;Spring在TranscationDefinition接口定义了7种事务传播行为
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.PROPAGATION_REQUIRED:如果存在一个事务，则支持当前事务。如果没有事务则开启一个新的事务
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.PROPAGATION_SUPPORTS:如果存在一个事务，支持当前事务。如果没有事务，则非事务的执行。但是对于事务同步的事务管理器，PROPAGATION_SUPPORTS与不使用事务有少许不同
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.PROPAGATION_MANDATORY:如果已经存在一个事务，支持当前事务。如果没有一个活动的事务，则抛出异常
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.PROPAGATION_REQUIRES_NEW:总是开启一个新的事务。如果一个事务已经存在，则将这个存在的事务挂起
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.PROPAGATION_NOT_SUPPORTED:总是非事务的执行，并挂起任何存在的事务
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.PROPAGATION_NEVER:总是非事务的执行，如果存在一个活动事务，则抛出异常
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.PROPAGATION_NESTED:如果一个活动的事务存在，则运行在一个嵌套的事务中。如果没有活动的事务，则按PROPAGATION_REQUIRED执行
2.事务嵌套
&nbsp;&nbsp;&nbsp;&nbsp;事务嵌套是子事务嵌套在父事务中执行，子事务是父事务的一部分，在进入子事务之前，父事务建立一个回滚点，叫save point，然后执行子事务，这个子事务也算是父事务的一部分，然后子事务执行结束，父事务继续执行。
&nbsp;&nbsp;&nbsp;&nbsp;如果子事务回滚：父事务会回滚到进入子事务前建立的save point，然后尝试其他的事务或者业务逻辑，父事务之前的操作不会受到影响，更不会自动回滚
&nbsp;&nbsp;&nbsp;&nbsp;如果父事务回滚：父事务回滚，子事务也会跟着回滚。因为父事务结束之前，子事务是不会提交的。
&nbsp;&nbsp;&nbsp;&nbsp;事务的提交：子事务是父事务的一部分，由父事务统一提交
3.SpringMVC的工作原理
&nbsp;&nbsp;&nbsp;&nbsp;1.用户发送请求至前端控制器DispatcherServlet
&nbsp;&nbsp;&nbsp;&nbsp;2.DispatcherServlet接收到请求调用HandlerMapping处理器映射器
&nbsp;&nbsp;&nbsp;&nbsp;3.处理器映射器找到具体的处理器(可以根据XML配置、注解进行查找)，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherSrvlet
&nbsp;&nbsp;&nbsp;&nbsp;4.DispatcherServlet调用HandlerAdapter处理器适配器
&nbsp;&nbsp;&nbsp;&nbsp;5.HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)
&nbsp;&nbsp;&nbsp;&nbsp;6.Controller执行完成返回ModelAndView
&nbsp;&nbsp;&nbsp;&nbsp;7.HandlerAdapter将Controller执行结果ModelAndView返回给DispatcherServlet
&nbsp;&nbsp;&nbsp;&nbsp;8.DispatcherServlet将ModelAndView传给ViewReslover视图解析器
&nbsp;&nbsp;&nbsp;&nbsp;9.ViewReslover解析后返回具体View
&nbsp;&nbsp;&nbsp;&nbsp;10.DispatcherServlet根据View进行渲染视图(即 将模型数据填充至视图中)
&nbsp;&nbsp;&nbsp;&nbsp;11.DispatcherServlet相应用户
4.Spring中IOC的注入方式
&nbsp;&nbsp;&nbsp;&nbsp;①.接口注入:接口注入模式因为具备侵略性，它要求组件必须与特定的接口相关联，因此不被看好，实际使用有限。
&nbsp;&nbsp;&nbsp;&nbsp;②.Setter注入:对于习惯了传统javabean开发的程序员，通过setter方法设定依赖关系更加直观。如果依赖关系较为复杂，那么构造子注入模式的构造函数也会相当庞大，而此时设值注入模式更加简洁。如果使用第三方类库，可能要求我们的组件提供一个默认的构造函数，此时构造子注入模式也不适用。
&nbsp;&nbsp;&nbsp;&nbsp;③.构造器注入:在构造期间完成一个完整的、合法的对象。所有依赖关系在构造函数中集中呈现。依赖关系在构造时由容器一次性设定，组件被创建之后一直处于相对'不变'的稳定状态。只有组件的创建者关心其内部依赖关系，对调用者而言，该依赖关系处于'黑盒'之中。
&nbsp;&nbsp;&nbsp;&nbsp;④.注解注入:@Resource先会按照名称到spring容器中查找，如果查找不到，就回退按照类型匹配，如果再次没有匹配到，就会抛出异常。如果在开发的时候，建议大家都是用@Rescourc(name="userDao"),此时只能按照名称匹配。
