# SSM整合

>  各个框架在SSM中担任的角色：
>
> Spring框架：开源的轻量级JavaEE开发框架；整合型框架；IOC控制反转和AOP面向切面编程
>
> * IOC管理对象（例如MyBatis中操作数据库的SqlSession对象）
> * AOP应用声明式事务---实现MyBatis事务操作
>
> SpringMVC框架：用作表述层开发的框架（表述层表示前台页面和后端Servlet程序）；
>
> * 用来处理浏览器向服务器发送的请求并做出响应（向域对象共享数据，页面跳转）
>
> MyBatis框架：持久层框架
>
> * 用来连接数据库，访问、操作数据库中的数据

* 整合
  * Spring整合SpringMVC：两个框架各自管理各自的组件（不整合就是使用一个配置文件进行组件管理）
* Spring和SpringMVC各自IOC容器创建时间
  * SpringMVC管理的组件---控制层组件；Spring管理的组件---业务层组件
  * 而控制层代码中实现业务要依赖业务层组件，在成员变量位置对业务层组件进行自动装配
  * 配置的DispatcherServlet前端控制器会跟随服务启动而加载，读取SpringMVC配置文件，创建控制层组件对象，而控制层对象对业务层组件有自动装配需求，所以Spring的IOC容器要在SpringMVC的IOC容器加载之前加载完成。
* JavaWeb三大组件
  * 监听器、过滤器、Servlet
  * 监听器最早执行
  * 因为SpringMVC的IOC容器是在Servlet初始化时执行的，所以可以考虑将Spring的IOC容器在监听器执行阶段加载完成
* 三种监听器
  * ServletContextListener（最常用）
    * 监听ServletContext状态
    * 两个抽象方法：上下文对象初始化和销毁
  * HttpSessionListener
    * 监听HttpSession状态
  * HttpSessionAttributeListener
    * 监听HttpSession状态
* Spring提供了ContextLoaderListener监听器，实现了ServletContextListener接口
  * ContextLoaderListener：在服务器启动时加载Spring配置文件
  * 直接在web.xml中配置该监听器
    * 默认加载的Spring配置文件的位置和名称为：WEB-INF/applicationContext.xml
    * 可通过上下文初始化参数（SpringMVC配置文件是在Servlet内初始化参数配置）来配置Spring配置文件的位置和名称
* Spring整合SpringMVC配置各自IOC容器，为何SpringMVC的IOC容器可以访问到Spring的IOC容器组件
  * 在SpringMVC的DispatcherServlet源码中，DispatcherServlet进行初始化操作时，创建了WebApplicationContext---SpringMVC的IOC容器，与此同时为该IOC容器设置了父容器【setParent( parent );父容器就是Spring的IOC容器】；
  * 所以Spring和SpringMVC的IOC容器关系是父子IOC容器；
    * 子容器可以访问父容器管理的组件
    * 父容器无法访问子容器管理的组件



## SSM整合准备工作

### 创建Maven工程，pom文件添加依赖

* spring-context
* spring-beans
* spring-web
* spring-webmvc
* spring-jdbc
  * 虽然使用MyBatis框架操作数据库，但该依赖有声明式事务管理器DataSourceTransactionManager
* spring-aspects
* spring-test
* mybatis
* mybatis-spring
  * 提供了一个类SqlSessionFactoryBean；直接配置Spring配置文件；该工厂bean提供SqlSessionFactory对象
* druid
* junit
* mysql-connector-java
* log4j
* pagehelper
  * 分页插件
* logback-classic
  * thymeleaf中依赖了slf4j，该依赖是slf4j日志门面的实现
* javax-servlet-api
  * SpringMVC中前端控制器间接继承了HttpServlet，所以需要该依赖
* jackson-databind
* commons-fileupload
* thymeleaf-spring5





### 配置web.xml

* Spring字符编码过滤器
* Spring处理请求方式过滤器

* SpringMVC前端控制器
* Spring的ContextLoaderListener监听器
* 上下文参数-Spring配置文件位置和名称



### 创建SpringMVC配置文件并配置

* 控制层组件扫描
* Thymeleaf视图解析器
* mvc注解驱动
* mvc默认Servlet处理静态资源
* mvc视图控制器
* SpringMVC内部组件化插拔式，即插即用，有需要再配置
  * 文件上传解析器-CommonsMultipartFileResolver--id：multipartResolver
  * mvc拦截器
  * mvc异常解析器-SimpleMappingExceptionResolver



### 搭建MyBatis环境

* MyBatis核心配置文件mybatis-config.xml
  * properties配置文件需要创建---数据库连接信息
  * pojo包下的实体类需要创建---将下划线映射为驼峰
  * mapper接口需要创建---映射到映射文件（接口名和映射文件名一致，接口包和映射文件包一致）
* Mapper接口---MyBatis中面向Mapper接口执行SQL
* MyBatis的Mapper映射文件
* 添加MyBatis的日志文件
  * log4j.xml



### 创建Spring配置文件并配置

* SpringMVC管理控制层组件，即Spring的IOC容器除去控制层组件其它都进行管理

  * ```xml
    <context:component-scan base-package="com.lixc.ssm">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    ```

* 创建数据源---DataSource
* 创建JdbcTemplate
* 配置事务管理
  * 配置事务管理器-DataSourceTransactionManager
  * 开启事务注解（事务注解驱动）

#### 整合MyBatis（添加SqlSessionFactoryBean、MapperScannerConfigurer两个标签）

* 配置==SqlSessionFactoryBean==；可以直接在Spring的IOC 中获取SqlSessionFactory对象

  * SqlSessionFactoryBean实现了FactoryBean<SqlSessionFactory>接口；所以该SqlSessionFactoryBean提供的对象就是SqlSessionFactory

  * 这样可以在业务层直接对SqlSessionFactory进行自动装配

  * ```xml
    <!--配置SqlSessionFactoryBean：可以直接在IOC容器获取SqlSessionFactory-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--设置MyBatis核心配置文件的路径-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!--设置数据源-可用来替代核心配置文件的配置信息-->
        <property name="dataSource" ref="dataSource"/>
        <!--配置类别名所对应的包-可用来替代核心配置文件的配置信息-->
        <property name="typeAliasesPackage" value="com.lixc.ssm.pojo"/>
    </bean>
    ```

* 配置==MapperScannerConfigurer==；提供了每一个Mapper接口的代理实现类对象

  * 作用：配置Mapper接口的扫描；可以将指定包下所有的Mapper接口，根据上面配置获取到的SqlSession创建代理实现类对象，并将这些Mapper接口代理实现类对象交给IOC容器进行管理

    * 业务层无需再对SqlSessionFactory进行自动装配（之后还要获取SqlSession，在获取对应Mapper接口，再调用方法）
    * 所有可以被Spring管理的bean（例如业务层） 可直接在成员变量位置对Mapper接口进行自动装配

  * 属性标签配置basePackage属性，配置MyBatis接口所在的包

  * ```xml
    <!--配置Mapper接口的扫描；可以将指定包下所有的Mapper接口，根据上面配置获取到的SqlSession创建代理实现类对象，并将这些Mapper接口代理实现类对象交给IOC容器进行管理-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.lixc.ssm.mapper"/>
    </bean>
    ```





## SSM整合-实现员工列表功能

* 业务（RESTful风格---请求地址风格一致【不再使用问号键值对的请求参数】）
  * 查询所有员工功能
    * GET：/emp
  * 根据ID查询员工信息
    * GET：/emp/{id}
  * 跳转到添加页面
    * GET：/emp/add
  * 添加员工信息
    * POST：/emp
  * 跳转到修改页面
    * GET：/emp/update
  * 修改员工信息
    * PUT：/emp
  * 根据ID删除员工信息
    * DELETE：/emp/{id}





* 添加静态资源
* Thymeleaf语法 th:each="xxx,status : ${yyy}" 其中status是Thymeleaf提供的辅助对象，有对获取数据辅助的功能
  * 例如${status.count}就是计数功能





























## 末尾