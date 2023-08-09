

![image-20230527144730443](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230527144730443.png)

# MyBatis

* SSM简介
  * Spring框架
    * Spring框架使用其核心IOC思想管理服务器中各个组件；使用AOP思想面向切面编程，在不修改源码基础上实现功能增强
  * SpringMVC框架
    * SpringMVC框架负责接收浏览器发送请求，并响应数据给浏览器
  * MyBatis框架---持久层框架
    * MyBatis框架封装JDBC，负责访问数据库，完成持久化操作

## 1.MyBatis简介

* MyBatis特性
  * MyBatis支持定制化SQL、存储过程以及高级映射的优秀的持久层框架
  * MyBatis框架避免了几乎所有的JDBC相关代码和手动设置参数（#{}---字符串拼接或${}---占位符赋值）以及获取结果集
  * MyBatis可以使用简单的XML配置或注解方式来配置和原始映射，将接口和Java的POJO映射成数据库的记录
    * POJO===Plain Old Java Object 普通的Java对象
  * MyBatis是个半自动的ORM框架
    * Object Relation Mapping
* MyBatis下载
  * https://github.com/mybatis/mybatis-3
* MyBatis框架和其他持久化层技术对比
  * JDBC
    * SQL夹杂在Java代码中，高耦合
    * 业务需求发生变化，会导致SQL频繁修改
    * 代码冗长，开发效率低
  * Hibernate和JPA
    * 操作简便，开发效率较高
    * 程序中的长的难的SQL需要绕开框架
    * 自动生成SQL，不容易做特殊优化
    * 全映射的全自动框架，大量字段的POJO进行部分映射会比较困难
    * 反射操作较多，导致数据库性能下降
  * MyBatis
    * 轻量级，性能出色
    * SQL与Java编码分开，Java专注业务，SQL专注数据；解耦
    * 开发效率稍逊于Hibernate



## 2.搭建MyBatis

* 开发环境

  * IDE：idea 2020.3
  * 构建工具：apache-maven-3.6.3
  * MySQL版本：MySQL 5.5.55
  * MyBatis版本：mybatis-3.5.10

* ==创建Maven工程==

  * 打包方式：jar
  * 引入依赖
    * junit
    * MyBatis核心
    * MySQL驱动

* ==创建MyBatis核心配置文件==

  * 习惯命名：mybatis-config.xml（建议，并非强制要求）
    * 整合Spring之后，该配置文件可省略
  * 核心配置文件存放地址：/src/main/resources目录下
  * 核心配置主要用于配置:
    * 连接数据库的环境
    * MyBatis的全局配置信息
  * 核心配置文件配置如何连接数据库；映射文件用于如何操作数据库

* ==创建Mapper接口==

  > MyBatis中的Mapper接口相当于Java中的DAO（Data Access Object）；区别在于Mapper仅仅是接口，无需提供实现类

  * Mapper接口起名规范：POJO类名+Mapper

* ==创建MyBatis映射文件==

  * 映射文件名称：Mapper接口名称（POJO实体类名+Mapper）.xml
  * 映射文件和Mapper接口要保证两个一致
    * mapper标签的namespace与Mapper接口全类名保持一致
    * mapper标签下的DQL、DML标签中的id要与Mapper接口方法名保持一致

* 加入Log4j功能

  * 添加pom依赖
  * 创建log4j.xml，并配置信息

* junit测试

  * 获取MyBatis核心配置文件输入流
  * 获取SqlSessionFactoryBuilder对象
  * 获取SqlSessionFactory对象---通过构建核心配置文件输入流方式获取
  * 获取SqlSession对象（核心对象）---是MyBatis框架提供的操作数据库的对象
  * 获取Mapper接口的代理实现类对象
  * 调用Mapper接口方法，实现业务
  * SqlSession必须进行事务操作，否则默认事务回滚
  * 关闭SqlSession会话---SqlSession对象













## 3.核心配置文件详解

* properties

  * 引入properties文件；
  * 使用${key}方式来访问value
  * <properties  resource="jdbc.properties" / >

* typeAliases设置类型别名：在MyBatis范围中，可以使用该别名表示具体类型

  * typeAlias：type指定具体类型；alias指定别名；

    * alias属性不定义，默认使用类名且不区分大小写
    * 可以在映射文件中使用

  * typeAlias还是不够简洁---引出package标签

    * < package name="POJO包名" / >
      * 此方式设定（例如name="com.lixc.mybatis.pojo"），包下所有类都拥有默认别名---类名且不区分大小写（建议大写）

  * ```xml
    ...
    	<typeAliases>
            <!-- 该方式最简洁-拥有默认别名 -->
            <package name="com.lixc.mybatis.pojo"/>
    	</typeAliases>
    ...
    ```

* environments

* mappers

  * ```xml
    ...
    	<mappers>
            <!-- 原始方式：通过resource引入具体映射文件，不够简洁-->
            <mapper resource="mapper/UserMapper.xml" />
            
            <!--
    			以包的方式引入映射文件；必须满足以下条件：
    				1、映射文件所在的包，必须和Mapper接口所在包一致(jave包和resources包编译后target包下等级合并)
    				2、映射文件名称，必须和Mapper接口名称一致
    		-->
            <package name="com.lixc.mybatis.mapper"/>
    	</mappers>
    ...
    ```

  * 所以映射文件的包路径：（resources目录下 + com/lixc/mybatis/mapper/Mapper接口名称.xml）

    * 创建Directory包必须以 / 方式而不是点



## 4.==MyBatis增删改查==



## 5.==MyBatis获取参数值的两种方式==

> MyBatis获取参数值的两种方式：#{}和${}
>
> ${}本质是字符串拼接；但是为字符串类型或日期类型拼接时需要加单引号
>
> #{}本质是占位符赋值；任何类型只需赋值即可

* 单个字面量类型参数
  * 可以使用${}或#{}以任意名称获取参数值；
  * 注意${}需注意字符串类型
* 多个字面量类型参数
  * MyBatis会自动将多个参数放入Map集合中，以arg0，arg1,...为键，参数为值；以param1，param2...为键，参数为值
  * 只需要通过${}或#{}访问Map集合中的键即可获取值；
  * 注意${}需要手动加单引号
* Map集合类型参数
  * 手动创建的Map集合类型的参数；
  * 通过${}或#{}访问集合的键即可获取值；
  * 注意${}需要加单引号
* 实体类类型参数
  * 参数为实体类对象时；
  * ${}或#{}访问实体类属性名，获取对应属性值；
  * 注意${}需要加单引号
* 使用@Param标识参数
  * 命名参数：为MyBatis框架在封装参数时定义参数对应键
  * @Param("username")定义在Mapper接口方法的形参列表参数之前，表示为该参数赋一个键，该参数就是键对应的值，之后通过#{username}或${usernam}方式获取属性值即可



* 通过以上五种Mapper接口参数不同类型，可大致分为两种情况：
  * 参数为实体类类型；
  * 参数为@Param注解；











## 6.==MyBatis的各种查询功能==

* 若DQL语句查询结果为多条，不能使用实体类类型作为方法返回值，否则会抛出TooManyResultsException异常

* 注意查询后返回类型---MyBatis框架为Java中的常用类型设置了别名

  * Integer类型 ---> int、 integer

  * int类型---> 下划线int、下划线integer

  * String类型--->string

  * Map类型--->map

  * ```xml
    <mapper namespace="com.lixc.mybatis.mapper.XxxMapper">
        <select id="Mapper接口方法名" resultType="integer">
            SELECT COUNT(*) FROM `表名`;
        </select>
    </mapper>
    ```

* 将查询的数据类型作为Map集合类型
  * 查询一条数据直接设定Map即可；实体类属性名作为key，属性值作为value
  
  * 查询多条数据
  
    * 方式一：使用List<Map>类型即可----包含多个包含实体类数据的Map集合
  
    * 方式二：使用Map集合，但需要添加@MapKey注解，注解中填写作为key的查询字段
  
      * ```java
        @MapKey("id")
        Map<String,Object> getAllUsers();
        
        
        //对应的Mapper映射文件
        <select id="getAllUsers" resultType="map">
            SELECT * FROM `表名`
        </select>
        ```
* 可以将查询的多条数据的类型设定为List<Map > 集合类型；













## 7.==特殊SQL的执行==

* 模糊查询（正常#{}方式获取参数回报错）

  * 使用${}来获取参数
  * 使用CONCAT拼接函数
  * 最常用方式：依旧使用#{}方式获取参数，使用双引号包裹%
    * "%"#{参数key}"%"

* 批量删除---使用到IN关键字进行多条数据批量删除

  * 不能使用#{}通过占位符赋值方式执行SQL；
    * 会出现 IN( ' 1,2 ' )携带单引号的形式；
    * MySQL5.5版本不会报错，但只会删除第一条数据----明显执行逻辑有问题
  * 只能通过${}方式在IN关键字情况下进行批量删除

* 动态设置表名

  * 也不能使用#{}占位符赋值方式对表名进行赋值
  * 只能使用${}字符串拼接方式动态设置表名

* 添加功能获取自增的主键

  * JDBC本身就提供了原生方法：

    * Result result = preparedStatement.getGeneratedKeys()；

    * ```java
      //获取自动递增的主键
      PreparedStatement ps = connection.getPreparedStatement(sql,Statement.RETURN_GENERATED_KEYS);
      ResultSet resultSet = preparedStatement.getGeneratedKeys();
      resultSet.next();
      resultSet.getInt(1);
      ```

  * MyBatis框架中：

    * ```xml
      <insert id="Mapper接口方法名" useGeneratedKeys="true" keyProperty="id">
          INSERT INTO `表名` VALUES(NULL,#{xxx},...);
      </insert>
      
      
      <!--测试-->
      XxxMapper xxxMapper = sqlSession.getMapper(XxxMapper.class);
      Xxx xxx = new Xxx(null,xx,...);
      xxxMapper.insertXxx();
      System.out.println(xxx);
      //虽然并未传参id，但Mapper映射文件中设定了useGeneratedKeys表示添加语句中使用了自增的主键；
      keyProperty：将添加的数据的自增主键 添加到实体类对象的参数的属性
      此时可看到对象中id一栏已经有值---添加数据数据库自动递增的ID。
      ```

    * 





## 8.==自定义映射resultMap==

> 字段名或属性名不一致；一对多、多对一；都需要使用到resultMap
>
> ==resultMap自定义映射的三个功能==：
>
> * ==处理字段名和属性名不一致的映射关系==
>
>   1. SQL起别名
>
>   2. 全局配置（MySQL，Java规范必须遵守）
>
>   3. resultMap中column字段名和property属性名映射
>
> * 处理多对一的映射关系（都是在resultMap标签中的方式）---对一对应对象
>
>   1. 级联映射
>
>   2. association
>
>   3. 分步查询
>
> * 处理一对多的映射关系（都是在resultMap标签中的方式）---对多对应集合
>
>   1. collection
>
>   2. 分步查询

### 字段名和属性名不一致：

* 数据库表字段使用下划线

* POJO实体类使用驼峰

* 如何处理映射关系

  1. 方式一：为字段名起别名，别名与实体类字段名保持一致

  2. 方式二：前提-当字段名符合MySQL命名规范（下划线），属性名符合Java命名规范（驼峰）

     * 可以在MyBatis核心配置文件中设置==全局配置==（settings标签-设置全局配置），将下划线自动映射为驼峰

     * ```xml
       <settings>
           <!-- 将下划线映射为驼峰 -->
           <setting name="mapUnderscoreToCamelCase" value="true"></setting>
       </settings>
       ```

  3. 方式三：在MyBatis的Mapper映射文件中-自定义映射resultMap

     * ```xml
       <!--
       	resultMap自定义映射标签
       		id属性：唯一标识，在DQL语句标签中resultMap属性中填写该值
       		type属性：处理映射关系的实体类的类型
       	resultMap标签下的常用子标签：
       		id标签：对应数据库表主键字段；column属性表示数据库表字段名，property属性表示POJO属性名
       		result标签：对应数据库表普通字段；column属性表示数据库表字段名，property属性表示POJO属性名
       		association标签：处理多对一的映射关系；property属性对应实体类中属性，javaType设定属性的类型
       			id标签：...
       			result标签：...
       -->
       <resultMap id="" type="">
           <id column="" property=""/>
           <result column="" property=""/>
       </resultMap>
       ```



### 多对一映射处理

> 场景：多个员工对应一个部门

1. ==级联方式处理映射关系==

   * ```xml
     
     <resultMap id="自定义映射resultMap唯一标识" type="映射的实体类类型">
         <id column="主键字段名" property="属性名"/>
         <result column="普通字段名" property="属性名"/>
         <result column="字段名" property="关联其他实体类.其他实体类属性名"/>
         <result column="字段名" property="关联其他实体类.其他实体类属性名"/>
     </resultMap>
     
     <select id="Mapper接口方法名" resultType="自定义映射唯一标识">
         SELECT ...
     </select>
     ```

2. ==使用association处理映射关系==

   * ```xml
     <!--
     	association标签专门用来处理多对一的映射关系---专门处理实体类类型属性
     	association标签：处理多对一的映射关系；property属性对应实体类中属性，javaType设定属性的类型
     			id标签：...
     			result标签：...
     -->
     <resultMap id="自定义映射resultMap唯一标识" type="映射的实体类类型">
         <id column="主键字段名" property="属性名"/>
         <result column="普通字段名" property="属性名"/>
         <association property="映射的实体类类型中的对应实体类的属性值" javaType="对应实体类的类型">
             <id column="" property=""/>
             <result column="" property=""/>
         </association>
     </resultMap>
     
     <select id="Mapper接口方法名" resultType="自定义映射唯一标识">
         SELECT ...
     </select>
     ```

3. ==分步查询==

   * ```xml
     <resultMap id="自定义映射resultMap唯一标识" type="映射的实体类类型">
     	<association property="映射的实体类类型的属性名" 
                  	select="执行下一个分步查询的全类名+方法名" column="通过分步查询获取的字段名-用来执行下一步的条件"/>
     </select>
     ```

   * 分步查询优点

     * 可以实现延迟加载（懒加载）

     * 前提必须设置全局配置（MyBatis核心配置文件内）

       * ```xml
         <settings>
             <!--将下划线映射为驼峰（只是对字段进行映射，和延迟加载无关）-->
             <setting name="mapUnderscoreToCamelCase" value="true" />
             <!--开启延迟加载-->
             <setting name="lazyLoadingEnabled" value="true" />
             <!--false表示按需加载（默认就是false，但是开启延迟加载时，还要清楚需要按需加载的设置，所以写出来）-->
             <setting name="aggressiveLazyLoading" value="false" />
         </settings>
         ```

       * 因为是全局配置，若想某个分步查询不去延迟加载，association标签中设定fetchType属性（Mapper映射文件内）

         * ```xml
           
           <!--fetchType属性值：lazy延迟加载（全局配置作为前提）；eager立即加载
           	在开启了全局配置的延迟加载环境中，通过该属性设置当前分步查询是否使用延迟加载
           -->
           <resultMap id="唯一值" type="映射的实体类类型">
               ...
               <association property="dept" fetchType="eager"
                            select="com.xxx.xxx.类名.方法名"
                            column="字段名-分步查询下一步的传参"/>
           </resultMap>
           ```







### 一对多映射处理

1. collection标签处理映射关系

   * collection处理一对多的映射关系（处理集合类型的属性）

   * ```xml
     
     <!--处理一对多collection和处理多对一的association使用相似，只有标签属性一个是ofType表示集合中的类型，一个是JavaType表示Java类型-->
     <resultMap id="唯一标识" type="实体类的类型">
         ...
         <collection property="" ofType="集合中的具体类型">
             <id .../>
             <result ... />
         </collection>
     </resultMap>
     ```

2. 分步查询





## 9.==动态SQL==

> MyBatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能
>
> 存在的意义就是为了解决拼接SQL语句字符串时的痛点问题
>
> 本质就是Mapper映射文件中的一系列标签

### if标签

* getEmpByCondition方法进行测试

* 通过if标签的test属性中的表达式判断标签中的内容是否有效（无效：null或空字符串）

  * ```xml
    <mapper namespace="com....XxxMapper">
        <select id="方法名" resultType="实体类名">
            SELECT * FROM `表名` WHERE 
            <if test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
                字段名=#{@Param注解定义的参数名 或 实体类对象的属性名}
            </if>
            <if test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
                 AND 字段名=#{@Param注解定义的参数名 或 实体类对象的属性名}
            </if>
            ...
        </select>
    </mapper>
    ```

  * 问题很多==

    * if标签中任何表达式都不成立--->会多出一个WHERE
    * 第一个表达式不成立--->WHERE 后面会直接跟一个AND 

  * 解决方式：where后面跟一个恒成立的条件，且所有if标签中表达式成立后的SQL语句前缀都要有AND

    * ```XML
      <mapper namespace="com....XxxMapper">
          <select id="方法名" resultType="实体类名">
              SELECT * FROM `表名` WHERE 1=1
              <if test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
                  AND 字段名=#{@Param注解定义的参数名 或 实体类对象的属性名}
              </if>
              <if test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
                  AND 字段名=#{@Param注解定义的参数名 或 实体类对象的属性名}
              </if>
              ...
          </select>
      </mapper>
      ```

### where标签

* <where>标签，包裹if标签

  * 判断where标签中有条件成立，自动生成WHERE 关键字

  * where标签会自动将成立条件中拼接的SQL语句前面多余的AND关键字去掉（内容后的AND关键字不会进行判断和处理）

  * where标签中没有条件成立，不会生成WHERE关键字

  * ```xml
    <mapper namespace="">
        <select id="" resultType="">
            SELECT * FROM `表名`
            <where>
                <if test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
                	字段名=#{@Param注解定义的参数名 或 实体类对象的属性名}
            	</if>
            	<if test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
                	AND 字段名=#{@Param注解定义的参数名 或 实体类对象的属性名}
            	</if>
            	...
            </where>
        </select>
    </mapper>
    ```

### trim标签

* trim标签，包裹if标签

  * prefix、suffix属性：

    * 在标签中内容前面或后面添加指定内容

  * prefixOverrides、suffixOverrides

    * 在标签中内容前面或后面去除指定内容

  * ```xml
    <mapper namespace="">
        <select id="" resultType="">
            SELECT * FROM `表名`
            <!--在标签内容前加WHERE，标签后去除AND-->
            <trim prefix="WHERE" suffixOverrides="AND">
                <if test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
                	字段名=#{@Param注解定义的参数名 或 实体类对象的属性名} AND
            	</if>
            	<if test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
                	字段名=#{@Param注解定义的参数名 或 实体类对象的属性名} AND
            	</if>
            	...
            </trim>
        </select>
    </mapper>
    ```





### choose、when、otherwise

> choose、when、otherwise相当于 if...else if ...else
>
> when、otherwise标签都要在choose标签内使用
>
> when至少设置一个，otherwise最多设置一个
>
> 因为相当于Java中if else if else的功能，所以即便所有条件成立也只会执行第一个成立条件的内容（不要加AND）

* 创建getEmpByChoose---使用choose标签查询员工信息

* ```xml
  <mapper namespace="">
      <select id="" resultType="">
          SELECT * FROM `表名`
          <choose>
              <when test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
              	字段名=#{@Param注解定义的参数名 或 实体类对象的属性名}
          	</when>
          	<when test=" 实体类属性名(直接写名即可) != null and 属性名 != '' ">
              	字段名=#{@Param注解定义的参数名 或 实体类对象的属性名}
          	</when>
          	...
          </choose>
      </select>
  </mapper>
  ```





### foreach

> foreach标签 做 批量添加、批量删除操作
>
> 可使用集合或数组参数作为批量删除、添加的传参

* insertMoreEmp



* foreach标签
  * collection属性：用来接收的传参（设置要循环额的数组或集合）---使用@Param注解值
  * item属性：自定义迭代后的数据名
    * 若是实体类，可使用#{item名 . 实体类属性名}获取数据
    * 基本数据类型，#{item名}
  * separator属性：每次foreach循环以什么作为分隔符
  * open属性：整个循环以什么开始
  * close属性：整个循环以什么结尾

* ```xml
  <mapper namespace="">
      <insert id="">
          INSERT INTO `` VALUES
          <foreach collection="@Param注解value值" item="自定义 集合或数组每项数据" separator=",---表示分隔符">
              (null,#{item属性值.属性名},#{item属性值.属性名},..)
          </foreach>
      </insert>
      
      <delete id="">
          DELETE FROM `` WHERE `` IN
          (
          	<foreach collection="@Param注解value值" item="自定义 集合或数组每项数据" separator=",">
              	#{item的属性值}
          	</foreach>
          )
      </delete>
      
      <delete id="">
          DELETE FROM `` WHERE `` IN
          <!--foreach标签属性
  			open属性：当前循环以什么开始
  			close属性：当前循环以什么结束
  		-->
          <foreach collection="@Param注解value值" item="自定义 集合或数组每项数据" separator="," open="(" close=")">
              #{item的属性值}
          </foreach>
      </delete>
      
      <delete id="">
          DELETE FROM `` WHERE
          <foreach collection="@Param注解value值" item="自定义 集合或数组每项数据" separator="or">
              `` = #{item的属性值}
          </foreach>
      </delete>
  </mapper>
  ```





### SQL片段

> 通过SQL片段标签（sql）记录一段SQL语句，配合include标签对片段进行引入；
>
> 可重复使用

* 使用SQL标签设置SQL语句，配合include标签引入设置的SQL语句

  * ```xml
    <mapper namespace="">
        <sql id="自定义">
            ``,``...
        </sql>
        <select id="Mapper接口方法名" resultType|resultMap属性="">
            SELECT <include refid="sql标签定义的唯一id"/> FROM ...
        </select>
    </mapper>
    ```







## MyBatis的缓存

### MyBatis的一级缓存

* MyBatis的一级缓存（默认开启）是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同数据，就会从缓存中获取，不会从数据库重新访问
* 一级缓存失效的四种情况
  * 不同SqlSession对应不同的一级缓存
  * 同一个SqlSession但查询条件不同
  * 同一个SqlSession但两次查询期间执行了DML语句
  * 同一个SqlSession但两次查询期间清空缓存---sqlSession.clearCache();

### MyBatis的二级缓存

* MyBatis的二级缓存是SqlSessionFactory级别的；通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；此后再次查询相同语句，会从缓存中获取数据

* 二级缓存开启条件：

  1. 核心配置文件中，设置全局配置属性：cacheEnabled="true"，默认就是true，无需设置

  2. 映射文件中设置标签cache：<cache/>

  3. 二级缓存必须在SqlSession关闭或提交后有效：sqlSession.close();

  4. 查询的数据所转换的实体类类型必须实现序列化：implements Serializable

* 二级缓存失效情况：

  * 两次查询期间执行了任意增删改操作，会使一级缓存和二级缓存同时失效





### 二级缓存的相关配置



### MyBatis缓存查询的顺序

* 先查询二级缓存，因为二级缓存可能会有其他程序已经查询到的数据，可直接拿来使用
* 二级缓存未命中，查询一级缓存
* 一级缓存未命中，查询数据库
* SqlSession关闭后，一级缓存数据会存入二级缓存



### 整合第三方缓存EHCache（了解）

* 针对二级缓存



## 11.MyBatis的逆向工程

> 本质就是代码生成器
>
> QBC查询方式 Query By Criteria

* 使用奢华尊享版
* 逆向工程创建的工程文件，都是针对单表进行的操作；多表相关操作还是需要定义映射关系、操作语句





## 分页插件











