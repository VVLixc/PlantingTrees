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














# 末尾

