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
            } catch (AuthenticationException e) {
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
#### 四.核心服务 ####
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">4.1 AuthenticationManager,ProviderManager和AuthenticationProvider</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AuthenticationManager(接口)是认证相关的核心接口，也是发起认证的出发点，因为在实际需求中，我们可能会允许用户使用用户名+密码登陆，同时允许用户使用邮箱+密码，手机号+密码登陆，甚至可能会允许用户使用指纹登录，所以要求认证系统要支持多种认证方式。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spring Security中AuthentiocationManager接口的默认实现是ProviderManager,但它本身并不直接处理身份验证请求，它会委托给已配置的AuthenticationProvider列表，每个列表依次被查询以查看它是否可以执行身份验证。每个Provider验证程序将抛出异常或返回一个完全填充的Authentication对象。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;也就是说，Spring Security中核心的认证入口始终只有一个：AuthenticationManager,不同的认证方式：用户名+密码(UsernamePasswordAuthenticationToken)，邮箱+密码，手机号码+密码登陆则对应三个AuthenticationProvider。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;下面我们来看一下ProviderManager的核心源码:
```java
public class ProviderManager implements AuthenticationManager, MessageSourceAware, InitialzingBean {
    private List<AuthenticationProvider> providers = Collections.emptyList();
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        Class<? extends Authentication> toTest = authentication.getClass();
        AuthenticationException lastException = null;
        AuthenticationException parentException = null;
				Authentication result = null;
        Authentication parentResult = null;
        boolean debug = logger.isDebugEnabled();
        for(AuthenticationProvider provider : getProviders()) {
            if(!provider.supports(toTest)) {
                continue;
            }
            try {
                result = provider.authenticate(authentication);
                if(result != null) {
                    copyDetails(authentication, result);
                    break;
                }
            } catch (AccountStatusException | InternalAuthenticationServiceException e) {
                prepareException(e, authentication);
						} catch (AuthenticationException e) {
                lastException = e;
            }
        }
        if(result == null && parent != null) {
            try {
                result = parentResult = parent.authenticate(authentication);
            } catch (ProviderNotFoundException e) {
            } catch (AuthenticationException e) {
                lastException = parentException = e;
            }
        }
        if(result != null) {
            if(eraseCredentialsAfterAuthentication && (result instanceof CredentialsContainer)) {
                ((CredentialsContainer) result).eraseCredentials();
            }
            if(parentResult == null) {
                eventPublisher.publishAuthenticationSuccess(result);
            }
						return result;
        }
        if(lastException == null) {
            lastException = new ProviderNotFoundException(messages.getMessage("ProviderManager.providerNotFound",new Object[] { toTest.getName(); }, "No AuthenticationProvider found for {0}"));
        }
        if(parentException == null) {
            prepareException(lastException, authentication);
        }
        throw lastException;
    }
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在ProviderManager进行认证的过程中，会遍历providers列表，判断是否支持当前authentication对象的认证方式，若支持该认证方式时，就会调用所匹配provider(AuthenticationProvider)对象的authenticate方法进行认证操作。若认证失败则会返回null，下一个AuthenticationProvider会继续尝试认证，如果所有认证器都无法认证成功，则ProviderManager会抛出一个ProviderManagerException异常。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">4.2 DaoAuthenticationProvider</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在Spring Security中较常用的AuthenticationProvider是DaoAutnenticationProvider,这也是Spring Security最早支持的AuthenticationProvider之一。顾名思义，Dao正是数据访问层的缩写，也暗示了这个身份认证器的实现思路。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在实际项目中，最常见的认证方式是使用用户名和密码。用户在登陆表单中提交了用户名和密码，而对于已注册的用户，在数据库中已保存了正确的用户名和密码，认证便是负责对比同一用户名，提交的密码和数据库中所保存的密码是否相同便是了。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在Spring Security中，对于使用用户名和密码进行认证的场景，用户在登陆表单中提交的用户名和密码，被封装成UsenamePasswordAuthenticationToken，而根据用户名加载用户的任务则是交给了UserDetailsService，在DaoAuthenticationProvider中，对应的方法就是retrieveUser，虽然有两个参数，但是retrieveUser只有第一个参数起主要作用，返回一个UserDetails。retrieveUser方法的具体实现如下：
```java
protected final UserDetails retrieveUser(String username, UsernamePasswordAuthenticationToken authentication) throws AuthenticationException {
    prepareTimingAttackProtection();
    try {
        UserDetails loadedUser = this.getUserDetailsService().loadUserByUsername(username);
        if(loadedUser == null) {
            throw new InternalAuthenticationServiceException("UserDetailsService return null, which is an interface contract violation");
        }
        return loadedUser;
    } catch (UsenameNotFoundException ex) {
        mitigateAgainstTimingAttack(authentication);
        throw ex;
    } catch (InternalAuthenticationServiceException ex) {
        throw ex;
    } catch (Exception ex) {
        throw new InternalAuthenticationServiceException(ex.getMessage(), ex);
    }
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在DaoAuthenticationProvider类的retrieveUser方法中，会以传入的username作为参数，调用UserDetailsService对象的loadUserByUsername方法加载用户。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">4.3 UserDetails与UserDetailsService</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">4.3.1 UserDetails接口</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在DaoAuthenticationProvider类中retrieveUser方法签名是这样的：
```java
protected final UserDetails retrieveUser(String username, UsernamePasswordAuthenticationToken authentication) throws AuthenticationException {
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该方法返回UserDetails对象，这里的UserDetails也是一个接口，它的定义如下：
```java
public interface UserDetails extends Serializable {
    Collection<? extends GrantedAuthority> getAuthorities();
    String getPassword();
    String getUsername();
    boolean isAccountNonExpired();
    boolean isAccountNonLocked();
    boolean isCredentialsNonExpired();
    boolean isEnabled();	
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;顾名思义，UserDetails表示详细的用户信息，这个接口涵盖了一些必要的用户信息字段，具体的实现类对它进行了扩展。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;虽然Authentication与UserDetails很类似，但它们之间是有区别的。<b style="color: #00FFFF">Authentication的getCredentials()与UserDetails中的getPassword()需要被区分对待，前者是用户提交的密码凭证，后者是用户正确的密码，认证器其实就是对这两者进行比对。</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此外Authentication中的getAuthorities()实际是由UserDetails的getAuthorities()传递而形成的。还记得Authentication接口中getUserDetails()方法吗？其中的UserDetails用户详细信息就是经过了provider(AuthenticationProvider)认证之后被填充的。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">4.3.2 UserDetailsService接口</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;大多数身份验证提供程序都利用了UserDetails和UserDetailsService接口。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;UserDetailsService接口定义如下：
```java
public inteface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsenameNotFoundException;
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在UserDetailsService接口中，只有一个loadUserByUsername方法，用于通过username来加载匹配的用户。当找不到username对应的用户时，会抛出UsernameNotFoundException异常。<b style="color: #00FFFF">UserDetailsService和AuthenticationProvider两者的职责常常被人搞混，记住一点即可，UserDetailsService只负责从特定的地方(通常是数据库)加载用户信息，仅此而已。</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;UserDetailsService常见的实现类有JdbcDaoImpl,InMemoryUserDetailsManager，前者从数据库加载用户，后者从内存中加载用户，当然你也可以自己实现UserDetailsService。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">4.4 Spring Security Architecture</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面已经介绍了Spring Security的核心组件(SecurityContextHolder, SecurityContext和Authentication)和核心服务(AuthenticationManager,ProviderManager和AuthenticationProvider)
