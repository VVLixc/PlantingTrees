# Redis7---0809

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
















# 末尾

