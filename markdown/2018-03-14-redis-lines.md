---
layout: post
title: "redis使用总结"
categories: DB
tags: Redis
author: linkzw
---

* content
{:toc}

## 安装

	wget http://download.redis.io/releases/redis-3.2.0.tar.gz
	tar -zxvf redis-3.2.0.tar.gz
	cd redis-3.2.0
	sudo make clean
	sudo make install

##  简介

Redis提供了丰富的命令（command）对数据库和各种数据类型进行操作，这些command可以在Linux终端使用。在编程时，比如使用Redis 的Java语言包，这些命令都有对应的方法。下面将Redis提供的命令做一总结。

官网命令列表：http://redis.io/commands （英文）

## 1、连接操作相关的命令
	quit：关闭连接（connection）
	auth：简单密码认证

## 2、对value操作的命令

	exists(key)：确认一个key是否存在
	del(key)：删除一个key
	type(key)：返回值的类型
	keys(pattern)：返回满足给定pattern的所有key
	randomkey：随机返回key空间的一个key
	rename(oldname, newname)：将key由oldname重命名为newname，若newname存在则删除newname表示的key
	dbsize：返回当前数据库中key的数目
	expire：设定一个key的活动时间（s）
	ttl：获得一个key的活动时间
	select(index)：按索引查询
	move(key, dbindex)：将当前数据库中的key转移到有dbindex索引的数据库
	flushdb：删除当前选择数据库中的所有key
	flushall：删除所有数据库中的所有key

## 3、对String操作的命令

	set(key, value)：给数据库中名称为key的string赋予值value
	get(key)：返回数据库中名称为key的string的value
	getset(key, value)：给名称为key的string赋予上一次的value
	mget(key1, key2,…, key N)：返回库中多个string（它们的名称为key1，key2…）的value
	setnx(key, value)：如果不存在名称为key的string，则向库中添加string，名称为key，值为value
	setex(key, time, value)：向库中添加string（名称为key，值为value）同时，设定过期时间time
	mset(key1, value1, key2, value2,…key N, value N)：同时给多个string赋值，名称为key i的string赋值value i
	msetnx(key1, value1, key2, value2,…key N, value N)：如果所有名称为key i的string都不存在，则向库中添加string，名称key i赋值为value i
	incr(key)：名称为key的string增1操作
	incrby(key, integer)：名称为key的string增加integer
	decr(key)：名称为key的string减1操作
	decrby(key, integer)：名称为key的string减少integer
	append(key, value)：名称为key的string的值附加value
	substr(key, start, end)：返回名称为key的string的value的子串

## 4、对List操作的命令

	rpush(key, value)：在名称为key的list尾添加一个值为value的元素
	lpush(key, value)：在名称为key的list头添加一个值为value的 元素
	llen(key)：返回名称为key的list的长度
	lrange(key, start, end)：返回名称为key的list中start至end之间的元素（下标从0开始，下同）
	ltrim(key, start, end)：截取名称为key的list，保留start至end之间的元素
	lindex(key, index)：返回名称为key的list中index位置的元素
	lset(key, index, value)：给名称为key的list中index位置的元素赋值为value
	lrem(key, count, value)：删除count个名称为key的list中值为value的元素。count为0，删除所有值为value的元素，count>0从头至尾删除count个值为value的元素，count<0从尾到头删除|count|个值为value的元素。 lpop(key)：返回并删除名称为key的list中的首元素 rpop(key)：返回并删除名称为key的list中的尾元素 blpop(key1, key2,… key N, timeout)：lpop命令的block版本。即当timeout为0时，若遇到名称为key i的list不存在或该list为空，则命令结束。如果timeout>0，则遇到上述情况时，等待timeout秒，如果问题没有解决，则对keyi+1开始的list执行pop操作。
	brpop(key1, key2,… key N, timeout)：rpop的block版本。参考上一命令。
	rpoplpush(srckey, dstkey)：返回并删除名称为srckey的list的尾元素，并将该元素添加到名称为dstkey的list的头部

## 5、对Set操作的命令
	sadd(key, member)：向名称为key的set中添加元素member
	srem(key, member) ：删除名称为key的set中的元素member
	spop(key) ：随机返回并删除名称为key的set中一个元素
	smove(srckey, dstkey, member) ：将member元素从名称为srckey的集合移到名称为dstkey的集合
	scard(key) ：返回名称为key的set的基数
	sismember(key, member) ：测试member是否是名称为key的set的元素
	sinter(key1, key2,…key N) ：求交集
	sinterstore(dstkey, key1, key2,…key N) ：求交集并将交集保存到dstkey的集合
	sunion(key1, key2,…key N) ：求并集
	sunionstore(dstkey, key1, key2,…key N) ：求并集并将并集保存到dstkey的集合
	sdiff(key1, key2,…key N) ：求差集
	sdiffstore(dstkey, key1, key2,…key N) ：求差集并将差集保存到dstkey的集合
	smembers(key) ：返回名称为key的set的所有元素
	srandmember(key) ：随机返回名称为key的set的一个元素

## 6、对zset（sorted set）操作的命令

	zadd(key, score, member)：向名称为key的zset中添加元素member，score用于排序。如果该元素已经存在，则根据score更新该元素的顺序。
	zrem(key, member) ：删除名称为key的zset中的元素member
	zincrby(key, increment, member) ：如果在名称为key的zset中已经存在元素member，则该元素的score增加increment；否则向集合中添加该元素，其score的值为increment
	zrank(key, member) ：返回名称为key的zset（元素已按score从小到大排序）中member元素的rank（即index，从0开始），若没有member元素，返回“nil”
	zrevrank(key, member) ：返回名称为key的zset（元素已按score从大到小排序）中member元素的rank（即index，从0开始），若没有member元素，返回“nil”
	zrange(key, start, end)：返回名称为key的zset（元素已按score从小到大排序）中的index从start到end的所有元素
	zrevrange(key, start, end)：返回名称为key的zset（元素已按score从大到小排序）中的index从start到end的所有元素
	zrangebyscore(key, min, max)：返回名称为key的zset中score >= min且score <= max的所有元素 zcard(key)：返回名称为key的zset的基数 zscore(key, element)：返回名称为key的zset中元素element的score zremrangebyrank(key, min, max)：删除名称为key的zset中rank >= min且rank <= max的所有元素 zremrangebyscore(key, min, max) ：删除名称为key的zset中score >= min且score <= max的所有元素
	zunionstore / zinterstore(dstkeyN, key1,…,keyN, WEIGHTS w1,…wN, AGGREGATE SUM|MIN|MAX)：对N个zset求并集和交集，并将最后的集合保存在dstkeyN中。对于集合中每一个元素的score，在进行AGGREGATE运算前，都要乘以对于的WEIGHT参数。如果没有提供WEIGHT，默认为1。默认的AGGREGATE是SUM，即结果集合中元素的score是所有集合对应元素进行SUM运算的值，而MIN和MAX是指，结果集合中元素的score是所有集合对应元素中最小值和最大值。

## 7、对Hash操作的命令

	hset(key, field, value)：向名称为key的hash中添加元素field<—>value
	hget(key, field)：返回名称为key的hash中field对应的value
	hmget(key, field1, …,field N)：返回名称为key的hash中field i对应的value
	hmset(key, field1, value1,…,field N, value N)：向名称为key的hash中添加元素field i<—>value i
	hincrby(key, field, integer)：将名称为key的hash中field的value增加integer
	hexists(key, field)：名称为key的hash中是否存在键为field的域
	hdel(key, field)：删除名称为key的hash中键为field的域
	hlen(key)：返回名称为key的hash中元素个数
	hkeys(key)：返回名称为key的hash中所有键
	hvals(key)：返回名称为key的hash中所有键对应的value
	hgetall(key)：返回名称为key的hash中所有的键（field）及其对应的value
	
## 8、持久化

	save：将数据同步保存到磁盘
	bgsave：将数据异步保存到磁盘
	lastsave：返回上次成功将数据保存到磁盘的Unix时戳
	shundown：将数据同步保存到磁盘，然后关闭服务


## 9、远程服务控制
	info：提供服务器的信息和统计
	monitor：实时转储收到的请求
	slaveof：改变复制策略设置
	config：在运行时配置Redis服务器

## Redis高级应用
### 1、安全性
	设置客户端连接后进行任何操作指定前需要密码，一个外部用户可以再一秒钟进行150W次访问，具体操作密码修改设置redis.conf里面的requirepass属性给予密码，当然我这里给的是primos
	之后如果想操作可以采用登陆的时候就授权使用:
	sudo /opt/java/redis/bin/redis-cli -a primos
	或者是进入以后auth primos然后就可以随意操作了

### 2、主从复制

	做这个操作的时候我准备了两个虚拟机，ip分别是192.168.15.128和192.168.15.133
	通过主从复制可以允许多个slave server拥有和master server相同的数据库副本
	具体配置是在slave上面配置slave
	slaveof 192.168.15.128 6379
	masterauth primos
	如果没有主从同步那么就检查一下是不是防火墙的问题，我用的是ufw，设置一下sudo ufw allow 6379就可以了
	这个时候可以通过info查看具体的情况

### 3、事务处理

	redis对事务的支持还比较简单，redis只能保证一个client发起的事务中的命令可以连续执行，而中间不会插入其他client的命令。当一个client在一个连接中发出multi命令时，这个连接会进入一个事务的上下文，连接后续命令不会立即执行，而是先放到一个队列中，当执行exec命令时，redis会顺序的执行队列中的所有命令。
	比如我下面的一个例子
	set age 100
	multi
	set age 10
	set age 20
	exec
	get age –这个内容就应该是20
	multi
	set age 20
	set age 10
	exec
	get age –这个时候的内容就成了10，充分体现了一下按照队列顺序执行的方式
	discard 取消所有事务，也就是事务回滚
	不过在redis事务执行有个别错误的时候，事务不会回滚，会把不错误的内容执行，错误的内容直接放弃，目前最新的是2.6.7也有这个问题的
	乐观锁
	watch key如果没watch的key有改动那么outdate的事务是不能执行的

### 4、持久化机制
	redis是一个支持持久化的内存数据库
	snapshotting快照方式，默认的存储方式，默认写入dump.rdb的二进制文件中，可以配置redis在n秒内如果超过m个key被修改过就自动做快照
	append-only file aof方式，使用aof时候redis会将每一次的函 数都追加到文件中，当redis重启时会重新执行文件中的保存的写命
	令在内存中。
### 5、发布订阅消息 sbusribe publish操作，其实就类似linux下面的消息发布

### 6、虚拟内存的使用
	可以配置vm功能，保存路径，最大内存上线，页面多少，页面大小，最大工作线程
	临时修改ip地址ifconfig eth0 192.168.15.129

## redis-cli参数
	Usage: redis-cli [OPTIONS] [cmd [arg [arg …]]]
	-h Server hostname (default: 127.0.0.1)
	-p Server port (default: 6379)
	-s Server socket (overrides hostname and port)
	-a Password to use when connecting to the server
	-r Execute specified command N times
	-i When -r is used, waits seconds per command.
	It is possible to specify sub-second times like -i 0.1
	-n Database number
	-x Read last argument from STDIN
	-d Multi-bulk delimiter in for raw formatting (default: \n)
	-c Enable cluster mode (follow -ASK and -MOVED redirections)
	–raw Use raw formatting for replies (default when STDOUT is not a tty)
	–latency Enter a special mode continuously sampling latency
	–slave Simulate a slave showing commands received from the master
	–pipe Transfer raw Redis protocol from stdin to server
	–bigkeys Sample Redis keys looking for big keys
	–eval Send an EVAL command using the Lua script at
	–help Output this help and exit
	–version Output version and exit

	Examples:
	cat /etc/passwd | redis-cli -x set mypasswd
	redis-cli get mypasswd
	redis-cli -r 100 lpush mylist x
	redis-cli -r 100 -i 1 info | grep used_memory_human:
	redis-cli –eval myscript.lua key1 key2 , arg1 arg2 arg3
	(Note: when using –eval the comma separates KEYS[] from ARGV[] items)

## 常用命令：

### 1） 查看keys个数
	keys * // 查看所有keys
	keys prefix_* // 查看前缀为”prefix_”的所有keys

### 2） 清空数据库
	
	flushdb // 清除当前数据库的所有keys
	flushall // 清除所有数据库的所有keys


> http://blog.csdn.net/guochunyang/article/details/47317851

* Keys 

redis本质上一个key-value db，所以我们首先来看看他的key. 
首先key也是字符串类型，但是key中不能包括边界字符；由于key不是binary safe的字符串，所以像”my key”和”mykey\n”这样包含空格和换行的key是不允许的 
	
	注： 
	顺便说一下在redis内部并不限制使用binary字符，这是redis协议限制的。”\r\n”在协议格式中会作为特殊字符。 
	redis 1.2以后的协议中部分命令已经开始使用新的协议格式了(比如MSET)。总之目前还是把包含边界字符当成非法的key吧，免得被bug纠缠。 
	另外关于key的一个格式约定介绍下，object-type:id:field。比如user:1000:password，blog:xxidxx:title 
	还有key的长度最好不要太长。道理很明显占内存啊，而且查找时候相对短key也更慢。 
	不过也推荐过短的key，比如u:1000:pwd,这样的。显然没上面的user:1000:password可读性好。

* String 类型 

	string是redis最基本的类型，而且string类型是二进制安全的。 
	意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象。从内部实现来看其实string可以看作byte数组，最大上限是1G字节。 
	下面是string类型的定义。 
	
	struct sdshdr { 
		long len; 
		long free; 
		char buf[]; 
	}; 
	
	buf是个char数组用于存贮实际的字符串内容。其实char和c#中的byte是等价的，都是一个字节 
	len是buf数组的长度，free是数组中剩余可用字节数。 
	由此可以理解为什么string类型是二进制安全的了。因为它本质上就是个byte数组。当然可以包含任何数据了。 
	另外string类型可以被部分命令按int处理.比如incr等命令， 
	redis的其他类型像list,set,sorted set ，hash它们包含的元素与都只能是string类型。 
	如果只用string类型，redis就可以被看作加上持久化特性的memcached.当然redis对string类型的操作比memcached多很多啊。

* List 类型 

	redis的list类型其实就是一个每个子元素都是string类型的双向链表。所以[lr]push和[lr]pop命令的算法时间复杂度都是O 
	另外list会记录链表的长度。所以llen操作也是O.链表的最大长度是(2的32次方-1)。 
	我们可以通过push,pop操作从链表的头部或者尾部添加删除元素。这使得list既可以用作栈，也可以用作队列。 
	有意思的是list的pop操作还有阻塞版本的。当我们[lr]pop一个list对象，如果list是空，或者不存在，会立即返回nil。但是阻塞版本的b[lr]pop可以则可以阻塞， 
	当然可以加超时时间，超时后也会返回nil。为什么要阻塞版本的pop呢，主要是为了避免轮询。 
	例子如果我们用list来实现一个工作队列。执行任务的thread可以调用阻塞版本的pop去 
	获取任务这样就可以避免轮询去检查是否有任务存在。当任务来时候工作线程可以立即返回，也可以避免轮询带来的延迟。

* Set 类型 

	redis的set是string类型的无序集合。set元素最大可以包含(2的32次方-1)个元素。 
	set的是通过hash table实现的，所以添加，删除，查找的复杂度都是O。hash table会随着添加或者删除自动的调整大小。 
	需要注意的是调整hash table大小时候需要同步（获取写锁）会阻塞其他读写操作。 
	可能不久后就会改用跳表（skip list）来实现跳表已经在sorted set中使用了。 
	关于set集合类型除了基本的添加删除操作，其他有用的操作还包含集合的取并集(union)，交集(intersection)， 
	差集(difference)。

* Sorted Set 类型 

	和set一样sorted set也是string类型元素的集合，不同的是每个元素都会关联一个double类型的score。sorted set的实现是skip list和hash table的混合体

	当元素被添加到集合中时，一个元素到score的映射被添加到hash table中，所以给定一个元素获取score的开销是O, 
	另一个score到元素的映射被添加到skip list并按照score排序，所以就可以有序的获取集合中的元素。 
	添加，删除操作开销都是O和skip list的开销一致,redis的skip list实现用的是双向链表,这样就可以逆序从尾部取元素。 
	sorted set最经常的使用方式应该是作为索引来使用.我们可以把要排序的字段作为score存储，对象的id当元素存储。

* Hash Set 类型 

	redis hash是一个string类型的field和value的映射表.它的添加，删除操作都是O.hash特别适合用于存储对象。 
	相较于将对象的每个字段存成单个string类型。将一个对象存储在hash类型中会占用更少的内存，并且可以更方便的存取整个对象。

	省内存的原因是新建一个hash对象时开始是用zipmap（又称为small hash）来存储的。 
	这个zipmap其实并不是hash table，但是zipmap相比正常的hash实现可以节省不少hash本身需要的一些元数据存储开销。 
	尽管zipmap的添加，删除，查找都是O(n)，但是由于一般对象的field数量都不太多。 
	所以使用zipmap也是很快的,也就是说添加删除平均还是O(1)。 
	如果field或者value的大小超出一定限制后，redis会在内部自动将zipmap替换成正常的hash实现. 
	这个限制可以在配置文件中指定 
	hash-max-zipmap-entries 64 #配置字段最多64个 
	hash-max-zipmap-value 512 #配置value最大为512字节

## 命令： 

### key相关的命令 

	exists key 							
	测试指定key是否存在，返回1表示存在，0不存在 
	
	del key1 key2 ….keyN 				
	删除给定key,返回删除key的数目，0表示给定key都不存在 
	
	type key 							
	返回给定key的value类型。返回 none 表示不存在key,string字符类型，list 链表类型 set 无序集合类型… 
	
	keys pattern 						
	返回匹配指定模式的所有key,下面给个例子 
	
	randomkey 							
	返回从当前数据库中随机选择的一个key,如果当前数据库是空的，返回空串 
	
	rename oldkey newkey 				
	原子的重命名一个key,如果newkey存在，将会被覆盖，返回1表示成功，0失败。可能是oldkey不存在或者和newkey相同 
	
	renamenx oldkey newkey 				
	同上，但是如果newkey存在返回失败 
	
	dbsize 								
	返回当前数据库的key数量 
	
	expire key seconds 					
	为key指定过期时间，单位是秒。返回1成功，0表示key已经设置过过期时间或者不存在 
	
	ttl key 							
	返回设置过过期时间的key的剩余过期秒数 -1表示key不存在或者没有设置过过期时间 
	
	select db-index 					
	通过索引选择数据库，默认连接的数据库所有是0,默认数据库数是16个。返回1表示成功，0失败 
	
	move key db-index 					
	将key从当前数据库移动到指定数据库。返回1成功。0 如果key不存在，或者已经在指定数据库中 
	
	flushdb 							
	删除当前数据库中所有key,此方法不会失败。慎用 
	
	flushall 							
	删除所有数据库中的所有key，此方法不会失败。更加慎用

### String相关的命令 

	set key value 						
	设置key对应的值为string类型的value,返回1表示成功，0失败 
	
	setnx key value 					
	同上，如果key已经存在，返回0 。nx 是not exist的意思 
	
	get key 							
	获取key对应的string值,如果key不存在返回nil 
	
	getset key value 					
	原子的设置key的值，并返回key的旧值。如果key不存在返回nil 
	
	mget key1 key2 … keyN 				
	一次获取多个key的值，如果对应key不存在，则对应返回nil。 
	
	mset key1 value1 … keyN valueN 		
	一次设置多个key的值，成功返回1表示所有的值都设置了，失败返回0表示没有任何值被设置 
	
	msetnx key1 value1 … keyN valueN 	
	同上，但是不会覆盖已经存在的key 
	
	incr key 							
	对key的值做加加操作,并返回新的值。注意incr一个不是int的value会返回错误，incr一个不存在的key，则设置key为1 
	
	decr key 							
	同上，但是做的是减减操作，decr一个不存在key，则设置key为-1 
	
	incrby key integer 					
	同incr，加指定值 ，key不存在时候会设置key，并认为原来的value是 0 
	
	decrby key integer 					
	同decr，减指定值。decrby完全是为了可读性，我们完全可以通过incrby一个负值来实现同样效果，反之一样。 
	
	append key value 					
	给指定key的字符串值追加value,返回新字符串值的长度。 
	
	substr key start end 				
	返回截取过的key的字符串值,注意并不修改key的值。

### List相关的命令 

	lpush key string 					
	在key对应list的头部添加字符串元素，返回1表示成功，0表示key存在且不是list类型 
	
	rpush key string 					
	同上，在尾部添加 
	
	llen key 							
	返回key对应list的长度，key不存在返回0,如果key对应类型不是list返回错误 
	
	lrange key start end 				
	返回指定区间内的元素，下标从0开始，负值表示从后面计算，-1表示倒数第一个元素 ，key不存在返回空列表 
	
	ltrim key start end 				
	截取list，保留指定区间内元素，成功返回1，key不存在返回错误 
	
	lset key index value 				
	设置list中指定下标的元素值，成功返回1，key或者下标不存在返回错误 
	
	lrem key count value 				
	从key对应list中删除count个和value相同的元素。count为0时候删除全部 

	lpop key 							
	从list的头部删除元素，并返回删除元素。如果key对应list不存在或者是空返回nil，如果key对应值不是list返回错误 

	rpop 								
	同上，但是从尾部删除 

	blpop key1…keyN timeout 			
	从左到右扫描返回对第一个非空list进行lpop操作并返回，比如blpop list1 list2 list3 0 ,如果list不存在list2,list3都是非空则对list2做lpop并返回从list2中删除的元素。如果所有的list都是空或不存在，则会阻塞timeout秒，timeout为0表示一直阻塞。 
	当阻塞时，如果有client对key1…keyN中的任意key进行push操作，则第一在这个key上被阻塞的client会立即返回。如果超时发生，则返回nil。有点像unix的select或者poll 

	brpop 								
	同blpop，一个是从头部删除一个是从尾部删除 

	rpoplpush srckey destkey 			
	srckey对应list的尾部移除元素并添加到destkey对应list的头部,最后返回被移除的元素值，整个操作是原子的.如果srckey是空或者不存在返回nil

### Set相关的命令 

	sadd key member 					
	添加一个string元素到,key对应的set集合中，成功返回1,如果元素以及在集合中返回0,key对应的set不存在返回错误 
	
	srem key member 					
	key对应set中移除给定元素，成功返回1，如果member在集合中不存在或者key不存在返回0，如果key对应的不是set类型的值返回错误 
	
	spop key 							
	删除并返回key对应set中随机的一个元素,如果set是空或者key不存在返回nil 
	
	srandmember key 					
	同spop，随机取set中的一个元素，但是不删除元素 
	
	smove srckey dstkey member 			
	从srckey对应set中移除member并添加到dstkey对应set中，整个操作是原子的。成功返回1,如果member在srckey中不存在返回0，如果key不是set类型返回错误 
	
	scard key 
	返回set的元素个数，如果set是空或者key不存在返回0 
	
	sismember key member 
	判断member是否在set中，存在返回1，0表示不存在或者key不存在 
	
	sinter key1 key2…keyN 
	返回所有给定key的交集 
	
	sinterstore dstkey key1…keyN 
	同sinter，但是会同时将交集存到dstkey下 
	
	sunion key1 key2…keyN 
	返回所有给定key的并集 
	
	sunionstore dstkey key1…keyN 
	同sunion，并同时保存并集到dstkey下 

	sdiff key1 key2…keyN 
	返回所有给定key的差集 

	sdiffstore dstkey key1…keyN 
	同sdiff，并同时保存差集到dstkey下 

	smembers key 
	返回key对应set的所有元素，结果是无序的

### Sorted Set相关的命令 

	zadd key score member 
	添加元素到集合，元素在集合中存在则更新对应score 
	
	zrem key member 
	删除指定元素，1表示成功，如果元素不存在返回0 
	
	zincrby key incr member 
	增加对应member的score值，然后移动元素并保持skip list保持有序。返回更新后的score值 
	
	zrank key member 
	返回指定元素在集合中的排名（下标）,集合中元素是按score从小到大排序的 
	
	zrevrank key member 
	同上,但是集合中元素是按score从大到小排序 
	
	zrange key start end 
	类似lrange操作从集合中去指定区间的元素。返回的是有序结果 
	
	zrevrange key start end 
	同上，返回结果是按score逆序的 
	
	zrangebyscore key min max 
	返回集合中score在给定区间的元素 
	
	zcount key min max 
	返回集合中score在给定区间的数量 
	
	zcard key 
	返回集合中元素个数 
	
	zscore key element 
	返回给定元素对应的score 

	zremrangebyrank key min max 
	删除集合中排名在给定区间的元素

	zremrangebyscore key min max 
	删除集合中score在给定区间的元素

Hash相关命令 

	hset key field value 
	设置hash field为指定值，如果key不存在，则先创建 
	
	hget key field 
	获取指定的hash field 
	
	hmget key filed1….fieldN 
	获取全部指定的hash filed 

	hmset key filed1 value1 … filedN valueN 
	同时设置hash的多个field 
	
	hincrby key field integer 
	将指定的hash filed 加上给定值 
	
	hexists key field 
	测试指定field是否存在 

	hdel key field 
	删除指定的hash field 

	hlen key 
	返回指定hash的field数量 

	hkeys key 
	返回hash的所有field 

	hvals key 
	返回hash的所有value 

	hgetall 
	返回hash的所有filed和value