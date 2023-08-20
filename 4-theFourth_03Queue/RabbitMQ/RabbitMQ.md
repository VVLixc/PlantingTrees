# RabbitMQ

> RabbitMQ是流行的消息队列服务软件，是开源的AMQP（Advanced Message Queuing Protocol高级消息队列协议）实现。
>
> 支持多种客户端（如Java、Python、C、JS、PHP、Ruby等等），用于在分布式系统中存储转发消息。
>
> 可实现异步处理、流量削峰、系统解耦；易用性、扩展性、高可用等方面表现优异。
>
> 学习要求：
>
> * Java、Maven、SpringBoot

RabbitMQ 3.8.8

## 消息队列

### MQ相关概念

> **什么是MQ：**
>
> * MQ（Message Queue），本质就是个==队列==，==FIFO先进先出==，==队列中存放的内容就是Message==；
> * 同时还是一种==跨进程的通信机制==，用于==上下游传递消息==（例如A发送微信给B，A就是上游，B就是下游；他们之间就是在传递消息）；
> * 互联网架构中，==MQ是一种非常常见的 “逻辑解耦 + 物理解耦” 的消息通信服务；==
> * ==使用MQ之后，消息发送上游只需依赖MQ==，无需再依赖其他服务。
>
> **为什么要用MQ：**
>
> 1. ==流量削峰：==
>
>    * ==MQ对访问进行排列达到削峰的目的；（客户端和服务器端之间添加消息队列作为缓冲层）==
>    * 拿订单举例，若订单系统处理的极限是10000次/秒，若在下单高峰，系统肯定处理不了；
>      * 使用消息队列来做缓冲，将订单进行排队处理，把1秒内下的订单分散成一段时间来进行处理，就达到了流量削峰的功能
>        * 当然排队分散时间来进行处理，==访问速度也就是效率肯定会降低，但总比无法下单的情况要好==。
>
> 2. ==应用解耦/系统解耦：==
>
>    * ==MQ可作为系统之间的缓冲，这样就不会出现某个系统出现故障导致整个流程失败。提高系统的可用性==
>
>    * 拿电商系统举例，应用中有订单、库存、支付、物流系统；用户创建订单后，若耦合调用库存、支付、物流系统，任何一个子系统出现故障，都会造成下单操作异常。
>      * 当基于消息队列方式，系统间相互调用的问题会减少（消息队列会作为订单系统和其他系统之间的缓冲层）；
>        * 比如物流系统出现故障，物流系统要处理的数据会被缓存到消息队列中，不会影响到用户下单操作。
>          * 当物流系统故障恢复，继续处理订单信息即可。
>      * ![image-20230819175256756](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230819175256756.png)
>
> 3. ==异步处理：==
>
>    * 某些服务间调用是异步的，例如A调用B，B需花费很长时间执行，但A需要知道B何时执行完成；
>      * ==之前有两种方式解决---不够优雅：==
>        * A过些时间调用B的查询API进行查询
>        * A提供一个CallBack回调API，B执行完成之后调用API通知A服务
>      * ==使用MQ：==
>        * ==A调用B后，只需监听B处理完成的消息，B处理完成会将消息给MQ，MQ再将此消息转发给A。==
>          * 这样A既不用循环调用B的查询API，也无需提供回调API；B同样也无需做这些操作；A服务还能及时得到异步处理成功的消息。
>
> **MQ的分类：**
>
> * ActiveMQ：
>   * 吞吐量万级，时效性ms，可用性高，很低概率丢失数据
>   * 社区维护很少，高吞吐量场景较少使用
> * Kafka：
>   * 为大数据而生，百万级TPS的吞吐量；在大数据领域以及日志采集功能被大规模使用
>   * 队列越多，Load会明显飙高，社区更新较慢
> * RocketMQ：
>   * 吞吐量十万级，消息可做到零丢失，支持十亿级别的消息堆积
>   * 支持的客户端语言不多
> * RabbitMQ：
>   * 当前最主流的消息中间件之一，高并发性能好，吞吐量万级，支持多种语言，社区活跃度高
>   * 商业版需收费，学习成本较高
>
> **MQ的选择：**
>
> * Kafka：
>   * 适用于大型公司收集大量数据，如日志采集功能
> * RocketMQ：
>   * 天生为金融互联网领域而生，具有更好的可靠性、稳定性
> * RabbitMQ：
>   * 性能好，时效是微秒级别，功能完备，社区活跃度比较高；数据量没那么大的中小型公司优先选择。



### RabbitMQ

> **RabbitMQ的概念：**
>
> * ==RabbitMQ是一个消息中间件：==
>   * 它负责接收并转发消息。
>   * ==可将RabbitMQ看做一个快递站点：==
>     * 发送包裹，放到站点，快递员快递到收件人
>     * RabbitMQ和快递站主要区别就是，它不负责处理，而是==接收，存储和转发消息数据。==
>
> **四大核心概念：**
>
> * ==生产者：==
>   * ==产生数据发送消息的程序是生产者。==
> * ==交换机：==
>   * 是RabbitMQ非常重要的一个组件，它==一方面接收来自生产者的消息，另一方面会将消息推送到队列中。==
>   * ==交换机必须确切清楚如何处理接收到的消息，是将消息推送到特定队列还是多个队列亦或是将消息丢弃；是由交换机类型决定的。==
> * ==队列：==
>   * 队列是==RabbitMQ内部使用的一种数据结构==，尽管==消息流经RabbitMQ和应用程序，但它们只能存储在队列中。==
>   * ==队列仅受主机内存和磁盘限制的约束，本质是一个大的消息缓冲区。==
>   * ==许多生产者可以将消息发送到同一个队列，许多消费者可以尝试从一个队列接收数据。==
> * ==消费者：==
>   * ==消费者大多情况是一个等待接收消息的程序。==
>   * ==生产者、消息中间件和消费者很多情况下并不在同一机器上。==
>   * ==同一个应用程序既可以是生产者又可以是消费者。==
>
> * ![MQ的核心组件](D:\PlantingTrees\selfStudy\JavaStudyPlanAndNotes\4-theFourth_03Queue\RabbitMQ\RabbitMQ教程\MQ的核心组件.jpg)
>
> **RabbitMQ核心部分：**
>
> * ==六大核心部分/六大模式：==
>   * ==Hello World 简单模式==
>   * ==Work Queues 工作模式==
>   * ==Publish/Subscribe 发布/订阅模式==
>   * ==Routing 路由模式==
>   * ==Topics 主题模式==
>   * ==Publisher Confirms 发布确认模式==
>
> **名词介绍：**
>
> * Broker：
>   * 接收和分发消息的应用。
>   * 叫做RabbitMQ Server（RabbitMQ服务器）也叫做Message Broker（消息实体）
> * Exchange：
>   * 交换机（每个RabbitMQ消息实体（Message Broker）中可以有多个交换机）
> * Queue：
>   * 队列（一个交换机可对应多个队列）
> * Producer：
>   * 生产者
> * Consumer：
>   * 消费者
> * Channel：
>   * 信道；即发消息的通道
>   * 每个生产者/消费者和Broker消息实体之间会建立一个连接的TCP连接Connnection，每一个连接中会有多个Channel信道
> * Virtual host：
>   * 每个Broker中可已有多个Virtual host，每个Virtual host中又可以有多个Exchange和Queue
> * Binding：
>   * Exchange和Queue之间的连线
>
> **RabbitMQ安装：**
>
> * 官网：https://www.rabbitmq.com/download.html
> * Linux指令：
>   * cat /etc/*-release：查看Linux发行版发布文件内容（发行版名称、版本号、发行日期等）
>   * ip addr
>   * uname -a：查看当前系统版本
>     * 使用该命令是确认当前Linux系统信息：el7 所以下载RabbitMQ也要下载对应的el7
> * 文件上传：
>   * 将两个文件上传到/opt目录下
> * 安装文件：
>   * rpm -ivh erlang-23.3.4.18-1.el7.x86_64.rpm
>   * yum install socat -y
>   * rpm -ivh rabbitmq-server-3.9.16-1.el7.noarch.rpm
> * 常用命令：
>   * 添加开机启动RabbitMQ服务：
>     * chkconfig rabbitmq-server on
>     * 或者 setup >>> 系统服务 >>> space选中*号
>   * 启动服务：
>     * /sbin/service rabbitmq-server start
>     * 或systemctl start rabbitmq-server.service
>   * 查看服务状态：
>     * /sbin/service rabbitmq-server status
>     * 或systemctl status rabbitmq-server.service
>   * 停止服务（选择执行）：
>     * /sbin/service rabbitmq-server stop 
>     * 或 systemctl stop rabbitmq-server.service
>   * 开启web管理插件（RabbitMQ后台管理界面 方便操作管理）：
>     * rabbitmq-plugins enable rabbitmq_management
>   * 用默认账号密码(guest)访问地址  http://Linux系统IP:15672/ 出现权限问题
>     * 注意防火墙放开端口  （5672和15672两个端口都要放开）
>       * 也可以关闭防火墙
>         * systemctl stop firewalld
>         * systemctl enable firewalld 保证下次开机时防火墙也不再开启
> * 添加一个新的用户：
>   * 创建账号
>     * rabbitmqctl add_user 用户名 密码
>   * 设置用户角色
>     * rabbitmqctl set_user_tags 用户名 administrator
>       * administrator 赋予了超级管理员的权限
>   * 设置用户权限set_permissions [-p <vhostpath>] <user> <conf> <write> <read>
>     * rabbitmqctl set_permissions -p "/" 用户名 ".*" ".*" ".*"
>     * 用户 user_admin 具有/vhost1 这个 virtual host 中所有资源的配置、写、读权限
>       * guest就是因为没有这一步才登录不上
>   * 当前用户和角色
>     * rabbitmqctl list_users
> * 再次利用admin用户登录
> * 重置命令：
>   * 关闭应用的命令为
>     * rabbitmqctl stop_app
>   * 清除的命令为
>     * rabbitmqctl reset
>   * 重新启动命令为
>     * rabbitmqctl start_app



## Hello World

### 依赖

```xml
<!--指定 jdk 编译版本-->
<build>
 <plugins>
 <plugin>
 <groupId>org.apache.maven.plugins</groupId>
 <artifactId>maven-compiler-plugin</artifactId>
 <configuration>
 <source>8</source>
 <target>8</target>
 </configuration>
 </plugin>
 </plugins>
</build>
<dependencies>
 <!--rabbitmq 依赖客户端-->
 <dependency>
 <groupId>com.rabbitmq</groupId>
 <artifactId>amqp-client</artifactId>
 <version>5.8.0</version>
 </dependency>
 <!--操作文件流的一个依赖-->
 <dependency>
 <groupId>commons-io</groupId>
 <artifactId>commons-io</artifactId>
 <version>2.6</version>
 </dependency>
</dependencies>
```



### 消息生产者





### 消息消费者







## Work Queues







## 发布确认







## 交换机







## 死信队列







## 延迟队列







## 发布确认高级







## RabbitMQ其他知识点







## RabbitMQ集群














































# 末尾