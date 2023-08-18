# Redis7菜鸡---0809

Redis是一种KV键值对类型的缓存数据库

## 入门概述

> Redis（Remote Dictionary Server）：远程字典服务器
>
> Redis是完全开源、C语言编写的、高性能的Key-Value数据库，提供丰富的数据结构（String、Hash、List、Set、SortedSet等）。
>
> 数据存储在内存中，同时Redis支持事务、持久化、LUA脚本、发布/订阅、缓存淘汰、流技术等特性；
>
> 提供了主从模式、Redis Sentinel和 Redis Cluster集群架构方案。
>
> 结论：Redis是完全开源，遵守BSD协议使用C语言编写的一种K-V键值对形式的内存数据库。
>
> Redis之父：安特雷兹   https://github.com/antirez
>
> https://github.com/redis/redis：
>
> * Redis 是一个持久（自身也有一块磁盘，若Redis宕机可将内存中数据保存至磁盘，等Redis启动后进行恢复）保存在磁:盘上的内存数据库。数据模型是键值对，但支持许多不同类型的值：Strings, Lists, Sets, Sorted Sets, Hashes, Streams, HyperLogLogs, Bitmaps。
>
> Redis主流功能：
>
> * 分布式缓存（挡在Mysql数据库之前的带刀侍卫）
>   * Redis和MySQL数据库区别：
>     * 关系型数据库；KV数据库（NOSQL）
>     * 某些场景Redis明显优于MySQL，例如计数器、排行榜、抢红包等
>     * Redis通常用于某些特定场景，要和MySQL配合使用（两者是共用和配合使用的关系）
>       * 硬件层面：
>         * 一个存储在磁盘，一个在内存
>       * 查询：
>         * Redis是KV键值对的数据模型；（kv键值对就是hashmap，时间复杂度O1）
>       * 关系的处理：
>         * MySQL是RDBMS关系型数据库管理系统，Redis是NOSQL（Not Only SQL）概念
> * 内存存储和持久化（RDB + AOF）
>   * Redis支持异步将内存中数据写到硬盘上，同时不影响继续服务
>     * on disk persistence / persists on disk：在硬盘上持久存在
> * 高可用架构搭配
>   * 单机 >>> 主从（Master-Slave） >>> 哨兵（Sentinel） >>> 集群（Cluster）
> * 缓存穿透、击穿、雪崩
> * 分布式锁
> * 队列
>   * Redis提供List和Set操作，使得Redis能作为一个很好的消息队列平台来使用
>   * 常通过队列功能做购买限制
> * 排行榜、点赞
>   * Redis提供的ZSet数据类型能够快速实现复杂的排行榜
>
> Redis优势：
>
> * 性能极高（在内存进行操作；读11万次/秒、写8万次/秒）
> * 数据类型丰富，不仅支持简单的KV类型数据；还提供list、set、zset、hash等数据结构的存储
> * 支持数据持久化，可将内存数据保持在磁盘中，重启时可再次加载进行使用
> * 支持数据备份，即master-slave主从模式的数据备份
>
> 下载
>
> * 官方网址：https://redis.io
> * Redis中文文档：https://redis.com.cn/documentation.html
>
> Redis迭代演化和Redis7新特性：
>
> * 2009年正式推出的Redis
>
> * 版本号第二位为奇数，非稳定版本（例如2.7、2.9、3.1..）；偶数则为稳定版本（例如2.8、3.0、3.2..）	
> * 查看新特性：
>   * https://github.com/redis/redis/releases
>   * 需要了解的有：
>     * Client-eviction
>     * Multi-part AOF：AOFRW不再是运维痛点
> * Redis7大体和之前的版本功能保持一致，主要是自身底层性能和资源利用率上的优化和提高



## 安装配置（基于Linux）
> 如何查看本地Linux是32还是64位（使用64位）：getconf LONG_BIT
>
> 官方文档已经推荐使用Linux进行Redis相关开发
>
> Linux系统安装Redis前必须先具备gcc编译环境：
>
> * gcc是Linux下的一个编译程序，是C程序的编译工具
> * 查看是否安装有gcc环境：gcc -v
>   * 只要输出的信息末尾 gcc版本大于等于4.8.5即可
>     * 否则执行：yum -y install gcc -c++
>
> Redis版本选择（必须6.0.8版本之上；当前使用7.0.12）：
>
> * 首先确认是否装有Redis：redis -server -v
>   * Redis卸载步骤：
>     * 停止redis服务
>     * 删除usr/local/bin目录下与redis相关文件
>
> 1. 将.tar.gz包放入opt目录下
>
> 2. /opt目录下解压redis，并进入redis7目录
>
> 3. 在redis目录下执行make命令（操作Makefile文件---编译安装）：make && make install
>
> 4. 查看默认安装目录：/usr/local/bin
>
>    1. ```sh
>       [root@vc redis-7.0.12]# ll /usr/local/bin/
>       总用量 21552
>       -rwxr-xr-x. 1 root root  5205448 8月   9 22:55 redis-benchmark
>       lrwxrwxrwx. 1 root root       12 8月   9 22:55 redis-check-aof -> redis-server
>       lrwxrwxrwx. 1 root root       12 8月   9 22:55 redis-check-rdb -> redis-server
>       -rwxr-xr-x. 1 root root  5422856 8月   9 22:55 redis-cli
>       lrwxrwxrwx. 1 root root       12 8月   9 22:55 redis-sentinel -> redis-server
>       -rwxr-xr-x. 1 root root 11436664 8月   9 22:55 redis-server
>       
>       redis-benchmark：性能测试工具
>       redis-check-aof：修复有问题的AOF文件
>       redis-check-rdb：修复有问题的dump.rdb文件
>       重点***redis-cli：客户端，操作入口
>       redis-sentinel：Redis集群使用
>       重点***redis-server：Redis服务器启动命令
>       ```
>
> 5. 将redis7解包后目录下默认的redis.conf拷贝到自定义路径下（此处在根目录下使用/myredis）
>
>    1. cp -r /opt/redis-7.0.12/redis.conf /myredis/redis7.conf
>
> 6. 修改该自定义路径下的配置文件做初始化配置
>
>    1. daemonize no   			修改为    daemonize yes
>    2. protected-mode yes    修改为     protected-mode no
>    3. bind 127.0.0.1               修改为     直接注释掉或改为本机IP地址，否则影响远程IP连接
>    4. 添加redis密码                修改为     requirepass 自定义密码
>
> 7. 启动服务
>
>    1. 在/usr/local/bin目录下运行：redis-server /myredis/redis7.conf
>    2. 确认进程是否存在：ps -ef | grep redis | grep -v grep
>
> 8. 连接服务
>
>    1. redis-cli  -a   自定义的redis密码   -p  6379     （-p 6379不写默认）
>    2. ps -ef | grep redis | grep -v grep可以看到有两个redis进程，一个服务器一个客户端
>    3. ping弹出pong表示成功可正常使用
>
> 9. Redis端口6379：对应手机键盘MERZ，骂人的意大利广告女郎
>
> 10. Helloworld
>
>     1. set k1 helloworld
>     2. get k1
>
> 11. 关闭
>
>     1. 在Redis内部直接：SHUTDOWN
>     2. 单实例关闭：redis-cli -a  密码  shutdown
>     3. 多实例关闭，指定端口关闭：redis-cli  -p  6379  shutdown





## Redis十大数据类型

> 这里提到的数据类型都是指的Value的数据类型，Key的类型一般都是字符串。
>
> 命令不区分大小写；但Key区分大小写；
>
> 帮助命令：help @类型（例如help @string、help @sorted-set）

### String（Redis字符串）

> 简介：
>
> * String是Redis最基本的数据类型，一个key对应一个value。
>
> * 二进制安全的---支持序列化；
>
> * 一个Redis中value最多可以是512M
>
> 运用：
>
> * 单key单value
> * 最常用：
>   * set key value
>     * `EX` *秒*——设置指定的过期时间，以秒为单位。
>     * `PX` *milliseconds* -- 设置指定的过期时间，以毫秒为单位。
>     * `EXAT` *timestamp-seconds* -- 设置key过期的指定 Unix 时间（以秒为单位）。
>       * 如何获取指定key过期的Unix时间，单位为秒（获取Unix时间戳）
>         * Long.toString( System.currentTimeMills()/1000L );
>     * `PXAT` *timestamp-milliseconds* -- 设置密钥过期的指定 Unix 时间（以毫秒为单位）。
>     * `NX`-- 仅当key尚不存在时才设置该key。
>     * `XX`-- 仅当key已存在时才设置该key。
>     * `KEEPTTL`-- 保留设置前与key关联的生存时间。
>       * 若指定key过期时间后，重新set但未指定过期时间，默认永不过期
>     * `GET`-- 返回存储在 key 处的旧字符串，如果 key 不存在则返回 nil。`SET`如果存储在 key 中的值不是字符串，则会返回错误并中止。
>   * get key
> * 同时设置/获取多个键值：
>   * MSET key value [ key value ... ]   同时设置一个或多个kv键值对
>   * MGET key [ key ... ]   同时获取一个或多个kv键值对
>   * MSETNX  key value [ key value ... ]   同时设置一个或多个kv键值对，当且仅当所有给定key都不存在
> * 获取指定区间范围内的值：
>   * getrange  key  起始索引 结束索引 ：获取指定区间范围内的值，类似between...and的关系（0，-1 表示获取全部数据）
>   * setrange key 索引 具体值：设置指定区间范围内的值（覆盖效果，不是追加）
> * 数值增减（一定要是数字才能进行增减）：
>   * 递增数值：INCR key
>   * 增加指定的整数：INCRBY key increment
>   * 递减数值：DECR key
>   * 减少指定的整数：DECRBY key decrement
> * 获取字符串长度和内容追加：
>   * STRLEN key
>   * APPEND key value（向后追加）
> * 分布式锁：
>   * setex  键  秒  值：（Set With Expire） 设置键值对的同时设置过期时间
>   * setnx  键  值：（Set If Not Exist）只有当key不存在才会设置key值
> * getset（先get再set）：
>   * getset  键  新值：将给定 key 的值设为 value ，并返回 key 的旧值(old value)。简单一句话，先get然后立即set
>   * set 键 新值 get：指令效果相同
>
> 应用场景：
>
> * 例如无限点赞某个视频或商品
>   * INCR 键  1
> * 是否喜欢的文章
>   * INCR 键  1



### List（Redis列表）

> 简介：
>
> * 简单的字符串列表，按插入顺序排序。可添加一个元素到列表的头部或尾部。
> * 底层实际是一个双端链表（两端操作性能很高，通过索引下标操作中间节点性能较差），最多可包含2的32次方减1个元素（每个列表超过40亿个元素）。
>
> 运用：
>
> * 单key多value
> * 主要功能push/pop等，一般用在栈、队列、消息队列等场景；
> * key不存在创建新的链表；key存在则新增内容；若value全移除，对应的key也随之消失
> * lpush/rpush/lrange：l表示left、r表示right
>   * lpush  列表名  v1 [ v2 v3 ....]
>   * rpush  列表名  v1 [ v2 v3 ....]
>   * lrange 列表名  下标  下标：
>     * lrange  list1  0  -1  （0  -1 表示列表全部）
>     * 和lpush插入元素顺序相反，和rpush插入顺序一致
> * lpop/rpop
>   * lpop 列表名 【移除/弹出数值；不写默认一个】：从左边弹出第一个元素
>   * rpop 列表名 【移除/弹出数值；不写默认一个】：从右边弹出第一个元素
> * lindex
>   * lindex 列表名  index索引值：按照索引下标获取元素（从上到下）
> * llen  列表名：获取列表中元素的个数
> * lrem 列表名  数字N  给定值v1
>   * 删除N个列表中值为v1的元素；N为0表示删除所有值为v1的元素
> * ltrim  列表名 起始索引 结束索引
>   * 截取指定范围的值再赋值给列表（重定向/覆盖）
> * rpoplpush  源列表  目的列表：
>   * 移除列表的最后一个元素，并将该元素添加到另一个列表（lpush后lrange新增的数据肯定是在第一位）并返回
> * lset  列表名 索引 新元素：
>   * 将列表中索引位置的元素替换为指定的新元素
> * linsert  列表名  before/after  已有值   插入的新值：
>   * 在列表中某个已有值（重复也只会对第一个值操作）的前/后再添加新的值
>
> 应用场景：
>
> * 微信公众号订阅的消息：
>   * 例如lixc订阅了两篇公众号孟岩和E大，此时两位分别发布了文章：m1，e1
>     * lpush  lixcxxx  m1 e1
>   * 当lixc查看自己订阅全部文章时（类似分页，下面就表示显示10条）
>     * lrange lixcxxx  0 9



### Hash（Redis哈希表）

> 简介：
>
> * Hash是一个String类型的field字段和value值的映射表；特别适合用于存储对象。
> * 每个Hash可存储2的32次方减1个键值对。
>
> 运用：
>
> * KV模式不变，但V是一个键值对（ Map<String , Map< Object,Object >> ）
> * hset/hget/hmset/hmget/hgetall/hdel
>   * hset  hash键  键  值
>   * hgset  hash键  键
>   * hmset  hash键  键1  值1   键2   值2 ...
>   * hmget  hash键  键1  键2   ...
>   * hgetall hash键：获取哈希表的V也就是对应的所有键值对
>   * hdel hash键 键1 键2  ...
> * hlen hash键：获取某个hash键内的全部键值对数量
> * hexists hash键 键1：判断在hash键对应的键值对中是否存在键1
> * hkeys/hvals：
>   * hkeys  hash键：获取该hash键下对应的所有键
>   * hvals   hash键：获取该hash键下对应的所有键对应的值
> * hincrby/hincrbyfloat
>   * hincrby hash键 键  增量整数
>   * hincrbyfloat hash键 键 增量浮点数
> * hsetnx  hash键  键  值
>   * 键不存在则赋值，存在则无效
>
> 应用场景：
>
> * 购物车场景（中小厂适用）：
>   * 新增商品：hset  cart001:uid001  1010   1
>   * 商品数量增加：hincrby  cart001:uid001  1010  1
>   * 商品总数：hlen  cart001:uid001
>   * 全部选择：hgetall cart001:uid001




### Set（Redis集合）

> 简介：
>
> * Set是String类型的无序集合。
> * 集合成员唯一，不能出现重复数据；
> * 集合中对象的编码可以是intset或hashtable。
> * Set集合是通过哈希表实现的，所以添加、删除、查找的复杂度都是O(1)。
> * 集合中可存储2的32次方减1个成员。
>
> 运用：
>
> * 单key多value，且无重复
> * sadd key member [member ...]：
>   * 添加元素；无序不重复
> * smembers key：
>   * 遍历集合中所有元素
> * sismember key member：
>   * 判断元素是否在集合中（1存在0不存在）
> * srem key member [ member ... ]：
>   * 删除member元素
> * scard key：
>   * 获取集合中元素个数
> * srandmember key  【count】：
>   * 随机展示集合中的元素（个数不指定默认是一个）
> * spop key 【count】：
>   * 从集合中弹出指定个数的元素
> * smove key1 key2 member：
>   * 从key1集合中将member元素取出放在key2集合中
> * 集合运算：
>   * 集合的差集运算：
>     * sdiff  key  [ key ... ]：哪个集合在前显示的就是属于前集合不属于后集合的元素构成的集合
>   * 集合的并集运算：sunion key [ key ... ]
>     * 两集合元素合并构成的结合
>   * 集合的交集运算：
>     * sinter key [ key ... ]：两集合共同拥有的元素构成的集合
>     * Redis7新命令：
>       * sintercard  numberkeys  key  [ key ... ]  [ Limit limit ]：
>         * numberkeys表示集合个数；limit表示需要显示几个
>       * 不返回结果集，只返回结果的基数（返回由所有给定集合的交集产生的集合的基数【基数表示去重后的元素个数】）
>
> 应用场景：
>
> * 微信抽奖小程序：
>   * spop key 【count】：抽到了就从集合剔除
>   * srandmember key  【count】：抽到了不剔除继续参与抽奖
> * 微信朋友圈点赞查看同赞朋友：
>   * smembers key
>   * sinter key [ key ... ]
> * qq内推可能认识的人：
>   * sdiff key [ key ... ]
>   * sinter key [ key ... ]



### ZSet（Redis有序集合）

> 简介：
>
> * ZSet就是Sorted Set；String类型的有序集合。
> * 不允许成员重复。
> * 会在每个元素关联一个double类型的分数（Redis正是通过分数来为集合中的成员进行从大到小的排序）。
>   * k1 score v1   每个值前都关联一个分数
> * ZSet的成员是唯一的，但分数（Score）却可重复。
> * ZSet集合通过哈希表实现，所以添加、删除、查找的复杂度都是O(1)。
> * 集合中可存储2的32次方减1个成员。
>
> 运用：
>
> * ZSet是在Set基础上每个val值前加score分数值
>   * 之前set是 k1  v1  v2  v3；
>   * 现在zset是k1 score v1 score v2
> * zadd key  score member 【score member ...】
> * zrange key start stop 【withscores】：
>   * 按元素份数从小到大排序；0 -1表示全部
>   * withscores表示携带分数
> * zrevrange key start stop 【withscores】：
>   * 从大到小排序
> * zrangebyscore key min max [WITHSCORES] [LIMIT offset count]
>   * 获取指定分数范围内的集合元素
>   * ( 表示不包含：ZRANGEBYSCORE zset1 (60 90 withscores
>   * limit作用是返回限制：开始下标   多少步
> * zscore  key  member：获取集合内指定元素的分数
> * zcard key：元素个数
> * zrem key member [member ...]：删除指定集合内的元素
> * zincrby key increment member：增量指定元素的分数
> * zcount key min max：获取指定分数范围内的元素个数
> * zmpop numkeys key [key ...] MIN|MAX [COUNT count]：
>   * 从键名列表中的第一个非空排序集中弹出一个或多个元素，他们是成员分数对
>     * ZMPOP 1 zset1 min count 2：弹出zset1集合中最小的两个元素
>     * ZMPOP 1 zset1 max count 1：弹出zset1集合中最大的一个元素
> * zrank key member：获得指定元素的下标值
> * zrevrank key member：逆序获得下标值
>
> 应用场景：
>
> * 根据商品销售对商品进行排序显示：
>   * zadd goods1 1 joy1 1 
>   * zincrby goods 1 joys
>   * zrevrange goods 0 -1 withscores







### GEO（Redis地理空间）

> 简介：
>
> * 经纬度
> * 地理位置是使用二维的经纬度表示，只要确定一个点的经纬度即可取得他在地球的位置。
>   * 最后会转为二进制再通过base32编码
> * GEO主要用来存储地理位置信息，并对存储的信息进行操作。
>   * 添加地理位置的坐标
>   * 获取地理位置的坐标
>   * 计算两个位置之间的距离
>   * 根据用户给定的经纬度坐标来获取指定范围内的地理位置集合。
> * 获取某个坐标经纬度：http://api.map.baidu.com/lbsapi/getpoint/
> * ZSet的类型
>
> 运用：
>
> * geoadd：
>   * 添加经纬度坐标
>   * geoadd city 116.403963 39.915119 "天安门" 116.403414 39.924091 "故宫" 116.024067 40.362639 "长城"
>   * type city >>> zset
>   * zrange city 0 -1 >>> 中文乱码：
>     * Redis客户端中文乱码问题：
>       * redis-cli -a xxx --raw
> * geopos：（position）
>   * 返回经纬度
>   * GEOPOS city 天安门 长城 故宫
> * geohash：
>   * 返回坐标的geohash表示（geohash算法生成base32编码值）
>   * geohash city 天安门 长城 故宫
> * geodist：
>   * 两个位置之间的距离
>   * geodist city 天安门 长城 m
>     * m：米
>     * km：千米
>     * ...
> * georadius：
>   * 以半径为中心，查找附件的xxx 
>   * 127.0.0.1:6379> GEORADIUS city 116.418038 39.91979 10 km withdist withcoord count 10 withhash desc
>     故宫
>     1.3358
>     4069885568908290
>     116.40341609716415405
>     39.92409008156928252
>     天安门
>     1.3082
>     4069885555089531
>     116.40396326780319214
>     39.91511970338637383
> * GEORADIUSBYMEMBER：
>   * 找出位于指定范围内的元素，中心点由给定的位置元素决定
>   * GEORADIUSBYMEMBER city 天安门 10 km withdist withcoord count 10 withhash
>
> 应用场景：
>
> * 美团地图位置附近的酒店推送
> * 高德地图附近的核算检查点



### HyperLogLog（Redis基数统计）

> 简介：
>
> * HyperLogLog是用来做基数（不重复的数字；例如IP）统计的算法；
> * 优点：在输入元素数量或体积非常大时，计算基数所需的空间总是固定且很小的。
> * 每个HyperLogLog键只需花费12KB内存，即可计算接近2的64次方个不同元素的基数（这和计算基数元素越多耗费内存越多的集合形成鲜明对比）。
> * 但HyperLogLog只会根据输入元素来计算基数，而不会存储输入元素本身，所以不能像集合那样返回输入的各个元素。
> * 需求：
>   * 统计某个网站、文章的UV（Unique Visitor，独立访客，一般理解为客户端IP；需要去重考虑）
>   * 用户搜索网站关键词的数量
>   * 统计用户每天搜索不同词条个数
> * HyperLogLog：去重复统计功能的基数估计算法（用于在不占用大量存储空间的情况下估计一个集合中的唯一元素数量）
> * 基数：是一种数据集，去重后的真实个数
> * 基数统计：统计一个集合中不重复的元素个数，即对集合去重后剩余元素的计算
>   * 去重脱水后的真实数据
>
> 运用：
>
> * pfadd key [element [element ...]]
>   * Probabilistic Filter（概率过滤器）
>   * 添加指定元素到HyperLogLog中
> * pfcount key [key ...]
>   * 返回给定的HyperLogLog的基数估计值
> * pfmerge destkey sourcekey [sourcekey ...]
>   * 将多个HyperLogLog合并为一个HyperLogLog
>
> 应用场景：
>
> * 天猫网站首页亿级UV的Redis的统计方案



### BitMap（Redis位图）

> 简介：
>
> * Bit Arrays或Simple Bit Map，可以称之为位图。
> * 由0和1状态表现的二进制位的bit数组。
>   * 用户是否登录
>   * 电影、广告是否被点击
>   * 钉钉打卡签到
> * 用String类型作为底层数据结构实现的一种统计二值状态的数据类型（使用type指令可以看出BitMap也是String类型）；
> * 位图本质是数组；基于String类型按位操作。该数组由多个二进制位（一个字节也就是一个byte=8bit也就是8位）组成，每个二进制位都对应一个偏移量（我们称之为一个索引）
> * BitMap支持的最大位数2^32位。极大地节省空间，使用512M内存即可存储多达42.9亿（2 ^32）的字节信息
> * BitMap位图能做什么：状态统计。
>
> 运用：
>
> * setbit key offset value：   setbit 键 偏移位  0/1
>   * 给指定key的值的第offset赋值val
> * getbit key offset：
>   * 获取指定key的第offset偏移位的值
> * setbit和getbit案例说明---按照天：
>   * setbit sign:u1:202308 0 1
>   * setbit sign:u1:202308 1 1
>   * ...
>   * setbit sign:u1:202308 30 1
>   * getbit sign:u1:202308 30
>   * bitcount  sign:u1:202308 0 30 
> * strlen：
>   * 统计字节数占用多少；
>   * 不是统计字符串长度，而是统计占用几个字节；超过8位后自动按8位1个字节（setbit key1 32 1获取到的strlen就是5）
> * bitcount key [start end [BYTE|BIT]]：
>   * 全部键里面含有1的个数；
>   * 可直接bitcount  key来查询
> * bitop：
>   * 对多个位图进行逻辑操作
>   * BITOP operation destkey key [key ...]：
>     * `operation`：指定逻辑操作，可以是AND（与）、OR（或）、XOR（异或）或NOT（非）。
>       * 与（AND）：相同位置上**都为1**的两个二进制数，**结果为1**；否则为0。
>       * 或（OR）：两个二进制数中**任意位置上为1**的情况，**结果为1**；否则为0。
>       * 非（NOT）：二进制数的**每一位取反**，即0变为1，1变为0。
>       * 异或（XOR）：对于相同位置上的两个二进制数，如果**值不同，则结果为1**；否则为0。
>     * `destkey`：指定结果存储的目标键名。
>     * `key`：要执行逻辑操作的位图键名。
>
> 应用场景：
>
> * 一年365天，全年天天登陆占用多少字节：
>   * 365/8=45.xxx 46个字节
> * 按照年：
>   * 存储一个用户一年的签到情况只会使用到46个字节，1000W用户也只需要438.91MB
>   * 加入是亿级系统：
>     * 每天使用1个1亿位的Bitmap约占12MB的内存（10^8/8/1024/1024），10天的Bitmap的内存开销约为120MB，内存压力不算太高。
>   * 此外，实际使用中，最好对BitMap位图设置过期时间，让Redis自动删除不再需要的签到记录来节省内存开销







### BitField（Redis位域）

> 简介：
>
> * 可一次性操作多个比特位域（指连续的多个比特位）；会执行一系列操作并返回一个响应数组，数组中的元素对应参数列表中的相应操作的执行结果。
> * 说白了就是通过BitField命令可一次性对多个比特位域进行操作。
>
> 运用：
>
> * 
>
> 应用场景：
>
> * 



### Stream（Redis流）

> 简介：
>
> * 类似MQ消息中间件；
> * Redis消息队列的两种方案：
>   * List实现消息队列：
>     * 实现方式就是点对点模式
>     * lpush rpop
>     * rpush lpop
>     * 常用来做异步队列使用
>   * Pub/Sub：
>     * Rdis发布订阅（Pub/Sub）缺点是消息无法持久化。若网络断开、宕机，消息就会别丢弃。而且也没有ACK签收机制来保证数据可靠性。
>       * 简单来说发布订阅（pub/sub）可分发消息，但无法记录历史消息。
> * Redis5.0版本新增的数据结构。主要用于消息队列（MQ：Message Queue）；Redis版的消息中间件；
> * Stream提供了消息的持久化和主备复制功能，可让任何客户端访问任何时刻数据，且记录每个客户端的访问位置，还能保证消息不丢失。
> * Stream就是：Redis版的MQ消息中间件+阻塞队列
> * Stream流能：
>   * 实现消息队列，支持消息持久化、支持自动生成全局唯一ID、支持ACK确认消息的模式、支持消费组模式等，让消息队列更加稳定可靠。
> * 原理：Stream是一个消息链表，将所有加入的消息串起来，每个消息都有一个唯一ID和对应内容
>
> 运用：
>
> 队列相关指令：
>
> * xadd：添加消息到队列末尾
>   * 消息ID必须比上个ID大
>   * 默认使用星号表示自动生成规矩
>   * *：用于XADD命令中，让系统生成自动ID
>   * xadd mystream * id 003 name vc
> * xrange：用于获取消息列表，忽略删除的消息
>   * -代表最小值，+代表最大值；count表示最多获取多少个值（类似MySQL分页）
>   * xrange mystream - +
> * xrevrange
> * xdel：
>   * xdel mystream 1691768198078-0（这是消息的id）
> * xlen
> * xtrim：对Stream的长度进行截取，若超长会进行截取
>   * maxlen：允许的最大长度
>     * xtrim mystream maxlen 2：会将超出两条范围的信息截取，先进先出所以XRANGE上面的先被截取
>   * minid：允许的最小id
>     * xtrim mystream minid 1691768869804-0：id小于指定的id就会被截取
> * xread：用于获取消息（阻塞/非阻塞），只会返回大于指定id的消息；【类似Java中的阻塞队列】
>   * count 最多读取多少条消息；
>   * block 是否以阻塞的方式读取消息，默认不阻塞；若值为0，表示永远阻塞
>   * 非阻塞：
>     * xread count 2 streams mystream 0-0
>       * 0-0表示从头获取；0也可以
>   * 阻塞：
>     * xread count 1 block 0 streams mystream $：阻塞等待比当前最新的id还要新的消息
>
> 消费组相关指令：
>
> * xgroup create：用于创建消费者组（创建消费者组必须制定ID，0表示从头消费，$表示从尾消费新的消息）
>   * xgroup create mystream groupA $
>     * $表示从Stream尾部开始消费
>   * xgroup create mystream groupB 0
>     * 0表示从Stream头部开始消费
> * xreadgroup group：
>   * ‘>’：表示从第一条尚未被消费的消息开始读取
>   * xreadgroup group groupB consumer1 streams mystream >
>     * Stream中的消息一旦被消费者组内的消费者读取，就不能再被读取，即同一个消费者组的消费者们不能消费同一条消息。再次执行xreadgroup读取的就是空值。
>     * 不同消费者组的消费者可以消费同一条Stream消息
>   * 消费者组的目的
>     * 让组内多个消费者共同分担信息读取，实现消息读取负载在多个消费者间均衡分布
>       * xreadgroup group groupX consumer1 **count 2** streams mystream >
> * 重点问题：
>   * 消息的已读未签收和已读已签收：
>     * 消费者使用XACK命令通知Stream消息已处理完成
> * xpending：
>   * 查询每个消费组内所有消费者 【已读取未签收】的消息
>     * xpending mystream groupX
>       5
>       1691768869804-0
>       1691769872034-0
>       consumer1
>       2
>       consumer2
>       2
>       consumer3
>       1
> * xack：向消息队列确认消息处理已完成
>   * xpending mystream groupX - + 10 consumer2
>     1691769496990-0
>     consumer2
>     483412
>     1
>     1691769501842-0
>     consumer2
>     483412
>     1
>   * xack mystream groupX 1691769496990-0
>   *  xack mystream groupX 1691769501842-0
>   * 将消费者组每一个消费者读取的信息向消息队列确认处理完成后
>     * xpending mystream groupX
>       0
> * xinfo：用于打印Stream\Consumer\Group的详细信息
>   * xinfo stream mystream
>
> 使用建议：
>
> * Stream还是不能100%等价于Kafka、RabbitMQ来使用的，生产案例少，慎用。



### Redis 键（Key）

> keys *：查看当前库所有的key
>
> exists key：判断某个key是否存在，可同时判断多个（存在几个返回数字几）
>
> type key：查看该key什么类型
>
> del key：删除指定的key数据（可同时删除多个）
>
> unlink key：非阻塞删除，仅将key从keyspace元数据中删除，真正的删除会在后续异步中操作；（可同时非阻塞删除多个）
>
> * 若真要删除很大的数据，使用原子操作命令del指令是阻塞的，若在高并发程序下就会产生阻塞队列，系统性能急剧下降。所以使用unlink指令更方便
>
> ttl key：Time To Live查看还有多少秒过期，-1表示永不过期（未指定key的超时时间默认就是永不过期），-2表示已过期
>
> expire key 秒钟：为给定的key设置过期时间
>
> move key dbindex【0-15】：将当前数据库的key移动到给定的数据库db中
>
> * 一个Redis服务器默认存在16个数据库；
>   * 可通过select 15、select 16切换数据库指令看出16即超出范围；
>   * 也可通过查看Redis自定义后的配置文件redis.conf：显示databases 16
>
> select dbindex：切换数据库【0-15】，默认为0
>
> * （切换到某个库，>前缀就会有 [下标] 显示；没数字表示默认在0号库）
>
> dbsize：查看当前数据库key的数量
>
> flushdb：清空当前库
>
> flushall：通杀全部库





## Redis持久化

> https://redis.io/docs/management/persistence/
>
> 为何需要持久化：
>
> * 数据保护：
>   * 持久化操作可确保数据库的安全性和可靠性。通过将数据写入磁盘，即使出现电力中断、系统崩溃或其他故障，也能够恢复数据库并保护数据免受损失。
> * 数据恢复：
>   * 持久化使得数据库能在重启后恢复之前的状态。若没有持久化Redis将无法在重启后还原闲钱保存的数据。
> * 长期存储：
>   * 持久化操作允许数据在长期内存储，以便于备份、迁移、复制等操作。使得Redis不仅是一个缓存解决方案，还可用作长期存储和数据分析的工具
>
> Redis提供了两种主要持久化方式：RDB（Redis Database）和AOF（Append-Only File）；
>
> * RDB是一种**快照**方式，将数据库当前状态保存到硬盘上；
>
> * AOF**记录了**所有对Redis服务器的**写操作**，以便在服务器重启时重新执行这些操作。



### 持久化双雄

#### RDB（Redis Database）

> RDB（Redis数据库）持久化以指定的时间间隔执行数据集的时间点快照。
>
> 快照文件就称为RDB文件，保存到磁盘的文件名dump.rdb。
>
> 何时执行数据集的时间点快照（redis.conf配置文件）：
>
> * Redis6.0.16及以下：
>   * 900秒（15分钟），若至少修改了一次文件，就写一份新的RDB文件
>   * 300秒（5分钟），若至少修改了10次文件，就写一份新的RDB文件
>   * 60秒（1分钟），若至少修改了10000次文件，就写一份新的RDB文件
>
> * Redis6.2以及Redis7：
>   * 3600秒（1小时），若至少修改了一次文件，就写一份新的RDB文件
>   * 300秒（5分钟），若至少修改了100次文件，就写一份新的RDB文件
>   * 60秒（1分钟），若至少修改了10000次文件，就写一份新的RDB文件
>
> RDB操作：
>
> * 自动触发：
>   * 默认的就是上面提到的1小时、5分钟、1分钟的快照相关机制
>   * 本次测试5秒两次修改：
>     * save 5 2（表示每5秒检查一次，是否有两次修改）
>   * 修改dump文件保存路径：
>     * 默认是dir ./；
>     * 自定义：dir /myredis/dumpfiles
>   * 修改dump文件名称：
>     * 默认：dbfilename dump.rdb
>     * 自定义：dbfilename dump6379.rdb
>   * Redis支持config get 命令获取配置文件的内容：
>     * config get requirepass：获取之前定义的密码
>     * config get port：获取Redis服务器的端口号
>     * config get dir；即可获取dump文件保存路径
>   * 触发备份：
>     * 在redis服务器中分别触发：
>       * 五秒内，修改两次
>       * 两次修改时间间隔超过5秒
>   * 如何恢复：
>     * 将备份文件dump.rdb移动到redis安装目录并启动服务即可
>     * 备份成功后故意flushdb清空redis，看是否可以恢复数据
>       * 执行flushdb/flushall命令也会产生备份文件dump.rdb，但里面是空的，无意义
>     * 物理恢复，一定服务和备份分级隔离
>       * 使用SHUTDOWN命令模拟redis服务器宕机，它会马上将当前的快照保存
>         * 所以不可将备份文件和生产redis服务器放在同一台机器，必须分开各自存储，以防生产机物理损坏后备份文件也挂了。
> * 手动触发：
>   * Redis提供了两个命令来手动生成RDB文件，分别是save和bgsave。（生产环境只允许用bgsave命令，决不允许使用save）
>   * SAVE（生产禁用！！！）：
>     * 在主程序（Redis服务器）中执行save指令会阻塞当前redis服务器，直到持久化工作完成（生成RDB文件）；执行命令期间，Redis不能处理其他指令，所以线上禁止使用该命令。
>   * BGSAVE（默认）：
>     * 会在后台异步进行快照操作，不阻塞快照同时还可响应客户端请求，该触发方式会fork一个子进程又子进程复制持久化过程。
>       * Redis会使用bgsave对当前内存中的所有数据做快照，该操作是子进程在后台完成的，这就允许主进程同时可以修改数据。
>       * fork：
>         * GitHub中的fork就相当叉到自己的库，克隆一份
>         * Linux操作系统中，fork会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，尽量避免膨胀
>     * LASTSAVE：可通过lastsave命令获取最后一次成功执行快照的时间
>       * 获取到的是Unix时间戳，通过Linux指令来查看：date -d @具体时间戳
>
> RDB优劣势：
>
> * 优势：
>   * 适合大规模数据恢复
>   * 按照业务定时备份
>   * 对数据完整性和一致性要求不高
>   * RDB文件在内存中加载速度远快于AOF
> * 劣势：
>   * 因为是按时间间隔、修改次数进行的备份，所以当服务意外宕机，就会丢失从当前至最近一次快照期间的数据，快照之间的数据会丢失
>   * 内存数据的全量同步，若数据量太大会导致I/O严重影响服务器性能
>   * RDB依赖于主进程的fork，在更大的数据集中就可能导致服务请求的瞬间延迟。fork时内存中的数据被克隆了一份，大致是2倍的膨胀性，需考虑
>
> 如何检查修复dump.rdb文件：
>
> * 在/usr/local/bin目录下（默认安装目录），可以看到redis-check-rdb软链接（符号链接）
>   * redis-check-rdb  dump.rdb文件路径：该指令即可完成对RDB文件的检查修复
>
> 哪些情况会触发RDB快照：
>
> * 自动触发：redis.conf配置文件中的快照配置
> * 手动触发：save/bgsave指令
> * flushdb/flushall指令也会产生RDB文件，但文件是空的，无意义
> * 执行shutdown且没有设置开启AOF持久化
> * 主从复制时，主节点自动触发
>
> 如何禁用快照：
>
> * 动态所有停止RDB保存规则的方法：redis-cli config set save ""
> * 推荐：快照禁用（配置文件默认注释快照配置）
>
> RDB优化配置项详解（除了真正用到的save、dbfilename、dir，其他都默认）：
>
> * save < seconds >  < changes >
> * dbfilename
> * dir
> * stop-writes-on-bgsave-error：
>   * 默认yes；保证数据一致性（no就是快照失败也不在乎）
> * rdbcompression：
>   * 默认yes；采用LZF算法进行RDB快照文件的压缩存储
> * rdbchecksum
>   * 默认yes；存储快照后使用CRC64算法进行数据校验，增加大约10%性能消耗
> * rdb-del-sync-files
>   * 默认no；在没有持久化情况下是否删除复制中的RDB文件，此选项禁用。



#### AOF（Append Only File）

> AOF（仅追加文件）持久化记录服务器接收的每个写操作。然后这些操作可在服务器启动时再次重放，重建原始数据集。命令的记录格式和Redis协议本身相同。
>
> AOF以日志的形式来记录每个写操作；只许追加文件但不可改写文件；Redis启动时就会读取该文件重新构建数据。
>
> 默认情况下，Redis未开启AOF；若需开启需设置配置：
>
> * appendonly yes
>
> 从Redis7.0.0开始，Redis使用了多部分AOF机制。也就是原始的单个AOF文件被分割为基本文件（最多一个）和增量文件（可能不止一个）
>
> * 基本文件表示当AOF被重写时存在的数据的初始（RDB或AOF格式）快照。
> * 增量文件包含自上次创建基本AOF文件以来的增量更改。
> * 所有这些文件都放在一个单独的目录中，并由清单文件跟踪。
>
> AOF保存的文件是appendonly.aof文件。

> AOF持久化工作流程：
>
> * | 1    | Client作为命令的来源，会有多个源头以及源源不断的请求命令。   |
>   | ---- | ------------------------------------------------------------ |
>   | 2    | 在这些命令到达Redis Server 以后并不是直接写入AOF文件，会将其这些命令先放入AOF缓存中进行保存。这里的AOF缓冲区实际上是内存中的一片区域，存在的目的是当这些命令达到一定量以后再写入磁盘，避免频繁的磁盘IO操作。 |
>   | 3    | AOF缓冲会根据AOF缓冲区***同步文件的三种写回策略\***将命令写入磁盘上的AOF文件。 |
>   | 4    | 随着写入AOF内容的增加为避免文件膨胀，会根据规则进行命令的合并(又称***AOF重写)\***，从而起到AOF文件压缩的目的。 |
>   | 5    | 当Redis Server 服务器重启的时候会从AOF文件载入数据。         |
>
> AOF缓冲区三种写回策略（默认appendfsync）：
>
> * appendfsync always：
>   * 同步写回；每个写命令执行完立刻同步的将日志写回磁盘。
> * appendfsync everysec（默认）：
>   * 每秒写回；每个写命令执行完，先把日志写到AOF文件的内存缓冲区，每隔一秒把缓冲区内容写入磁盘
> * appendfsync no：
>   * 操作系统控制写回；每个写命令执行完，先把日志写到AOF文件的内存缓冲区，由操作系统决定何时把缓冲区内容写入磁盘
> * ![image-20230812192240589](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230812192240589.png)

> AOF配置/启动/修复/恢复：
>
> * Redis6和7配置文件说明：
>   * 开启aof（Redis默认使用的是RDB）：appendonly yes
>   * 使用默认写回策略（appendfsync everysec）
>   * AOF文件的保存路径：
>     * Redis6
>       * AOF保存文件的位置和RDB保存文件位置一致，都是通过dir来配置
>     * Redis7
>       * appenddirname "这里指定dir之后的路径"
>       * 最后AOF文件存储的路径就是：
>         * /dir配置路径/appenddirname配置路径
>   * AOF文件的名称
>     * Redis6：
>       * appendfilename "指定AOF文件名（默认appendonly.aof）"
>     * Redis7：
>       * MP-AOF（Multi-Part AOF）实现：
>         * base：基础AOF文件；一般由子进程通过重写产生，最多一个文件
>         * incr：增量AOF文件；一般在AOFRW开始执行时创建，可能存在多个
>         * manifest：清单文件；来跟踪管理这些AOF
>       * appendfilename "指定AOF文件名前缀（默认appendonly.aof）"
> * 正常恢复：
> * 异常恢复：
>   * 故意写乱正常AOF文件，模拟网络闪断文件写error
>   * 重启Redis会进行AOF文件载入，启动失败
>     * lsof -i:6379：查看6379端口是否启动
>   * 异常修复命令：
>     * redis-check-aof    --fix    要修复的文件路径：修复incr增量文件
>   * 重新启动即可

> AOF持久化优劣势：
>
> * 优势：
>   * 更好的保护数据不丢失、性能高、可做紧急恢复。
> * 劣势：
>   * AOF文件通常比相同数据集的等效RDB文件大，恢复速度慢于RDB
>   * AOF运行效率低于RDB，每秒写回策略效率较好，不同步效率和RDB相同

> AOF重写机制：
>
> * 为了解决AOF随Redis服务不断运行写操作不断增多文件越来越大的问题，Redis增加了AOF重写机制；当AOF文件大小超过设定峰值，自动启动AOF文件的内容压缩，只保留可恢复数据的最小指令集；或手动使用 bgrewriteaof 命令来重写。
>   * 一句话：启动AOF文件的内容压缩，只保留可恢复数据的最小指令集。
> * 触发机制：
>   * 自动触发：
>     * auto-aof-rewrite-percentage 100
>     * auto-aof-rewrite-min-size 64mb
>     * 同时满足才会触发
>   * 手动触发：bgrewriteaof
> * 案例：
>   * 前期准备：
>     * 开启AOF
>     * 重写峰值：auto-aof-rewrite-min-size 1k
>     * 关闭混合，设置为no
>       * aof-use-rdb-preamble no（默认yes）
>     * 删除之前全部AOF和RDB，去除干扰项
>   * 结论：
>     * AOF文件重写并不是对源文件进行重新整理，而是直接读取服务器现有键值对，然后用一条命令去代替之前记录这个键值对的多条命令，生成一个新的文件替换掉原来的AOF文件
>     * AOF重写触发机制：
>       * 通过配置文件中auto-aof-rewrite-percentage默认值100，以及auto-aof-rewrite-min-size默认值64mb，默认配置当AOF文件大小是上次重写后大小的一倍且文件大于60MB时触发。



### RDB-AOF混合持久化

> Redis优先加载AOF（默认RDB模式，但开启AOF后，优先加载AOF）。
>
> 同时开启了RDB和AOF持久化，重启只会加载AOF文件（当然若AOF文件没有，也会找RDB文件，都没有这启动失败）。
>
> 这两种持久化方式如何选择：
>
> * RDB持久化方式是在指定的时间间隔对数据进行快照存储
> * AOF持久化方式会记录每次对服务器的写操作，服务器重启会重新执行这些命令来恢复原始数据。
> * 推荐方式：混合使用：
>   * Redis重启会优先加载AOF文件来恢复原始数据，因为更完整；RDB更适合备份数据库（AOF不断变化不好备份），留着RDB以防万一。
>   * RDB可做全量持久化，AOF做增量持久化
> * RDB + AOF混合持久化开启：
>   * aof-use-rdb-preamble yes（默认yes）



### 纯缓存模式

> 关闭RDB和AOF：
>
> * save ""
>   * 禁用RDB持久化；
>   * 禁用后，仍可使用save、bgsave指令生成RDB文件
> * appendonly no
>   * 禁用AOF持久化
>   * 禁用后，仍可使用bgrewriteaof生成AOF文件





## Redis事务

> https://redis.io/docs/interact/transactions/
>
> multi  cmd1 cmd2 ... exec
>
> Redis事务可一次执行多个命令，本质是一组命令的集合。一个事务中的所有命令都会被序列化，按顺序串行化执行而不会被其他命令插入，不允许加塞。
>
> * 能干嘛：在一个队列中，一次性、顺序性、排他性的执行一系列命令。
>
> Redis事务：
>
> * 单独的隔离操作
> * 没有隔离级别的概念
> * 不保证原子性（不保证同时成功或同时失败）
> * 排他性（Redis保证一个事务内的命令依次执行，而不被其他命令插入）
>
> Redis事务的使用：
>
> * 常用命令：
>   * discard：
>     * 取消事务，放弃执行事务块内所有命令
>   * exec：
>     * 执行所有事务块内的命令
>   * multi：
>     * 标记一个事务块的开始
>   * watch key [ key ... ]：
>     * 监视一个或多个key，若事务执行前这个或多个key被其他命令所改动，那么事务将被打断
>   * unwatch：
>     * 取消watch命令对所有key的监视
> * case1：正常执行
>   * multi
>   * exec
> * case2：放弃事务
>   * multi
>   * discard
> * case3：全体连坐（before EXEC）
>   * 故意写错，语法编译不通过
> * case4：冤有头债有主（after EXEC）
>   * Redis不提供事务回滚功能，必须由开发者在事务执行出错后自行恢复数据库状态
>   * 语法没有错误（例如incr xxx；这里的xxx并不是一个数字无法进行增量，但语法没错误；所以该命令前后的命令都会正常执行）
>     * 前期语法没错，编译通过；执行exec后才报错
>   * 所以千万注意Redis数据库事务和传统数据库事务区别，不保证全部成功和全部失败
> * case5：watch监控
>   * Redis使用watch来提供乐观锁定，类似于CAS（Check-And-Set）
>     * 悲观锁：
>       * 每次拿数据都认为别人会修改，所以都会上锁，别人拿数据时就会block直到他拿到锁
>     * 乐观锁（Optimistic Lock）：
>       * 每次拿数据都认为别人不会修改，所以不会上锁，但在更新时就会判断在此期间别人有没有更新这个数据
>       * 乐观锁策略：提交版本  必须大于  记录当前版本才能执行更新
>   * 初始化两个key，先监控再开启multi事务，保证两个key变动在同一个事务中
>     * 正常情况在multi之前watch key，之后对key进行修改，成功
>     * 加塞篡改情况：在multi开启事务后对watch监控的数据进行修改，exec之前其他客户端对监控的数据已经进行了修改，此时exec会整个事务都会失败
>   * unwatch：在执行multi前unwatch放弃监控，那么事务中即使数据在另一个客户端已被修改也能执行成功。
>   * 细节：
>     * 一旦执行了exec，在事务前watch加的监控锁都会被取消。
>     * 客户端连接丢失（例如退出连接），所有key都会被取消监视
>
> 总结：
>
> * 开启：以multi开启一个事务
> * 入队：多个命令列入到事务中，不会立即执行
> * 执行：exec触发事务（当然discard也可以放弃事务）





## Redis管道（Redis pipelining）

> 面试题：如何优化频繁命令往返造成的性能瓶颈：
>
> * Redis是一种基于客户端-服务端模型以及请求/响应协议的TCP服务；一个请求会遵循以下步骤：
>   * 客户端向服务端发送命令分四步（发送命令>>>命令排队>>>命令执行>>>返回结果），并监听Socket返回，通常以阻塞模式等待服务器响应。
>   * 服务端处理命令，并将结果返回给客户端。
> * 上述两步称为：Round Trip Time（简称RTT；数据包往返于两端的时间）
>   * 若同时需执行大量命令，那么就会等待上一条命令应答后再执行，不仅仅多个RTT，还频繁调用系统IO，发送网络请求，系统还会将数据从用户态转为内核态，就会对进程上下文有比较大的影响，性能拉跨。
>
> 引出管道pipeline：
>
> * 管道可一次性发送多条命令给服务端，服务端处理完毕通过一条响应一次性将结果返回，减少客户端与Redis的通信次数来降低往返延时时间。pipeline实现的原理是队列，先进先出特性保证了数据的顺序性。
>
> https://redis.io/docs/manual/pipelining/
>
> Pipeline就是为了解决RTT往返时间，仅是将命令打包一次性发送，对Redis执行不造成任何其他影响。
>
> * 一句话：批处理命令变种优化措施，类似Redis的原生批命令（mset、mget...）
>
> 案例：
>
> * 创建一个文件，里面有很多Redis指令；
> * cmd 文件 | redis-cli -a 001214 --pipe：
>   * All data transferred. Waiting for the last reply...
>     Last reply received from server.
>     errors: 0, replies: 7
>   * 表示执行成功。失败0个，返回7个
>
> Redis管道总结：
>
> * Pipeline与原生批量命令对比：
>   * 原生批量命令具备原子性（mset、mget...），而管道是在Redis服务外执行，不具备原子性
>   * 原生批量命令一次只能执行一种类型的命令，而pipeline支持批量执行任意类型命令
>   * 原生批命令是服务端实现，而pipeline需要客户端与服务端共同完成
> * Pipeline与事务对比
>   * 事务具有原子性（但不保证），管道不具有原子性
>   * 事务一条条的将命令发送，在exec才会执行；管道一次性将多条命令发送到服务器
>   * 执行事务会阻塞执行其他命令的执行，执行管道命令不会
> * 使用Pipeline注意事项
>   * 指令发生异常，后续指令会继续执行
>   * 使用pipeline组装的命令个数不要太多，不然数据量太大客户端阻塞时间可能过久，同时服务器端此时也被迫恢复一个队列答复，会占用很多内存





## Redis发布订阅（Pub/Sub）

> https://redis.io/docs/interact/pubsub/
>
> 了解即可。
>
> 。。。消息中间件还是交给Kafka、RabbitMQ



## Redis复制（replica）

> https://redis.io/docs/management/replication/
>
> Redis复制是什么：
>
> * 主从复制，master以写为主，slave以读为主
> * 当master数据变化，自动将新的数据异步同步（replication）到其他slave数据库
>
> Redis replication能干嘛：
>
> * 读写分离
> * 容灾恢复
> * 数据备份
> * 水平扩容支撑高并发
>
> Redis replication怎么用：
>
> * 配从（库）不配主（库）
> * 权限细节：
>   * master若配置了requirepass参数，需要密码登录；
>   * slave就要配置masterauth来设置校验密码，否则master就会拒绝slave的访问请求
> * 基本操作命令：
>   * info replication
>     * 查看复制节点的主从关系和配置信息
>   * replicaof  主库IP  主库port
>     * 一般写入进redis.conf配置文件内（配从）
>   * slaveof 主库IP 主库port
>     * 相当于上面replicaof配置命令的手写命令；
>     * 每次与master断开，都需重新连接，除非配置redis.conf
>     * 运行期间修改slave节点的信息，若该数据库已经是某主数据库的从数据库，会断掉和原主数据库的同步关系，转而和新的主数据库同步
>   * slaveof no one
>     * 使当前数据库停止与其他数据库的同步，转为主数据库



### Redis主从复制案例演示

> 架构说明：
>
> * 一主二从；三台Linux虚拟机，每台安装Redis
> * 拷贝多个克隆文件：redis6739.conf、redis6380.conf、redis6381.conf
>
> 口诀：
>
> * 三边网络相互ping通（三台主机可以互相访问）且注意防火墙配置（确保主机端口开放）
> * 三大命令：
>   * 主从复制（写在redis.conf配置文件）：
>     * replicaof  主库IP  主库port；（配从（库）不配主（库））
>   * 改换门庭：
>     * slaveof 新主库IP 新主库port
>   * 自立为王：
>     * slaveof no one
>
> redis.conf配置文件（以master为例）：
>
> * 从出厂的最干净的配置文件开始配起：
>   * cp -r /opt/redis-7.0.12/redis.conf  /myredis/redis6379.conf
>
> 1. daemonize yes
>    1. 后台运行，不弹出命令窗口
> 2. protected-mode no
>    1. 关闭保护模式
> 3. #bind 127.0.0.1 
>    1. 直接注释掉或改为本机IP地址，否则影响远程IP连接
> 4. requirepass 自定义密码
> 5. port 6379
> 6. dir /myredis
> 7. pid文件，pidfile
>    1. 使用默认即可
> 8. logfile "/myredis/6379.log"
>    1. log文件名字，logfile
> 9. dbfilename dump6379.rdb
>    1. dump.rdb文件名字
> 10. appendonly  no&&   appendfilename   && appenddirname （不再开启）
>     1. aof文件名字，appendfilename
>     2. 使用默认即可，用不用都可以（因为有RDB）
> 11. 从机访问主机的通行密码masterauth（从机需要配置）
>     1. replicaof  主机IP  主机port
>     2. masterauth "主机密码"
>
> 常用三招：
>
> * 一主二仆：
>   * 方案一：配置文件固定写死
>     * redis.conf配置（配从库不配主库）：replicaof 主机IP 主机PORT   && masterauth   主机密码
>     * 配置完成后，先启动master在启动两台slave（注意启动redis-cli -a 密码 -p 端口    这里的端口不指定默认会访问6379）
>     * 主从关系查看：
>       * 日志：
>         * 主机日志（配置文件设定的日志名为6370.log：vim 6369.log）：
>           * Synchronazition xxxxx 6380 succeed   &&   Synchronazition xxxxx 6381 succeed
>         * 备机日志：
>           * Connecting xxx 6379     之后 Successful
>       * 命令：
>         * info replication：查看复制节点的主从关系和配置信息
>   * 主从问题演示：
>     * 从机可执行写命令吗：
>       * no，读写分离
>     * 从机切入点问题：
>       * slave是从头复制还是从切入点开始复制：
>         * 首次一锅端全部复制，之后master写，slave跟
>     * 主机shutdown后，从机会上位吗
>       * 原地待命；从机数据可正常使用，等待主机重启。
>     * 主机shutdown后，重启后主从关系还存在吗，从机是否还能顺利复制
>       * 可以
>     * 某台从机down后，master继续，从机重启后还能跟上大部队吗
>       * 和问题二一致，可以
>   * 方案二：命令操作手动指定
>     * 从机停机去除配置文件关联主机配置项；三台都是主机状态
>     * 配置文件没写死，在从机服务器上通过执行 “slaveof 新主库IP 新主库port”  命令再次完成主从复制
>     * 但是注意，使用命令设置主从关系，从机重启，关系失效
>   * 配置文件 和 命令 的区别：
>     * 配置持久稳定
>     * 命令单次生效
> * 薪火相传：
>   * 上一个slave可作为下一个slave的master，可有效减轻master写压力
>   * 中途变更转向：会清除之前的数据，重新建立拷贝最新的
>   * slaveof  新主库IP 新主库端口
>   * （是不是可以考虑在配置文件中，slave1设置了和master的主从关系，slave2设置了和slave1的主从关系）
> * 反客为主：
>   * slaveof no one



### 复制原理和工作流程

> slave首次全新连接master，一次完全同步（全量复制）将被自动执行，slave自身原有数据会被master数据覆盖清除
>
> master会将rdb快照文件和所有缓存的命令发送到slave，完成一次完全同步；slave接收到数据后，将其存盘并加载到内存中，完成复制初始化
>
> master 发出ping包的周期，默认10秒
>
> master继续将新收集到的修改命令自动传给slave
>
> 从机断开重连，master只会把已经复制的offset后面的数据复制给slave，类似端点续传

> 主从复制缺点：
>
> * 复制延时，信号衰减
> * master宕机
>   * 默认情况不会在slave节点中自动重选master
>   * 无人值守安装变成刚需
>     * 引出哨兵、集群





## Redis哨兵（sentinel）

### 哨兵值守基本介绍

> Redis哨兵的高可用性：https://redis.io/docs/management/sentinel/
>
> * High availability for non-clustered Redis：非Redis集群的高可用性；
>   * 即两种：主从复制 + 哨兵；纯Redis集群
>
> Redis Sentinel是什么：
>
> * 哨兵（吹哨人）巡查监控后台master主机是否故障，若出现故障就会根据投票数自动将某一个从库转换为新主库，继续对外服务。
> * 哨兵（一般配置三台）的作用：俗称就是  无人值守运维
>   * 监控Redis运行状态，包括master主机和slave从机
>   * 当master宕机，能自动将slave切换为新的master
>
> Redis Sentinel能做什么（宏观层面Sentinel功能）：
>
> * Monitoring 主从监控：
>   * Sentinel会不断地监控Redis主从运行是否正常
> * Notification  消息通知：
>   * Sentinel可将故障转移的结果发送给客户端
> * Automatic failover 故障转移：
>   * 若master异常，则会进行主从切换，将其中一个slave作为新的master
> * Configuration provider  配置中心：
>   * 客户端通过连接哨兵来获得当前Redis服务的主节点地址

### Redis Sentinel案例演示

> 前提说明  Redis Sentinel架构：
>
> * 三个哨兵（官方建议，哨兵一定要配集群也就是开启多个哨兵）：自动监控和维护集群，不能存放数据，只是吹哨人
> * 一主两从：用于数据读取和存放
>
> 案例步骤：
>
> * Redis安装目录下默认的sentinel.conf文件（这里是/opt/redis.../sentinel.conf）拷贝到/myredis目录（运行Redis服务）下进行配置
>
>   * 重点配置项参数说明：
>     * sentinel monitor < master-name> < ip> < redis-port> < quorum>：设置要监控的master服务器
>       * 默认是sentinel monitor mymaster 127.0.0.1 6379 2
>       * quorum：确认客观下线的最少的哨兵数量（最少有几个哨兵认可客观下线，同意故障迁移的法定票数）
>         * 意思是至少有quorum个Sentinel认为这个master有故障，才会对这个master进行下线以及故障转移
>         * 网络是不可靠的，某个Sentinel节点可能因为自身网络等原因，无法连接master，而此时的master并未出现故障，所以就需要多个Sentinel都一致认为该master出现问题才可进行下一步操作，保证了公平性、高可用
>     * sentinel auth-pass < master-name> < password>：连接master主机的密码
>
> * ==本次Redis Sentinel文件（sentinel.conf）通用配置：==
>
>   * 由于机器硬件关系，本应另起三台服务器来配置哨兵（哨兵默认端口号26379），这里将三个哨兵全部配置在master主机上
>
>   * 分别建立三个哨兵配置文件
>
>     * sentinel26379.conf、sentinel26380.conf、sentinel26381.conf；配置如下：
>
>       * ```sh
>         bind 0.0.0.0
>         daemonize yes
>         protected-mode no
>         port 26379
>         logfile "/myredis/sentinel26379.log"
>         pidfile /var/run/redis-sentinel26379.pid
>         dir /myredis
>         sentinel monitor mymaster 192.168.222.129 6379 2
>         sentinel auth-pass mymaster 001214
>         
>         bind 0.0.0.0
>         daemonize yes
>         protected-mode no
>         port 26380
>         logfile "/myredis/sentinel26380.log"
>         pidfile /var/run/redis-sentinel26380.pid
>         dir /myredis
>         sentinel monitor mymaster 192.168.222.129 6379 2
>         sentinel auth-pass mymaster 001214
>         
>         bind 0.0.0.0
>         daemonize yes
>         protected-mode no
>         port 26381
>         logfile "/myredis/sentinel26381.log"
>         pidfile /var/run/redis-sentinel26381.pid
>         dir /myredis
>         sentinel monitor mymaster 192.168.222.129 6379 2
>         sentinel auth-pass mymaster 001214
>         ```
>
> * 先启动Redis一主二从服务，测试正常的主从复制
>
>   * ==master主机redis6379.conf配置文件需要配置masterauth配置项==，因为后续可能会变为slave，需设置访问新主机的密码；
>   * 两台slave：和之前一样配置有 “replicaof 主机IP  主机PORT” ，“masterauth “主机密码” ”
>   * 在三台虚拟机下==启动一主二从Redis服务==
>
> * 以下是哨兵内容部分：
>
> ---------
>
> * ==启动三个哨兵，完成监控==：
>   * redis-sentinel  /myredis/sentinel26379.conf  --sentinel
>   * redis-sentinel  /myredis/sentinel26380.conf  --sentinel
>   * redis-sentinel  /myredis/sentinel26381.conf  --sentinel
>   * 启动哨兵完全不会影响主从复制的功能
> * ==手动关闭master服务器，模拟master主机宕机==：
>   * 问题思考：
>     * 两台从机数据复制未宕机前master主机数据是否正常：
>       * 数据正常
>       * 细节（主机宕机后，从机键入命令例如get xxx会出现broken pipe或sever closed the connection异常）：
>         * broken pipe表示对端的管道已经断开
>           * 当该异常产生并没多少影响，可能是某个客户端突然中止了进程导致的错误
>         * sever closed the connection
>     * 是否会从剩余的两台从机选出新的主机
>       * 会；哨兵投票选举
>     * 之前宕机的master重启是什么情况
>       * master宕机后，可以看到对应的sentinel日志（三个都可以看）中进行了重新选举，并将宕机的master转为slave和新选举的master主机设置为主从关系。
> * 对比配置文件：
>   * sentinel.conf会增多
>   * 原始的master配置文件 redis6379.conf  末尾新增配置信息：
>     * 新增replicaof配置项，配置了新主机的配置信息；
>     * 还有Redis持久化RDB快照模式的备份条件save xxx xxx
>   * 新选举出的master配置文件（例如redis6381.conf）：
>     * 之前replicaof配置项配置的主机信息自动被清除了
>     * 末尾也添加了信息 
>   * 结论：
>     * 配置文件内容，在运行期间会被Sentinel动态的进行修改
>     * 主从切换之后，主机redis.conf、从机redis.conf、哨兵sentinel.conf配置文件中的内容都会发生改变：
>       * 即原始主机会新增replicaof配置项，原始从机会去除replicaof配置项，哨兵配置文件的监控目标会随之调换
>     * 其他备注：
>       * 生产都是不同机房不同服务器，很少会出现三个哨兵全部进程全部挂掉的情况（本身也就是做主从监控、消息通知、故障转移这些操作的，不存在高并发这类情况，所以基本不会出现）
>       * 哨兵也可以同时监控多个master（sentinelxxx.conf文件进行一行一个的配置即可，不常用）



### 哨兵运行流程和选举原理

> 中小厂配置：三个哨兵监控一主二从
>
> SDown主观下线：
>
> * SDown（主观不可用）是单个Sentinel自己主观检测到master的宕机状态；从哨兵Sentinel角度，若发送ping心跳后，一定时间内没收到合法回复，就达到了SDown的条件
>   * 在Sentinel配置文件的down-after-milliseconds设置了判断主观下线的时间长度，默认30秒。
>
> ODown客观下线：
>
> * ODown需要一定数量的哨兵Sentinel，多个哨兵达成一致意见才能认为一个master主机客观上宕机。保证了公平性和高可用
>   * quorum这个参数就是进行客观下线的一个依据；
>
> 选举领导者哨兵：
>
> * 当主节点被认定客观下线，各个哨兵节点会进行协商，会先选举出领导者哨兵节点（兵王）并由该领导者节点进行failover故障转移。（兵王促成slave>>>master）
> * 哨兵领导者，兵王是如何被选出来的：
>   * Raft算法：
>     * 监视该主节点的所有哨兵都有可能称为领导者，Raft算法的基本思路就是先到先得：
>       * 即在一轮选举中，哨兵A向B发送成为领导者的申请，若B未同意过其他哨兵，则会同意A称为领导者leader
>         * 先选出leader哨兵领导者，然后在由leader选举出master（日志中是switch-master）
>
> 由leader哨兵领导者推动故障转移流程并选出一个新master：
>
> 1. 新主登基：
>    1. 选出master规则：权限高>>>复制偏移量大（复制的多）>>>runID每个Redis运行会有一个ID，谁小选谁
> 2. 群臣俯首
>    1. slaveof no one
> 3. 旧主拜服
>
> 无人值守安装配置

哨兵使用建议

> 哨兵节点数量应为多个，奇数；本身应该是集群，保证高可用
>
> 各个哨兵节点配置应保持一致
>
> 若哨兵节点部署在Docker等容器中，尤其要注意端口的正确映射
>
> 哨兵集群 + 主从复制，并不能保证数据零丢失（master断开后，投票和选举新master并不是瞬时的，写操作中断可能导致数据流失）：
>
> * 引出集群








## Redis集群（cluster）

### Redis Cluster集群基本介绍

> https://redis.io/docs/reference/cluster-spec/
>
> Redis集群是什么：
>
> * 由于数据量过大，单个master复制集难以承担，因此需要对多个复制集进行集群，形成水平扩展每个复制集值负责存储整个数据集的一部分，这就是Redis集群，作用就是提供在多个Redis节点间共享数据的程序集。
> * 一句话：Redis集群是一个提供在多个Redis节点间共享数据的程序集，可支持多个master。
>
> Redis Cluster集群能做什么：
>
> * Redis Cluster支持多个master，每个master又可以挂载多个slave：
>   * 读写分离；支持数据的高可用；支持海量数据的读写存储操作
> * 由于Cluster集群自带Sentinel哨兵的故障转移机制，内置了高可用的支持，无需再去使用哨兵功能.
> * 客户端与Redis的节点连接，无需连接集群中所有节点，只需任意连接集群中一个可用节点即可
> * 槽位slot负责分配到各个物理服务节点，由对应的集群来负责维护节点、插槽和数据之间的关系



### 集群算法-分片-槽位slot

> Redis集群的槽位slot：
>
> * Redis集群没使用hash算法，而是使用了哈希槽的概念。
>   * 槽位最多16384个；Redis集群官网建议1000个以内
> * Redis集群有16384个哈希槽，每个key通过CRC16校验后对16384取模来决定放置哪个槽。集群每个节点负责一部分槽
>
> Redis集群的分片：
>
> * 使用Redis集群我们会将存储的数据分散到多台Redis机器上，这称为分片。简言之，集群中每个Redis实例都被认为是整个数据的一个分片。
> * 如何找到给定的分片：
>   * 为了找到给定key的分片，我们对key进行CRC16(key)算法处理并通过对总分片数量取模。然后，使用确定性哈希函数，这意味着给定的key将多次始终映射到同一个分片，我们可以推断将来读取特定key的位置。
>
> Redis集群的槽位和分片的优势：
>
> * 最大优势就是方便扩缩容和数据分派查找。
> * 这种结构很容易添加或删除节点：从一个节点将哈希槽移动到另一个节点并不会停止服务，所以无论删除或改变某个节点哈希槽的数量都不会造成集群不可用
>
> slot槽位映射（三种业界解决方案）：
>
> * 哈希取余分区（小厂）：
>   * hash(key)/Redis机器数量
>   * 优点：简单直接，只需要确认好节点数量；负载均衡、分而治之
>   * 缺点：扩缩容麻烦，某个Redis机器宕机，由于机器数量发生变化，导致hash取余全部数据重新洗牌
> * 一致性哈希算法分区（中厂）：
>   * 当服务器个数发生变动尽量减少影响客户端到服务器的映射关系。
>   * 三大步骤：
>     * 算法构建一致性哈希环：
>       * 它也是按照使用取模的方法，前面笔记介绍的节点取模法是对节点（服务器）的数量进行取模。而一致性Hash算法是对2^32取模，简单来说，一致性Hash算法将整个哈希值空间组织成一个虚拟的圆环；
>       * 把这个由2^32个点组成的圆环称为Hash环
>     * redis服务器IP节点映射：
>     * key落到服务器的落键规则：
>   * 优点：具备容错性、扩展性
>   * 缺点：数据倾斜问题（被缓存的对象大部分集中缓存在某一台服务器上）
> * 哈希槽分区（大厂）：
>   * HASH_SLOT = CRC16(key) mod 16384
>   * 哈希槽实质就是一个数组，数组[0,2^14 -1]形成hash slot空间。
>   * 解决均匀分配的问题，在数据和节点之间又加入了一层，把这层称为哈希槽（slot），用于管理数据和节点之间的关系，现在就相当于节点上放的是槽，槽里放的是数据。
>   * 槽解决的是粒度问题，相当于把粒度变大了，这样便于数据移动。哈希解决的是映射问题，使用key的哈希值来计算所在的槽，便于数据分配
>
> 经典面试题---为什么Redis集群的最大槽数是16384个：
>
> * 如果槽位65536，发送心跳信息的消息头达8k，发送心跳包过于庞大
> * redis集群主节点数量基本不可能超过1000个，16384个槽位够用了
> * 槽位越小，节点少的情况下，压缩比高，容易传输
>
> Redis集群不保证强一致性，意味着在特定条件下，Redis集群可能丢失掉一些被系统收到的写入请求命令





### Redis Cluster集群案例演示

#### 三主三从Redis集群配置

> 三台Linux虚拟机上，==新建集群目录/myredis/cluster==
>
> * mkdir -p /myredis/cluster
>
> 新建六个独立的Redis实例服务
>
> * 由于机器硬件限制，这里三个Linux虚拟机分别创建一主一从达到三主三从的Redis服务；
>
> * ==创建集群配置文件：==
>
>   * 例如IP 129的Linux上，新建两个Redis集群配置文件
>
>     * vim /myredis/cluster/redisCluster6381.conf   vim /myredis/cluster/redisCluster6382.conf
>
>     * ```sh
>       bind 0.0.0.0
>       daemonize yes
>       protected-mode no
>       port 6381
>       logfile "/myredis/cluster/cluster6381.log"
>       pidfile /myredis/cluster6381.pid
>       dir /myredis/cluster
>       dbfilename dump6381.rdb
>       appendonly yes
>       appendfilename "appendonly6381.aof"
>       requirepass 001214
>       masterauth 001214
>        
>       cluster-enabled yes
>       cluster-config-file nodes-6381.conf
>       cluster-node-timeout 5000
>       ```
>
>   * ==启动六台Redis主机实例：==
>
>     * redis-server /myredis/cluster/redisCluster6381.conf
>     * ........
>
> 通过redis-cli命令为6台Redis构建集群关系：
>
> * ==构建主从关系命令（注意自己的真实IP，防火墙端口一定要放开）：==
>
>   * ```sh
>     6381 16381
>     6382 16382
>     6383 16383
>     6384 16384
>     6385 16385
>     6386 16386
>     这些端口必须全部开放才能进行集群的创建！！！
>     ```
>
>   * ```sh
>     redis-cli -a 001214 --cluster create --cluster-replicas 1 192.168.222.129:6381 192.168.222.129:6382 192.168.222.130:6383 192.168.222.130:6384 192.168.222.131:6385 192.168.222.131:6386
>     
>     redis-cli -a 001214
>     --cluster create：集群形式的创建
>     --cluster-replicas 1：表示为每个master创建一个slave节点
>     
>     集群连接成功后 集群配置文件中cluster-config-file配置项指定的文件（nodes-6381.conf）会生成
>     ```
>
> ==链接进入6381作为切入点，查看并检验集群状态：==
>
> * 登录Redis：redis-cli -a 001214 -p 6381
> * cluster nodes：查看集群节点关系拓扑图指令
> * cluster info



#### 三主三从Redis集群读写

> 对6381新增两个key，查看效果：
>
> * 为什么会报错：
>   * 一定要注意槽位的范围区间，需要路由到位
> * 如何解决：
>   * 防止路由失效加参数-c，优化路由：==redis-cli -a 001214 -p 6381 -c==
>     * 之后连接Redis集群，-c避免路由失效
> * 查看某个key对应的槽位值：
>   * cluster keyslot 键名称



#### 主从容错切换迁移案例

> 容错切换迁移-------master主机停掉，对应的真实的slave会不会切换为master：
>
> * 首先确认该主机对应的真实从机是哪一台：cluster nodes指令
> * master主机停止后，再次查看集群信息：cluster nodes ；
>   * 可以看到被停掉的主机断开连接，对应的真实从机已被切换为master主机
> * 原始master再次重新连接启动，已自动切换为slave（info replication指令或cluster nodes指令都可以看到信息）
>
> 集群不保证数据一致性100%ok，一定会有数据丢失情况（写丢失的情况）
>
> 手动故障转移（节点从属调整）该如何处理：
>
> * 上面的当某个master停掉后，对应的真实slave会被切换为master；原master重启后会被自动切换为slave；如何再次将这两个主从关系对调：
>   * 登录原master
>   * ==cluster failover：手动进行集群的故障转移，也就是集群的节点从属调整。完成主从切换==



#### 主从扩容案例

> 三主三从的Redis集群扩容为四主四从：
>
> * ==新建6387、6388两个服务实例配置文件并启动：==
>
>   * vim /myredis/cluster/redisCluster6387.conf：还是使用最初创建集群的实例配置文件
>   * redis-server /myredis/cluster/redisCluster6387.conf：此时这两个都是master主机
>
> * ==将新增的6387节点（空槽号）作为master节点加入原集群（必须先开放节点端口和对应的总线端口）：==
>
>   * ```sh
>     开放端口！！！！！
>     6387端口 总线端口16377
>     6388 16388
>     
>     redis-cli -a 001214 --cluster add-node 192.168.222.131:6387 192.168.222.129:6381
>     
>     将新增的6387作为master节点加入原有集群
>     redis-cli -a 密码 --cluster add-node 自己实际IP地址:6387 自己实际IP地址:6381
>     6387 就是将要作为master新增节点
>     6381 就是原来集群节点里面的领路人，相当于6387拜拜6381的码头从而找到组织加入集群
>     ```
>
> * ==检查集群情况第一次：cluster nodes==
>
>   * ```sh
>     redis-cli -a 001214 --cluster check 192.168.222.129:6381
>     ```
>
>   * 可以看到新增的6387master暂时并未分配到槽号slot
>
> * ==重新分配槽号：reshard==
>
>   * ```sh
>     重新分派槽号
>     命令:redis-cli -a 密码 --cluster reshard IP地址:端口号
>     redis-cli -a 001214 --cluster reshard 192.168.222.129:6381
>     
>     会提示想分配多少槽号：4096（四个主机均分）
>     会提示由谁来接收：将6387的id粘贴过来
>     all
>     yes
>     ```
>
> * ==检查集群情况第二次：cluster nodes==
>
>   * ```sh
>     redis-cli -a 001214 --cluster check 192.168.222.129:6381
>     ```
>
>   * 可以看到新增的6387master已分配到槽号slot（4096个槽位）
>
>     * 为什么6387是3个新的槽位区间，以前的还是连续？重新分配成本太高，所以前3家各自匀出来一部分，从6381/6383/6385三个旧节点分别匀出1364个坑位给新节点6387
>
>   * 0 slave
>
> * ==为主节点6387分配从节点6388==
>
>   * ```sh
>     命令：
>     redis-cli -a 密码 --cluster add-node ip:新slave端口 ip:新master端口 --cluster-slave --cluster-master-id 新主机节点ID
>      
>     redis-cli -a 001214 --cluster add-node 192.168.222.131:6388 192.168.222.131:6387 --cluster-slave --cluster-master-id 2e754665b9d3346fc72654dbb3b67129cd14717a-------这个是6387的编号，按照自己实际情况
>     ```
>
> * ==检查集群情况第三次：cluster nodes==
>
>   * ```sh
>     redis-cli -a 001214 --cluster check 192.168.222.129:6381
>     ```
>
>   * 有一个slave



#### 主从缩容案例

> Redis集群缩容，删除6387和6388，恢复三主三从：
>
> * 先清除6388从节点；清出来的槽号重新分配给6381；再删除6387；恢复三主三从
>
> ==检查集群情况第一次，先获取节点6388节点ID：cluster nodes==
>
> * ```sh
>   redis-cli -a 001214 --cluster check 192.168.222.129:6381
>   ```
>
> ==从集群中将6388从节点删除：==
>
> * ```sh
>   命令：redis-cli -a 密码 --cluster del-node ip:从机端口 从机6388节点ID
>    
>   redis-cli -a 001214 --cluster del-node 192.168.222.131:6388 e85ff301210617b88157bc79835096fd760918dc
>   ```
>
> * redis-cli -a 001214 --cluster check 192.168.222.129:6381：检查集群情况可以看到当前集群只有七台机器
>
> ==将6387槽号清空重新分配（这里全部分配给6381）：==
>
> * ```sh
>   redis-cli -a 001214 --cluster reshard 192.168.222.129:6381
>   
>   4096
>   接收ID：6381的id
>   原id：6387的id
>   done
>   yes
>   ```
>
> ==检查集群情况第二次：cluster nodes==
>
> * ```sh
>   redis-cli -a 001214 --cluster check 192.168.222.129:6381
>   ```
>
> * 此时会有三主四从，6387变成了6381的从机
>
> ==删除6387：==
>
> * ```sh
>   命令：redis-cli -a 密码 --cluster del-node ip:从机端口 从机6388节点ID
>    
>   redis-cli -a 001214 --cluster del-node 192.168.222.131:6387 2e754665b9d3346fc72654dbb3b67129cd14717a
>   ```
>
> ==检查集群情况第三次：cluster nodes==
>
> * ```sh
>   redis-cli -a 001214 --cluster check 192.168.222.129:6381
>   ```
>
> * 三主三从（就是6381槽位多一些）





### 集群常用操作命令和CRC16算法分析

> 不在同一个槽位slot下的多键（例如mset、mget）操作不支持，引出占位符：
>
> * 可通过{ }来定义同一个组的概念，使key中{ }相同内容的键值对放在同一个slot槽位
>
>   * ```sh
>     mset k1{z} z1 k2{z} z2 k3{z} z3
>     
>     mget k1{z} k2{z} k3{z}
>     ```
>
> Redis集群有16384个哈希槽，每个key通过CRC16校验后对16384取模来决定放置在哪个槽，集群的每个节点负责一部分哈希槽。
>
> 常用命令：
>
> * 集群是否完整才能对外提供服务：
>
>   * cluster-require-full-coverage   yes  （默认就是yes，推荐也是yes）
>
>   * 默认YES，现在集群架构是3主3从的redis cluster由3个master平分16384个slot，每个master的小集群负责1/3的slot，对应一部分数据。
>
>     cluster-require-full-coverage： 默认值 yes , 即需要集群完整性，方可对外提供服务 通常情况，如果这3个小集群中，任何一个（1主1从）挂了，你这个集群对外可提供的数据只有2/3了， 整个集群是不完整的， redis 默认在这种情况下，是不会对外提供服务的。
>
>   * 如果你的诉求是，集群不完整的话也需要对外提供服务，需要将该参数设置为no ，这样的话你挂了的那个小集群是不行了，但是其他的小集群仍然可以对外提供服务。
>
> * cluster countkeysinslot  槽位数字编号：
>
>   * 判断指定槽位是否被key占用，0没占用
>
> * cluster keyslot 键名称：
>
>   * 该键应该存在哪个槽位上
>
> * cluster nodes：获取每个节点信息





## SpringBoot集成Redis

> Jedis、Lettuce、RedisTemplate三者的联系
>
> 本地Java连接Redis常见问题：
>
> * bind配置注释掉
> * protected-mode保护模式设置no
> * Linux系统的防火墙白名单设置
> * Redis服务器IP地址和密码
> * 访问Redis服务器的PORT和auth密码

### 集成Jedis

> Jedis是什么：
>
> * Jedis Client是Redis官网推荐的一个面向Java客户端，库文件实现了对各类API进行封装调用
>
> 集成步骤：
>
> * 创建Module
> * POM文件配置
>   * 引入jedis依赖
> * YML文件配置
> * 主启动类
> * 业务类



### 集成Lettuce

> Lettuce是什么：
>
> * Lettuce是一个Redis的Java驱动包
>
> Jedis和Lettuce区别：
>
> * 都可以连接Redis服务器，但在SpringBoot2.0之后默认都是使用的Lettuce这个客户端连接Redis服务器，Lettuce底层使用的Netty，线程安全的



### 集成RedisTemplate-推荐

#### 连接单机

> 序列化问题：
>
> 1. 使用StringRedisTemplate类，解决了序列化问题
> 2. 使用RedisTemplate类，但要在配置类定义序列化的工具





#### 连接集群

> 







# Redis高阶---0818

> 技术要求：微服务BootCloud、Docker容器、Nginx反向代理、JUC多线程、JMeter

## Redis单线程 vs 多线程





## BigKey





## 缓存双写一致性之更新策略探讨





## Redis与MySQL数据双写一致性工程落地案例





## 案例落地实战BitMap/HyperLogLog/GEO





## 布隆过滤器BloomFilter





## 缓存预热、雪崩、击穿、穿透







## 手写Redis分布式锁





## Redlock算法和底层源码分析





## Redis经典五大数据类型源码及底层实现





## Redis为什么快？高性能设计之epoll和IO多路复用深度解析





## 终章の总结





# 高阶篇有些知识点未掌握，先看Redis6

## Redis6应用问题解决

### 缓存穿透

> 问题描述---出现缓存穿透的几个现象：
>
> * 应用服务器（用户通过浏览器访问Web应用服务器，应用服务器再去访问数据库数据，数据库数据又可通过Redis进行缓存）压力突然变大了（即突然有大量请求）
>   * 出现了很对非正常url访问
> * Redis命中率降低了（即访问Redis缓存数据库获取不存在数据）
> * 一直查询关系型数据库，最终造成数据库崩溃（缓存没有起到作用，一直访问数据库，这种过程就叫做缓存穿透）
>
> 出现缓存穿透现象的原因：
>
> * 网站遭受到了黑客攻击/恶意攻击
>
> 解决方案（四种）：
>
> 1. 对空值缓存---只能算是临时的应急解决方案：
>    * 若一个查询返回的数据为空（不管数据是否存在），我们仍将这个null结果进行缓存，为空结果设置很短的过期时间，最长不超过五分钟
> 2. 设置可访问的名单（白名单）---缺点就是每次即使拦截也会访问BitMap中，效率不会很高：
>    * 使用BitMap位图类型定义一个允许访问的名单，名单ID作为BitMap的偏移量；每次访问和BitMap中的ID进行比较，若访问ID不在其中，拦截不允许访问。
> 3. 采用布隆过滤器：
>    * 布隆过滤器Bloom Filter实际是一个很长的二进制向量（位图）和一系列随机映射函数（哈希函数）。
>    * 可用于检索一个元素是否在一个集合中。
>    * 将所有可能存在的数据哈希到一个足够大的BitMap中，一个一定不存在的数据就会被BitMap拦截掉，从而避免对底层存储系统的查询压力。
>    * 优点：空间效率和查询时间都远远优于一般的算法；
>    * 缺点：有一定的误识别率，并且删除困难
> 4. 进行实时监控：
>    * 当发现Redis命中率急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务。




### 缓存击穿

> 问题描述---出现缓存击穿的几个现象：
>
> * 数据库瞬时访问量过大
> * Redis缓存数据库中并未出现大量的key过期
> * Redis是正常的运行状态
>
> 出现缓存击穿的原因：
>
> * Redis缓存中某个key过期了，且刚好伴随着大量的访问使用到了该key
>
> 缓存击穿问题的解决方案：
>
> * key可能会在某些时间点被超高并发的访问，是一种非常热点的数据，此时就需要考虑缓存被击穿的问题
>
> 1. 预先设置热门数据：
>    * 在Redis高峰访问前，把一些热门数据提前缓存到Redis中，加大热门数据key的过期时长
> 2. 实时调整：
>    * 现场监控哪些数据热门，实时调整key的过期时长
> 3. 使用锁：
>    * 优点：绝对可以避免缓存击穿的问题
>    * 缺点：效率很低



### 缓存雪崩

> 问题描述---出现缓存雪崩的几个现象：
>
> * 数据库压力变大
>   * 应用服务器响应时间也随之变慢
>   * Redis中也就会有大量的访问等待
>   * 最后造成数据库、应用程序、Redis都崩溃了
>
> 出现缓存雪崩的原因：
>
> * 极短的时间段内，key出现了大量的集中过期情况（Redis缓存key批量过期）
>
> 缓存雪崩问题的解决方案：
>
> * 缓存失效时的雪崩效应对底层会产生很可怕的冲击
>
> 1. 构建多级缓存架构---对程序结构要求过于复杂：
>    * nginx缓存+redis缓存+其他缓存（ehcache等）
> 2. 使用锁或队列---效率极低，不适用高并发：
>    * 用加锁或队列的方式来保证不会有大量的线程对数据库进行读写，从而避免失效时大量的并发请求落到底层存储系统中。不适用高并发情况
> 3. 设置过期标志更新缓存---这个还需要做些操作：
>    * 记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存
> 4. 将缓存失效时间分散开：
>    * 例如可在原有的失效时间基础之上增加一个随机值（例如1-5分钟随机），这样每一个缓存的过期时间的重复率就会降低，很难引发key集体失效的事件。



### 分布式锁

> 分布式锁主流的实现方案（每种分布式锁解决方案都有各自优缺点）：
>
> * 基于数据库实现分布式锁
> * 基于缓存（Redis等）
> * 基于Zookeeper
>
> 性能方面：基于Redis缓存来实现分布式锁最高
>
> 可靠性：Zookeeper实现分布式锁最高
>
> 基于Redis实现分布式锁：
>
> * setnx 键 值：（Set If Not Exist）只有当key不存在才会设置key值
>   * 相当于 “set key value nx”
>   * 第一次设置后，之后再想设置都不会成功，除非key不存在了（del key）
>   * 但要考虑到key的锁的时间，也不能一直锁着
>     * 通过setnx 上锁，通过del释放锁
>     * 若锁一直未释放，设置key的过期时间，自动释放
>       * setnx key value
>       * expire key seconds
>       * 上面的操作需要两条指令，非原子操作，不能保证上锁之后一定会设定key的过期时间
>         * set key value nx ex seconds
> * setex 键 秒 值：（Set With Expire） 设置键值对的同时设置过期时间
>   * 相当于 “set key value ex 秒”
>
> 优化UUID防误删：
>
> * set lockkey uuid nx ex seconds
>   * 通过UUID表示不同的操作
> * 释放锁，首先判断当前UUID和要释放的UUID是否一致
>
> LUA保证删除原子性：
>
> * 上述使用Redis进行上锁解锁过程，缺乏原子性：
>   * 若程序A对key上锁，执行完具体操作后，判断uuid一致之后要删除锁，还未删除锁时，锁到了过期时间，自动释放，程序B获取到锁，那么之后A删除的锁就不是自己的当前程序A的锁而是程序B的锁了；
>     * 引出LUA脚本（保证原子性）
>
> 为了确保分布式锁可用，至少要确保锁的实现同时满足一下四个条件：
>
> * 互斥性：任意时刻只能一个客户端持有锁
> * 不会发生死锁：即使一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁（通过设置过期时间）
> * 解铃还须系铃人：加锁解锁必须在同一个客户端，客户端不能将其他客户端的锁去除
> * 加锁解锁必须具有原子性







### Redis6新特性

> ACL：
>
> * acl list命令展现用户权限列表：
>   * user default on sanitize-payload #6a7f32f492e3126d06b7f648e9455314002b33de30bf02fe1eb1a86fca917ed2 ~* &* +@all
>     * 从default开始：用户名 是否启用 密码（没密码nopass）可操作性的key 可执行的命令
> * acl cat命令查看添加权限指令类别
>   * acl cat 数据类型：可以查看指定的数据类型的操作指令
> * acl whoami：查看当前用户
> * acl setuser  用户名：添加用户
> * auth 用户名 密码：切换用户
>
> Redis依然是单线程 + 多路IO复用










# 末尾

