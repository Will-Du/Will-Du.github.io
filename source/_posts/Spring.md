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
&nbsp;&nbsp;&nbsp;&nbsp;11.DispatcherServlet响应用户
4.Spring中IOC的注入方式
&nbsp;&nbsp;&nbsp;&nbsp;①.接口注入:接口注入模式因为具备侵略性，它要求组件必须与特定的接口相关联，因此不被看好，实际使用有限。
&nbsp;&nbsp;&nbsp;&nbsp;②.Setter注入:对于习惯了传统javabean开发的程序员，通过setter方法设定依赖关系更加直观。如果依赖关系较为复杂，那么构造子注入模式的构造函数也会相当庞大，而此时设值注入模式更加简洁。如果使用第三方类库，可能要求我们的组件提供一个默认的构造函数，此时构造子注入模式也不适用。
&nbsp;&nbsp;&nbsp;&nbsp;③.构造器注入:在构造期间完成一个完整的、合法的对象。所有依赖关系在构造函数中集中呈现。依赖关系在构造时由容器一次性设定，组件被创建之后一直处于相对'不变'的稳定状态。只有组件的创建者关心其内部依赖关系，对调用者而言，该依赖关系处于'黑盒'之中。
&nbsp;&nbsp;&nbsp;&nbsp;④.注解注入:@Resource先会按照名称到spring容器中查找，如果查找不到，就回退按照类型匹配，如果再次没有匹配到，就会抛出异常。如果在开发的时候，建议大家都是用@Rescourc(name="userDao"),此时只能按照名称匹配。
5.拦截器与过滤器的区别(https://www.jianshu.com/p/cf088baa9b04?utm_campaign=hugo&utm_medium=reader_share&utm_content=note)
&nbsp;&nbsp;&nbsp;&nbsp;过滤器:是在java web中将传入的request、response提前过滤掉一些信息，或者提前设置一些参数，然后再传入servlet或struts2的action进行业务逻辑处理。比如过滤掉非法url(不是login.do的地址请求，如果用户没有登陆就都过滤掉)，或者在传入servlet或Struts2的action前统一设置字符集，或者去除掉一些非法字符。
&nbsp;&nbsp;&nbsp;&nbsp;拦截器:是面向切面编程的。就是在service或者一个方法前调用一个方法，或者在一个方法后调用一个方法。比如动态代理就是拦截器的简单实现，在调用方法前打印出字符串(或者做其他的业务逻辑)，也可以在调用方法后打印出字符串，甚至在抛出异常的时候做业务逻辑的操作。
&nbsp;&nbsp;&nbsp;&nbsp;主要区别:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;①.拦截器是基于java的反射机制的，而过滤器是基于函数回调。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;②.拦截器不依赖于servlet容器，过滤器依赖于servlet容器。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;③.拦截器只能对action请求起作用，而过滤器可以对几乎所有的请求起作用。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;④.拦截器可以访问action上下文、值栈里面的对象，而过滤器不能访问。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⑤.在action的生命周期中，拦截器可以多次调用，而过滤器只能在容器初始化时被调用一次。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⑥.<label style="color:red">拦截器可以获取IOC容器中的各个bean，而过滤器就不行，在拦截器里注入一个service，可以调用业务逻辑。</label>
&nbsp;&nbsp;&nbsp;&nbsp;本质区别:从灵活性上来说拦截器功能更强大些，filter能做的事情他都能做，而且可以在请求前，请求后执行，比较灵活。filter主要是针对URL地址做一个编码的事情、过滤掉没用的参数、安全校验，太细的活还是建议使用interceptor。具体还是需要根据不同情况进行选择合适的。
&nbsp;&nbsp;&nbsp;&nbsp;执行顺序:过滤前->拦截前->action处理->拦截后->过滤后。
6.Spring中常见的几种advice(https://www.cnblogs.com/cslgzl/p/10533335.html)
&nbsp;&nbsp;&nbsp;&nbsp;①.环绕advice:环绕advice类似一个拦截器链，这个拦截器链的中心就是被拦截的方法。Invocation.proceed()方法运行指向连接点的拦截器链并返回proceed()的结果。
&nbsp;&nbsp;&nbsp;&nbsp;②.Before advice:一个更简单的通知类型是before通知。它不需要MethodInvocation对象，因为它只是在进入方法之前被调用。before advice的一个主要优点是它不需要调用proceed()方法，因此就不会发生无意间运行拦截连失败的情况。
&nbsp;&nbsp;&nbsp;&nbsp;③.After advice:一个after advice可以访问返回值(但不能修改)、被调用方法、方法参数及目标对象。
&nbsp;&nbsp;&nbsp;&nbsp;④.Threws advice
&nbsp;&nbsp;&nbsp;&nbsp;⑤.introfuction advice
### Spring Faramework ###
7.不同版本的Spring Framework有哪些主要功能？

|<font color='#0A0A0A'>version</font>|<font color='#0A0A0A'>Feature</font>|
|:-----  |:-----|
|Spring 2.5| 发布于2007年。这是第一个支持注解的版本。|
|Spring 3.0| 发布于2009年。它完全利用java5中的改进，并为JEE6提供了支持。|
|Spring 4.0| 发布于2013年。这是第一个完全支持JAVA8的版本。|
8.什么是Spring Framework?
&nbsp;&nbsp;&nbsp;&nbsp;Spring是一个开源应用框架，旨在降低应用程序开发的复杂度。
&nbsp;&nbsp;&nbsp;&nbsp;它是轻量级、松散耦合的。
&nbsp;&nbsp;&nbsp;&nbsp;它具有分层体系结构，允许用户选择组件，同时还为J2EE应用程序开发提供了一个有凝聚力的框架。
&nbsp;&nbsp;&nbsp;&nbsp;它可以集成其它框架，如Structs、Hibernate、EJB等，所以又称为框架的框架。
9.列举Spring Framework的优点
&nbsp;&nbsp;&nbsp;&nbsp;1.由于Spring Framework的分层架构，用户可以自由选择需要的组件。
&nbsp;&nbsp;&nbsp;&nbsp;2.Spring Framework支持POJO(Plain Old Java Object)编程，从而具备持续集成和可测试性。
&nbsp;&nbsp;&nbsp;&nbsp;3.由于依赖注入和控制反转，JDBC得以简化。
&nbsp;&nbsp;&nbsp;&nbsp;4.它是开源免费的。
10.Spring Framework有哪些不同的功能
- 轻量级：Spring在代码质量和透明度方面都很轻便。
- IOC：控制反转。
- APO：面向切面编程可以将应用业务逻辑和系统服务分离，以实现高内聚。
- 容器：Spring负责创建和管理对象(Bean)的生命周期和配置。
- MVC：对WEB应用提供了高度可配置性，其它框架的集成也十分方便。
- 事务管理：提供了用于事务管理的通用抽象层。Spring的事务支持也可用于容器较少的环境。
- JDBC异常：Spring的JDBC抽象层提供了一个异常层次结构，简化了错误处理策略。
11.Spring Framework中有多少个模块，他们分别是什么？
&nbsp;&nbsp;&nbsp;&nbsp;<b>Spring核心容器，</b>该层基本上是Spring Framework的核心。它包含以下模块：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.Spring Core
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.Spring Bean
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.SpEL(Spring Expression Language)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.Spring Context
&nbsp;&nbsp;&nbsp;&nbsp;<b>数据访问/集成，</b>该层提供与数据库交互的支持。它包含以下模块：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.JDBC(Java DataBase Connectivity)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.ORM(Object Relational Mapping)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.OXM(Object XML Mappers)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.JMS(Java Messaging Service)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.Transaction
&nbsp;&nbsp;&nbsp;&nbsp;<b>Web,</b>该层提供了创建web应用程式的支持。它包含以下模块：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.Web
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.Web-Servlet
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.Web-Socket
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.Web-Portlet
&nbsp;&nbsp;&nbsp;&nbsp;<b>AOP,</b>该层支持面向切面编程。
&nbsp;&nbsp;&nbsp;&nbsp;<b>Instrumentation,</b>该层为类检测和类加载器实现提供支持。
&nbsp;&nbsp;&nbsp;&nbsp;<b>Test,</b>该层为使用JUint和TestNG进行测试提供支持。
&nbsp;&nbsp;&nbsp;&nbsp;<b>Messaging,</b>该模块为STOMP提供支持。它还支持注解编程模型，该模型用于从WebSocket客户端路由和处理STOMP消息。
&nbsp;&nbsp;&nbsp;&nbsp;<b>Aspects,</b>该模块为与AspectJ的集成提供支持。
12.什么是Spring配置文件
&nbsp;&nbsp;&nbsp;&nbsp;Spring配置文件是XML文件。该文件主要包含类信息。它描述了这些类是如何配置以及相互引用的。但是，XML配置文件冗长且更加干净。如果没有正确规划和编写，那么在大项目中管理变得十分困难。
13.Spring应用程序有哪些不同的组件
&nbsp;&nbsp;&nbsp;&nbsp;Spring应用一般有一下组件：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>接口：</b>定义功能。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>Bean类:</b>它包含属性、setter和getter方法、函数等。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>Spring面向切面编程(AOP)：</b>提供面向切面编程的功能。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>Bean配置文件：</b>包含类的信息以及如何配置它们。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>用户程序：</b>它使用接口。
14.使用Spring有哪些方式？
&nbsp;&nbsp;&nbsp;&nbsp;1.作为一个成熟的Spring Web应用程序。
&nbsp;&nbsp;&nbsp;&nbsp;2.作为第三方Web框架，使用Spring Frameworks中间层。
&nbsp;&nbsp;&nbsp;&nbsp;3.用于远程使用。
&nbsp;&nbsp;&nbsp;&nbsp;4.作为企业级Java Bean，它可以包装现有的POJO(Plain Old Java Objects)
### 依赖注入(IOC) ###
15.什么是Spring IOC容器
&nbsp;&nbsp;&nbsp;&nbsp;Spring框架的核心是Spring容器。容器创建对象，将它们装配在一起，配置它们并管理他们的完整生命周期。Spring容器使用依赖注入来管理组成应用程序的组件。
&nbsp;&nbsp;&nbsp;&nbsp;容器通过读取提供的配置元数据来接受对象进行实例化，配置和组装的指令。该元数据可以通过XML、Java注解或Java代码提供。
16.什么是依赖注入？
&nbsp;&nbsp;&nbsp;&nbsp;在依赖注入中，您不必创建对象，但必须描述如何创建它们。您不是直接在代码中将组件和服务连接在一起，而是描述配置文件中哪些组件需要哪些服务。由IOC容器将它们装配在一起。
17.可以通过多少种方式完成依赖注入？
- 构造函数注入
- setter注入
- 接口注入
&nbsp;&nbsp;&nbsp;&nbsp;在Spring Framework中，仅使用构造函数和setter注入。
18.区分构造函数注入和setter注入

|<font color='#0A0A0A'>构造函数注入</font>|<font color='#0A0A0A'>Setter注入</font>|
|:-----  |:-----|
| 没有部分注入 | 有部分注入 |
| 不会覆盖setter属性 | 会覆盖setter属性 |
| 任意修改都会创建一个新的实例 | 任意修改不会创建一个新的实例 |
| 适用于设置很多属性 | 适用于设置少量属性 |
19.Spring中有多少种IOC容器？
&nbsp;&nbsp;&nbsp;&nbsp;1.BeanFactory:就像一个包含bean集合的工厂类。它会在客户端要求时实例化bean。
&nbsp;&nbsp;&nbsp;&nbsp;2.ApplicationContext:ApplicationContext接口扩展了BeanFactory接口。它在BeanFactory基础上提供了一些额外的功能。
20.区分BeanFactory和ApplicationContext

|<font color='#0A0A0A'>BeanFactory</font>|<font color='#0A0A0A'>ApplicationContext</font>|
|:-----  |:-----|
| 它使用懒加载 | 它使用即时加载 |
| 它使用语法显式提供资源对象 | 它自己创建和管理资源对象 |
| 不支持国际化 | 支持国际化 |
| 不支持基于依赖的注解 | 支持基于依赖的注解 |
21.列举IOC的一些好处
&nbsp;&nbsp;&nbsp;&nbsp;1.它将最小化应用程序的代码量。
&nbsp;&nbsp;&nbsp;&nbsp;2.它将使您的应用程序易于测试，因为它不需要单元测试用例中的任何单例或JNDI查找机制。
&nbsp;&nbsp;&nbsp;&nbsp;3.它以最小的影响和最少的侵入机制促进松耦合。
&nbsp;&nbsp;&nbsp;&nbsp;4.它支持即时的实例化和延迟加载服务。
22.Spring IOC的实现机制
&nbsp;&nbsp;&nbsp;&nbsp;Spring中的IOC的实现原理就是工厂模式加反射机制，示例：
```java
interface Fruit {
	public abstract void eat();
}
class Apple implements Fruit {
	public void eat() {
		System.out.println("Apple");
	}
}
class Orange implements Fruit {
	public void eat() {
		System.out.println("Orange");
	}
}
class Factory {
	public static Fruit getInstance(String ClassName) {
		Fruit f = null;
		try {
			f = (Fruit) Class.forName(ClassName).newInstance();
		} catch (Expression e) {
			e.printStackTrace();
		}
		return f;
	}
}
class Client {
	public static void main(String[] a) {
		Fruit f = Factory.getInstnce("com.test.Apple");
		if (f != null) {
			f.eat();
		}
	}	
}
```
### Beans ###
23.什么是spring bean？
- 它们是构成用户应用程序主干的对象。
- Bean是由Spring IOC容器管理。
- 它们是由Spring IOC容器实例化、配置、装配和管理。
- Bean是基于用户提供给容器的配置元数据创建。
24.Spring提供了哪些Bean的配置方式？
&nbsp;&nbsp;&nbsp;&nbsp;1.基于XML配置：Bean所需的依赖项和服务在XML格式的配置文件中指定。这些配置文件通常包含许多bean定义和特定于应用程序的配置选项。他们通常以bean标签开头。例如：
```XML
<bean id="studentBean" class="com.test.bean.studentBean">
	<property name="name" value="Edureka"></property>
</bean>
```
&nbsp;&nbsp;&nbsp;&nbsp;2.基于注解配置：您可以通过在相关的类、方法或字段声明上使用注解，将bean配置为组件类本身，而不是使用XML来描述bean的装配。默认情况下，Spring容器中未打开注解装配。因此，您需要在使用它之前在Spring配置文件中启用它。例如“
```XML
<beans>
	<context:annotation-config/>
</beans>
```
&nbsp;&nbsp;&nbsp;&nbsp;3.基于Java API配置：Spring的Java配置是通过使用@Bean和@Configuration来实现。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;①.@Bean注解扮演与元素相同的角色。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;②.@Configuration类允许通过简单地调用同一个类中的其他@Bean方法来定义bean间关系。
&nbsp;&nbsp;&nbsp;&nbsp;例如:
```java
@Configuration
public class StudentConfig {
	@Bean
	public StudentBean myStudent() {
		return new StudentBean();
	}
}
```
25.Spring支持集中bean scope？
&nbsp;&nbsp;&nbsp;&nbsp;Spring bean 支持5种scope：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;①.Singleton:每个Spring IOC容器仅有一个实例。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;②.Prototype:每次请求都会产生一个新的实例。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;③.Request:每一次HTTP请求都会产生一个新的实例，并且该bean仅在当前HTTP请求内有效。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;④.Session:每一次HTTP请求都会于产生一个新的bean，同时该bean仅在当前HTTP session内有效。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⑤.Global-session:类似于标准的HTTP Session作用域，不过它仅仅在基于protlet的web应用中才有意义。Protlet规范定义了全局Session的概念，它被所有构成某个protlet web应用的各种不同的protlet Session的生命周期范围内。如果你在web中使用global-session作用域来标识bean，那么web会自动当成session类型使用。
&nbsp;&nbsp;&nbsp;&nbsp;仅当用户使用支持web的ApplicationContext时，最后三个才可用。
26.spring bean容器的生命周期是什么样的？
&nbsp;&nbsp;&nbsp;&nbsp;spring bean容器的生命周期流程如下：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;①.Spring 容器根据配置中的bean定义去实例化bean。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;②.Spring使用依赖注入填充所有属性，如bean中所定义的配置。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;③.如果bean实现BeanNameAware接口，则工厂通过传递bean的ID来调用setBeanName()。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;④.如果bean实现BeanFactoryAware接口，工厂通过传递自身的实例来调用setBeanFactory()。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⑤.如果存在与bean关联的任何BeanPostProcessors，则调用preProcessBeforeInitialization()方法。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⑥.如果为bean指定了init方法(的init-method属性)，那么将调用它。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⑦.最后，如果它存在与bean关联的任何BeanPostProcessors，则将调用postProcessAfterInitializationn()方法。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⑧.如果bean实现DisposableBean接口，当spring容器关闭时，会调用destory()。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⑨.如果为bean指定了destory方法(的destory-method属性)，那么将调用它。
26.什么是Spring的内部bean？
&nbsp;&nbsp;&nbsp;&nbsp;只有将bean用作另一个bean的属性时，才能将bean声明为内部bean。为了定义bean，Spring基于XML的配置元数据在<property>或<constructor-arg>中提供了<bean>元素的使用。内部bean总是匿名的，他们总是作为原型。
&nbsp;&nbsp;&nbsp;&nbsp;例如：假设我们有一个Student类，其中引用了Preson类。这里我们将只创建一个Person类实例并在Student中使用它。
```java
public class Student {
	private Person person;
}
public class Person {
	private String name;
	private String address;
}
```
```XML
<!-- bean.xml -->
<bean id="StudentBean" class="com.test.bean.Student">
	<property name="person">
		<bean class="com.test.bean.Person>
			<property name="name" value="Will"></property>
			<property name="address" value="中国"></property>
		</bean>
	</property>
</bean>
```
27.什么是Spring装配
&nbsp;&nbsp;&nbsp;&nbsp;当bean在Spring容器中组合在一起时，它被成为装配或bean装配。Spring容器需要知道需要什么bean以及容器应该如何使用依赖注入来将bean绑定在一起，同时装配bean。
28.自动装配有哪些方式
&nbsp;&nbsp;&nbsp;&nbsp;Spring容器能够自动装配bean，也就是说，可以通过检查BeanFactory的内容让Spring自动解析bean的协作者。
&nbsp;&nbsp;&nbsp;&nbsp;自动装配的不同模式：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;no：这是默认设置，表示没有自动装配。应使用显式bean引用进行装配。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;byName：它根据bean的名称注入对象依赖项。它匹配并装配其属性与XML文件中由相同名称定义的bean。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;byType：它根据类型注入对象依赖项。如果属性的类型与XML文件中的一个bean名称匹配，则匹配并装配属性。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;构造函数：它通过调用类的构造函数来注入依赖项。它有大量的参数。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;autodetect：首先容器尝试通过构造函数使用autowire装配，如果不能，则尝试通过byType自动装配。
29.自动装配有什么局限性？
&nbsp;&nbsp;&nbsp;&nbsp;覆盖的可能性：您始终可以使用和设置指定依赖项，这将覆盖自动装配。
&nbsp;&nbsp;&nbsp;&nbsp;基本元数据类型：简单属性(如元数据类型，字符串和类)无法自动装配。
&nbsp;&nbsp;&nbsp;&nbsp;令人困惑的性质：总是喜欢使用明确的装配，因为自动装配不太精确。
### 注解 ###
30.用过哪些重要的注解
&nbsp;&nbsp;&nbsp;&nbsp;@Controller：用于SpringMVC项目中的控制器类。
&nbsp;&nbsp;&nbsp;&nbsp;@service：用于服务类。
&nbsp;&nbsp;&nbsp;&nbsp;@RequestMapping：用于在控制器处理程式方法中配置URI映射。
&nbsp;&nbsp;&nbsp;&nbsp;@ResponseBody：用于发送Object作为响应，通常用于发生XML或JSON数据作为响应。
&nbsp;&nbsp;&nbsp;&nbsp;@PathVariable：用于将动态值从URI映射到处理程式方法参数。
&nbsp;&nbsp;&nbsp;&nbsp;@Autowired：用于在Spring bean中自动装配依赖项。
&nbsp;&nbsp;&nbsp;&nbsp;@Qualifier：使用@Autowired注解，以避免存在多个bean类型实例时出现混淆。
&nbsp;&nbsp;&nbsp;&nbsp;@Scope：用于配置Spring Bean的范围。
&nbsp;&nbsp;&nbsp;&nbsp;@Configuration,@ComponentScan和@Bean：用于基于java的配置。
&nbsp;&nbsp;&nbsp;&nbsp;@Aspect、@Before、@After、@Around、@Pointcut：用于切面编程(AOP)。
31.如何在Spring中启动注解装配？
&nbsp;&nbsp;&nbsp;&nbsp;默认情况下，Spring容器中未打开注解装配。因此，要使用基于注解装配，我们必须通过配置<context:annotation-config />元素在Spring配置文件中启用它。
32.@Component、@Controller、@Repository、@Service有何区别
&nbsp;&nbsp;&nbsp;&nbsp;@Component：这将java类标记为bean。它是任何Spring管理组件的通用构造型。Spring的组件扫描机制可以将其拾取并将其拉入应用程序环境中。
&nbsp;&nbsp;&nbsp;&nbsp;@Controller：这将一个类标记为Spring Web MVC控制器。标有它的bean会自动导入到IOC容器中。
&nbsp;&nbsp;&nbsp;&nbsp;@Service：此注解是组件注解的特化。他不会对@Component注解提供任何行为。您可以在服务层中使用@Service而不是@Component，因为它以更好的方式指定了意图。
&nbsp;&nbsp;&nbsp;&nbsp;@Repository：这个注解是具有类似用途和功能的@Component注解的特化。它为DAO提供了额外的好处。它将DAO导入IOC容器，并使未经检查的异常有资格转化为Spring DataAccessException。
33.@Required注解有什么用？
&nbsp;&nbsp;&nbsp;&nbsp;@Required应用于bean属性setter方法。此注解仅指示必须在配置时使用bean定义中的显式属性值或使用自动装配填充受影响的bean属性。如果尚未填充受影响的bean属性，则容器将抛出BeanInitializationException。
```java
public class Employee {
	private String name;
	@Required
	public void setName(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
}
```
34.@Autowired注解有什么用？
&nbsp;&nbsp;&nbsp;&nbsp;@Autowired可以更准确地控制应该在何处以及如何进行自动装配。此注解用于在setter方法、构造函数，具有任意名称或多个参数的属性或方法上自动装配bean。默认情况下，它是类型驱动的注入。
```java
public class Employee {
	private String name;
	@Autowired
	public void serName(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
}
```
35.@Qualifier注解有什么用？
&nbsp;&nbsp;&nbsp;&nbsp;当您创建多个相同类型的bean并希望仅使用属性装配其中一个bean时，您可以使用@Qualifier注解和@Autowired通过指定应该装配哪个确切的bean来消除歧义。
&nbsp;&nbsp;&nbsp;&nbsp;例如：这里我们分别有两个类，Employee和EmpAccount。在EmpAccount中，使用@Qualifier指定了必须装配id为emp1的bean。
```java
public class Employee {
	private String name;
	@Autowired
	public void setName(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
}
public class EmpAccount {
	private Empoyee emp;
	@Autowired
	@Qualifier(emp1)
	public void showName() {
		System.err.println("Employee name:" + emp.getName());
	}
}
```
36.@RequestMapping注解有什么用？
&nbsp;&nbsp;&nbsp;&nbsp;@RequestMapping注解用于将特定HTTP请求方法映射到将处理相应请求的控制器中的特定类/方法。此注解可应用于两个级别：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;类级别：映射请求的URL。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;方法级别：映射URL以及HTTP请求方法。
### 数据访问 ###
37.Spring DAO有什么用？
&nbsp;&nbsp;&nbsp;&nbsp;Spring DAO使得JDBC、Hibernate或JDO这样的数据访问技术更容易以一种统一的方式工作。这使得用户容易在持久性技术之间切换。它允许您在编写代码时，无需考虑捕获每种技术不同的异常。
38.Spring JDBC API中存在哪些类？
&nbsp;&nbsp;&nbsp;&nbsp;JdbcTemplate
&nbsp;&nbsp;&nbsp;&nbsp;SimpleJdbcTemplate
&nbsp;&nbsp;&nbsp;&nbsp;NamedParameterJdbcTemplate
&nbsp;&nbsp;&nbsp;&nbsp;SimpleJdbcInsert
&nbsp;&nbsp;&nbsp;&nbsp;SimpleJdbcCall
39.使用spring访问Hibernate的方法有哪些？
&nbsp;&nbsp;&nbsp;&nbsp;我们可以通过两种方式使用Spring访问Hibernate：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.使用Hibernate模板和回调进行控制反转。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.扩展HibernateDAOSupport并应用AOP拦截节点。
40.列举Spring支持的事务管理类型
&nbsp;&nbsp;&nbsp;&nbsp;1.程序化事务管理：在此过程中，在编译的帮助下管理事务。它为您提供极大的灵活性，但维护起来非常困难。
&nbsp;&nbsp;&nbsp;&nbsp;2.声明式事务管理：在此，事务管理与业务代码分离。仅使用注解或基于XML的配置来管理事务。
41.Spring支持哪些ORM框架
&nbsp;&nbsp;&nbsp;&nbsp;Hibernate、MyBatis、JPA、JDO、OJB
### AOP ###
42.什么是AOP
&nbsp;&nbsp;&nbsp;&nbsp;AOP(Aspect-Oriented Programming),即面向切面编程，它与OOP(Object-Oriented Programming，面向对象编程)相辅相成，提供了与OOP不同的抽象软件结构的视角。
&nbsp;&nbsp;&nbsp;&nbsp;在OOP中，我们以类(class)作为我们的基本单元，而AOP中的基本单元是Aspect(切面)。
43.AOP中的Aspect、Advice、Pointcut、JoinPoint和Adivce参数分别是什么？
&nbsp;&nbsp;&nbsp;&nbsp;Aspect：是一个实现交叉问题的类，例如事务管理。方面可以是配置的普通类，然后在Spring Bean配置文件中配置，或者我们可以使用Spring AspectJ支持使用@Aspect注解，将类声明为Aspect。
&nbsp;&nbsp;&nbsp;&nbsp;Advice：是针对特定JoinPoint采取的操作。在编程方面，它们是在应用程式中达到具有匹配切入点的特定JoinPoint时执行的方法。您可以将Advice视为Spring拦截器(Interceptor)或Servlet过滤器(filter)。
&nbsp;&nbsp;&nbsp;&nbsp;Advice Arguments：我们可以在advice方法中传递参数。我们可以在切入点中使用args()表达式来应用于与参数模式匹配的任何方法。如果我们使用它，那么我们需要在确定参数类型的advice方法中使用相同的名称。
&nbsp;&nbsp;&nbsp;&nbsp;Pointcut：是与JoinPoint匹配的正则表达式，用于确定是否需要执行Advice。Pointcut使用与JoinPoint匹配的不同类型的表达式。Spring框架使用AspectJ Pointcut表达式语言来确定将应用通知方法的JoinPoint。
&nbsp;&nbsp;&nbsp;&nbsp;JoinPoint：是应用程序中的特定点，例如方法执行、异常处理、更改对象变量值等。在Spring AOP中，JoinPoint始终是方法的执行器。
44.什么是通知(Advice)
&nbsp;&nbsp;&nbsp;&nbsp;特定JoinPoint处的Aspect所采取的动作称为Advice。Spring AOP使用一个Advice作为拦截器，在JoinPoint“周围”维护一系列的拦截器。
45.有哪些类型的通知(Advice)
&nbsp;&nbsp;&nbsp;&nbsp;Before：这些类型的Advice在JoinPoint方法之前执行，并使用@Before注解标记进行配置。
&nbsp;&nbsp;&nbsp;&nbsp;After Returning：这些类型的Advice在连接点方法正常执行后执行，并使用@AfterReturning注解标记进行配置。
&nbsp;&nbsp;&nbsp;&nbsp;After Throwing：这些类型的Advice尽在JoinPoint方法通过抛出异常退出并使用@AfterThrowing注解标记配置时执行。
&nbsp;&nbsp;&nbsp;&nbsp;After(finally)：这些类型的Advice在连接点方法之后执行，无论方法退出是正常还是异常返回，并使用@After注解标记进行配置。
&nbsp;&nbsp;&nbsp;&nbsp;Around：这些类型的Advice在连接点之前和之后执行，并使用@Around注解标记进行配置。
46.指出在Spring AOP中concern和cross-cutting concern的不同之处
&nbsp;&nbsp;&nbsp;&nbsp;concern是我们想要在应用程序的特定模块中定义的行为。它可以定义为我们想要实现的功能。
&nbsp;&nbsp;&nbsp;&nbsp;cross-cutting concern是一个适用于整个应用的行为，这会影响整个应用程序。例如：日志记录、安全性和数据传输是应用程序几乎每个模块都需要关注的问题，因此它们是跨领域的问题。
47.