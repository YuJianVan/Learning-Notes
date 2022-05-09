## Redis常用命令

~~~
Redis -cli

key *		查询所有键

expire name time(s) 设置键过期时间为time，单位为s

ttl key  查看key的剩余过期时间

exists key  判断是否存在键

move key  移除键

type key  key的类型

info replication 查看详细信息`

~~~



## Redis的数据类型

> #### Redis五种数据类型：string、hash、list、set、zset



### 公用命令

> - del key
> - dump key：序列化给定key，返回被序列化的值
> - exists key：检查key是否存在
> - expire key second：为key设定过期时间,以秒计算,可以不写second，默认为秒
> - ttl key：返回key剩余时间,-1为永久,-2为失效
> - persist key：移除key的过期时间，key将持久保存
> - keys pattern：查询所有符号给定模式的key eg：keys *
> - randomkey：随机返回一个key
> - rename key newkey：修改key的名称
> - move key db：移动key至指定数据库中 eg:move a 1
> - type key：返回key所储存的值的类型

> expirekey second的**使用场景**：
> 1、限时的优惠活动
> 2、网站数据缓存
> 3、手机验证码
> 4、限制网站访客频率

### key的命名建议

>1. key不要太长，尽量不要超过1024字节。不仅消耗内存，也会降低查找的效率
>2. key不要太短，太短可读性会降低
>3. 在一个项目中，key最好使用统一的命名模式，如user:123:password
>4. key区分大小写



### String

> string类型是二进制安全的，redis的string可以包含任何数据，如图像、序列化对象。一个键最多能存储512MB。`二进制安全是指，在传输数据的时候，能保证二进制数据的信息安全，也就是不会被篡改、破译；如果被攻击，能够及时检测出来 `

> - **set key_name value**：命令不区分大小写，但是key_name区分大小写
> - **setnx key value**：当key不存在时设置key的值。（SET if Not eXists）,分布式锁的问题
> - setex：创建一个key，并且设置他的过期时间
> - get key_name
> - **getrange** key start end：获取key中字符串的子字符串，从start开始，end结束
> - setrange key offset value：设置从offset往后的值
> - mget key1 [key2 …]：获取多个key
> - getset key_name value：返回key的旧值,并设定key的值。当key不存在，返回nil
> - strlen key：返回key所存储的字符串的长度
> - **incr key_name** ：INCR命令key中存储的值+1,如果不存在key，则key中的值话先被初始化为0再加1
> - **INCRBY KEY_NAME** 增量
> - **DECR KEY_NAME**：key中的值自减一
> - **DECRBY KEY_NAME**
> - append key_name value：字符串拼接，追加至末尾，如果不存在，为其赋值

> **String应用场景**：
> 1、String通常用于保存单个字符串或JSON字符串数据
> 2、因为String是二进制安全的，所以可以把保密要求高的图片文件内容作为字符串来存储
> 3、计数器：常规Key-Value缓存应用，如微博数、粉丝数。INCR本身就具有原子性特性，所以不会有线程安全问题



### hash

Redis hash是一个string类型的field和value的映射表，**hash特别适用于存储对象**。每个hash可以存储**232-1**(40亿左右)键值对。可以看成KEY和VALUE的MAP容器。相比于JSON，hash占用很少的内存空间。

***常用命令***

> - hset key_name field value：为指定的key设定field和value
> - hmset key field value[field1,value1]
> - hsetnx：当不存在才创建该field
> - hget key field
> - hmget key field[field1]
> - hgetall key：返回hash表中所有字段和值
> - hkeys key：获取hash表所有字段
> - hvals key：获取hash表所有值
> - hlen key：获取hash表中的字段数量
> - hdel key field [field1]：删除一个或多个hash表的字段
> - hexists：在key里面是否存在指定的field
> - hincrby key field increment：增加某个field的值

***应用场景***

> Hash的应用场景，通常用来存储一个用户信息的对象数据。
>
> 1. 相比于存储对象的string类型的json串，json串修改单个属性需要将整个值取出来。而hash不需要。
> 2. 相比于多个key-value存储对象，hash节省了很多内存空间
> 3. 如果hash的属性值被删除完，那么hash的key也会被redis删除



### list

类似于Java中的LinkedList。

***常用命令***

> - lpush key value1 [value2]：从左侧插入，右边的先出,**相当于一个栈**
> - eg:lpush list 1 2 3 lrange list 0 -1 输出：3 2 1
>
> - rpush key value1 [value2]: 从右侧插入，左边的先出
> - eg:rpush list 1 2 3 lrange list 0 -1 输出：1 2 3
>
> - lpushx key value：从左侧插入值，如果list不存在，则不操作
> - rpushx key value：从右侧插入值，如果list不存在，则不操作
> - llen key：获取列表长度
> - lindex key index：获取指定索引的元素,从零开始
> - lrange key start stop：获取列表指定范围的元素
> - lpop key ：从左侧移除第一个元素
> - prop key：移除列表最后一个元素
> - irem:删除指定个数的同一元素
> - eg:irem list 2 3 删掉了集合中的两个三
>
> - blpop key [key1] timeout：移除并获取列表第一个元素，如果列表没有元素会阻塞列表到等待超时或发现可弹出元素为止
> - brpop key [key1] timeout：移除并获取列表最后一个元素，如果列表没有元素会阻塞列表到等待超时或发现可弹出元素为止
> - ltrim key start stop ：对列表进行修改，让列表只保留指定区间的元素，不在指定区间的元素就会被删除
> - eg:list1中元素1 2 3 4 5 ltrim list1 2 3 list1剩余元素:3 4
>
> - lset key index value ：指定索引的值
> - linsert key before|after world value：在列表元素前或则后插入元素

***应用场景***

> 1. 对数据大的集合数据删减
>
>    &nbsp;&nbsp; 列表显示、关注列表、粉丝列表、留言评价...分页、热点新闻等
>
> 2. 任务队列
>    &nbsp;&nbsp; list通常用来实现一个消息队列，而且可以确保先后顺序，不必像MySQL那样通过order by来排序

> 补充：
>
> - rpoplpush list1 list2 移除list1最后一个元素，并将该元素添加到list2并返回此元素
>   用此命令可以实现订单下单流程、用户系统登录注册短信等。

**性能总结**

> 它是一个字符串链表，left、right都可以插入添加；
> 如果键不存在，创建新的链表；
> 如果键已存在，新增内容；
> 如果值全移除，对应的键也就消失了。
> 链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了。



### set

唯一、无序

> - sadd key value1[value2]：向集合添加成员
>
> - scard key：返回集合成员数
>
> - smembers key：返回集合中所有成员
>
> - sismember key member：判断memeber元素是否是集合key成员的成员
>
> - srandmember key [count]：返回集合中一个或多个随机数
>
> - srem key member1 [member2]：移除集合中一个或多个成员
>
> - spop key：移除并返回集合中的一个随机元素
>
> - smove source destination member：将member元素从source集合移动到destination集合
>
> - sdiff key1 [key2]：返回给定的第一个集合和其他集合的差集(即在key1中的值而在其他key中找不到)
>
> - sdiffstore destination key1[key2]：返回给定的第一个集合与其他的集合的差集并存储在destination中
>
>   eg：set1：1 2 3   set2：3 4 5 6   sdiffstore set3 set1 set2   smembers set3  result：1 2
>
> - sinter key1 [key2]:返回所有集合的交集
>
> - sunion key1 [key2]：返回所有集合的并集

> 对两个集合间的数据[计算]进行交集、并集、差集运算
> 1、以非常方便的实现如共同关注、共同喜好、二度好友等功能。对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存储到一个新的集合中。
> 2、利用唯一性，可以统计访问网站的所有独立 IP



### zset

有序且不重复。每个元素都会关联一个double类型的分数，Redis通过分数进行从小到大的排序。分数可以重复

> - zadd key score1 memeber1
> - zcard key ：获取集合中的元素数量
> - zcount key min max 计算在有序集合中指定区间分数的成员数
> - zcount key min max 计算在有序集合中指定区间分数的成员数
> - zrange key start stop 指定输出索引范围内的成员
> - zrangebyscore key min max 指定输出score区间内的成员
>
> - zrank key member：返回有序集合指定成员的索引
> - zrevrange key start stop ：返回有序集中指定区间内的成员，通过索引，分数从高到底
> - zrem key member [member …] 移除有序集合中的一个或多个成员
> - zremrangebyrank key start stop 移除有序集合中给定的索引区间的所有成员(第一名是0)(低到高排序）
> - zremrangebyscore  key min max 移除有序集合中给定的分数区间的所有成员

> 常用于排行榜：
>
> 1. 如推特可以以发表时间作为score来存储
> 2. 存储成绩
> 3. 还可以用zset来做带权重的队列，让重要的任务先执行







## 持久化

>#### Redis持久化的方式有RDB和AOF



### RDB

#### 介绍

>##### &nbsp;在指定的时间间隔内生成内存中整个数据集的持久化快照。快照文件默认被存储在当前文件夹中，名称为`dump.rdb`，可以通过dir和dbfilename参数来修改默认值。
>
>##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。&nbsp;&nbsp;整个过程中，主进程是不进行任何的IO操作的，这就确保了极高的性能。
>
>

#### Fork

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fork的作用相当于复制一个与当前进程一样的进程。但是是一个全新的进程，并作为原进程的子进程。

#### 配置文件

```shell
# redis是基于内存的数据库，可以通过设置该值定期写入磁盘。
# 注释掉“save”这一行配置项就可以让保存数据库功能失效
# 900秒（15分钟）内至少1个key值改变（则进行数据库保存--持久化） 
# 300秒（5分钟）内至少10个key值改变（则进行数据库保存--持久化） 
# 60秒（1分钟）内至少10000个key值改变（则进行数据库保存--持久化）
save 900 1
save 300 10
save 60 10000
 
#当RDB持久化出现错误后，是否依然进行继续进行工作，yes：不能进行工作，no：可以继续进行工作，可以通过info中的rdb_last_bgsave_status了解RDB持久化是否有错误
stop-writes-on-bgsave-error yes
 
#使用压缩rdb文件，rdb文件压缩使用LZF压缩算法，yes：压缩，但是需要一些cpu的消耗。no：不压缩，需要更多的磁盘空间
rdbcompression yes
 
#是否校验rdb文件。从rdb格式的第五个版本开始，在rdb文件的末尾会带上CRC64的校验和。这跟有利于文件的容错性，但是在保存rdb文件的时候，会有大概10%的性能损耗，所以如果你追求高性能，可以关闭该配置。
rdbchecksum yes
 
#rdb文件的名称
dbfilename dump.rdb
 
#数据目录，数据库的写入会在这个目录。rdb、aof文件也会写在这个目录
dir /data
```

#### 触发条件

1. 通过配制文件中的save条件（可自己配置）

   ```shell
   save 900 1
   save 300 10
   save 60 10000
   ```

2. 手动通过save和bgsave命令

- save：save时只管保存，其他不管，全部阻塞
- bgsave：redis会在后台异步的进行快照操作，同时还可以响应客户端请求。可以通过lastsave命令获取最后一次成功执行快照的事件

3. 通过flushall命令，也会产生dump.rdb文件，但是里面是空的，无意义。
4. 通过shutdown命令，安全退出，也会生成快照文件（和异常退出形成对比，比如：kill杀死进程的方式）



#### 如何恢复

```shell
appendonly no
dbfilename dump.rdb
dir /var/lib/redis  #可以自行指定
```

`appendonly 设置成no`，redis启动时会把/var/lib/redis 目录下的dump.rdb 中的数据恢复。dir 和dbfilename 都可以设置。我测试时`appendonly 设置成yes 时候不会将dump.rdb文件中的数据恢复`。



#### 优势

1. 恢复数据的速度很快，适合大规模的数据恢复，而又对部分数据不敏感的情况
2. dump.db文件是一个压缩的二进制文件，文件暂用空间小

#### 劣势

1. 当出现异常退出时，会丢失**最后一次**快照后的数据
2. 当fork的时候，内存的中的数据会被克隆一份，大致两倍的膨胀需要考虑。而且，当数据过大时，fork操作占用过多的系统资源，造成主服务器进程假死。

#### 使用场景

1. 数据备份 
2. 可容忍部分数据丢失 
3. 跨数据中心的容灾备份



### AOF

#### 介绍

> ##### 以日志的形式来记录每个写操作，将Redis执行过的所有写指令记录下来（读操作不可记录），只许追加文件但不可以改写文件，redis启动之初会读取改文件重新构建数据。保存的是appendonly.aof文件

> ##### **aof机制默认关闭，可以通过`appendonly = yes`参数开启aof机制，通过`appendfilename = myaoffile.aof`指定aof文件名称。**

aof持久化的一些策略配置

```shell
#aof持久化策略的配置
#no表示不执行fsync，由操作系统保证数据同步到磁盘，速度最快。
#always表示每次写入都执行fsync，以保证数据同步到磁盘。
#everysec表示每秒执行一次fsync，可能会导致丢失这1s数据。
appendfsync everysec
```

对于触发aof重写机制也可以通过配置文件来进行设置：

```shell
# aof自动重写配置。当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，即当aof文件增长到一定大小的时候Redis能够调用bgrewriteaof对日志文件进行重写。当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100）时，自动启动新的日志重写过程。
auto-aof-rewrite-percentage 100
# 设置允许重写的最小aof文件大小，避免了达到约定百分比但尺寸仍然很小的情况还要重写
auto-aof-rewrite-min-size 64mb
```

当aop重写时会引发重写和持久化追加同时发生的问题，可以通过`no-appendfsync-on-rewrite no`进行配置

```shell
# 在aof重写或者写入rdb文件的时候，会执行大量IO，此时对于everysec和always的aof模式来说，执行fsync会造成阻塞过长时间，no-appendfsync-on-rewrite字段设置为默认设置为no，是最安全的方式，不会丢失数据，但是要忍受阻塞的问题。如果对延迟要求很高的应用，这个字段可以设置为yes，，设置为yes表示rewrite期间对新写操作不fsync,暂时存在内存中,不会造成阻塞的问题（因为没有磁盘竞争），等rewrite完成后再写入，这个时候redis会丢失数据。Linux的默认fsync策略是30秒。可能丢失30秒数据。因此，如果应用系统无法忍受延迟，而可以容忍少量的数据丢失，则设置为yes。如果应用系统无法忍受数据丢失，则设置为no。
no-appendfsync-on-rewrite no
```



#### 如何恢复

##### 正常恢复

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;将文件放到dir指定的文件夹下，当redis启动的时候会自动加载数据，注意：`aof文件的优先级比dump大`。

##### 异常恢复

- 有些操作可以直接到appendonly.aof文件里去修改。

  eg：使用了flushall这个命令，此刻持久化文件中就会有这么一条命令记录，把它删掉就可以了

- 写坏的文件可以通过 `redis-check-aof --fix`进行修复

#### 

#### 优势

1. 根据不同的策略，可以实现每秒，每一次修改操作的同步持久化，就算在最恶劣的情况下只会丢失不会超过两秒数据。

2. 当文件太大时，会触发重写机制，确保文件不会太大。
3. 文件可以简单的读懂

#### 劣势

1. aof文件的大小太大，就算有重写机制，但重写所造成的阻塞问题是不可避免的
2. aof文件恢复速度慢



### 总结

1. 如果你只希望你的数据在服务器运行的时候存在，可以不使用任何的持久化方式

2. 一般建议同时开启两种持久化方式。AOF进行数据的持久化，确保数据不会丢失太多，而RDB更适合用于备份数据库，留着一个做万一的手段。

3. 性能建议：

   因为RDB文件只用做后备用途，建议只在slave上持久化RDB文件，而且只要在15分钟备份一次就够了，只保留900 1这条规则。

   如果Enalbe AOF,好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自己的AOF文件就可以了。

   代价：

   1、带来了持续的IO；

   2、AOF rewrite的最后将rewrite过程中产生的新数据写到新文件造成的阻塞几乎是不可避免的。只要硬盘许可，应该尽量减少AOF rewrite的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上。默认超过原大小100%大小时重写可以改到适当的数值。

   如果不Enable AOF,仅靠Master-Slave Replication 实现高可用性也可以。能省掉一大笔IO也减少了rewrite时带来的系统波动。代价是如果Master/Slave同时宕掉，会丢失10几分钟的数据，启动脚本也要比较两个Master/Slave中的RDB文件，载入较新的那个。新浪微博就选用了这种架构。



### 主从复制（MASTER/SLAVE）

>命令：SLAVEOF 端口号 端口

master可以读写，slave只能读



#### 一主二从

![一仆二主](https://img-blog.csdnimg.cn/20200326185430327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0NzY2ODgz,size_16,color_FFFFFF,t_70)

当master宕机后，slave等候。



#### 薪火相传

其中一个redis既是主又是从，减轻master的压力。



![薪火相传](https://img-blog.csdnimg.cn/20200326185522463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0NzY2ODgz,size_16,color_FFFFFF,t_70)



#### 反客为主

原master关闭后，其中一个slave中输入命令：

```shell    
SLAVEOF no one
```

 使当前数据库停止与其他数据库的同步，转成主数据库

其他slave变为新master的slave



#### 哨兵模式（sentinel）

反客为主的自动版，能够后台监控Master库是否故障，如果故障了根据投票数自动将slave库转换为主库。一组sentinel能

同时监控多个Master。

使用步骤：

1. 在Master对应redis.conf同目录下新建sentinel.conf文件，名字绝对不能错；

2. 配置哨兵，在sentinel.conf文件中填入内容(可以配置多个)：

   ```shell
   #说明：最后一个数字1，表示主机挂掉后slave投票看让谁接替成为主机，得票数多少后成为主机。
   sentinel monitor 被监控数据库名字（自己起名字） ip port 1
   ```

3. 启动哨兵模式(路径按照自己的需求进行配置)：

   ```shell
   redis-sentinel  /myredis/sentinel.conf
   ```



注意：

1. 当master挂掉后，会通过选票进行选出下一个master。而且只有使用了sentinel.conf启动的才能开启选票

2. 当原来的master后来后，很不幸变成了slave。



### 复制原理

1. Slave启动成功连接到master后会发送一个sync命令；

2. Master接到命令启动后的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，master

   将传送整个数据文件到slave，以完成一次完全同步；

3. `全量复制`：而slave服务在数据库文件数据后，将其存盘并加载到内存中；

4. `增量复制`：Master继续将新的所有收集到的修改命令依次传给slave，完成同步；

5. 但是只要是重新连接master，一次完全同步（全量复制）将被自动执行。

 [结合这几个注意事项](#zhu yi)

### 复制的缺点

​       延时，由于所有的写操作都是在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使得这个问题更加严重。




### 命令

| 命令                                                         | 作用                                             |
| ------------------------------------------------------------ | ------------------------------------------------ |
| slaveof 主库ip  主库端口                                     | 配置从库                                         |
| info replication                                             | 查看redis主从复制的情况                          |
| slaveof  no one                                              | 使当前数据库停止与其他数据库的同步，转成主数据库 |
| sentinel monitor 被监控数据库名字(自己起名字) 127.0.0.1 6379 1 | 配置哨兵，监视master                             |
| redis-sentinel /myredis/sentinel.conf                        | 以哨兵模式启动redis                              |