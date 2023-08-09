# Java开发手册 嵩山版

## 1. 编程规约

### (1) 命名规范

> * 代码==命名不能使用_和$开始和结束==（Java是允许的，但无意义！）
>
>   * 延伸到#这个符号---可以在类或方法注释上使用
>
>     * ```java
>       /**
>        * com.lixc.standard01programstatute
>        * 命名规范
>        * @see #method() 相当于有一个链接，ctrl+鼠标左键可点击到真正方法位置
>        * @author Lixc
>        * @version 1.0
>        * @since 2023-06-30 14:41
>        */
>       @SpringBootTest
>       @DisplayName("命名规范")
>       public class NameTest {
>           /**
>            * @see #method()
>            */
>           public void method(){
>           }
>       }
>       ```
>
> * ==常量命名全部大写，单词间使用_隔开==
>
> * ==抽象类命名使用Abstract==或Base（建议也别使用Base）开头；==异常类使用Exception结尾；测试类以Test结尾==
>
> * ==POJO类任何布尔类型变量都不要使用 is 作为前缀==，否则部分框架解析可能会**引起序列化错误**
>
> * ==包名统一小写==（这个无需强制）；包名统一使用单数形式，类名可使用复数形式
>
>   * com.lixc.program.util.XxxUtils



### (2) 常量定义

> * ==避免使用魔法值==（即未经预先定义的常量）直接出现在代码中
> * Long或long类型赋值时，数值后使用L



### (3) 代码格式

### (4) OOP规约

> * 不要通过对象引用来==访问此类的静态变量或静态方法，直接使用类名==
>
> * ==方法重写必须添加@Override注解==
>
> * 相同参数类型、业务含义，才可以==使用可变参数（参数列表最后），且避免使用Object==。建议==尽量不要使用可变参数来编程==
>
> * ==接口过时时必须加@Deprecated注解==，并==清晰说明新接口或新服务==是什么
>
> * 常量或确定有值的对象来调用equals方法：=="xxx".equals(xxx)==
>
> * 所有==包装类型的数值比较，必须使用equals方法==进行比较
>
>   >  Integer类型会有一个IntegerCache.cache的缓存区间：-128-127，此时不会创建新对象；
>   >
>   > 此时==比较会返回true
>   >
>   > 一旦超出该范围两个Integer比较就是不同对象，大坑！
>
> * 任何货币单位，均==以最小货币单位且整型类型进行存储==
>
> * 浮点数之间的等值判断：
>
>   * 基本数据类型不能使用==比较
>   * 包装数据类型不能使用equals比较
>   * BigDecimal的等值比较
>     * equals方法会比较值和精度（例如1.0和1.00会返回false）
>     * 应使用==compareTo方法会忽略精度==
>
> * 定义数据对象DO时，确保属性类型和数据库字段类型相匹配
>
>   * 例如MyBatis-Plus中使用通用Mapper添加数据时，默认使用雪花算法生成主键id
>     * 所以数据库字段需要设定为BIGINT，对应的Java对象类型则为Long类型
>
> * 禁止使用构造方法BigDecimal( double )的方式将double值转为BigDecimal对象
>
>   * 构造方法BigDecimal( double )存在精度损失风险
>   * 推荐：
>     * ==入参为String的构造方法：BigDecimal( double.toString() );==
>     * ==BigDecimal的valueOf方法：BigDecimal.valueOf( double );==
>
> * POJO类包括
>
>   * DO、DTO、VO
>   * ==定义这些POJO类时，不要为属性设定任何默认值==
>
> * ==构造方法禁止加入任何业务逻辑==，若有初始化逻辑，添加在init方法中



### (5) 日期时间

>* ==yyyy-MM-dd HH:mm:ss==
>* ==获取当前毫秒数：System.currentTimeMillis()；==
>* ==不允许在任何地方使用：==
>  * java.sql.Date
>  * java.sql.Time
>  * java.sql.Timestamp
>* 禁止写死一年365天
>  * ==获取今年的天数：LocalDate.now().lengthOfYear();==
>  * ==获取指定某年的天数：LocalDate.of(2011,1,1).lengthOfYear();==



### (6) 集合处理

> * hashCode和equals
>
>   * ==只要重写equals，必须重写hashCode==
>     * 因为Set存储不重复对象，根据hashCode和equals来判断对象是否重复
>       * hashCode不同对象也可能出现相同的值，需要equals双重判断
>     * Map中的键存储自定义对象，也必须重写hashCode和equals方法
>     * String由于已经重写了这两个方法，所以可以直接使用作为key
>
> * ==判断所有的集合元素是否为空，使用isEmpty方法==，禁止使用size() == 0方式
>
> * 使用Collectors.toMap转Map集合：
>
>   * ```java
>     @Test
>     public void toMap() {
>         ArrayList<Pair<String,Integer>> list = new ArrayList<>();
>         list.add(new Pair("version", 12));
>         list.add(new Pair("version", 11));
>         list.add(new Pair("version", 10));
>         Map<Object, Object> collect = list.stream().collect(Collectors.toMap(Pair::getKey, Pair::getValue, (v1, v2) -> v2));
>         System.out.println(collect);
>     }
>     ```
>
>   * 使用前需判断value值非空
>
> * ArrayList集合调用subList方法，返回值不能强转为ArrayList~~，使用List接收~~
>
> * 使用==Map的keySet、values、entrySet获取的集合数据，不能进行元素添加的操作==
>
> * ==集合转数组：必须使用toArray方法，传参为类型完全一致，长度为0的一个数组==
>
>   * ```java
>     public void test(){
>         List<String> list = new ArrayList<>();
>         list.add("1");
>         list.toArray(new String[0]);
>     }
>     ```
>
>   * 为何使用toArray含参方法，不直接使用无参的：
>
>     * 使用toArray()返回类型为Object[]，还需要操作，无意义。
>
>   * 为何使用toArray含参方法，数组大小填0：
>
>     * 0：动态创建与集合size大小相同的数组，性能最好
>     * 大于0小于size：会重新创建大小等于集合size的数组，增加GC负担
>     * 等于size：高并发场景，数组创建完成但集合size又变大，又会重新创建大小为集合size的数组
>     * 大于size：空间浪费，且会在空闲的位置插入null值，存在NPE隐患
>
> * 使用==Collection接口任何实现类的addAll方法，都要对输入的集合参数进行NPE判断==
>
> * Arrays.asList()将数组转为集合，不能对集合使用修改的相关方法（add、remove、clear会抛异常）
>
> * 使用泛型通配符
>
>   * <? extends T>指定的集合，不能使用add方法
>   * <? super T>指定的集合，不能使用get方法
>
> * 将无泛型的集合元素赋值给泛型限制的集合时，使用instanceOf进行判断，避免出现类型转换异常
>
> * foreach循环不要对元素进行remove、add操作；remove元素使用iterator方式，并发情况还需要对iterator对象加锁
>
> * Comparator实现类满足：
>
>   * x，y比较结果和y，x比较结果相反---自反性
>   * x>y，y>z，则x>z---传递性
>   * x=y，则 x，z 比较结果和 y，z 比较结果相同。---对称性



### (7) 并发处理

### (8) 控制语句

> * 一个switch块中
>
>   * 每个case要么通过~~continue/~~break/return来终止，要么注释说明将继续执行到哪一个case为止；
>   * 必须包含一个default语句放在最后，即便什么代码都没有
>   * break是退出switch语句块，return是退出方法体
>
> * 若switch括号内的变量类型为String且变量为外部参数，必须判断非空，否则括号那一行直接报NPE异常
>
> * 三目运算符表达式condition ? 表达式1 :  表达式2中，高度注意表达式1,2在类型对齐时，可能抛出因自动拆箱导致的NPE异常
>
>   * 触发类型对齐的拆箱操作的场景：
>
>     * 表达式1或表达式2只要有一个是原始类型
>     * 表达式1和表达式2的值类型不一致，会强制拆箱升级为表示范围更大的那个类型
>
>   * ```java
>     public static void main(String[] args){
>         Integer a = 1;
>         Integer b = 2;
>         Integer c = null;
>         Boolean falg = false;
>         //由于a*b是int类型，两个表达式会触发类型对齐的拆箱操作，c就会强制拆箱为int类型--->NPE
>         Integer result = flag ? a*b : c;
>     }
>     ```
>
> * 高并发场景中，避免使用 “等于” 作为中断或退出的条件
>
>   * 若并发控制未处理好，容易产生等值判断被 “击穿” 的情况，所以使用大于或小于的区间判断条件来代替
>     * 判断奖品数量等于0则终止发放，但因并发处理错误导致数量瞬间变为负数--->活动无法终止



### (9) 注释规约

> * 类、类属性、类方法注释必须使用Javadoc规范
> * 所有抽象方法（包括接口方法）必须使用Javadoc注释；
>   * 返回值，参数，异常说明、实现功能
> * 所有类必须添加创建者和创建日期



### (10) 前后端规约

> * 前后端交互API（Application Programming Interface），需要明确
>   * 协议：生产环境必须https
>   * 域名
>   * 路径：每一个API对应一个路径
>     * 代表一种资源（REST风格：一切皆资源），不能为动词->请求方法以表明动作意义
>     * URL路径避免大写，单词间若需分隔使用_下划线
>     * 路径禁止携带请求内容类型后缀（.json、.xml），通过accept头表达即可
>   * 请求方法
>     * GET：获取
>     * POST：新增
>     * PUT：修改
>     * DELETE：删除
>   * 请求内容
>     * URL携带参数必须无敏感信息或符合安全要求；body里带参数时必须设置Content-Type
>   * 状态码
>   * 响应体
>     * 响应体body可放置多种数据类型，使用Content-Type头确定
> * 前后端数据列表相关的接口返回若为空，则返回空数组[]或空集合{}
> * 服务端发生错误时，返回给前端的响应信息必须包括：
>   * HTTP状态码
>     * 常见状态码：200成功、401请求要求身份验证（常见于需要登录但未登录的情况）、403无权访问、404请求资源不存在、500服务器内部错误
>   * errorCode
>   * errorMessage
>   * 用户提示信息
> * 前后端交互的JSON格式数据：
>   * 必须以小写英文开头--->lowerCamelCase小写驼峰样式
> * 对于需要使用超大整数场景，服务端一律使用String类型返回，禁止Long类型
> * HTTP请求通过URL传递参数（?拼接的表示传递参数）时，不能超过2048字节
> * HTTP通过body传递内容，必须控制长度，否则后端解析会出错
>   * nginx默认1MB，Tomcat默认2MB，若确实需要传较大内容，可调节服务器端限制
> * 分页场景中
>   * 用户输入参数小于1，前端返回第一页给后端
>   * 后端发现用户输入的参数大于总页数，直接返回最后一页



### (11) 其他

> * 使用正则表达式，利用好预编译功能（不要在方法体内定义：Pattern p = Pattern.compile("规则")；）
> * 避免使用Apache BeanUtils进行属性的copy，性能差
>   * 推荐Spring BeanUtils，Cglib BeanCopier；均是浅拷贝
> * Math.random()方法返回的是double类型（0<=x<1，注意除零异常）；
>   * 若想获取随机整数，不要讲x放大到10的若干倍然后取整，直接使用Random对象的nextInt或nextLong方法即可





## 2. 异常日志

### (1) 错误码

> * 错误码制定原则：快速溯源、沟通标准化
> * 错误码不体现版本号和错误等级信息
> * 全部正常，但必须填充错误码返回五个零：00000
> * 错误码为字符串类型，共五位，分为两部分：错误产生来源 + 四位数字编号
>   * 错误产生来源：
>     * A：错误来源于用户；（参数错误、用户安装版本过低、支付超时等）
>     * B：错误来源于当前系统；（业务逻辑异常、程序健壮性差等）
>     * C：错误来源于第三方服务；（CDN服务出错、消息投递超时等）
>   * 四位数字编号
> * 编号不与公司业务架构，更不与组织架构挂钩
> * 错误码使用者避免随意定义新的错误码（尽可能在原有错误码表找语义相同或相近的错误码使用）
> * 错误码不能直接输出给用户作为提示信息使用



### (2) 异常处理

> * 可以通过预检查方式规避运行时异常
>   * 比如通过判断x!=null，替代catch(NullPointerException e){}
> * 异常捕获后不要用来作流程控制、条件控制
>   * 异常设计初衷就是解决程序运行中各种意外情况，且异常处理效率比条件判断方式低很多
> * catch时分清稳定代码和非稳定代码；尽可能拆分细一些
>   * 稳定代码：无论如何不会出错的代码
>   * 非稳定代码：尽可能区分异常类型，再做对应异常处理
> * 捕获异常是为了处理，若什么处理都不做就抛给调用者，最外层的业务使用者，必须处理异常（也要考虑return的场景），转化为用户可理解的内容
> * 事务场景，抛异常若需回滚，必须手动回滚事务
> * finally块对资源对象，流对象关闭
> * 不要在finally块中使用return返回数据
> * 捕获异常和抛出异常必须完全匹配，或捕获异常为抛出异常的父类
> * 在调用RPC、二方包、或动态生成类的相关方法时，捕获异常必须使用Throwable类进行拦截
>   * 一方包：本工程各模块的依赖
>   * 二方包：公司内部的依赖库
>   * 三方包：公司之外的开源库；例如apache、ibm、google等



### (3) 日志规约

> * 应用中不可直接使用日志系统中的API，而应依赖使用日志框架中的API，使用门面模式的日志框架，有利于处理各个类日至处理方式的统一
>   * 日志系统：Log4j、Logback
>   * 日志框架：Slf4j（推荐）、JCL
> * 所有日志至少保存15天
> * 日志输出时，字符串变量之间的拼接使用占位符的方式
> * 生产环境禁止直接使用System.out或Syetem.err输出日志；或使用e.printStackTrace打印异常堆栈
> * 日志打印禁止直接用JSON工具将对象转为String







## 3. 单元测试

> * 单元测试必须遵守AIR原则
>   * Automatic：自动化
>   * Independent：独立性
>   * Repeatable：可重复
> * 单元测试不准使用System.out人肉验证，必须使用assert验证
> * 保证单元测试的独立性，单元测试用例之间不能互相调用，也不能依赖执行的先后顺序
> * 单元测试可重复执行，不能收到外部环境影响
> * 单元测试要保证测试粒度足够小，有助于精确定位问题，单测粒度至多是类级别的，一般是方法级别
> * 核心业务、应用、模块的增量代码确保单元测试通过
> * 单测代码必须定义在如下工程目录：src/test/java，不允许写在业务代码目录下
>   * 源码编译会跳过此目录，而单元测试框架默认扫描此目录





## 4. 安全规约

> * 隶属于用户个人页面或功能必须进行权限控制校验（SpringSecurity安全管理框架两大核心功能点：身份验证和权限控制）
> * 用户敏感数据禁止直接展示，必须对数据进行脱敏（例如手机号码隐藏中间4位）
> * 用户输入的SQL参数严格使用参数绑定或METADATA字段值限定，防止SQL注入，禁止字符串拼接SQL访问数据库
> * 用户请求传入的参数必须做有效性校验
> * 禁止向HTML页面输出未经安全过滤或未正确转义的用户数据
> * 表单、AJAX提交必须执行CSRF（跨站点请求伪造）安全验证
> * URL外部重定向传入的目标地址必须执行白名单过滤
> * 使用平台资源，譬如短信、邮件、电话、下单、支付，必须实现正确的防重放机制（如数量限制、疲劳度控制、验证码校验，避免被滥刷导致资损）





## 5. MySQL数据库

### (1) 建表规约

> * 表达是与否概念	字段，必须使用is_xxx命名方式命名，数据类型为unsigned tinyint（1表示是，0表示否）。
>   * 任何字段若为非负数，必须是unsigned。
> * 表名、字段名必须使用小写字母或数字，禁止数字开头，禁止两个下划线之间只出现数字。
>   * 数据库字段名修改代价很大，因为无法进行预发布；
>   * MySQL在Windows下不区分大小写，但在Linux下默认区分大小写。因此数据库名、表名、字段名都不允许出现任何大写字母，避免节外生枝。
> * 表名不使用复数名词。
>   * 表名仅仅表示表内实体内容，不应表示实体数量；
>   * 对于DO（Data Object，属于POJO类的分支）类名也应使用单数形式；
> * 禁用保留字（desc、range、match、delayed等）
> * 主键索引名为  pk_xxx；唯一索引名为  uk_xxx；普通索引名为  idx_xxx。
>   * pk：primary key；
>   * uk：unique key；
>   * idx：index。
> * 小数类型为decimal；禁止使用float或double。
>   * 存储过程中，float或double都存在精度损失的问题；
>   * 若存储的数据范围超过decimal的范围，建议将数据拆成整数和小数分开存储。
> * 若存储的字符串长度几乎相等，可使用char定长字符串类型（明显取最大长度设定）。
> * varchar为可变长字符串，不会预先分配空间
>   * 长度不要超过5000，若存储长度大于此值，使用text定义字段类型，并独立出来一张表，用主键来对应，避免影响其他字段索引效率
> * 表必备三字段
>   * id：必为主键，类型为bigint unsigned，单表时自增、步长为1；
>   * create_time：datetime类型；主动式创建；
>   * update_time：datetime类型；被动式更新。
> * 数据库中不能使用物理删除操作（DDL语句：DROP），使用逻辑删除（DML：DELETE）。
>   * 逻辑删除在数据库删除后可以追溯到行为操作。



### (2) 索引规约

> * 业务上具有唯一特性的字段，即使是组合字段，也必须建成唯一索引
>   * 不要认为唯一索引影像insert速度，速度损耗可忽略，提高查询速度是明显的。
> * 超过三个表禁止join，需join的字段，数据类型必须一致；多表关联查询时，保证被关联的字段需要有索引。
>   * 页面搜索严禁左模糊或全模糊，如果需要就是用搜索引擎来解决。
>   * 索引文件具有B-Tree的最左前缀匹配特性，若左边值未确定，索引失效。



### (3) SQL语句

> * 不要用count( 列名 )或count( 常量 )来替代count(*)，count( *)是SQL92定义的标准统计行数的语法，跟数据库无关，跟NULL和非NULL无关。
>   * count(*)会统计值为NULL的行，而count(列名)不会统计此列为NULL值的行。
> * count(distinct col)计算col列除NULL之外的不重复行数；
>   * 注意count(distinct col1,col2)若其中某一列全为NULL，那么即使另一列有不同的值，也返回为0.
> * 某列值全为NULL时，count返回结果为0，但sum函数的返回结果为NULL，使用sum()注意NPE
>   * 避免sum的NPE问题：
>     * SELECT IFNULL(SUM(column),0) FROM table;
> * 使用 ISNULL() 来判断是否为NULL值。
>   * NULL与任何值的比较都为NULL；不会返回true或false。
> * 代码中执行分页查询时，若count为0应直接返回，避免执行后面的分页语句。
> * 不得使用外键与级联；一切外键概念必须在应用层解决。
> * 数据订正时（特别是删除或修改记录操作），要先select，避免出现误删除，确认无误才执行更新语句。
> * 数据库对表进行查询或变更，只要涉及多个表，都需要在列名前加表的别名（或表名）进行限定。



### (4) ORM映射

> * 表查询中，一律不要使用*作为查询的字段列表，必须明确写明需要查询的字段。
> * 前面提到了：POJO类布尔属性一律不能加is前缀，而数据库字段必须加is_。
>   * 在resultMap中进行字段与属性之间的映射即可。
> * SQL.xml配置参数使用：#{}，不要使用${}（容易引起SQL注入）。
> * IBATIS自带的queryForList（String name，int start，int size）不推荐使用；会查询name对应SQL语句的所有记录，效率低。
> * 不允许直接使用HashMap与Hashtable作为查询结果集的输出。
> * 更新数据库表记录时，必须同时更新记录对应的update_time字段值为当前时间。



## 工程结构









## 设计规约











