---
title: redis内存满了如何处理
date: 2019-11-27 15:23:20
tags: 面试题
---
&nbsp;&nbsp;&nbsp;&nbsp;Redis是基于内存的key-value数据库，因为系统的内存大小有限，所以我们在使用Redis的时候可以配置Redis能使用的最大的内存大小。
<!-- more -->
** 1.通过修改配置文件 **
&nbsp;&nbsp;&nbsp;&nbsp;通过Redis安装目录下面的redis.conf配置文件中添加以下配置设置内存大小(Redis的配置文件不一定使用的是安装目录下面的redis.conf文件，启动redis服务的时候是可以传一个参数指定redis的配置文件)
```
// 设置Redis最大占用内存大小为100M
maxmemory 100mb
```
** 2.通过命令修改 **
&nbsp;&nbsp;&nbsp;&nbsp;Redis支持运行时通过命令动态修改内存大小(如果不设置最大内存大小或者设置最大内存大小为0，在64位操作系统系下不限制内存大小，在32位操作系统下最多使用3GB内存)
```
// 设置Redis最大占用内存大小为100M
127.0.0.1:6379>config set maxmemory 100mb
// 查看设置的Redis能使用的最大内存大小
127.0.0.1:6379>config get maxmemory
```
** 3.Redis的内存淘汰 **
&nbsp;&nbsp;&nbsp;&nbsp;既然可以设置Redis最大占用内存大小，那么配置的内存就有用完的时候。那在内存用完的时候，还继续往Redis里面添加数据不就没有内存可用吗？实际上Redis定义了几种策略来处理这种情况：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>noeviction(默认策略)：</b>对于写请求不再提供服务，直接返回错误(DEL请求和部分特殊请求除外)。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>allkeys-lru:</b>从所有key中使用LRU算法进行淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>volatile-lru:</b>从设置过期时间的key中使用LRU算法进行淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>allkeys-random:</b>从所有key中随机淘汰数据。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>volatile-random:</b>从设置了过期时间的key中随机淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>volatile-ttl:</b>在设置了过期时间的key中，根据key的过期时间进行淘汰，越早过期的越优先被淘汰。
&nbsp;&nbsp;&nbsp;&nbsp;当使用volatile-lru、volatile-random、volatile-ttl这三个策略时，如果没有key可以被淘汰，则和noveiction一样返回错误。
&nbsp;&nbsp;&nbsp;&nbsp;<b>如何获取及设置内存淘汰策略：</b>
```
// 获取当前内存淘汰策略
127.0.0.1:6379>config get maxmemory-policy
// 通过配置文件设置淘汰策略(修改redis.conf文件)
maxmemory-policy allkeys-lru
// 通过命令修改淘汰策略
127.0.0.1:6379>config set maxmemory-policy allkeys-lru
```
** LRU算法 **
&nbsp;&nbsp;&nbsp;&nbsp;LRU(Least Recently Used):即最近最少使用，是一种缓存置换算法。在使用内存作为缓存的时候，缓存的大小一般是固定的。当缓存被占满，这个时候继续往缓存里面添加数据，就需要淘汰一部分老的数据，释放内存空间用来存储新的数据。这个时候就可以使用LRU算法了，其核心思想是：如果一个数据在最近一段时间没有被用到，那么将来被使用到的可能性也很小，所以就可以被淘汰掉。
&nbsp;&nbsp;&nbsp;&nbsp;使用java实现一个简单的LRU算法:
```java
public class LRUCache<K,V> {
	// 容量
	private int capacity;
	// 当前有多少节点的统计
	private int count;
	// 缓存节点
	private Map<K,Node<K,V>> nodeMap;
	private Node<K,V> head;
	private Node<K,V> tail;
	
	public LRUCache(int capacity) {
		if(capacity < 1) {
			throw new IllegalArgumentException(String.valueOf(capacity));
		}
		this.capacity = capacity;
		this.nodeMap = new HashMap<>();
		// 初始化头结点和尾结点，利用哨兵模式减少判断头结点和尾结点为空的代码
		Node headNode = new Node(null, null);
		Node tailNode = new Node(null, null);
		headNode.next = tailNode;
		tailNode.pre = headNode;
		this.head = headNode;
		this.tail = tailNode;
	}
	
	public void put(K key, V value) {
		Node<K, V> node = nodeMap.get(key);
		if(node == null) {
			if(count >= capacity) {
				// 先移除一个结点
				removeNode();
			}
			node = new Node<>(key, value);
			// 添加结点
			addNode(node);
		} else {
			// 移动结点到头结点
			moveNodeToHead(node);
		}
	}
	
	public Node<K, V> get(K key) {
		Node<K, V> node = nodeMap.get(key);
		if(node != null) {
			moveNodeToHead(node);
		}
		return node;
	}
	
	private void removeNode() {
		Node node = tail.pre;
		// 从链表里面移除
		removeFromList(node);
		nodeMap.remove(node.key);
		count--;
	}
	
	private void removeFromList(Node<K, V> node) {
		Node node = node.pre;
		Node next = node.next;
		pre.next = next;
		next.pre = pre;
		node.next = null;
		node.pre = null;
	}
	
	private void addNode(Node<K, V> node) {
		// 添加结点到头部
		addToHead(node);
		nodeMap.put(node.key, node);
		count++;
	}
	
	private void addToHead(Node<K, V> node) {
		Node next = head.next;
		next.pre = node;
		node.next = next;
		node.pre = head;
		head.next = node;
	}
	
	private void moveNodeToHead(Node<K, V> node) {
		// 从链表里面移除
		removeFromList(node);
		// 添加结点到头部
		addToHead(node);
	}
	
	class Node<K, V> {
		K key;
		V value;
		Node pre;
		Node next;
		
		public Node(K key, V value) {
			this.key = key;
			this.value = value;
		}
	}
}
```
** LRU在Redis中的实现 **
&nbsp;&nbsp;&nbsp;&nbsp;<b>近似LRU算法：</b>Redis使用的是近似LRU算法，它跟常规的LRU算法还不大一样。近似LRU算法通过随机采样法淘汰，每次随机出5(默认)个key，从里面淘汰最近最少使用的key。
&nbsp;&nbsp;&nbsp;&nbsp;可以通过maxmemory-samples参数修改采样数量，例如：maxmemory-samples 10,maxmemory-samples配置的越大，淘汰的结果越接近于严格的LRU算法。
&nbsp;&nbsp;&nbsp;&nbsp;Redis为了实现近似LRU算法，给每一个key额外增加了一个24bit的字段，用来存储该key最后一次被访问的时候。
** Redis3.0对近似LRU算法的优化 **
&nbsp;&nbsp;&nbsp;&nbsp;Redis3.0对近似LRU算法进行了一些优化。新算法会维护一个候选池(大小为16)，池中的数据根据访问时间排序，第一次随机选取的key会放入池中，随后每次随机选取的key只有在访问时间小于池中最小的时间才会放入池中，直到候选池被放满。当放满后，如果有新的key需要放入，则将池中最后访问时间最大(最近被访问)的移除。当需要淘汰时，则直接从池中选取最近访问时间最小(最久没被访问)的key淘汰掉。
** LFU算法 **
&nbsp;&nbsp;&nbsp;&nbsp;LFU算法是Redis4.0里面新加的一种淘汰策略、它的全称是Least Frequently Used，它的核心思想是根据key的最近访问的频率进行淘汰，很少被访问的优先被淘汰，被访问的多则被留下来。
&nbsp;&nbsp;&nbsp;&nbsp;LFU算法能更好的表示一个key被访问的热度。假如你使用的是LRU算法，一个key很久没有被访问到，只刚刚偶尔被访问一次，那么它就会被认为是热点数据，不会被淘汰，而有些key将来很有可能被访问到的则被淘汰了。如果使用LFU算法则不会出现这种情况，因为使用一次并不会使一个key成为热点数据。
&nbsp;&nbsp;&nbsp;&nbsp;LFU一共有两种策略：
- volatile-lfu:在设置过期时间的key中使用LFU算法淘汰key。
- allkeys-lfu:在所有key中使用LFU算法淘汰数据。
&nbsp;&nbsp;&nbsp;&nbsp;<label style="color:red">注：设置使用这两种淘汰策略跟前面讲的一样，不过要注意一点的是这两种策略只能在Redis4.0及以上设置，如果在Redis4.0以下设置会报错。</label>