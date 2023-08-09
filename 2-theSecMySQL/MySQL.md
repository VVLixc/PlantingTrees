# MySQL简介

企业中大部分业务数据都是用关系型数据库存储，MySQL数据库是目前主流

* 数据库、数据库管理系统、SQL语句
  * 数据库：DataBase
    * 存储数据的仓库，实际就是一堆文件；文件中存储了特定格式的数据
  * 数据库管理系统：DataBaseManageSystem 简称DBMS
    * 用来管理数据库数据（通过SQL可对数据库数据CRUD）
    * MySQL、Oracle、SqlServer、DB2等都是DBMS
  * SQL：通过DBMS执行SQL语句来进行数据库增删改查相关操作
  * 三者关系：
    * DBMS --执行-> SQL --操作->DB

* 怎么查看服务
  * 计算机---右键---管理---服务和应用程序---服务
* 数据库中最基本的单元是表：table



# SQL语句的分类

（Structure Query Language结构化查询语言）

* DQL：数据查询语言（凡带有SELECT都是DQL）
  * Data Query Language
* DML：数据操作语言（凡带有INSERT、DELETE、UPDATE都是DML） 
  * Data Manipulation Language
* DDL：数据定义语言（凡带有CREATE、DROP、ALTER、Truncate都是DDL）
  * Data Definition Language
* DCL：数据控制语言（授权GRANT、撤权REVOKE...）
  * Data Control Language
* TCL：事务控制语言（事务回滚ROLLBACK、事务保存点SAVEPOINT、事务提交COMMIT...）
  * Transaction Control Language





# 注意事项

* AND 和 OR 一条语句出现 AND 优先级高

* 若某个字段数据存在_、%这种特殊字符，可用 **\ 转义字符**来模糊查询

* ORDER BY

  * 排序（默认升序ASC；DESC表示降序）
  * ORDER BY a，b；排序中a主导，a值相同则按照b大小排序（默认升序）
  * 了解：也可根据字段位置进行排序；//SELECT id,name FROM table1 ORDER 2；表示按照name升序排序。（不建议这样写，不健壮）

* 关键字顺序

  * ```sql
    SELECT
    	...
    FROM
    	...
    WHERE
    	...
    GROUP BY
    	...
    HAVING
    	...
    ORDER BY
    	...
    LIMIT
    	...;
    语句的执行顺序：
    1)FROM
    2)WHERE
    3)GROUP BY
    4)HAVING
    5)SELECT
    6)ORDER BY
    7)LIMIT
    ```

* 为什么**分组函数（统计函数、聚合函数、多行处理函数）不能在WHERE条件后直接使用**

  * 原因就是分组函数若在WHERE下执行，此时还未对表的具体某列进行分组

* 为什么**分组函数（统计函数、聚合、多行处理）可以在SELECT后面使用**

  * 原因就是SELECT是在分组GROUP BY执行后进行执行的，此时已完成了分组

* **使用GROUP BY**后，SELECT语句后**只能跟参加分组的字段，和分组函数**；其他一律不能跟（MySQL可能不会报错，但无意义；Oracle直接报错）

* SELECT后面可以跟列名，函数，还可以跟 "字面量/字面值"（查询出的结果就是表数据原本条数的每一条都是该字面值）；

* 生成0-100随机整数（左右都是开区间）

  * SELECT ROUND(RAND()*100,0) FROM DUAL;

* 所有数据库中，有NULL参与的数学运算，最后结果都是NULL；

  * 为了避免这个现象，使用IFNULL()

* DISTINCT：

  * 关键字只能出现在**所有字段的最前方**
  * 若没有使用（）包裹去重字段，DISTINCT xxx，之后的字段会和前面的xxx联合起来进行去重











# DQL

## 函数

### 数据处理函数/单行处理函数

* 单行处理函数：一个输入对应一个输出
  * 和单行处理函数相对的是多行处理函数（多个输入对应一个输出）
* 常见单行处理函数
  * LOWER()
  * UPPER()
  * SUBSTR()；
    * 截取字符串，从某个位置开始（起始为1），截取多少字符。
  * CONCAT();
  * LENGTH()
  * TRIM()
  * STR_TO_DATE()
  * DATE_FORMAT()
  * ROUND()；
    * ROUND(列名，保留几位小数) ---四舍五入
    * ROUND(123456.6746, -1);//结果就是123460
  * RAND()
  * IFNULL()
  * CASE ... WHEN ... THEN ... WHEN ... THEN ... ELSE ... END
    * SELECT 
      	`id`,`user_name`,`resume`,
      CASE 
      	`resume`
      WHEN '漂亮' THEN `Salary`*1.1
      ELSE `Salary` END AS `Salary_up`
      FROM 
      	`employee`;



### 分组函数

* 输入多行，最终输出一行
* 分组函数（统计函数、聚合函数、多行处理函数）
  * COUNT()
  * SUM()
  * MAX()
  * MIN()
  * AVG()
* 分组函数使用时，必须先进行分组，然后才能用。（否则整张表作为一组）
* 分组函数会自动忽略NULL，我们不需要提前对NULL进行处理（例如IFNULL(...,...)）
* 分组函数不能直接使用在WHERE 子句中





## 分组查询

### GROUP BY





## 连接查询

* 从单独一张表进行查询叫做单表查询；多表联合起来跨表查询数据，叫做连接查询

* 连接查询分类

  * SQL92：1992出现的语法
  * SQL99：1999出现的语法

* 根据表连接查询的方式分类

  * **内连接：将完全匹配到的数据显示出来；两张表平等关系**
    * 等值连接
      * 条件是等量关系  =
    * 非等值连接
      * 条件不是等量关系 ：between ... and ...
    * 自连接
      * 一张表作为两张表
  * **外连接：可将未做匹配的数据显示出来；两张表根据LEFT或RIGHT分出主次关系**
    * 左外连接 LEFT  OUTER  JOIN ... ON ...；（OUTER可以省略）
    * 右外连接 RIGHT  OUTER  JOIN ... ON ...；（OUTER可以省略）
  * 全连接（几乎不用）

* ```sql
  #SQL92语法：可以发现表连接条件 会和之后表数据筛选条件混在一起；结构不清晰
  SELECT e.name,d.name FROM employee e,dept d WHERE e.id=d.id AND ...;
  
  #SQL99语法：表连接条件独立，若需要数据筛选再去WHERE；结构更清晰(INNER JOIN省略掉了INNER 表示内连接)
  SELECT e.name,d.name FROM employee e JOIN dept d ON e.id=d.id WHERE ...;
  ```

  



## 子查询

* 嵌入在其它SQL语句中的SELECT语句，也叫嵌套查询

* 可出现什么位置：SELECT ..... FROM ..... WHERE .....;（三个省略号位置都可出现）

  * ```SQL
    #WHERE子句中的子查询;  
    SELECT `user_name`,`Salary` FROM `employee` WHERE `Salary`>(SELECT MIN(`Salary`) FROM `employee`);
    
    #FROM后面的子查询；  可以将子查询的结果当做一个临时表
    SELECT 
    	G.GW,X.XZDJ
    FROM 
    	X
    JOIN
    	(SELECT GW,AVG(PJGZ) FROM A GROUP BY GW) G 
    ON 
    	G.PJGZ BETWEEN X.XZDJMIN AND X.XZDJMAX; 
    	
    #SELECT后面的子查询（了解）
    ```





## 合并查询

* UNION

  * UNION进行结果集合并时，要求两个结果集的列数相同

    * ```sql
      #下面的合并查询：MySQL中不会报错但数据会有异常；Oracle直接报错（结果集合并时，列和列的数据类型也要一致）
      SELECT id,name FROM table1 WHERE age>'xxx'
      UNION 
      SELECT id,sex FROM table1 WHERE age<'xxx';
      ```

## LIMIT

* 取出结果集的部分数据；主要用于分页查询
* 在排序ORDER BY之后才执行LIMIT；
* LIMIT startIndex length；（LIMIT  起始位置，长度）







# 建表

## 数据类型

* VARCHAR（255）
  * 变长字符串
  * 优缺点
    * 动态分配空间，不会浪费
    * 速度慢
* CHAR（255）
  * 定长字符串
  * 优缺点
    * 速度快
    * 浪费空间
* INT（11）
  * 整型
* BIGINT
  * 长整型
* FLOAT
  * 单精度浮点型数据
* DOUBLE
  * 双精度浮点型数据
* DATE
  * 短日期类型
* DATETIME
  * 长日期类型
* CLOB
  * 字符大对象 Character Large Object
  * 最多可存储4G的字符串
  * 长度超过255字符，采用CLOB字符大对象存储
* BLOB
  * 二进制大对象 Binary Large bject
  * 专门用来存放图片、声音、视频等流媒体数据
  * 向BLOB中插入流媒体数据时，需要使用IO流

## 数据处理函数/单行处理函数

* MySQL中日期格式
  * %Y
  * %m
  * %d
  * %h
  * %i
  * %s
  * Java中日期格式：yyyy-MM-dd HH:mm:ss SSS

* STR_TO_DATE
  * 字符串转日期
* DATE_FORMAT
  * 日期数据按照特定格式转换为字符串
  * DATE_FORMAT(birth,'%m/%d'%Y);//转换成定义格式：10/23/1999
  * 通常使用在查询日期后，设置展示的日期格式
* NOW()
  * 获取当前系统时间；DATETIME类型

## 快速建表

* CREATE TABLE t_table2 AS SELECT * FROM t_table1;
  * 该操作将t_table1的表结构和表数据给到新建的表t_table2;
  * 表复制
* CREATE TABLE t_table3 LIKE t_table2;
  * 创建表结构一致；没有表数据
* 自我复制（蠕虫复制）
  * INSERT INTO t_table3 SELECT * FROM t_table2

## 快速删除表数据

* DELETE FROM t_table1;
  * 这种删除方式比较慢
* DELETE删除数据原理
  * 表中数据被删除，但是表数据在硬盘的真实存储空间不会被释放
  * 优缺点
    * 支持回滚
    * 删除效率比较低
* TRUNCATE快速物理删除表数据原理
  * 表被一次截断，物理删除
  * TRUNCATE TABLE t_copyemp；（DDL语句：数据定义语句---CREATE、DROP、ALTER、TRUNCATE）
  * 优缺点
    * 删除表数据效率很快
    * 不支持回滚，不能恢复！！！



## 约束

* 约束（Constraint）用于确保数据库数据满足特定的商业规则
* 建表时，为表中特定字段加入约束，保证表中数据的完整性，有效性。（约束就是为了保证表数据有效）

* 建表时添加在列后的约束---**列级约束**；没有在列后面（在最后一行，逗号之后）设定的约束---**表级约束**
  * 要将**多个字段联合**起来添加某一个约束时，使用表级约束。



* NOT NULL 非空约束
  * 只有列级约束，没有表级约束



* UNIQUE 唯一约束
  * 字段数据不可重复，但可存在多个NULL（未设置NOT NULL情况下）
  * 表中字段可加多个UNIQUE
  * 将表多个字段联合唯一：UNIQUE(字段1 , 字段2)
  * UNIQUE 和NOT NULL联合约束（MySQL中：对应字段就作为主键约束；Oracle不一样）



* PRIMARY KEY 主键约束
  * **主键约束、主键字段、主键值**
    * 使用PK的就是主键约束；
    * 主键约束的字段就是主键字段
    * 主键字段中的内容，就是主键值
  * **主键值是每一行记录的唯一标识**
  * **任何一张表都应有主键，没有主键表没有意义，表无效**
  * 主键值**不能重复，不能为NULL**
  * 表中字段不可重复定义PK
  * 将表多个字段联合做主键：PRIMARY KEY(字段1 , 字段2 , ...) //**复合主键**；一个字段做约束是**单一主键**
  * 实际开发中**不建议使用复合主键**
    * 建议使用单一主键；因为主键值存在意义就是这行记录的身份证号
  * 主键值建议使用类型
    * INT 、BIGINT 、CHAR等类型；
    * 不建议VARCHAR。主键值一般都是数字，定长
  * 主键分类
    * 单一主键、复合主键
    * 自然主键、业务主键
      * 自然主键：主键值是一个自然数，和业务无关
      * 业务主键：主键值和业务紧密相关（例如拿银行卡号、身份证号做主键值）
      * 实际开发中，自然主键使用较多；业务主键不好，一旦主键和业务挂钩，当业务发生变动可能会影响主键值，不建议使用。尽量使用自然主键
  * 自动维护主键值---AUTO_INCREMENT
    * 经常配合使用；默认从1开始，以1递增
    * 所以尽量使用INT；数据庞大使用BIGINT



* FOREIGN KEY 外键约束
  * 使用主外键关联的父表子表
    * 删除需要先将子表数据清除；若父表数据在子表有数据关联，删除父表会报错
    * 先创建父表，先删除子表
  * FOREIGN KEY (子表列名) REFERENCES  父表( 父表主键字段 )
  * 子表外键引用的父表的字段
    * 可以是PK主键
    * 也可以是UNIQUE唯一（外键值可以为NULL）
    * **不一定是主键，但至少具有UNIQUE约束唯一性**





* CHECK 自定义检查约束（不建议使用，MySQL有些版本不支持）







# 存储引擎

* SHOW ENGINES; 
* 存储引擎是MySQL中特有术语（Oracle有，但不叫这个名字 OLAP OLTP）
* MySQL支持九大存储引擎；版本不同支持情况不同
* 存储引擎就是对表数据存储的方式





* 常用存储引擎
  * MyISAM
    * 使用三个文件表示每个表
      * 格式文件：存储表结构的定义（.frm结尾）
      * 数据文件：存储表行的内容（.MYD结尾）
      * 索引文件：存储表上索引（.MYI结尾）
    * 特点：
      * 可被转换为压缩、只读表来节省空间
      * 不支持事务，安全性低
  * InnoDB
    * 默认、重量级的存储引擎
    * 主要特征
      * 在目录中以.frm格式文件表示
      * InnoDB表空间tablesapce被用于存储表的内容（索引也在这里）；
        * 表空间是一个逻辑名称；表空间存储数据+索引
      * 支持事务处理
      * 提供一组用来记录事务性活动的日志文件
      * 提供全ACID兼容
      * MySQL服务器崩溃后提供自动恢复
      * 多版本（MVCC）和行极锁定
      * 支持外键及引用的完整性，包括级联删除和更新
    * 特点
      * 支持事务，保证数据的安全；支持数据库崩溃后的自动恢复机制；
      * 效率不高，不能压缩、转换为只读，不能很好的节省存储空间
  * MEMORY
    * 数据存储在内存，且行的长度固定；这两个特点使MEMORY速度非常快
    * 特征
      * 在MySQL目录内，每个表都以.frm格式的文件表示
      * 表数据及索引存储在内存中（目的就是快）
      * 表级锁机制
      * 不能包含TEXT或BLOB字段
      * 之前被称为HEAP引擎（堆引擎）
    * 优缺点
      * 查询效率最高
      * 不安全，关机后数据消失（数据和索引都在内存中；不需要和硬盘交互）





# 事务

* 事务就是用来保证数据的一致性哇，完整性；由一组DML（Data Manipulation Language）语句组成，这组DML语句要么全成功，要么全失败（例如转账就要用到事务，保证数据的一致性）
* 只有DML和事务有关；
* MySQL默认支持自动提交事务；（每执行一条DML语句就提交）
* 开始一个事务 ：START TRANSACTION; 或者 SET AUTOCOMMIT=OFF; 都可以开启事务
* 事务的ACID特性
  * Atomicity  /ˌætəˈmɪsəti/ 原子性
  * Consistency /kənˈsɪstənsi/ 一致性
  * Isolation 隔离性
  * Durability /dərəˈbɪlɪti/ 持久性
* Isolation隔离级别
  * 脏读 Drity Read
    * 事务A查询数据读到了事务B更新的但未提交的数据；脏读
    * 所有事务都可看到其他事务未提交事务的执行结果
  * 不可重复读 nonrepeatable read
    * 事务A多次查询数据，在事务B提交后读到了和之前不同的数据；不可重复读
  * 幻读  phantom read
    * 每次读取到的数据都是开启事务时的数据，不够真实
  * Read Uncommitted
  * Read committed
  * Repeatable Read
  * Serializable
  * Oracle数据库默认隔离级别就是Read Committed 读已提交
  * MySQL数据库默认隔离级别是Repeatable Read 可重复读



# 索引

* 为了提高查询效率存在的一种机制
* MySQL在查询方面主要通过两种方式
  * 全表扫描
  * 根据索引检索
* MySQL中索引排序方式
  * 索引是一个B-Tree数据结构
  * 遵循左小右大原则存放；采用中序遍历方式取数据
* 索引实现原理
  * 任何数据库中，主键都会自动添加索引；（MySQL中，含有UNIQUE唯一约束的字段，也会自动创建索引）
  * 任何数据库中，任何一张表的任何一条记录在硬盘存储中都有一个硬盘的物理存储编号
  * MySQL中，索引是一个单独的对象，不同的存储引擎以不同的形式存在
    * MyISAM：索引存储在.MYI文件中
    * InnoDB：索引存储在一个逻辑名称叫做tablespace当中
    * MEMORY：索引存储在内存中
    * 无论索引存储在何处，索引在MySQL中都是一个树的形式存在（自平衡二叉树：B-Tree）
* 何时需要考虑添加索引
  * 数据量庞大
  * 经常出现在WHERE子句后（该字段总是被扫描）
  * 很少进行DML操作（因为DML后，索引需要重新排序）
* 建议
  * 不要随意添加索引，索引也需要维护，太多索引反而降低系统性能
  * 通过主键查询，通过UNIQUE约束字段查询，效率比较高
* 索引语法
  * 创建索引
  * 查看一条SQL语句是否通过索引进行检索
    * 使用EXPLAIN
    * EXPLAIN SELECT ...
    * 结果中看type字段下是否为ALL（ALL表示进行了全表扫描）
* 索引失效
  * 模糊查询以%起始，没办法对索引进行检索
  * 使用OR情况
    * 要求OR两边的条件字段都要有索引，才会进行索引检索；只有一个索引会失效
  * 使用复合索引时，未使用左侧列查找
  * WHERE中，索引列进行了运算
  * WHERE中，索引列使用了函数
  * 使用类型转换？？？
* 索引分类
  * 单一索引、复合索引
  * 主键索引、唯一索引、普通索引
  * 注意：唯一性比较弱的字段添加索引作用不大；越唯一效率越高





# 视图VIEW

* 视图在实际开发作用
  * 为一段复杂的SQL语句创建视图，作为引用，在之后的每个SQL语句中替代复杂SQL





# 数据库三范式

* 数据库设计范式
  * 数据库表的设计依据。
* 数据库三个设计范式
  * 第一范式：每张表必须有主键，每一个字段原子性不可再分
  * 第二范式：在第一范式基础上，要求所有非主键字段完全依赖主键，不要产生部分依赖
  * 第三范式：第二范式基础上，要求所有非主键字段直接依赖主键，不要产生传递依赖
* 表设计
  * 多对多怎样设计：多对多，三张表，关系表，两个外键
  * 一对多怎样设计：一对多，两张表，多的表，加外键
  * 一对一怎样设计：一对一，外键唯一
* 嘱咐
  * 数据库三范式是理论
  * 实践和理论有偏差，目的为了满足用户需求，有时需要冗余换执行速度
    * 因为SQL中，表连接会影响效率（笛卡尔积）
    * 存在冗余，但是可以减少表连接次数，这样也是合理的；并且一张表，对开发人员难度降低









* 索引创建后只对创建索引的列生效
* 对没创建索引的列进行查询，会全局查询（每一行数据），速度慢
* 创建索引后，会生成索引的一种数据结构（例如二叉树），访问速度快
* 缺点
  * 占用较大磁盘空间
  * 对DML语句的效率影响
* 索引分类
  * 主键索引 PRIMARY KEY
  * 唯一索引 UNIQUE INDEX
  * 普通索引 INDEX
  * 全局索引 FULLNEXT
* 创建索引语句
  * CREATE INDEX 索引名 ON 表名（列名）；
  * CREATE UNIQUE INDEX 唯一索引明 ON 表名（列名）；
* 删除索引
  * DROP INDEX 索引名 ON 表名；
* 查询索引
  * SHOW INDEX FROM 表名；
* 





















# 通用命令

* cmd命令
  * 启服：net start 服务名称（mysql）
  * 停服：net stop 服务名称（mysql）
  * 进入mysql：mysql -u root -p
  * 退出mysql：exit
* **DESC 表名;**----查看表结构；（Describe：描述）
* **SELECT VERSION();**-----查看数据库版本
* **SELECT DATABASE();**-----查看当前数据库名
* **\c** 终止一条命令语句输入
* **AS**-----起别名（可以省略，使用 空格 代替；别名包含空格可使用' '单引号包裹）
* 所有数据库中，字符串使用单引号是标准。Oracle数据库只能使用单引号，MySQL数据库可使用双引号
* 数据导入导出
  * mysqldump -uroot -p -B 数据库1，数据库2... >文件的绝对路径.sql
  * mysqldump -uroot -p 数据库.表名 > 文件的绝对路径.sql



# SQL优化

* SELECT * FROM .....
  * 效率较低
  * 可读性差
  * 建议将*换成具体想要查询的字段
* GROUP BY ... HAVING ...
  * HAVING效率会降低；
  * 能使用WHERE过滤掉的，尽量使用WHERE
* 连接查询（多表查询）
  * SELECT 后面跟的查询列名即便在两张表是唯一的，也要加上表名 . 列名；显示声明，提高效率
  * 给表起别名，效率问题？？？
* UNION效率高一些
  * 对于表连接来说，连接一次则匹配次数满足笛卡尔积
  * 但UNION减少了匹配次数
    * 例如：A连接B连接C，每个都有10条记录
      * 直接使用正常表连接会匹配1000次
      * 使用UNION
        * A表连接B表：10*10--->100
        * A表连接C表：10*10--->100
        * A表连接B表  UNION  A表连接C表 匹配200次
  * UNION将表数据匹配从乘积变为了加法
* 索引失效
  * 避免使用%作为模糊查询起始位置，无法使用索引检索
  * 尽量少用OR；可以考虑使用UNION方式
  * 











* 增删改查
  * CRUD
  * CREATE RETRIVE UPDATE DELETE





windows+R---》mspaint 画图













