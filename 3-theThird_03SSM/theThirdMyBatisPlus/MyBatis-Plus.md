# MyBatis-Plus

> MyBatis-Plus是MyBatis的增强工具，在MyBatis基础上只==做增强不做改变，简化开发，提高效率==
>
> ==MyBatis-Plus提供了通用的service和mapper==，可在不编写任何SQL语句情况下，快速实现对==单表==的CRUD、批量、逻辑删除、分页等操作。

## 入门案例

> ==开发环境：==
>
> * IDE：IDEA 2020.3
> * JDK：JDK1.8
> * 数据库：MySQL 5.5.55
> * Maven构建工具：Maven 3.6.3
> * SpringBoot：2.7.12
> * MyBatis-Plus：3.5.3

> ==创建数据库、表：==
>
> * **主键id设置类型为BIGINT(20)**；
>   * **通过MyBatis-Plus进行数据插入时，默认使用雪花算法来生成id，长度较长，所以使用BIGINT类型**。

> ==创建SpringBoot工程（使用Spring Initializr创建）：==
>
> * 添加依赖：
>   * lombok简化实体类、日志开发场景启动器
>   * mysql驱动
>   * MyBatis-Plus场景启动器：mybatis-plus-boot-starter
> * SpringBoot配置文件application.yaml配置数据源信息：
>   * **添加日志功能：（输出MyBatis-Plus执行的SQL语句）**
>     * mybatis-plus：configuration：**log-impl：org.apache.ibatis.logging.stdout.StdOutImpl**
> * 创建实体类
>   * **只添加@Data注解即可满足实体类常用要求**：无参、get/set、equals、hashCode、toString
>     * 若想使用含参那就@Noxxx、@Allxxx、@Data
> * 创建Mapper接口
>   * 添加@Mapper注解向容器导入该组件 或在主程序类上方添加@MapperScan（basePackages=“xxx”）扫描包路径下所有Mapper组件
>     * 不再使用@Mapper注解，直接**在主程序类添加@MapperScan注解**
>   * **继承BaseMapper接口，泛型为需要操作的实体类**
> * 创建Service接口及实现类
>   * Service接口继承IService接口
>   * Service接口实现类继承IService接口的实现类ServiceImpl---泛型为Mapper接口，实体类

> ==测试：==
>
> * 使用**@SpringBootTest注解修饰的单元测试类，可以使用Spring容器的组件**
>
>   * @Autowired进行属性注入时，会报红
>     * IDEA的问题
>     * 也可在**Mapper接口上方添加@Repository注解**
>
> * 添加日志功能后输出的结果：
>
>   * ```java
>     ==>  Preparing: SELECT id,name,age,email FROM user
>     ==> Parameters: 
>     <==    Columns: id, name, age, email
>     <==        Row: 1, Jone, 18, test1@baomidou.com
>     <==        Row: 2, Jack, 20, test2@baomidou.com
>     <==        Row: 3, Tom, 28, test3@baomidou.com
>     <==        Row: 4, Sandy, 21, test4@baomidou.com
>     <==        Row: 5, Billie, 24, test5@baomidou.com
>     <==      Total: 5
>     ```
>
>   * 可以看出**MyBatis-Plus操作的表和表字段 由BaseMapper标识的泛型---实体类决定**
>
>     * 默认实体类名就是表名，实体类属性就是表字段
>     * @TableName可以修改查询的表名，@TableField(exist = false)指定该属性不再表中



## 基本CRUD

### BaseMapper

> ==MP中的针对单表进行CRUD的常用操作，在内置的BaseMapper中都已经得到了实现，可直接使用==
>
> * INSERT操作后，获取实体类对象，MP会将实体类对象ID填充进来
>   * 基于雪花算法生成的id
> * BaseMapper中的方法，形参中部分使用到了Wrapper类型，此为条件构造器，可针对不同SQL设定不同条件，若没有条件，可传参为null
>   * 查询时可设为null
>   * DML语句DELETE、UPDATE 若条件为null，会更新或删除所有的数据！注意
>
> 由于BaseMapper只是基于单表的常用的SQL功能实现，所以若想自定义SQL，使用MyBatis功能即可：
>
> * MP默认的Mapper映射文件路径为：类路径下的mapper目录下 任意目录的任意文件.xml
> * 证明了MP确实是在MyBatis基础上只做增强不做改变



### 通用Service

> ==MP中封装了通用的Iservice接口（顶级Service接口）和该接口的ServiceImpl实现类；封装了常用的CRUD业务逻辑==
>
> * IService由我们自定义的Service接口继承，泛型为操作的实体类
> * ServiceImpl由自定义的Service接口的实现类来继承，
>   * 泛型为：
>     1. 继承了BaseMapper接口的自定义Mapper接口
>     2. 实体类



## 常用注解

### ==@TableName==

> * **@TableName( "数据库表名" )**用来标识该实体类对应的数据库表名；默认是实体类名（无需关心大小写）
>
> * 标识在实体类上方
>
> * 若所有的数据库表名都有统一的前缀（例如t_xxx），可以在**配置文件中全局配置**：对表名前缀进行设置
>
>   * ```yaml
>     mybatis-plus:
>       configuration:
>         map-underscore-to-camel-case: true
>         log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
>       # 设置MP的全局配置
>       global-config:
>         db-config:
>           table-prefix: t_ #设置全局的表名前缀
>     ```

### ==@TableId==

> * @TableId用来将实体类对应属性指定为数据库表的主键字段（MP默认将表中id字段作为主键，然后通过雪花算法为ID赋值）
>
> * 标识在属性上方
>
> * 注解属性
>
>   * value：当属性名和字段名不匹配，可为该属性赋与字段名一致的值
>
>   * type：指定主键字段的生成策略（默认是雪花算法的策略；即便数据表指定了主键自增还是会使用雪花算法生成ID）
>
>     * 若想满足主键字段ID自增：
>
>       * 数据库表字段设为自增
>
>       * IdType.AUTO；数据库ID自增
>
>         * ```java
>           @TableId(type=IdType.AUTO)
>           ```
>
>     * **常用的主键生成策略**
>
>       * **IdType.ASSIGN_ID（默认）**
>         * 基于雪花算法的策略生成数据ID，与数据库ID是否设置自增无关
>       * **IdType.AUTO**
>         * 使用数据库的自增策略，前提要确保数据库设置了ID自增，否则无效
>
> * 若所有的数据库都要设置统一的主键生成策略，可以在**配置文件中全局配置**：对id-type进行配置
>
>   * ```yaml
>     mybatis-plus:
>       configuration:
>         map-underscore-to-camel-case: true
>         log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
>       global-config:
>         db-config:
>           table-prefix: t_ # 配置MP操作表的默认前缀
>           id-type: auto # 配置的主键生成策略：主键自增（assign-id是雪花算法的主键生成策略）
>     ```
>
> * 雪花算法的优点
>
>   * 整体上按照时间自增排序，并且整个分布式系统内不会产生ID碰撞，且效率较高

### ==@TableField==

> * 指定属性对应的字段名
>
> * @TableField( exists = false )用来标识该属性在表中不存在该字段；默认是true
> * 标识在属性上方
> * MP默认开启驼峰映射？？？

### ==@TableLogic==

> * 逻辑删除
>   * 物理删除：真实删除，将对应数据从数据库（磁盘？）中删除，不可恢复
>   * 逻辑删除：虚假删除，将对应数据中是否被删除的字段状态改为被删除状态，可进行数据恢复
> * 属性上方添加@TableLogic注解后（确保该字段值为0），若删除某条数据，该属性值对应的字段值就变为1
>   * 查询也就查询不到了，但是并未真实删除；所以该注解为逻辑删除



## 条件构造器和常用接口

> Wrapper 条件构造器：
>
> * MP中的Wrapper是一个顶级的条件构造抽象类
>   * AbstractWrapper继承了Wrapper抽象类，本身也是一个抽象类
>     * ==QueryWrapper==：查询条件封装
>     * ==UpdateWrapper==：Update条件封装
>     * AbstractLambdaWrapper：使用Lambda语法的抽象类
>       * ==LambdaQueryWrapper==
>       * ==LambdaUpdateWrapper==
>
> * 无论创建哪个条件构造器，泛型都是实体类型
> * Lambda表达式内的逻辑优先运算

### QueryWrapper

主要用于查询、删除功能；（修改也可以用到，但推荐使用UpdateWrapper，更简单些）

> * 条件构造器-组装查询条件
>   * 创建QueryWrapper对象时指定泛型为操作的实体类型
>   * 一般的QueryWrapper对象的方法指定的字段名（注意不是实体类属性名）
>     * 可以拼接多个条件
>   * 若指定了实体类逻辑删除属性，且数据库对应设定了字段值为0的字段，该字段为0也会作为WHERE筛选条件进行拼接
>   * 常用方法：isNotNull、between、like...
> * 条件构造器-组装排序条件
>   * 常用方法：orderByAsc、orderByDesc
> * 条件构造器-组装删除条件
>   * 若设定了逻辑删除，删除就变为了修改
>
> * 条件构造器-条件的优先级
>   * 条件构造器中，Lambda表达式优先执行
>     * 若使用了and或or方法，此时使用了Lambda表达式，那么Lambda表达式一定优先执行
> * 条件构造器-组装SELECT子句
>   * 若不想查询所有字段，可通过selectMaps( Wrapper<Entity> wrapper )方法来进行查询，返回的就是以Map为泛型的List集合
> * 条件构造器-实现子查询
>   * 子查询：指嵌套在其他SQL语句中的SELECT语句，也称为嵌套查询
>   * queryWrapper.inSql方法

### UpdateWrapper

主要用于修改功能

不单可以在UpdateWrapper中设置查询条件，还可对字段值进行修改

> 例如BaseMapper中的update方法，xxxMapper.update(null, updateWrapper )；---无需创建实体类对象

MP中提供了StringUtils工具类，使用StringUtils.isNotBlank方法判断某个字符串是否不为空字符串，不为null，不为空白符

### condition

真正处理业务逻辑，肯定会对传输来的数据进行条件判断，非空字符串、非空等；

此时可以在Wrapper条件构造器中传参时进行condition条件判断，true才会进行条件组装

### LambdaQueryWrapper

> 相较于非Lambda的Wrapper条件构造器来说，方法传参发生变化
>
> * 非Lambda的条件构造器传参有String类型的字段名
> * Lambda条件构造器传参将String类型的字段名，调整为了==函数式接口，就是为了避免字段名书写错误==
>   * lambdaQueryWrapper .like( StringUtils.isNotBlank(xxx), 实体类::get属性名，xxx)；

### LambdaUpdateWrapper

> 既可设置查询条件，又能设置字段值的修改；String类型的字段值都调整为函数式接口



## 插件

### 分页插件

> MP自带分页插件，只需简单的配置即可实现分页功能
>
> * 添加配置类
>
>   * 可以将在主程序上方标识的@MapperScan注解移到此处（建议添加在MP的配置类上）
>
>   * 向容器注册MyBatisPlusInterceptor组件
>
>     * 向MyBatisPlusInterceptor添加分页插件PaginationInnerInterceptor时，最好使用含参构造，将数据库类型声明出来
>
>   * ```java
>     @Configuration
>     @MapperScan("com.lixc.mybatisplus.mapper")
>     public class MyBatisPlusConfig{
>         @Bean
>         public MyBatisPlusInterceptor mybatisPlusInterceptor(){
>             MyBatisPlusInterceptor interceptor = new MyBatisPlusInterceptor();
>             interceptor.addInterceptor( new PaginationInnerInterceptor(DbType.MySQL) );
>             return interceptor;
>         }
>     }
>     ```

### 自定义分页

> * 自定义Mapper中添加分页相关的方法（例如根据age进行分页）
>   * 返回值为Page<实体类型>；形参第一个必须为Page<实体类型>（MP所提供的的分页对象）
> * Mapper映射文件中定义Mappe接口所执行的信息（注意返回类型的类型别名在SB2配置文件中MP下进行配置）
>   * 无需为分页做业务，只需关心查询的业务



### 乐观锁

> 乐观锁与悲观锁
>
> * 乐观锁：
>   * 关注version版本号；更新数据的同时也会更新版本号；
>   * 若多个事务同时获取到同一条数据（版本号肯定也一致）；那么在第一个事务操作完成提交后，版本号会改变，则第二个事务在操作数据时就不会成功，重新获取最新版本号的数据
> * 悲观锁：操作同一条数据，只允许一个事务操作，其它事务都处于阻塞状态

> 乐观锁实现流程：
>
> * 数据库添加version字段
> * 取出记录时，获取version
> * version作为条件更新时，修改version=version+1；此时若WHERE语句中version版本不正确，则更新会失败

> MP实现乐观锁：
>
> * 数据库添加version字段---版本号字段
>
> * 数据库字段对应在实体类属性名上方添加@Version注解；//表示乐观锁版本号字段
>
> * MP配置类中MyBatisPlusInterceptor组件内添加乐观锁插件
>
>   * ```java
>     //添加乐观锁插件
>     interceptor.addInterceptor(new OptimisticLockerInnerInterceptor);
>     ```

> 修改冲突的测试：
>
> * 若只是使用了MP提供的乐观锁，不做任何业务的优化，数据库数据修改冲突时只会修改第一个；
> * 所以考虑数据库修改后返回的修改条数，若为0，重新获取该数据，此时version版本号也会更新，重新修改即可



## 通用枚举

> 表中某些字段值是固定的，例如性别；此时即可通过MP提供的通用枚举来实现
>
> MP提供的通用枚举相关注解：
>
> * @EnumValue：将注解所标识的属性的值存储到数据库中
>
> * ```java
>   @Getter
>   public enum SexEnum{
>       MALE(1,"男");
>       FEMALE(2,"女");
>       @EnumValue
>       private Integer sex;
>       private String sexName;
>       public SexEnum(Integer sex,String sexName){
>           ...
>       }
>   }
>   ```
>
> SB2设置MP相关配置：
>
> * ```yaml
>   mybatis-plus:
>   	type-enums-package: com.lixc.mybatisplus.enums #扫描通用枚举的包
>   ```



## 代码生成器

> MP的提供的代码生成器要比MyBatis代码生成器更自动化
>
> 导入依赖：
>
> * MP逆向工程依赖
> * freemarker依赖
>
> 执行官网提供的方法来生成相关代码



## 多数据源

> ==创建多个数据库==

> ==引入多数据源所需的依赖==：dynamic-datasource-spring-boot-starter

> ==SB2配置文件中配置多数据源==

> 创建多个Service层代码
>
> * 多个类上方额外添加==@DS( "SB2中配置的数据源名" )注解==，表示操作数据库使用的数据源
>   * @DS注解可加在类或方法上，采取就近原则

> 其他类照旧即可

> 测试
>
> * 还可实现数据库的读写分离：写操作在主数据源进行，读操作在从数据源进行即可；
>   * 若存在对同一张表进行读和写，可以利用@DS的特性进行操作即可





## MyBatisX插件

> 最简单最实用的功能：快速定位Mapper接口或Mapper映射文件

> MyBatisX插件-代码生成器的功能：
>
> * 右侧datasource可视化图标左侧+ 选中MySQL后，配置相关连接信息
> * 创建完成后，找到操作的表，右键MyBatisX-Generator

> MyBatisX插件快速生成CRUD：
>
> * Mapper接口自定义方法无需定义返回值，直接写方法名，但方法名必须规范（insert、select、delete、update），
>   * ALT + ENTER ---》Generate MyBatis Sql
>     * 不止会自动补全Mapper接口方法，还会自动创建Mapper映射文件对应的信息













## 末尾