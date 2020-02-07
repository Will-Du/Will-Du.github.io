---
title: 最强java Redis客户端
date: 2020-02-07 20:13:08
tags: redis
---
为什么要在Java分布式应用程序中使用缓存？
在提高应用程序速度和性能上，每一毫秒都很重要。根据谷歌的一项研究，假如一个网站在3秒钟或更短时间内没有加载成功，会有53%的手机用户会离开。
<!-- more -->
缓存是让分布式应用程序加速的重要技术之一。存储的信息越接近CPU，访问速度就越快。从CPU缓存中加载数据比从RAM中加载要快得多，比从硬盘或网络上加载要快得多得多。
要存储经常访问的数据，分布式应用程序需要在多台机器中维护缓存。分布式缓存是降低分布式应用程序延迟、提高并发性和可伸缩性的一种重要策略。
Redis是一种流行的开源内存数据存储，可用作数据库、缓存或消息代理。由于是从内存而非磁盘加载数据，Redis比许多传统的数据库解决方案更快。
然而，对开发者来说让Redis分布式缓存正确工作是一个巨大挑战。例如，必须谨慎处理本地缓存失败，即替换或删除缓存条目。每次更新或删除存储计算机本地缓存中的信息时，必须更新分布式缓存系统所有计算机内存中的缓存。
好消息是，有一些类似Redisson这样的Redis框架，可以帮助构建应用程序所需的分布式缓存。下面将讨论Redisson中分布式缓存的三个重要实现：Maps、String Cache和JCache。
<b style="color: orangered">1.Redisson分布式缓存</b>
&nbsp;&nbsp;&nbsp;&nbsp;Redisson是一个基于Redis的框架，用Java实现了一个Redis包装器(wrapper)和接口。Redisson包含很多常见的Java类，例如分布式对象、分布式服务、分布式锁和同步器，以及分布式集合。正如下面即将介绍的，其中一些接口同时支持分布式和本地缓存。
<b style="color: orangered">2.Map</b>
&nbsp;&nbsp;&nbsp;&nbsp;Map是Java最有用的集合之一。Redisson提供了一个名为RMap的Java Map实现，支持本地缓存。
&nbsp;&nbsp;&nbsp;&nbsp;如果希望执行多个读操作或网络环回(roundtrip)，应使用支持本地缓存的RMap。通过本地存储Map数据，RMap比不启用本地缓存是快45倍。通用分布式缓存使用RMapCache，本地缓存使用RLocalCachedMap。
&nbsp;&nbsp;&nbsp;&nbsp;Redis引擎自身能够执行缓存，不需要在客户端执行代码。然而， 虽然本地缓存能显著提高读取速度，但需要由开发人员维护，并且可能需要一些开发工作。Redisson为开发人员提供了RLocalCacheMap对象，让本地缓存实现起来更容易。
&nbsp;&nbsp;&nbsp;&nbsp;下面的代码展示了如何初始化RMapCache对象：
```java
RMapCache<String, SomeObject> map = redisson.getMapCache("anyMap");
map.put("key1", new SomeObject(), 10, TimeUnit.MINUTES, 10, TimeUnit.SECONDS);
```
&nbsp;&nbsp;&nbsp;&nbsp;上面的代码将字符串"key1"放到RMapCache中，并与SomeObject()关联。然后它指定了两个参数，TTL设为10分钟，最大空闲时间10秒。
&nbsp;&nbsp;&nbsp;&nbsp;当不再需要时，应销毁RMapCache对象：
```java
map.destory();
```
&nbsp;&nbsp;&nbsp;&nbsp;Redisson关闭后不用再做销毁操作。
<b style="color: orangered">3.Spring Cache</b>
&nbsp;&nbsp;&nbsp;&nbsp;Spring是一个用于构建企业级WEB应用程序的Java框架，也提供了缓存支持。
&nbsp;&nbsp;&nbsp;&nbsp;Redisson包含了Spring缓存功能，提供两个对象：RedissonSpringCacheManager和RedissonSpringLocalCachedCacheManager.RedissonSpringLocalCachedCacheManager支持本地缓存。
&nbsp;&nbsp;&nbsp;&nbsp;下面是一个RedissonSpringLocalCachedCacheManager对象的示例配置：
```java
@Configuration
@ComponentScan
@EnableCaching
public static class Application {
    @Bean(destoryMethod="shutdown")
    RedissonClient redisson() throws IOException {
        Config config = new Config();
        config.useClusterServers().addNodeAddress("127.0.0.1:7004","127.0.0.1:7001");
        return Redisson.create(config);
    }
    @Bean
    CacheManager cacheManager(ResissonClient redissonClient) {
        Map<String, CacheConfig> config = new HashMap<String, CacheConfig>();
        // 新建"testMap"缓存，ttl=24分钟，maxIdleTime=12分钟
        config.put("testMap", new CacheConfig(24*60*1000, 12*60*1000));
        return new RedissonSpringCacheManager(redissonClient, config);
    }
}
```
&nbsp;&nbsp;&nbsp;&nbsp;此外，还可以读取JSON或YAML文件配置RedissonSpringCacheManager。
&nbsp;&nbsp;&nbsp;&nbsp;与RMaps一样，每个RedissonSpringCacheManager实例都有两个重要参数：ttl(生存时间)和maxIdleTime。如果这些参数设为0或者没有定义，那么数据将无限期地保留在缓存中。
<b style="color: orangered">4.JCache</b>
&nbsp;&nbsp;&nbsp;&nbsp;JCache是一个Java缓存API，允许开发人员从缓存临时存储、检索、更新和删除对象。
&nbsp;&nbsp;&nbsp;&nbsp;Redisson提供了Redis的JCache API实现。下面是在Redisson中使用默认配置调用JCache API的示例：
```java
MutableConfiguration<String, String> config = new MutableConfiguration<>();
CacheManager manager = Caching.getCachingProvider().getCacheManager();
Cache<String, String> cache = manager.createCache("nameCache", config);
```
&nbsp;&nbsp;&nbsp;&nbsp;此外，还可以使用自定义配置文件、Redisson Config对象或Redisson RedissonClient对象配置JCache。例如，下面的代码使用自定义Redisson配置来调用JCache：
```java
MutableConfiguration<String, String> jcacheConfig = new MutableConfiguration<>();
Config redissonCfg = ...
Configuration<String, String> config = RedissonConfiguration.fromConfig(redissonCfg, jcacheConfig);
CacheManager manager = Caching.getCachingProvider().getCacheManager();
Cache<String, String> cache = manager.createCache("nameCache", config);
```
&nbsp;&nbsp;&nbsp;&nbsp;Redisson的JCache实现已经通过JCache TCK的所有测试。