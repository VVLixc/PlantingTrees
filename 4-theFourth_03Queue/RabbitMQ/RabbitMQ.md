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

> Work Queues工作队列模式（又称作任务队列）

### 轮训分发消息

> 轮询分发消息就是多个消费者接收生产者的消息时，会一个个排好队进行消息的接收（你一条我一条，不会出现分配不均的情况）
>
> 一个消息只能处理一次，不可以处理多次。
>
> 工作线程（消费者）之间处于竞争的关系
>
> 此案例中会启动两个工作线程（工作线程就是消费者）
>
> 抽取工具类：
>
> * 因为每个Producer/Consumer的创建都会创建连接工厂并创建信道；将这些重复代码抽取出来。



### 消息应答

> 消息应答概念：
>
> * 为了保证消息在发送过程中不会丢失，RabbitMQ引入了消息应答机制：
>   * 消费者在接收到消息并处理该消息之后，告诉RabbitMQ它已经处理了，RabbitMQ可以把消息删除了。
>
> 自动应答（不推荐，尽量使用手动应答）：
>
> * 自动应答并不完善；需要在高吞吐量和数据传输安全性两者之间做权衡；
>   * 若消息在接收到之前，消费者那边出现连接或Channel关闭，消息就会丢失了
> * 该模式仅适用在消费者可以高效并以某种速率能够处理这些消息的情况下使用。
> * 默认消息采用的自动应答
>
> 手动应答：
>
> * channel.basicAck：用于肯定确认
>   * RabbitMQ接收到该消息成功处理的信息，可以将其丢弃了
> * channel.basicNack：用于否定确认
> * channel.basicReject：用于否定确认
>   * Reject（拒绝）与basicNack相比少一个参数（是否批量处理的参数multiple）
>   * 不处理该消息了 直接拒绝，可将其丢弃了
>
> Multiple的解释：
>
> * 手动应答的好处就是可以批量应答并且减少网络阻塞；
> * channel.basicAck(xxxxx, true)；此处的true就表示批量应答
> * 例如信道中有5,6,7,8四个消息，当接收8并完成处理后，
>   * multiple为true时，表示5,6,7,8都会被确认应答
>   * multiple为false时，5,6,7不会被确认应答，只有8会被确认应答（建议使用false，可保证消息最大程度不丢失）
>
> 消息应答重新入队（消息自动重新入队）：
>
> * 若消费者由于某些原因失去连接，导致消息未发送ACK确认，RabbitMQ就会自动对其进行重新排队。
>   * 这样其他消费者一样可以处理；
>   * 所以即使某个消费者偶尔死亡，也可以确保不丢失任何消息。
>
> 消息手动应答Demo：
>
> * 消息默认采用的自动应答，需改为手动应答（消息手动应答可保证消息不丢失，未ACK确认的消息会 被自动重新入队）
>
> * 生产者：
>
>   * ```java
>     public class ProducerAck {
>         public static final String QUEUE_NAME="Ack_Queue";
>         public static void main(String[] args) throws Exception {
>             Channel channel = RabbitMQUtils.getChannel();
>             //创建队列
>             channel.queueDeclare(QUEUE_NAME, false, false, false, null);
>             Scanner scanner = new Scanner(System.in);
>             while (scanner.hasNext()){
>                 String message=scanner.next();
>                 System.out.println("生产者消息："+message);
>                 channel.basicPublish("", QUEUE_NAME, null, message.getBytes(StandardCharsets.UTF_8));
>             }
>         }
>     }
>     ```
>
> * 消费者01：
>
>   * ```java
>     public class ConsumerAck01 {
>         public static final String QUEUE_NAME="Ack_Queue";
>         public static void main(String[] args) throws Exception{
>             Channel channel = RabbitMQUtils.getChannel();
>             boolean autoAck=false;
>             DeliverCallback deliverCallback= (consumerTag, message) -> {
>                 SleepUtils.sleep(1);
>                 System.out.println("接收到消息"+new String(message.getBody()));
>                 channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
>             };
>             CancelCallback cancelCallback= consumerTag -> System.out.println("消费者取消消费接口回调逻辑");
>             System.out.println("消费者01---等待接收消息（手动应答功能测试）...");
>             channel.basicConsume(QUEUE_NAME,autoAck,deliverCallback,cancelCallback);
>         }
>     }
>     ```
>
> * 消费者02：
>
>   * ```java
>     public class ConsumerAck02 {
>         public static final String QUEUE_NAME="Ack_Queue";
>         public static void main(String[] args) throws Exception{
>             Channel channel = RabbitMQUtils.getChannel();
>             boolean autoAck=false;
>             DeliverCallback deliverCallback= (consumerTag, message) -> {
>                 SleepUtils.sleep(30);
>                 System.out.println("接收到消息"+new String(message.getBody()));
>                 channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
>             };
>             CancelCallback cancelCallback= consumerTag -> System.out.println("消费者取消消费接口回调逻辑");
>             System.out.println("消费者02---等待接收消息（手动应答功能测试）...");
>             channel.basicConsume(QUEUE_NAME,autoAck,deliverCallback,cancelCallback);
>         }
>     }
>     ```
>
> * 正常情况下消息发送方发送两个消息 C1 和 C2 分别接收到消息并进行处理
>
>   * 在发送者发送消息 dd，发出消息之后的把 C2 消费者停掉，按理说该 C2 来处理该消息，但是由于它处理时间较长，在还未处理完，也就是说 C2 还没有执行 ack 代码的时候，C2 被停掉了，此时会看到消息被 C1 接收到了，说明消息 dd 被重新入队，然后分配给能处理消息的 C1 处理了




### RabbitMQ持久化

> 概念：
>
> * 表示消息是可以进行持久化存储的，可以将队列和消息都标记为持久化。
> * 持久化的目的就是保障了当RabbitMQ服务宕机以后不让消息丢失，能够在RabbitMQ中保存消息。
>
> 队列如何实现持久化：
>
> * channel.queueDeclare方法中boolean durable参数，true开启队列的持久化。
> * 需要注意若之前队列不是持久化，需先删除原队列，或重新创建一个新队列，否则可能出现错误。
>   * 可直接在Web界面选择某队列下拉delete删除
> * 持久化开启在Web界面Queue页面下Features会显示D（Durable）字样；此后即使重启RabbitMQ队列也依然存在
>
> 消息实现持久化：
>
> * 生产者channel.basicPublish方法AMQP.BasicProperties props参数来实现消息的持久化
>   * MessageProperties.PERSISTENT_TEXT_PLAIN
>   * 设置生产者发送消息为持久化消息，保存到磁盘上（不指定就是保存在内存中，随时可能会丢失）
> * 将消息标记为持久化并不能完全保证不会丢失消息。尽管它告诉 RabbitMQ 将消息保存到磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候 但是还没有存储完，消息还在缓存的一个间隔点。此时并没有真正写入磁盘。持久性保证并不强，但是对于我们的简单任务队列而言，这已经绰绰有余了。如果需要更强有力的持久化策略，参考后边课件发布确认章节。
>
> 不公平分发：
>
> * 之前提到的轮训分发（RabbitMQ分发消息的默认机制），某种场景下策略不是很好，比如两个消费者，A处理非常快，B处理非常慢，那么如果采用轮训分发机制的话，A就会处于空闲状态。
> * 不公平分发（能者多劳）：消费者在接收消息之前设置 channel.basicQos(1)
>   * 在Channels下面消费者Prefetch count：！
> * 意思就是如果这个任务我还没有处理完或者我还没有应答你，你先别分配给我，我目前只能处理一个任务，然后 rabbitmq 就会把该任务分配给没有那么忙的那个空闲消费者，当然如果所有的消费者都没有完成手上任务，队列还在不停的添加新任务，队列有可能就会遇到队列被撑满的情况，这个时候就只能添加新的 worker 或者改变其他存储任务的策略。
>
> 预取值：
>
> * 轮训分发就是你一条我一条的处理，不公平分发就是能者多劳，预取值（prefetch）就是指定分给消费者多少
>   * 指定预取值和不公平分发都是在消费者中使用同一个方法：channel.basicQos(int xxx)；
>     * 不设定就是轮询分发
>     * 1就是不公平分发
>     * 其他就是预取值分发
> * 预取值指的是消息堆积在信道中，而不是说例如7个值指定消费A预取值2，A就肯定只接收两条，而是A信道中堆积了2条



## 发布确认

### 发布确认原理

> 发布确认是解决消息不丢失的关键点。
>
> 持久化：
>
> 1. 设置队列持久化
> 2. 设置消息持久化
> 3. 发布确认
>    * 发布确认就是由生产者产生的消息，发送到队列之后将消息保存到磁盘之后，MQ再将保存成功的信息通知生产者
>



### 发布确认策略

> 开启发布确认的方法：
>
> * 发布确认在RabbitMQ默认没有开启，若开启发布确认通过调用channel.confirmSelect()方法（在生产者信道开启发布确认）。
>
> 单个确认发布：
>
> * 是同步确认发布的方式，发布一个消息只有它被确认发布，后续的消息才能继续发布
>   * channel.waitForConfirms()；
> * 缺点就是发布速度特别慢
>
> 批量确认发布：
>
> * 与单个确认发布消息相比，先发布一批消息然后一起确认可极大提高吞吐量；也是同步确认发布的方式（阻塞消息的发布）
> * 缺点就是当发生故障导致批量确认发布出现问题，不知道是哪个消息出现了问题；这样就必须将整个批处理保存在内存中，已记录重要的信息而后重新批量确认发布消息。
>
> 异步确认发布：
>
> * 异步确认发布编程逻辑要比以上两个复杂，但性价比最高，可靠性高、效率高；
> * 利用回调函数来达到消息可靠性传递，通过函数回调来保证是否投递成功
> * ![image-20230821171556022](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230821171556022.png)
> * channel.addConfirmListener( ConfirmCallback ackCallback, ConfirmCallback nackCallback )；
>
> 如何处理异步未确认消息：
>
> * 并发链路式队列：ConcurrentLinkedQueue
>   * 线程安全有序的哈希表，适用于高并发的场景：
>     * 可轻松将序号和消息进行关联
>     * 轻松批量删除条目，只要给到序号
>     * 支持高并发（多线程）
>
> 以上三种发布确认速度对比：
>
> 三种发布确认方式总结：
>
> * 单个确认发布：
>   * 同步等待确认；简单但吞吐量非常有限
> * 批量确认发布：
>   * 批量同步等待确认；简单合理的吞吐量，一旦出问题还需推断出那条消息出现了问题再重新发布
> * 异步确认发布：
>   * 最佳性能和资源使用，出现错误情况还可以很好控制，实现相对复杂





## 交换机

### Exchanges

> ==不指定交换机，默认就是AMQP default；通过默认交换机路由到指定的队列。==
>
> 之前了解的是==一个消息只能被消费一次（消费者之间处于竞争关系）==。
>
> * 其实==真正指的是一个队列的一个消息只能被消费一次==；但如果==交换机将一个消息发送给多个队列，多个队列再发送给多个消费者，就可以实现一个消息被消费多次的功能。（发布/订阅模式）==
> * 之前了解到的简单模式、工作模式，这次使用到了交换机就是发布、订阅模式。
>
> **交换机Exchanges概念：**
>
> * RabbitMQ消息队列核心思想就是：
>   * ==生产者生产的消息从不会直接发送到队列（之前没定义交换机则使用的AMQP default默认交换机）。==
>   * ==生产者只能将消息发送到交换机exchange==
>   * ==它一方面接收来自生产者的消息，另一方面会将消息推送到队列中。==
>   * ==交换机必须确切清楚如何处理接收到的消息，是将消息推送到特定队列还是多个队列亦或是将消息丢弃；是由交换机类型决定的。==
>     * 交换机类型：发布订阅模式、路由模式、主题模式
>
> 交换机Exchanges的类型：
>
> * 直接Direct（路由类型）
> * 主题Topic（主题类型）
> * 标题Headers（不常用了）
> * 扇出Fanout（就是发布订阅类型）
> * 无名exchange类型：通常用""空串进行标识
>   * 之前就是使用的该默认交换机；
>   * 消息能够路由发送到队列中，其实是由routingKey（bindingkey）绑定key指定的。
>     * 没有指定交换机，routingKey就是指的队列名称




### 临时队列

> 临时队列就是队列未实现持久化（durable=false），一旦断开了消费者的连接，队列将被自动删除（Web界面D标识就是持久化队列）。
>
> 创建临时队列：
>
> * String queueName = channel.queueDeclare().getQueue();
> * 可以在Web界面看到队列中AD Exd标识



### 绑定Bindings

> 绑定就是交换机与队列之间的捆绑关系；
>
> 通过指定RoutingKey来进行区别对待



### Fanout

> 介绍：
>
> * Fanout扇出就是发布订阅模式（RabbitMQ六大核心模式）
> * 该类型就是将接收到的所有消息广播到它知道的所有队列中。
>
> fanout扇出交换机（发布订阅模式）Demo：
>
> * ```java
>   public class ProducerOne {
>       public static final String EXCHANGE_NAME = "custom_exchange";
>
>       public static void main(String[] args) throws Exception {
>           Channel channel = RabbitMQUtils.getChannel();
>           // 声明交换机（交换机名称和交换机类型）
>           channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
>           Scanner scanner = new Scanner(System.in);
>           while (scanner.hasNext()) {
>               String message = scanner.next();
>               System.out.println("生产者声明fanout类型交换机 " + EXCHANGE_NAME + " 发送消息：" + message);
>               channel.basicPublish(EXCHANGE_NAME, "", null, message.getBytes(StandardCharsets.UTF_8));
>           }
>       }
>   }
>   ```
>
>
>   public class ConsumerOne {
>       public static final String EXCHANGE_NAME = "custom_exchange";
>
>       public static void main(String[] args) throws Exception {
>           Channel channel = RabbitMQUtils.getChannel();
>           // 声明交换机（交换机名称、类型）
>           channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
>           // 声明一个临时队列（消费者断开和该队列的连接，队列将被自动删除）。队列名称随机
>           String queueName = channel.queueDeclare().getQueue();
>           //绑定（交换机和队列之间的绑定）：队列名，交换机明，RoutingKEY（也称之为BindingKey）
>           channel.queueBind(queueName, EXCHANGE_NAME, "");
>           System.out.println("消费者One等待接收消息（发布订阅模式---fanout类型交换机）......");
>           channel.basicConsume(queueName, true,
>                   (String consumerTag, Delivery message) ->
>                           System.out.println("消费者One接收的消息：" + new String(message.getBody(), StandardCharsets.UTF_8)),
>                   consumerTag -> System.out.println("消费者One取消消费者接口回调逻辑"));
>       }
>   }
>
>   ```
> 
>   ```



### Direct exchange

> Direct exchange直接交换机---路由模式
>
> 队列只对绑定他的交换机的消息感兴趣。
>
> 直接交换机和扇出交换机区别：
>
> * RoutingKey（交换机和队列之间的绑定），
>   * 交换机和多个队列之间RoutingKey相同即消息都能收到就是扇出交换机（发布订阅模式）
>   * 交换机和多个队列之间RoutingKey不同，就是直接交换机（路由模式）
>
> 多重绑定：
>
> * 支持多重绑定，也就是绑定的RoutingKey支持不同的
> * 当然若交换机绑定类型是direct，但它绑定的多个队列的RoutingKey如果都相同，这样虽然绑定类型是direct但他的表现和fanout相似就和广播差不多。
>
> direct直接交换机（路由模式）Demo：
>
> * ```java
>   public class Producer {
>       public static final String EXCHANGE_NAME = "direct_exchange";
>   
>       public static void main(String[] args) throws Exception {
>           Channel channel = RabbitMQUtils.getChannel();
>           channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
>           Scanner scanner = new Scanner(System.in);
>           while (scanner.hasNext()) {
>               String message = scanner.next();
>               System.out.println("生产者声明direct类型直接交换机 " + EXCHANGE_NAME + " 发送消息：" + message);
>               channel.basicPublish(EXCHANGE_NAME, "error", null, message.getBytes(StandardCharsets.UTF_8));
>           }
>       }
>   }
>   
>   public class ConsumerOne {
>       public static final String EXCHANGE_NAME = "direct_exchange";
>   
>       public static void main(String[] args) throws Exception {
>           Channel channel = RabbitMQUtils.getChannel();
>           channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
>           channel.queueDeclare("queueOne", false, false, false, null);
>           channel.queueBind("queueOne", EXCHANGE_NAME, "info");
>   
>           System.out.println("消费者One等待接收消息（通知模式---direct类型直接交换机）......");
>           channel.basicConsume("queueOne", true,
>                   (consumerTag, message) -> System.out.println("消费者One接收消息：" + new String(message.getBody(), StandardCharsets.UTF_8)),
>                   consumerTag -> System.out.println("消费者One取消消费者接口回调逻辑"));
>       }
>   }
>   ```



### Topics

> Topics主题交换机要比扇出交换机和直接交换机更加的完美。
>
> Topic规范：
>
> * 主题交换机的RoutingKey不能随意写，必须是一个单词列表，以. 点分隔（例如"cat.foot","dog.mouth"）；单词列表最多不能超过255个字节
>   * *（星号）可替换一个单词
>   * #（井号）可以替代零个及多个单词
> * 队列绑定关系注意点：
>   * 当队列绑定关系是#，即该队列可接收所有数据，和fanout类似；
>   * 队列绑定键中没有*和#，就和direct类似。
>
> Topic主题交换机Demo：
>
> * ```java
>   public class Producer {
>       public static final String EXCHANGE_NAME = "topic_exchange";
>   
>       public static void main(String[] args) throws Exception {
>           Channel channel = RabbitMQUitls.getChannel();
>           channel.exchangeDeclare(EXCHANGE_NAME, "topic");
>   
>           HashMap<String, String> hashMap = new HashMap<>();
>           hashMap.put("quick.orange.rabbit", "被队列 Q1Q2 接收到");
>           hashMap.put("lazy.orange.elephant", "被队列 Q1Q2 接收到");
>           hashMap.put("quick.orange.fox", "被队列 Q1 接收到");
>           hashMap.put("lazy.brown.fox", "被队列 Q2 接收到");
>           hashMap.put("lazy.pink.rabbit", "虽然满足两个绑定但只被队列 Q2 接收一次");
>           hashMap.put("quick.brown.fox", "不匹配任何绑定不会被任何队列接收到会被丢弃");
>           hashMap.put("quick.orange.male.rabbit", "是四个单词不匹配任何绑定会被丢弃");
>           hashMap.put("lazy.orange.male.rabbit", "是四个单词但匹配 Q2");
>   
>           for (String routingKey : hashMap.keySet()) {
>               String message = hashMap.get(routingKey);
>               System.out.println("生产者发送消息："+routingKey);
>               channel.basicPublish(EXCHANGE_NAME, routingKey, null, message.getBytes(StandardCharsets.UTF_8));
>           }
>       }
>   }
>   
>   public class Consumer01 {
>       public static final String EXCHANGE_NAME = "topic_exchange";
>       public static final String QUEUE_NAME="queue01";
>   
>       public static void main(String[] args) throws Exception {
>           Channel channel = RabbitMQUitls.getChannel();
>           channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
>           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
>           channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "*.orange.*");
>           System.out.println("消费者01等待接收消息......");
>           channel.basicConsume(QUEUE_NAME, true,
>                   (String consumerTag, Delivery message) ->
>                           System.out.println("绑定关系：" + message.getEnvelope().getRoutingKey() +
>                                   " 获取的消息：" + new String(message.getBody(), StandardCharsets.UTF_8)),
>                   consumerTag -> System.out.println("消费者取消消费者接口回调逻辑"));
>       }
>   }
>   
>   public class Consumer02 {
>       public static final String EXCHANGE_NAME = "topic_exchange";
>       public static final String QUEUE_NAME="queue02";
>   
>       public static void main(String[] args) throws Exception {
>           Channel channel = RabbitMQUitls.getChannel();
>           channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
>           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
>           channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "*.*.rabbit");
>           channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "lazy.#");
>           System.out.println("消费者02等待接收消息......");
>           channel.basicConsume(QUEUE_NAME, true,
>                   (String consumerTag, Delivery message) ->
>                           System.out.println("绑定关系：" + message.getEnvelope().getRoutingKey() +
>                                   " 获取的消息：" + new String(message.getBody(), StandardCharsets.UTF_8)),
>                   consumerTag -> System.out.println("消费者取消消费者接口回调逻辑"));
>       }
>   }
>   ```





## 死信队列

### 死信的概念

> 死信就是无法被消费的消息。
>
> 一般来说由Producer生产的消息投递到Broker中，Consumer再从Queue取出消息进行消费；但某些时候由于特殊原因导致Queue中的某些消息无法被消费，这些消息没有后续的处理就成为了死信，有死信自然就有死信队列。
>
> 应用场景：
>
> * 为保证订单业务的数据不丢失，需使用到RabbitMQ的死信队列机制，当消息消费发生异常将消息投入死信队列中。
> * 用户商城下单成功并点击去支付后在指定时间内未支付自动取消订单。



### 死信的来源

> 消息TTL（Time to Live生存时间）过期；
>
> 队列达到最大长度（队列满了，无法在添加数据到MQ中）；
>
> 消息被拒绝（basicReject或basicNack）且requeue=false。



### 死信Demo

> 消费者：
>
> * 通过声明队列方法 中arguments参数，将普通队列消息成为死信之后转发到死信交换机
>
> * Map< String,Object > arguments
>   * 设置成为死信的来源（原因）：---当然这个成分死信的来源可不写，由生产者来定义（更加灵活）
>     * 过期时间：
>       * //map.put( "x-message-ttl" , 10000 );//设置消息过期时间10S
>   * 设置正常队列转发的死信交换机：
>     * map.put( "x-dead-letter-exchange" , 声明的死信交换机);
>   * 设置死信交换机和死信队列之间的RoutingKey：
>     * map.put( "x-dead-letter-routing-key" , RoutingKey);
>
> 生产者：
>
> * 发布消息（basicPublish）时设定props参数：设置死信的来源
>
>   * 消息TTL（Time to Live生存时间）过期；
>
>     * ```java
>       AMQP.BasicProperties props = new AMQP.BasicProperties().builder().expiration("10000").build();
>       ```
>
>   * 队列达到最大长度（队列满了，无法在添加数据到MQ中）；
>
>     * 消费者在声明队列方法 中 ：arguments参数：
>
>       * ```
>         arguments.put("x-max-length", 6);//表示队列最大承接6条消息
>         ```
>
>     * 注意！！！若修改前队列已存在需先清除：队列参数属性被修改必须删除重新生成
>
>   * 消息被拒绝（basicReject或basicNack）且requeue=false；
>
>     * basicConsume方法
>
>       * autoAck设置false
>
>       * DeliverCallback中对消息进行判断拒绝：
>
>         * ```java
>           (String consumerTag, Delivery message) -> {
>               String msg = new String(message.getBody(), StandardCharsets.UTF_8);
>               if("消息7".equals(msg)){
>                    System.out.println("C1拒绝消息：" + msg);
>                   channel.basicReject(message.xxx.gexxxTag(),false);
>               }else{
>                    System.out.println("C1接收消息：" + msg);
>                   channel.basicAck(message.xxx.gexxxTag(),false);
>               }
>           }
>           ```
>
>       * 








## 延迟队列







## 发布确认高级







## RabbitMQ其他知识点







## RabbitMQ集群















































# 末尾