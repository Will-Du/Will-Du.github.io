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
<!-- more -->

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
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color:orangered">1.2 Spring Security集成</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;目前Spring Security5支持与以下技术进行集成：
- HTTP basic access authentication
- LDAP system
- OpenID identity providers
- JAAS API
- CAS Server
- ESB Platform
- ......
- Your own authentication system
#### 二.核心组件 ####
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">2.1 SecurityContextHolder,SecurityContext和Authentication</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最基本的对象是SecurityContextHolder，它是我们存储当前应用程序安全上下文的详细信息，其中包括当前使用应用程序的主体的详细信息。如当前操作的用户是谁，该用户是否已经被认证，他拥有哪些权限等。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;默认情况下，SecurityContextHolder使用ThreadLocal来存储这些详细信息，这意味着Security Context始终可用于同一执行线程中的方法，即使Security Context未作为这些方法的参数显式传递。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">获取当前用户的信息</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为身份信息与当前线程已绑定，所以可以使用以下代码在应用程序中获取当前已验证用户的用户名：
```java
Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
if(principal instanceof UserDetails) {
    String username = ((UserDetails) principal).getUsername();
} else {
    String username = principal.toString();
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;调用getContext()返回的对象是SecurityContext接口的一个实例，对应SecurityContext接口定义如下：
```java
public interface SecurityContext extends Serializable {
    Authentication getAuthentication();
    void setAuthentication(Authentication authentication);
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在SecurityContext接口中定义了getAuthentication和setAuthentication两个抽象方法，当调用getAuthentication方法后会返回一个Authentication类型的对象，这里的Authentication也是一个接口，它的定义如下：
```java
public interface Authentication entends Principal, Serializable {
    Collection<? extends GrantedAuthority> getAuthorities();
    Object getCredentials();
    Object getDetails();
    Object getPrincipal();
    boolean isAuthenticated();
    void setAuthenticated(boolean isAuthenticated) throws IllegalArgumentException;
}
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color : orangered">2.2 小结</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SecurityContextHolder用来保存SecurityContext(安全上下文对象)，通过调用SecurityContext对象中的方法，如getAuthentication方法，我们可以方便获取Authentication对象，利用该对象我们可以进一步获取已认证用户的详细信息。
#### 三.身份认证 ####
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">3.1 Spring Security中的身份验证是什么?</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;让我们考虑每一个人都熟悉的标准身份验证方案：
- 系统会提示用户使用用户名和密码登陆
- 系统会验证用户名和密码是否正确
- 若验证通过则获取该用户的上下文信息(如权限列表)
- 为用户建立安全上下文
- 用户继续进行，可能执行某些操作，该操作可能受访问控制机制保护，该访问控制机制根据当前安全上下文信息检查操作所需的权限
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前三项构成了身份验证进程，因此我们将在Spring Security中查看这些内容。
- 获取用户名和密码并将其组合到UsernamePasswordAuthenticationToken的实例中(我们之前看到的Authentication接口的实例)
- 令牌传递给AuthenticationManager的实例进行验证
- AuthenticationManager在成功验证时返回完全填充的Authentication实例
- Security对象是通过调用SecurityContextHolder.getContext().setAuthentication(...)创建的，传入返回的身份验证Authentication对象
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">3.2 Spring Security身份验证流程示例</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">AuthenticationManager接口:</b>
```java
public interface AuthenticationManager {
    Authentication authenticate(Authentication authentication) throws AuthenticationException;
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">SampleAuthenticationManager类:</b>
```java
class SimpleAuthenticationManager implements AuthenticationManager {
    static final List<GrantedAuthority> AUTHORITY = new ArrayList<GrantedAuthority>();
    static {
        AUTHORITY.add(new SimpleGrantedAuthority("ROLE_USER"));
    }
    public Authentication authenticate(Authentication auth) throws AuthenticationException {
        if(auth.getName.equals(auth.getCredentials())) {
            return new UsernamePasswordAuthenticationToken(auth.getName(), auth.getCredentials(), AUTHORITY);
        }
            throw new BadCredentialsException("Bad Credentials");
    }
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color #6A6AFF">AuthenticationExample类：</b>
```java
public class AuthenticationExample {
    private static AuthenticationManager am = new SimpleAuthenticationManager();
    public static void main(String[] args) throws Exception {
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        while(true) {
            System.out.println("Please enter your username:");
            String name = in.readLine();
						System.out.println("please enter your password:");
						String password = in.readLine();
						try {
                Authentication request = new UsernamePasswordAuthenticationToken(name, password);
                Authentication result = am.authenticate(request);
                SecurityContextHolder.getContext().setAuthentication(result);
								break;
            } catch (AuthenticationExpection e) {
                System.out.println("Authentication failed:" + e.getMessage());
            }
        }
        System.out.println("Successfully authenticated. Security context contains: " 
            + SecurityContextHolder.getContext().getAuthentication());
    }
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在以上代码中，我们实现的AuthenticationManager将验证用户名和密码相同的任何用户。它为每个用户分配一个角色。上面验证过程是这样的：
```
Please enter your username:
will
Please enter your password:
123456
Authentication failed: Bad Credentials
Please enter your username:
will
Please enter your password:
will
Successfully authenticated. Security context contains: org.springframework.security.authentication.UsenamePasswordAuthenticationToken@..
```
