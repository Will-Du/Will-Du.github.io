---
title: Spring Security架构简介
date: 2020-01-03 16:02:05
tags: Spring
---
#### 一.技术概述 ####
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">1.1 Spring vs Spring Boot vs Spring Security</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1.1.1 Spring Framework</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">Spring Framework</b>为开发Java应用程序提供了全面的基础架构支持。它包含一些不错的功能，如“依赖注入”，以及一些现成的模块：
- Spring JDBC
- Spring MVC
- Spring Security
- Spring AOP
- Spring ORM
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这些模块可以大大减少应用程序的开发时间。例如：在Java Web开发的早期，我们需要编写大量的样板代码以将记录插入数据源。但是，通过使用Spring JDBC模块的JDBCTemplate,我们可以仅通过少量配置将其简化为几行代码。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1.1.2 Spring Boot</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">Spring Boot</b>是基于Spring Framework，它为你的Spring应用程序提供了自动装配特性，它的设计目标是让你尽可能快的上手应用程序的开发。以下是Spring Boot所拥有的一些特性：
- 可以创建独立的Spring应用程序，并且基于Maven和Gradle插件，可以创建可执行的JARs和WARs
- 内嵌Tomcat或Jetty等Servlet容器
- 提供自动配置的“starter”项目对象模型(POMS)以简化Maven配置
- 尽可能自动配置Spring容器
- 提供一些常见的功能，如监控、WEB容器、健康、安全等功能
- 绝对没有代码生成，也不需要XML配置
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1.1.3 Spring Security</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">Spring Security</b>是一个能够为基于Spring的企业应用系统提供声明式的安全访问控制解决方案的安全框架。它提供了一组可以在Spring应用上下文中配置的Bean，充分利用了Spring IOC(Inversion of Control 控制反转)，DI(Dependency Injection 依赖注入)和AOP(面向切面编程)功能，为应用系统提供声明式的安全访问控制功能，减少了为企业系统安全控制编写大量重复代码的工作。Spring Security拥有以下特性：
- 对身份验证和授权的全面且可扩展的支持
- 防御会话固定、点击劫持，跨站请求伪造等攻击
- 支持Servlet API集成
- 支持与Spring MVC集成