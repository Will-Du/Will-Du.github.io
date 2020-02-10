---
title: 多数微服务为什么选择Zookeeper
date: 2020-02-10 19:13:06
tags: 分布式
---
了解微服务的小伙伴都应该知道<b>Zookeeper</b>,<b>Zookeeper</b>是一个分布式的、开源的分布式应用程序协调服务。
现在比较流行的微服务框架Dubbo、Spring Cloud都可以使用Zookeeper作为服务发现与注册中心。但是，为什么Zookeeper就能实现服务发现和注册呢？
<!-- more -->
<b style="color: orangered">Zookeeper的特性</b>
&nbsp;&nbsp;&nbsp;&nbsp;我们先了解一下<b>Zookeeper</b>的特性吧，因为它的特性决定了它的使用场景。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1.树状目录结构</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>Zookeeper</b>是一个树状的文件目录结构，有点像应用系统中的文件系统的概念。每个子目录被称为znode，我们可以对每个znode进行增删改查。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2.持久节点(Persistent)</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;与<b>Zookeeper</b>服务端断开连接后，该节点仍然存在。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">3.持久有序节点(Persistent_sequential)</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在持久节点基础上，有zookeeper给该节点名称进行有序编号，如0000001,0000002。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">4.临时节点(Ephemeral)</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;与<b>Zookeeper</b>服务端断开连接后，该节点被删除。临时节点下，不存在子节点。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">5.临时有序节点(Ephemeral_sequential)</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在临时节点的基础上，由<b>Zookeeper</b>给该节点进行有序编号，如0000001,0000002。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">6.节点监听(Wacher)</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;客户端2注册监听它关心的临时节点SbuApp1的变化，当临时节点SubApp1发生变化时(如被删除的时候)，zookeeper会通知客户端2。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该机制是zookeeper实现分布式协调的重要特性。我们可以通过get、exists、getChildren三种方式对某个节点进行监听。但是该事件只会通知一次。
<b style="color: orangered">微服务中应用场景</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1.分布式锁</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;分布式锁主要解决不同进程中的资源同步问题。大家可以联想一下单进程中多线程共享资源的情况，线程需要访问共享资源，首先要获得锁，操作完共享资源后便释放锁。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;分布式中，上述的锁就变成了分布式锁。那这个分布式锁又是如何实现呢？
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;步骤1：根据zookeeper有序临时节点的特性，每个进程连接一个有序临时节点(进程1对应节点/znode/00000001，进程2对应节点/znode/00000002...以此类推)。每个进程监听对应的上一个节点的变化。编号最小的节点对应的线程获得锁，可以操作资源。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;步骤2：当进程1完成业务后，删除对应的子节点/znode/00000001，释放锁。此时，编号最小的锁便获得锁(即/znode/00000002对应进程)。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;重复以上步骤，保证了多个线程获取的是同一个锁，且只有一个进程能获得锁，就是<b>Zookeeper</b>分布式锁的实现原理。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2.服务注册与发现</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">2.1背景</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在微服务中，服务提供方把服务注册到<b>Zookeeper</b>中心去，但是每个应用可能拆分成多个服务对应不同的IP地址，<b>Zookeeper</b>注册中心可以动态感知到服务节点的变化。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;服务消费方需要调用提供方提供的服务时，从<b>Zookeeper</b>中获取提供方的调用地址列表，然后进行调用。这个过程成为服务的订阅。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">2.2服务注册原理</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;RPC框架会在<b>Zookeeper</b>的注册目录下，为每个应用创建一个持久节点，然后在对应的持久节点下，为每个微服务创建一个临时节点，记录每个服务的URL信息。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">2.3服务动态发现原理</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;由于服务消费方向Zookeeper订阅了(监听)服务提供方，一旦服务提供方有变动的时候(增加服务或者减少服务)，Zookeeper就会把最新的服务提供方列表推送给服务消费方，这就是服务动态发现原理。