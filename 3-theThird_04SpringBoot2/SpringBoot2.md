# SpringBoot2核心技术~~与响应式编程~~

> 学习要求：
>
> * 熟悉Spring基础
> * 熟悉Maven应用
>
> 环境要求：
>
> * Java8及以上
> * Maven3.3及以上

* Spring5由于Java8的新特性，内部源码发生变化：
  * 由于Java8提供了接口的默认实现，Spring5无需使用适配器模式（适配器来为接口实现所有的默认空方法，便于之后的子类来继承）；直接在顶级接口就定义了默认方法。

## 基础入门

### 1. Spring和SpringBoot

* 为什么用SpringBoot？
  
* 能够快速创建出生产级别的Spring应用
  
* SpringBoot优点

  * 创建独立的Spring应用
    * 原生Spring应用-写配置文件
  * 内嵌web服务器
    * 比如开发一个SpringMVC，最终要被打包war，部署到Tomcat服务器
    * 使用SpringBoot进行开发，内嵌web服务器，无需考虑Tomcat、letty或Undertow这些web服务器的安装
  * 自动stater依赖，简化构建配置
    * 之前Spring整合，导入依赖，需要对依赖进行版本控制
    * SpringBoot简化了依赖配置，它为我们做好各个依赖间的版本控制问题
  * 自动配置Spring以及第三方功能（激动人心的功能）
    * Spring整合MVC整合MyBatis，写一大堆配置
    * SpringBoot将这些帮我们自动配置
  * 提供生产级别的监控、健康检查及外部化配置
  * 无代码生成，无需编写XML

  > SpringBoot是整合Spring技术栈的一站式框架
  >
  > SpringBoot是简化Spring技术栈的快速开发脚手架

* SpringBoot缺点

  * 版本帝，迭代快
  * 封装太深，内部原理复杂，不易精通（SpringBoot底层是Spring框架、Spring底层是基于Java实现的）

### 2. SpringBoot2入门-HelloWorld（==简化开发、配置、部署==）

> 系统要求：
>
> * Java8
> * Maven3.3+
>   * Maven全局配置文件settings.xml配置本地仓库、中央镜像仓库-阿里云、JDK版本-1.8

> 根据Spring.io官方网址下可以看操作步骤
>
> 1. SpringBoot
> 2. learn
> 3. Reference Doc.
> 4. Getting Started
> 5. Developing Your First Spring Boot Application（一步步带领我们快速建立SpringBoot工程）

> 需求：浏览器发送/hello请求，服务器做出响应Hello,Spring Boot 2!

#### 1. 新建Maven工程

#### 2. 引入依赖

* pom文件添加父工程和web场景启动器依赖

  * ```xml
    <parent>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-parent</artifactId>
    	<version>2.3.4.RELEASE</version>
    </parent>
    
    <dependencies>
        <!--称之为Web场景启动器-->
    	<dependency>
    		<groupId>org.springframework.boot</groupId>
    		<artifactId>spring-boot-starter-web</artifactId>
    	</dependency>
    </dependencies>
    ```

#### 3. 创建主程序

* 新建类文件-----==主程序类；主配置类==

  * 添加@SpringBootApplication注解

    * 告诉SpringBoot，这是一个SpringBoot应用
    * 该类被称之为主程序类 （所有启动的入口）

  * ==建立main方法（主方法）调用主程序==

    * SpringApplication.run( 主程序类.class,args ); //启动主程序类；固定写法

  * ```java
    @SpringBootApplication//主程序类（所有启动的入口）；告诉SpringBoot这是一个SpringBoot的应用
    public class MainApplication {
        public static void main(String[] args) {
            SpringApplication.run(MainApplication.class, args);//启动运行主程序
        }
    }
    ```

#### 4. 编写业务

* 新建请求控制器（控制层文件）

  * ```java
    @RestController//相当于@Controller + @ResponseBody （表示该类为控制器，且类下每个方法都有响应体-不会进行视图跳转）
    public class HelloController{
        @RequestMapping("/hello")
        public String handle01(){
            return "Hello,Spring Boot 2!";//类添加@RestController注解，所以不会进行视图跳转，返回响应体数据
        }
    }
    ```

#### 5. 测试

* IDEA无需整合Tomcat，无需将项目部署到Tomcat，==直接调用main方法==即可（里面调用了主程序类启动）
  * 控制台正常输出
  * 浏览器访问localhost:8080/hello（/hello是控制器方法定义的映射关系路径）即可

#### 简化配置

> 将所有的配置抽取在一个配置文件中：固定名称 ==application.properties==
>
> * 可修改Tomcat设置、SpringMVC设置
>   * 例如修改Tomcat端口号：server.port=8888

* 参照官方文档可以看到可配置什么；进行相应配置即可
  1. SpringBoot
  2. learn
  3. Reference Doc.
  4. Application Properties



#### 简化部署（创建一个可执行的jar）-配置打包jar插件

* 若是之前创建web服务，pom文件需要定义打包方式为war，Maven->package打包后将war包部署到Tomcat服务器上（IDEA整合Tomcat），然后启动Tomcat服务，一系列操作很繁琐

* 如今使用SpringBoot后，pom文件build一个SpringBoot的Maven插件，把项目打成jar包，直接在目标服务器执行即可

  1. SpringBoot
  2. learn
  3. Reference Doc.
  4. Getting Started
  5. Developing Your First Spring Boot Application（一步步带领我们快速建立SpringBoot工程）
  6. Creating anExecutable Jar（告诉我们怎样创建一个可执行的jar）

  * ```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.3.4.RELEASE</version>
            </plugin>
        </plugins>
    </build>
    ```

  * 注意：Plugin 'org.springframework.boot:spring-boot-maven-plugin:' not found

    * 导入插件后出现该异常可能是缓存问题
      * File->Invalidate Caches
    * 清除缓存后还报红-添加版本号
      * 找到该项目引入的父标签SpringBoot的版本号（上面的build标签下的SpringBootMavenPlugin下面已经添加了版本号）

* Maven->clean->package
  * 部署该插件后（spring-boot-maven-plugin）进行打包（jar）会比未部署时多生成一个BOOT-INF目录
  * 在当前目录下cmd（target下---里面包含打成的jar包）
    * 进入生成jar包的目录
    * java -jar xxxxx-1.0-SNAPSHOT.jar（此时SpringBoot的应用已经停止-main方法调用主程序类的进程已关闭）
    * 就会启动该服务
    * 注意：取消cmd属性栏中的快速编辑模式
    * 浏览器访问服务地址即可



### 3. 自动配置原理的了解

#### 1. SpringBoot特点

##### 1.1 依赖管理

* ==父项目做依赖管理==

  * 每一个SpringBoot工程都要在pom文件中配置父工程

  * ```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
    </parent>
    -----------------------------------------------
    Ctrl + 鼠标左键 spring-boot-starter-parent 发现：SpringBoot工程配置的父项目又依赖了另一个父项目：
    <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>2.3.4.RELEASE</version>
    </parent>
    
    spring-boot-dependencies项目通过properties标签声明定义了几乎所有日常开发中常用的依赖的版本号；自动版本仲裁机制
    所以我们之后配置依赖，直接填写依赖的groupId和artifactId即可，无需关注版本号
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    -----------------------------------------------
    
    发现声明的例如mysql相关的依赖版本：<mysql.version>8.0.21</mysql.version>
    而我们本地想使用MySQL5相关的版本，可以在自己项目的pom文件中使用properties标签对mysql.version进行版本控制-替代spring-boot-dependencies（自己项目依赖的父项目的父项目）的properties标签对mysql的自动版本仲裁
    <properties>
        <mysql.version>5.1.47</mysql.version>
    </properties>
    就会发现，引入的mysql相关Maven依赖版本变成了mysql.version声明的版本号（dependency也无需配置版本号）
    ```

* ==开发导入starter场景启动器==

  > 1. SpringBoot
  > 2. learn
  > 3. Reference Doc.
  > 4. Using Spring Boot
  > 5. Starters（SpringBoot所有支持的场景）

  * starter是一组依赖的描述符

    * 一般引入一个starter，它的整个开发场景都被引入了

  * 官方starter命名方式：spring-boot-starter-*

    * ```xml
       * 就代表某种场景
      只要引入某个starter，这个场景的所有常规需要的依赖都会自动引入
      
      例如SpringBoot入门中引入的Web场景启动器：
      <!--称之为Web场景启动器-->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      
      
      之后见到的 *-spring-boot-starter-*：第三方提供的简化开发的场景启动器
      ```

  * 所有场景启动器最基本（底层）的依赖就是spring-boot-starter依赖（版本号可变）

    * ```xml
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
        <version>2.3.4.RELEASE</version>
        <scope>compile</scope>
      </dependency>
      ```

* ==无需关注版本号，自动版本仲裁==

  * ```xml
    1、引入依赖默认都可以不写版本
    2、引入非版本仲裁的jar，要写版本号。
    ```

* ==可以修改版本号==

  * 上面的“父项目依赖管理”提到了

    * 查看spring-boot-dependencies（自己项目依赖的父项目依赖的父项目）中properties声明的当前依赖的版本用的key

    * 在当前项目properties中重写即可（就近优先）

      * ```xml
        <properties>
            <mysql.version>5.1.47</mysql.version>
        </properties>
        ```

##### 1.2 自动配置

* 通过SpringBoot入门案例可以体会到SpringBoot自动帮助配置了

  * ==自动配置Tomcat==

    * 引入Tomcat依赖
    * 配置Tomcat

  * ==自动配置SpringMVC==

    * 引入SpringMVC全套组件
    * 自动配置SpringMVC常用组件

  * ==自动配置Web常见功能==

    * 例如：SpringMVC中字符编码过滤器、前端控制器
    * 通过main方法启动主程序，获取IOC容器，获取IOC容器所有的组件名称可以看到上面这些（包括更多）都被SpringBoot进行了配置（配置的web场景启动器）
    * 所以说：SpringBoot帮我们配置好了常见Web开发的所有场景

  * ==默认包结构==

    * 主程序所在的包及其下面所有的子包中的组件都会被默认扫描；无需配置组件扫描

    * 若在主程序所在包及其子包之外创建控制器，且希望被扫描到

      * 主程序类：@SpringBootApplication注解属性定义scanBasePackages

        * ```java
          @SpringBootApplication(scanBasePackages="com.lixc.xxx")
          该注解等同于相面三个注解的功能
          @SpringBootConfiguration
          @EnableAutoConfiguration
          @ComponentScan("com.lixc.xxx")
          ```

  * ==各种配置都拥有默认值==

    * 默认配置最终都是映射到某一个xxxProperties类
      * 例如Tomcat服务器端口号映射到ServerProperties类、文件上传最大值映射到MultiPartProperties类
    * 配置文件的值最终就会绑定在每个映射的XxxProperties类上，且该类作为组件被IOC容器管理（创建对象）
      * 通过打印IOC容器管理的组件名（数组）可以看到对应的XxxProperties类，及IOC管理的bean对象

  * ==按需加载所有自动配置项==

    * SpringBoot有非常多的starter场景

    * 引入了什么场景（starter），这个场景的自动配置才会被加载（当前引入了web场景启动器，就只会自动加载web场景的配置）

    * ```xml
      SpringBoot所有的自动配置功能都在spring-boot-starter场景下的spring-boot-autoconfigure依赖包中：
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-autoconfigure</artifactId>
          <version>2.3.4.RELEASE</version>
          <scope>compile</scope>
      </dependency>
      -----
      可以看到Maven依赖中spring-boot-autoconfigure下的autoconfigure包下的配置类非常多
      ```



#### 2. 容器功能

##### 2.1 组件添加

###### 2.1.1 @Configuration

> @Configuration声明在类上方，告诉SpringBoot这是一个配置类；等同于Spring配置文件，
>
> 配置类本身也是IOC容器的组件；
>
> SpringBoot2新特性：
>
> * 使用@Configuration注解中的proxyBeanMethods默认是true
>   * 表明@Configuration注解标识的配置类中的@Bean修饰的组件方法，无论外部通过配置类调用多少次该组件注册方法，获取的始终是注册在容器中的单实例对象
>   * 因为获取到的配置类（本身也是IOC容器的组件）本身就是通过CGLIB动态代理创建的代理对象
>   * 所以@Configuration( proxyBeanMethods=true )【默认true】代理对象调用组件注册方法，SpringBoot总会检查该组件在容器中是否已被创建---保持组件单实例
>   * 但@Configuration( proxyBeanMethods=false)，配置类就不再是代理对象，而是普通类对象，调用组件注册方法，每调用一次组件注册方法，返回的bean实例就是一个新创建的对象
> * proxyBeanMethods属性；代理bean的方法
>   * Full（@Configuration ( proxyBeanMethods=true )）表示全模式的配置类
>   * Lite（@Configuration ( proxyBeanMethods=false)）表示轻量级模式的配置类
>   * 最佳实战：
>     * 若配置类中组件（@Bean注解标识的方法）之间无依赖关系，使用Lite模式减少SpringBoot对组件的判断，加速容器启动
>     * 若配置类中组件之间存在依赖关系（例如A组件的对象属性包含B组件），只能使用Full模式，每次调用得到的bean对象都是单实例对象（保证A组件获取的对象属性B对象，和B组件获取的对象相同）
###### 2.1.2 @Bean、@Component、@Controller、@Service、@Repository
> @Bean注解声明在@Configuration配置类的方法上方，告诉SpringBoot这是IOC容器管理的bean对象（为IOC容器添加组件）
>
> * @Bean注解声明的方法名就是组件的id（也可以通过@Bean的属性值定义组件id）
> * 返回类型就是组件类型，返回的值就是组件在IOC容器中实例对象

###### 2.1.3 @ComponentScan、@Import

> @Import注解：
>
> * 必须定义在配置类（@Configuration）、组件类（@Component、@Controller/@RestController、@Service、@Repository） 之上
> * @Import ( { Xxx.class, Yyy.class } )
>   * 为容器自动创建出这两个类型的组件；
>   * 默认获取的组件名称为：类的全类名

###### 2.1.4 @Conditional

> 条件装配：满足@Conditional指定的条件，才进行组件注入
>
> * 例如@ConditionalOnBean( name="xxx" )
>   * 定义在配置类或组件注册方法上，表示容器中必须含有bean名称为xxx的组件，才会对该类中其他组件进行装配
> * SpringBoot底层中经常有使用该注解的地方



##### 2.2 原生配置文件引入

###### 2.2.1 @ImportResource

> 例如存在Spring的配置文件，里面有管理的系列组件，又不想在配置类中重新@Bean去注册组件
>
> 此时只需要在配置类上添加该@ImportResource注解，添加资源路径即可导入Spring的配置文件：
>
> * @ImportResource( "classpath:xxx.xml" )；通过该注解将Spring配置文件的数据导入到该配置类中进行组件的注册



##### 2.3 配置绑定

> 如何使用Java读取到properties文件中的内容，并且把它封装到JavaBean中，以供随时使用；
>
> * properties配置文件
>
>   * ```properties
>     mycar.name=BYD
>     mycar.price=39999
>     ```
>
> * JavaBean
>
>   * ```java
>     public class Car{
>         private String name;
>         private Integer price;
>         ...
>     }
>     ```
>
> 最平常Java的方式是读取配置文件，获取属性 对应properties配置文件中的value，然后将属性值注入到对象中
>
> * ```java
>   public static void main(String[] args){
>       Properties prop = new Properties();
>       prop.load(new FileInputStream("mycar.properties"));
>       Car car = new Car (prop.getProperties("mycar.name"),Integer.valueOf(prop.getProperties("mycar.price")));
>   }
>   ```

###### 2.3.1 @EnableConfigurationProperties + @ConfigurationProperties

> 必须在配置类使用该方式
>
> * ```java
>   @Configuration//配置类
>   @EnableConfigurationProperties(Car.class)
>   //1：开启Car的配置绑定
>   //2：把Car组件自动注册到容器中
>   public class MyConfig{
>       
>   }
>   
>   @ConfigurationProperties(prefix="mycar")//确保properties文件使用该前缀的key和JavaBean属性一致
>   public class Car{
>       private String name;
>       private Integer price;
>       ...
>   }
>   
>   //这样我们获取容器管理的Car对象时，就会自动注入properties文件中定义的属性值
>   @RestController
>   public class Xxx{
>       @Autowired
>       private Car car;//此时就已经对该对象的属性进行了自动注入;
>       //若既没有对Car这个类进行添加组件注解加在容器中，也没有在配置类上@EnableConfigurationProperties(Car.class)注解，启动都会异常---无法自动装配Car
>   }
>   ```

###### 2.3.2 @Component + @ConfigurationProperties

> * 使用我们SpringBoot提供的功能，只需在组件上添加@ConfigurationProperties注解定义相关属性值即可
>
>   * ```java
>     @Component//首先要保证该类是IOC容器管理的组件，才能使用SpringBoot提供的功能
>     @ConfigurationProperties(prefix="mycar")//确保properties文件使用该前缀的key和JavaBean属性一致
>     public class Car{
>         private String name;
>         private Integer price;
>         ...
>     }
>     
>     //这样我们获取容器管理的Car对象时，就会自动注入properties文件中定义的属性值
>     @RestController
>     public class Xxx{
>         @Autowired
>         private Car car;//此时就已经对该对象的属性进行了自动注入
>     }
>     ```



#### 3. 自动配置原理入门

##### 3.1 引导加载自动配置类

> @SpringBootApplication = @SpringBootConfiguration + @EnableAutoConfiguration + @ComponentScan
>
> ```java
> @SpringBootConfiguration
> @EnableAutoConfiguration
> @ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
> 		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
> public @interface SpringBootApplication {
> ```

###### 3.1.1 @SpringBootConfiguration

> @Configuration：代表当前类为配置类

###### 3.1.2 @ComponentScan

> 开启组件扫描

###### 3.1.3 @EnableAutoConfiguration

> * @EnableAutoConfiguration = @AutoConfigurationPackage + @Import(AutoConfigurationImportSelector.class)
>
>   * @AutoConfigurationPackage = @Import(AutoConfigurationPackages.Registrar.class)
>
>     * AutoConfigurationPackages.Registrar类
>
>       * ```java
>         @Override
>         public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
>            register(registry, new PackageImports(metadata).getPackageNames().toArray(new String[0]));
>         }
>         
>         debug发现这个方法中调用的register方法中：
>         new PackageImports(metadata).getPackageNames()；获取到的包名为com.lixc.boot；就是定义这个主程序类（主配置类-包含@Configuration所以也叫主配置类）的包
>         ```
>
>     * 所以@AutoConfigurationPackage注解功能：
>
>       * 利用Registrar给容器中导入一系列组件
>       * 将主程序所在包下的所有组件导入---解释了SpringBoot的默认包结构就是主程序类同级或子包组件才会被扫描
>
>   * @Import(AutoConfigurationImportSelector.class)





##### 3.2 按需开启自动配置项



##### 3.3 修改默认配置



##### 3.4 最佳实践

> 使用SpringBoot进行开发最佳实践

* ==引入场景依赖==
  * Spring Boot -> LEARN -> Reference Doc -> Using Spring Boot -> Starters
  * https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters
* ==查看配置了哪些（选做，涉及底层做的配置）==
  * 根据引入的场景，在spring-boot-autoconfigure-2.3.4.RELEASE.jar\org\springframework\boot\autoconfigure目录下针对特定场景进行分析（不是所有的都生效，但引入的starter场景常规的配置一般都生效）
  * 配置文件中配置debug=true，开启自动配置的报告
    * 控制台输出
      * Positive matches（正面匹配）：下面的自动配置类都匹配了
      * Negative matches（否定匹配）:下面的自动配置类都未匹配（@Conditional注解条件装配原因）
* ==是否需要修改==
  * 修改配置项（application.properties）
    * 参照文档：
      * Spring Boot -> LEARN -> Reference Doc -> Application Properties
      * https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties
    * 自己分析：
      * spring-boot-autoconfigure-2.3.4.RELEASE.jar\org\springframework\boot\autoconfigure目录下对应的自动配置类@EnableConfigurationProperties导入的XxxProperties绑定了配置文件的哪些配置项
  * 自定义加入或替换组件
    * @Bean、@Component...
  * 自定义器XxxCustomizer



#### 4. 开发小技巧

##### 4.1 Lombok

> 简化JavaBean开发：@NoArgsConstructor、@AllArgsConstructor、@Data
>
> 简化日志开发：@Slf4j

* ==引入Lombok依赖==---SpringBoot默认对Lombok进行了自动版本仲裁
* ==添加Lombok插件==

* ==使用==

  * JavaBean简化开发

    * ```java
      @NoArgsConstructor//无参构造
      @AllArgsConstructor//全参构造---若想只定义某几个属性而不是全部属性的构造函数，只能自定义
      @Data//默认生成getter、setter、equals、hashCode、toString
      public class LombokDemoPojo {
          private String lombokname;
          private Integer age;
          private String gender;
      }
      ```

  * 简化日志开发：@Slf4j注解，添加在类上方，为类中添加了log属性，之后无需再通过控制台进行输出

    * ```java
      @RestController//等于 @Controller + @ResponseBody
      @Slf4j//
      public class HelloController {
          @Autowired
          private Book book;
      
          @RequestMapping("/book")
          public Book handleOfBook() {
              log.info("请求进来了：" + book);//请求进来了：Book{name='局外人', price=99}
              return book;
          }
      }
      ```



##### 4.2 dev-tools

* SpringBoot官方提供的依赖

  * Spring Boot -> LEARN -> Reference Doc -> Using Spring Boot -> Developer Tools

  * ```xml
    添加该依赖
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>
    ```

* 注意：Ctrl + f9（Build Projects）重新编译

  * 对于静态页面，项目是不会重启的
  * 修改其他的会对项目重新编译---相当于重启（Shift + f10）





##### 4.3 Spring Initializer（项目初始化向导）

> 使用初衷：简化项目的创建和基本文件的创建
>
> * 使用Spring Initializr（Create Spring Boot application using Spring Boot Starters）创建SpringBoot时，选择starter service URL不同，创建的样式也不近相同
>   * Default：https://start.spring.io
>   * Custom：https://start.aliyun.com
> * 使用上面提供的Default创建地址
>   * 勾选SpringBoot的版本；场景启动器相关依赖我们只需要勾选即可
>   * pom文件：
>     * parent标签：自动引入spring-boot-starter-parent标准父工程
>     * dependencies标签：自动引入创建项目模块过程中勾选的场景依赖
>     * build标签：自动引入spring-boot-maven-plugin简化部署的插件
>   * src/main/java目录下
>     * 主程序类会自动创建（模块或工程名 + Application 的驼峰命名规范）
>   * src/main/resources目录下：
>     * SpringBoot配置文件（application.properties）自动创建
>     * static和templates文件夹自动创建，供我们存放静态资源（static：css、js）和页面（template：web页面）





## 核心功能

### 4. 配置文件

#### 1. 文件类型

##### 1.1 properties

SpringBoot配置文件就是application.properties配置文件

##### 1.2 yaml

> YAML Ain't Markup Language---递归缩写；其实就是：YAML Another Markup Language
>
> 非常适合用来做以数据为中心的配置文件（后缀为yaml或yml都可以）
>
> ==基本语法==：
>
> * key: value；注意key: 和 value之间有一个空格
> * 大小写敏感
> * 使用缩进表示层级关系；缩进规范来讲不允许使用tab（IDEA使用不会受影响），只能使用空格
> * 缩进空格数量不重要，只要保证相同层级的元素左对齐即可
> * #表示注释
> * 字符串无需加引号；若要加 ' ' 和 " " 表示字符串内容，会被转义/不转义
>   * 例如单引号和双引号中添加 \n
>     * 单引号在控制台输出的数据就是 \n；所以单引号会被转义
>     * 双引号在控制台输出的数据 就会换行；所以双引号不会被转义
>
> ==数据类型==：
>
> * 字面量：单个不可再分的值
>
>   * ```yaml
>     k: v
>     ```
>
> * 对象：键值对的集合
>
>   * ```yaml
>     k: {k1:v1,k2:v2,...}
>     #或
>     k: 
>     	k1: v1
>     	k2: v2
>     	...
>     ```
>
> * 数组：一组按次序排列的值
>
>   * ```yaml
>     k: [v1,v2,...]
>     #或
>     k: 
>     	- v1
>     	- v2
>     	- ...
>     ```



#### 2. 配置提示

> 使用@ConfigurationProperties注解配置的JavaBean，在SpringBoot配置文件中配置时没有提示
>
> * 官方提供添加spring-boot-configuration-processor==配置处理器==依赖
>
>   * ```xml
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-configuration-processor</artifactId>
>         <optional>true</optional>
>     </dependency>
>     ```
>
> * 而这个配置处理器只是在配置文件为JavaBean添加属性值时更方便，和项目无关，所以在打包时==配置处理器依赖无需打到jar包==
>
>   * ```xml
>         <build>
>             <plugins>
>                 <plugin>
>                     <groupId>org.springframework.boot</groupId>
>                     <artifactId>spring-boot-maven-plugin</artifactId>
>                     <!--SpringBoot打包插件添加这段；Maven->package打包时就不会将引入的配置处理器依赖打包-->
>                     <configuration>
>                         <excludes>
>                             <exclude>
>                                 <groupId>org.springframework.boot</groupId>
>                                 <artifactId>spring-boot-configuration-processor</artifactId>
>                             </exclude>
>                         </excludes>
>                     </configuration>
>                 </plugin>
>             </plugins>
>         </build>
>     ```



### 5. Web开发

> Web开发就是SpringBoot在SpringMVC的基础上做的简化

#### 1. SpringMVC自动配置概览

#### 2. 简单功能分析

##### 2.1 静态资源访问

> * 静态资源存放目录（类路径下---java、resources目录下）：
>
>   * 默认存放目录：
>
>     * static
>     * public
>     * resources
>     * META-INF/resources
>
>   * 自定义静态资源存放目录（一般就是存放在static目录下，该功能不常用）
>
>     * ```yaml
>       #使用自定义的静态资源存放目录，不再使用SpringBoot的静态资源默认存放目录（无法再使用访问）
>       spring:
>           resources:
>             static-locations: [classpath:/myStatic/]
>       ```
>
> * 静态资源访问：
>
>   * 访问静态资源的默认地址-前提是静态资源存放在SpringBoot默认目录下（上面提到了）：
>
>     * 当前项目根路径( http://localhost:8080 ) /静态资源名
>     * 原理：
>       * 静态映射方式：/** （SpringMVC中默认Servlet处理静态资源请求---SpringBoot应该是做了相应封装）
>       * 发起请求后，服务器会优先匹配请求控制器（@Controller），控制器若没有匹配到相关映射就会通过默认Servlet---静态资源处理器处理。若都为匹配---404
>
>   * 添加静态资源访问前缀
>
>     * 默认无前缀
>
>     * ```yaml
>       spring:
>         mvc:
>           static-path-pattern: /static/**
>           
>       #添加静态资源访问前缀后，再次请求静态资源：
>       	当前项目根路径( http://localhost:8080/ ) + static-path-pattern + /静态资源名
>       ```
>
> * 自定义静态资源存放目录无足轻重（一般就是放在static目录下），但是访问静态资源的地址前缀一定要定义（过滤器过滤所有请求时，可以排除静态资源的请求过滤）



##### 2.2 欢迎页支持

> * SpringBoot欢迎页的支持
>   * 方式一：静态资源路径下index.html
>     * index.html在静态资源的默认存放目录下（或自定义配置的静态目录）存放后，访问当前项目即可进入到欢迎页---index.html：访问当前项目根路径即可 
>     * 但不能同时支持为静态资源添加前缀名，否则index.html无法被默认访问（只能通过 .../前缀名/index.html访问）
>   * 方式二：请求控制器中控制器方法能处理/index请求

##### 2.3 自定义Favicon

> 直接将favicon.ico 放置在静态资源存放目录下即可
>
> * 注意：若访问网页图标一直未出现
>   * 浏览器关闭重开
>   * 清除缓存
>   * Maven项目clear、package

##### 2.4 静态资源配置原理

> SpringBoot启动时会默认加载非常多的XxxAutoConfiguration（127个场景）自动配置类；
>
> 也就会加载和Web开发相关的SpringMVC功能的自动配置类WebMvcAutoConfiguration
>
> 生效后为IOC容器中配置了什么：
>
> * ```java
>   @Configuration(proxyBeanMethods = false)
>   @Import(EnableWebMvcConfiguration.class)
>   @EnableConfigurationProperties({ WebMvcProperties.class, ResourceProperties.class })
>   @Order(0)
>   //WebMvcProperties类==对应配置文件spring.mvc前缀
>   //ResourceProperties类==对应配置文件spring.resources前缀
>   //WebMvcAutoConfigurationAdapter是一个@Configuration修饰的配置类，只有一个有参构造
>   public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer { ... }
>   ```

###### 配置类只有一个含参构造

* ```java
  //有参构造器 形参列表中所有的值都会从容器中获取
  //ResourceProperties	类上方将该JavaBean导入到容器，绑定所有spring.resources前缀配置相关的值
  //WebMvcProperties		类上方将该JavaBean导入到容器，绑定所有spring.mvc前缀配置相关的值
  //ListableBeanFactory	BeanFactory对象工厂是IOC容器的底层实现，Spring的对象工厂
  //HttpMessageConverters 找到所有的HttpMessageConverters
  //ResourceHandlerRegistrationCustomizer 找到 资源处理器的自定义器
  //DispatcherServletPath
  //ServletRegistrationBean<?> 给应用注册原生的Servlet、Filter、Listener。。。
  public WebMvcAutoConfigurationAdapter(ResourceProperties resourceProperties, WebMvcProperties mvcProperties,
        ListableBeanFactory beanFactory, ObjectProvider<HttpMessageConverters> messageConvertersProvider,
        ObjectProvider<ResourceHandlerRegistrationCustomizer> resourceHandlerRegistrationCustomizerProvider,
        ObjectProvider<DispatcherServletPath> dispatcherServletPath,
        ObjectProvider<ServletRegistrationBean<?>> servletRegistrations) {
     this.resourceProperties = resourceProperties;
     this.mvcProperties = mvcProperties;
     this.beanFactory = beanFactory;
     this.messageConvertersProvider = messageConvertersProvider;
     this.resourceHandlerRegistrationCustomizer = resourceHandlerRegistrationCustomizerProvider.getIfAvailable();
     this.dispatcherServletPath = dispatcherServletPath;
     this.servletRegistrations = servletRegistrations;
  }
  ```

###### 静态资源处理的默认规则

* 资源处理器添加方法的源码：

  * ```java
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
       //addMappings默认是true，设置为false表示所有静态资源的相关规则不会生效---例如静态资源存放在默认目录下允许直接访问的规则失效
       if (!this.resourceProperties.isAddMappings()) {
          logger.debug("Default resource handling disabled");
          return;
       }
       Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
       CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
       //访问webjars的相关规则
       if (!registry.hasMappingForPattern("/webjars/**")) {
          customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/")
                .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
       }
       //访问静态资源路径的规则---可以通过配置文件修改，之后静态资源只能存放在我们自定义的目录下
       String staticPathPattern = this.mvcProperties.getStaticPathPattern();//spring:mvc:static-path-pattern:默认值为/**，表示静态资源的请求拦截路径；可以自定义为静态资源添加访问前缀-静态资源请求拦截就会发生变化
       if (!registry.hasMappingForPattern(staticPathPattern)) {
          customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
                .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))//spring:resources:static-locations:默认值为{ "classpath:/META-INF/resources/","classpath:/resources/", "classpath:/static/", "classpath:/public/" }，表示静态资源存放路径；可以为静态资源自定义存放目录。
                .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
       }
    }
    ```

* 针对源码的功能测试：

  * ```yml
    spring:
      resources:
        #addMappings默认是true，设置为false表示所有静态资源的相关规则不会生效---例如静态资源存放在默认目录下允许直接访问的规则失效
        add-mappings: false
    ```

###### 欢迎页的处理规则

* 欢迎页处理器映射方法的源码：

  * ```java
    @Bean
    public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
          FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
       WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
             new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
             this.mvcProperties.getStaticPathPattern());
       welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
       welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
       return welcomePageHandlerMapping;
    }
    
    //可以看到调用WelcomePageHandlerMapping构造方法时进行了staticPathPattern的校验，只有静态资源访问路径为/**时才会支持Welcome Page欢迎页的功能；了解了为何为静态资源添加访问前缀后欢迎页功能失效的原因
    if (welcomePage.isPresent() && "/**".equals(staticPathPattern)) {
    			logger.info("Adding welcome page: " + welcomePage.get());
    			setRootViewName("forward:index.html");
    		}
    ```

  * HandlerMapping：处理器映射。保存了每一个Handler处理器（控制器方法）能处理哪些请求

###### favicon.ico

> 至于favicon.ico为何在自定义添加了静态资源的访问前缀后无法访问到：
>
> * 是因为favicon.ico是每次向服务器发送请求时，浏览器向服务器自动发送的 当前项目根路径/favicon.ico 的请求





#### 3. 请求参数处理

##### 3.1 请求映射

> 请求映射就是在请求控制器中编写控制器方法来映射请求地址

###### REST请求使用与原理

> 使用HTTP请求方式的动词来表示对资源（将服务器一切看做资源）的操作
>
> * 普通请求：若是增删改查业务，只能使用请求路径的含义来表示
>
> * REST风格请求：请求路径不变：针对操作不同提供GET查询、POST添加、PUT修改、DELETE删除。
>   * 实现REST风格请求的核心过滤器：HiddenHttpMethodFilter 处理请求方式过滤器
>     * PUT和DELETE操作实现方式：
>       * form表单标签中method属性值为post；
>       * 里面input标签设定：type=hidden隐藏域，name="_method"，value="put或delete"
>   * 之后通过控制器方法请求映射相关注解一一对应即可

* SpringBoot实现REST风格请求

  * 使用页面form表单发送请求（post）

    * 配置文件application.yml中手动开启处理请求方式过滤器

      * ```yml
        spring:
          mvc:
            hiddenmethod:
              filter:
                enabled: true  #开启页面form表单的REST风格功能，客户端工具无需Filter过滤
        ```

    * 页面定义form表单标签用来发起相关请求

      * ```html
        <form action="http://localhost:8080/user" method="post">
            <input type="hidden" name="_method" value="delete">
            <input type="submit">
        </form>
        ```

    * 请求控制器-控制器方法

      * ```java
        @DeleteMapping(value = "/user")
        public String deleteUser() {
            return "DELETE-张三";
        }
        ```

  * 使用客户端工具直接发送REST风格请求（PUT/DELETE）---不用手动开启处理请求方式过滤器

  * 原理：

    * ```java
      //向服务器发送请求会进入该方法
      protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
          HttpServletRequest requestToUse = request;
          //过滤表单发送来的请求---只有表单在发送PUT、DELETE请求时是POST请求方式；如果是客户端工具直接调用DELETE、PUT请求则会直接跳过这层判断
          if ("POST".equals(request.getMethod()) && request.getAttribute("javax.servlet.error.exception") == null) {
              String paramValue = request.getParameter(this.methodParam);
              if (StringUtils.hasLength(paramValue)) {
                  String method = paramValue.toUpperCase(Locale.ENGLISH);
                  if (ALLOWED_METHODS.contains(method)) {
                      requestToUse = new HiddenHttpMethodFilter.HttpMethodRequestWrapper(request, method);
                  }
              }
          }
          //最后会将封装好的请求放行；此时无论是表单提交的POST请求方式的PUT、DELETE请求，还是客户端工具例如Postman发送的请求，过滤器链都会进行放行。是一个Wrapper类型（HttpMethodRequestWrapper）的请求，以后的方法调用getMethod是调用RequestWrapper的。
          filterChain.doFilter((ServletRequest)requestToUse, response);
      }
      ```



###### 请求映射原理

> SpringBoot也是基于SpringMVC处理的请求
>
> SpringMVC中又是基于DispatcherServlet前端控制器来处理请求的：
>
> * ![image-20230612182244622](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230612182244622.png)
>
> * 由上图分析出，所有的请求都是由前端控制器的doDispatch方法来处理的
>
>   * getHandler方法来为当前请求找到具体的Handler处理器（控制器方法）
>
>     * ```java
>       @Nullable
>       protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
>           //HandlerMapping-处理器映射器
>           if (this.handlerMappings != null) {
>               Iterator var2 = this.handlerMappings.iterator();
>       
>               while(var2.hasNext()) {
>                   HandlerMapping mapping = (HandlerMapping)var2.next();
>                   HandlerExecutionChain handler = mapping.getHandler(request);
>                   if (handler != null) {
>                       return handler;
>                   }
>               }
>           }
>       
>           return null;
>       }
>       ```
>
>     * ![image-20230612183245384](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230612183245384.png)
>
>       * RequestMappingHandlerMapping：这个处理器映射器保存了所有的@RequestMapping和Handler的映射规则
>         * 所有的请求映射都保存在了HandlerMapping中
>
> * ==HandlerMapping处理器映射器==
>
>   * 是SpringBoot中一个关键的接口；**作用就是将请求映射到具体的Handler处理器上（例如Controller控制器方法）**
>   * 实现HandlerMapping有多种方式：
>     * SpringBoot使用RequestMappingHandlerMapping默认实现HandlerMapping；使用了一系列的策略模式
>       * 在RequestMappingHandlerMapping初始化时，会扫描所有控制器方法上的@RequestMapping注解，将其解析为一个个RequestMappingInfo对象。
>       * 当有请求到达，RequestMappingHandlerMapping就会根据请求信息匹配对应的RequestMappingInfo进而确定具体的Handler处理器。
>
> * ==SpringBoot请求映射的原理分析==：
>
>   * 客户端发起HTTP请求；
>   * DispatcherServlet前端控制器拦截请求，并交给HandlerMapping处理器映射器；
>   * HandlerMapping通过请求映射到具体的Handler处理器（如Controller控制器方法）；
>   * DispatcherServlet调用HandlerAdapter处理器适配器来执行具体的Handler处理器，并返回ModelAndView对象；
>   * DispatcherServlet将ModelAndView对象视图数据交由ViewResolver视图解析器处理，最终完成页面渲染。





##### 3.2  普通参数与基本注解

> SpringMVC底层处理Web请求，可以接收的传参类型

###### 注解

> @PathVariable
>
> @RequestParam
>
> @RequestHeader
>
> @RequestBody
>
> @CookieValue
>
> @RequestAtribute---获取请求域信息
>
> @MatrixVariable---矩阵变量
>
> * 矩阵变量请求路径的语法：存在矩阵变量的话就在请求路径后添加分号;
>   
> * 矩阵变量绑定在路径变量
>   
> * ```java
>   //例如请求路径为： .../cars/sell;id=1001;type=byd,audi,posiche
>   @GetMapping("/cars/{sell}")
>   public Map carSell(@PathVariable("sell") String sell,
>                      @MatrixVariable("id") Integer id,
>                     @MatrixVariable("type") List<String> type){
>
>   }
>
>   //例如请求路径为： .../work/1;age=20/2;age=30
>   @GetMapping("/work/{bossId}/{empId}")
>   public Map carSell(@MatrixVariable(value="age", pathVar="bossId") Integer bossAge,
>                     @MatrixVariable(value="age", pathVar="empId") Integer empAge){
>
>   }
>   ```
>
> * SpringBoot默认禁用掉了矩阵变量的使用
>
>   * 手动开启
>
>   * ```java
>     //WebMvcAutoConfiguration自动配置类的源码
>     public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer {
>     		@Override
>     		@SuppressWarnings("deprecation")
>     		public void configurePathMatch(PathMatchConfigurer configurer) {
>     			configurer.setUseSuffixPatternMatch(this.mvcProperties.getPathmatch().isUseSuffixPattern());
>     			configurer.setUseRegisteredSuffixPatternMatch(
>     					this.mvcProperties.getPathmatch().isUseRegisteredSuffixPattern());
>     			this.dispatcherServletPath.ifAvailable((dispatcherPath) -> {
>     				String servletUrlMapping = dispatcherPath.getServletUrlMapping();
>     				if (servletUrlMapping.equals("/") && singleDispatcherServlet()) {
>                         //UrlPathHelper类removeSemicolonContent属性默认为true，表示移除分号内容，所以矩阵变量相关功能才会失效
>     					UrlPathHelper urlPathHelper = new UrlPathHelper();
>     					urlPathHelper.setAlwaysUseFullPath(true);
>     					configurer.setUrlPathHelper(urlPathHelper);
>     				}
>     			});
>     		}
>     }
>     ```
>
>
>     //若要使用矩阵变量相关功能，则要手动将removeSemicolonContent属性值修改
>     //方式一：可以考虑自定义配置类（@Configuration）实现WebMvcConfigurer接口（该接口所有方法都使用了default修饰）来重写configurePathMatch方法即可
>     @Configuration
>     public class MyWebConfig implements WebMvcConfigurer{
>     		@Override
>     		public void configurePathMatch(PathMatchConfigurer configurer) {
>     			UrlPathHelper urlPathHelper = new UrlPathHelper();
>                 //false表示不再移除分号内容；矩阵变量功能才能生效
>                 urlPathHelper.setRemoveSemicolonContent(false);
>                 configurer.setUrlPathHelper(urlPathHelper);
>     		}
>     }
>         
>     //方式二：配置类直接向容器注册WebMvcConfigurer组件
>     @Configuration
>     public class MyWebConfig{
>     	@Bean
>         public WebMvcConfigurer webMvcConfigurer(){
>             return new WebMvcConfigurer(){
>                 @Override
>     			public void configurePathMatch(PathMatchConfigurer configurer) {
>     				UrlPathHelper urlPathHelper = new UrlPathHelper();
>                 	//false表示不再移除分号内容；矩阵变量功能才能生效
>                 	urlPathHelper.setRemoveSemicolonContent(false);
>                 	configurer.setUrlPathHelper(urlPathHelper);
>     			}
>             }
>         }
>     }
>     ```

###### Servlet API

> SpringMVC框架提供的重要组件之一HandlerMethodArgumentResolver处理器方法参数解析器，也支持Servlet API参数的解析
>
> * 在处理器方法参数解析器接口的ServletRequestMethodArgumentResolver实现类判断支持解析什么类型的Servlet API
>
>   * ```java
>     public class ServletRequestMethodArgumentResolver implements HandlerMethodArgumentResolver {
>     	@Override
>     	public boolean supportsParameter(MethodParameter parameter) {
>     		Class<?> paramType = parameter.getParameterType();
>     		return (WebRequest.class.isAssignableFrom(paramType) ||
>     				ServletRequest.class.isAssignableFrom(paramType) ||
>     				MultipartRequest.class.isAssignableFrom(paramType) ||
>     				HttpSession.class.isAssignableFrom(paramType) ||
>     				(pushBuilder != null && pushBuilder.isAssignableFrom(paramType)) ||
>     				(Principal.class.isAssignableFrom(paramType) && !parameter.hasParameterAnnotations()) ||
>     				InputStream.class.isAssignableFrom(paramType) ||
>     				Reader.class.isAssignableFrom(paramType) ||
>     				HttpMethod.class == paramType ||
>     				Locale.class == paramType ||
>     				TimeZone.class == paramType ||
>     				ZoneId.class == paramType);
>     	}
>     }
>     ```
>
>   * 





###### 复杂参数

Model、Map...



###### 自定义对象---POJO

> SpringMVC获取请求参数方式之一：使用自定义的POJO对象来进行绑定
>
> * 数据绑定：页面提交的请求数据都可以和对象属性进行绑定
> * SpringMVC数据绑定的核心原理使用DataBinder对象完成，DataBinder对象是SpringMVC框架中的一个工具类，用于将请求参数值转换为目标对象的属性值，并进行校验
>
> HandlerMethodArgumentResolver处理器方法参数解析器的实现类ServletMethodAttributeMethodProcessor









##### 3.3 POJO封装过程

##### 3.4 参数（控制器方法参数）处理原理

> HandlerMethodArgumentResolver处理器方法参数解析器接口含有26个方法参数解析器实现类
>
> * 处理器方法参数解析器HandlerMethodArgumentResolver是SpringMVC框架的重要组件之一，用于将请求参数绑定在控制器方法的参数上，实现类来进行解析---控制器方法参数位置能写什么，由处理器方法参数解析器来决定
>
> * 所以可以说SpringMVC框架对于控制器方法参数的处理非常灵活，可支持多种参数类型的注入（26类），同时也可以自定义类型
>
> 控制器方法执行完成
>
> * 会将模型数据和视图进行封装，保存到ModelAndViewContainer中
> * 总的来说，模型数据和视图是保存在ModelAndView对象中的，处理请求结束后，SpringMVC会将ModelAndView对象传递给DispatcherServlet，DispatcherServlet再将ModelAndView中的模型数据和视图分别传递给ViewResolver视图解析器和View对象进行解析和渲染，最终生成响应内容返回给客户端
> * 控制器方法参数类型Map、Model这些模型数据会被封装为ModelAndView对象，在进行渲染操作时会将ModelAndView对象中Model数据放在请求域中---也就是SpringMVC在进行页面跳转之前将模型数据存放在请求域中的
>
> 自定义参数映射
>
> * 配置类中对WebMvcConfigurer接口（定制化SpringMVC的功能）重写addFormatters方法（类型转换和格式化的自定义）
>
>   * ```JAVA
>     @Configuration(proxyBeanMethods=false)
>     public class WebConfig{
>         @Bean
>         public WebMvcConfigurer webMvcConfigurer(){
>             ...
>             @Override
>             public void addFormatters(FormatterRegistry registry){
>                 registry.addConverter(new Converter<String,Pet【POJO类型-自定义】>(){
>                     @Override
>                     public Pet convert(String source){
>                         Pet pet = new Pet();
>                         pet.setName(source.split(",")[0]);
>                         pet.setAge(source.split(",")[1]);
>                         return pet;
>                     }
>                 });
>             }
>         }
>     }
>     ```
>
>   * 









#### 4. 数据响应和内容协商

> 数据响应：
>
> * 响应页面：
>   * 单体项目的开发---处理请求完成后直接跳转页面
> * 响应数据
>   * 前后分离项目的开发---
>     * 例如我们只做后台的服务，前端发送请求后，后台处理请求后响应JSON数据、XML、xls、图片、音视频、自定义协议数据等等

##### 4.1响应JSON

> SpringMVC中若是对请求进行数据响应->响应JSON数据：
>
> * 首先需要在导入很多依赖的基础上，添加jackson的依赖
> * 在控制器方法（也就是处理器适配器进行handle处理的方法）上方或控制器类上方添加@ResponseBody响应体注解
>
> SpringBoot中若是对请求进行数据响应->响应JSON数据：（底层也是使用到了SpringMVC的框架技术）
>
> * 导入web场景启动器（里面自动引入了json场景，就已经包含了jackson的相关依赖）
> * 在控制器方法（也就是处理器适配器进行handle处理的方法）上方或控制器类上方添加@ResponseBody响应体注解



* 参数解析器SpringBoot封装了26种，HandlerMethodArgumentResolver接口的实现类

* 返回值处理器SpringBoot封装了15种，HandlerMethodReturnValueHandler接口的实现类

  * ```java
    //例如@ResponseBody注解标识的控制器或控制器方法对应的返回值处理器就是RequestResponseBodyMethodProcessor
    public class RequestResponseBodyMethodProcessor extends AbstractMessageConverterMethodProcessor {}
    
    public abstract class AbstractMessageConverterMethodProcessor extends AbstractMessageConverterMethodArgumentResolver implements HandlerMethodReturnValueHandler {}
    
    //通过RequestResponseBodyMethodProcessor类继承来实现了HandlerMethodReturnValueHandler接口，表示该类为返回值处理器
    
    //这个方法就是@ResponseBody注解对应的返回值处理器的处理返回值的方法
    @Override
    	public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType,
    			ModelAndViewContainer mavContainer, NativeWebRequest webRequest)
    			throws IOException, HttpMediaTypeNotAcceptableException, HttpMessageNotWritableException {
    
    		mavContainer.setRequestHandled(true);
    		ServletServerHttpRequest inputMessage = createInputMessage(webRequest);
    		ServletServerHttpResponse outputMessage = createOutputMessage(webRequest);
    
    		// Try even with null return value. ResponseBodyAdvice could get involved.
    		writeWithMessageConverters(returnValue, returnType, inputMessage, outputMessage);
    	}
    ```

* 归根结底：

  * @ResponseBody注解标识的返回值，可以根据返回值处理器（SpringMVC默认封装15种）中的消息转换器（SpringMVC默认封装10个）进行处理

    * ```java
      @RestController
      pubilc class ResponseBodyAnnoController{
          
          @GetMapping("/test/one")
          //此时该控制器方法返回值，就会通过@ResponseBody注解对应的RequestResponseBodyMethodProcessor返回值处理器中的第七种MappingJackson2HttpMessageConverter消息转换器对返回值进行处理---该消息转换器可以为所有类型的返回值进行转换
          public Person testOne(){
              return new Person("...");
          }
          
          @GetMapping("/test/two")
          //此时该控制器方法返回值，就会通过@ResponseBody注解对应的RequestResponseBodyMethodProcessor返回值处理器中的ResourceHttpMessageConverter消息转换器来进行返回值的处理
          public FileSystemResource(MessageConverter支持的多种类型转换的其中之一) testTwo(){
              
          }
      }
      ```





##### 内容协商

> 根据客户端接收能力不同，返回不同媒体类型（MediaType）的数据
>
> 控制器方法相关底层原理
>
> * 参数解析器---HandlerMethodArgumentResolver接口
> * 返回值处理器---HandlerMethodReturnValueHandler接口
>   * 消息转换器HttpMessageConverter接口

###### 内容协商的使用

* 引入XML依赖

  * ```xml
     <dependency>
                <groupId>com.fasterxml.jackson.dataformat</groupId>
                <artifactId>jackson-dataformat-xml</artifactId>
    </dependency>
    ```

* 可以发现，在引入该依赖后，SpringMVC为请求和响应提供了非常方便的**内容协商**功能

  * 根据请求接收数据的不同格式，返回的数据无需我们定义，就会出现不同样式

    * 当Request Header请求头信息中Accept类型为application/xml

      * ```xml
        <Person>
            <name>栗新聪</name>
            <gender>男</gender>
            <age>18</age>
        </Person>
        ```

    * 当Request Header请求头信息中Accept类型为application/json

      * ```json
        {
            "name": "栗新聪",
            "gender": "男",
            "age": 18
        }
        ```

* 浏览器开启参数形式的内容协商功能

  * 因为若是使用内容协商，浏览器请求头中Accept接收类型固定---XML权重较大，无法通过浏览器获取JSON格式数据

  * 配置文件中---application.yml

    * ```yaml
      #是否应使用请求参数（默认为“格式”）来确定请求的媒体类型。
      spring:
        mvc:
          contentnegotiation:
            favor-parameter: true
      ```

  * 浏览器发送请求时，请求路径携带请求参数

    * ?format=json 或 ?format=xml
    * http://localhost:8080/test/returnvalue?format=json



###### 内容协商的原理

###### 自定义HttpMessageConverter消息转换器

> 业务场景：
>
> * 浏览器发送请求--->返回XML数据
> * 使用AJAX发送数据--->返回JSON数据
> * 如果使用APP发送请求，返回自定义协议数据

1. 创建一个实现了HttpMessageConverter消息转换器的实现类

   * ```java
     public class LixcMessageConverter implements HttpMessageConverter<Lixc> {
         @Override
         public boolean canRead(Class<?> clazz, MediaType mediaType) {
             return false;//此处至针对响应做出业务，read操作返回false就行
         }
     
         @Override
         public boolean canWrite(Class<?> clazz, MediaType mediaType) {
             return clazz.isAssignableFrom(Lixc.class);//判断方法返回值类型是自定义的类型
         }
     
         @Override
         //返回当前消息转换器支持的媒体类型---服务器会统计所有的消息转换器支持的媒体内容类型
         public List<MediaType> getSupportedMediaTypes() {
             return MediaType.parseMediaTypes("application/lixc");//此处想让消息转换器支持自定义的内容协商：application/lixc
         }
     
         @Override
         public Lixc read(Class<? extends Lixc> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException {
             return null;//此处至针对响应做出业务，无需对read操作做出逻辑
         }
     
         @Override
         public void write(Lixc lixc, MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException {
             String customOutContent = lixc.getHeight() + "~" + lixc.getWeight() + "~" + lixc.getLooks();
             outputMessage.getBody().write(customOutContent.getBytes());
         }
     }
     ```

2. 配置类中对消息转换器进行扩展extend（不是config配置）

   * ```java
     @Configuration(proxyBeanMethods = false)
     public class WebConfig {
         @Bean
         public WebMvcConfigurer webMvcConfigurer() {
             return new WebMvcConfigurer() {
                 @Override
                 //向服务器扩展自定义的消息转换器
                 public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
                     converters.add(new LixcMessageConverter());
                 }
             };
         }
     }
     ```

3. 定义控制器方法，返回自定义消息转换器接收的返回值类型

   * ```java
     @GetMapping("/test/custom/converter")
     public Lixc testCustomMessageConverter(){
         return new Lixc(174.3, 52.0, "handsome");
     }
     ```

4. 测试

   * Accept定义类型为application/lixc
   * 输出内容：174.3~ 52.0~handsome

5. 扩展：若想通过浏览器根据请求参数来使用我们自定义的消息转换器，

   * ```java
     //方式一：在自定义的消息转换器的基础上 对内容协商的Accept进行添加
     @Configuration(proxyBeanMethods = false)
     public class WebConfig {
         @Bean
         public WebMvcConfigurer webMvcConfigurer() {
             return new WebMvcConfigurer() {
                 @Override
                 //配置内容协商
                 public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
                     //通过对内容协商配置进行媒体类型的配置，允许浏览器可通过该媒体类型发送请求并获取该媒体类型对应的自定义的消息转换器，进行数据响应
                     configurer.mediaType("lixc", MediaType.parseMediaType("application/lixc"));
                 }
             };
         }
     }
     
     
     //方式二：配置文件添加media-types
     spring:
       mvc:
         contentnegotiation:
      		#这个true表示在基于请求头的内容协商策略的情况下，再将基于参数的内容协商策略开启（都存储在内容协商管理器中）
           favor-parameter: true 
           #这个表示添加一种媒体类型-基于参数的内容协商策略
           media-types: {lxxx: application/lixc} 
     ```

6. 效果

   * 此时就针对业务：
     * 浏览器根据参数信息来获取内容协商管理器中的基于参数的内容协商策略，然后获取获取对应的媒体类型，对返回值进行处理
       * ?format=json或?format=xml；这两个是基于参数的内容协商策略默认允许的传参
       * ?format=xxx；首先对这个基于参数的内容协商策略添加媒体类型，然后即可以根据自定义的消息转换器进行消息响应
     * 客户端工具根据请求头信息获取内容协商管理器中的基于请求头的内容协商策略，然后获取对应的媒体类型，对返回值进行处理





#### 5. 视图解析和模板引擎

> SpringBoot默认不支持JSP，需要引入第三方模板引擎技术实现页面渲染
>
> * 因为SpringBoot打包方式jar包，而JSP不支持在压缩包内编译的方式
>
> SpringBoot应用程序中集成的模板引擎对应的场景启动器
>
> * spring-boot-starter-thymeleaf；SpringBoot默认的模板引擎
> * spring-boot-starter-freemarker
> * spring-boot-starter-groovy-templates
> * ...

##### 视图解析

> SpringBoot在处理完请求后完成页面跳转的过程。
>
> 视图处理的方式：转发、重定向、自定义视图
>
> ViewResolver视图解析器
>
> * SpringMVC默认提供的InternalResourceView转发视图和RedirectView重定向视图

* 执行控制器方法完成后，选择符合返回值类型的==返回值处理器==（此处以redirect:/xxx举例）
  * 找到ViewNameMethodReturnValueHandler（是否为字符串类型）
    * 将返回值转为字符串，存储在ModelAndViewContainer（模型数据和视图名）
      * ModelAndViewContainer会判断当前视图名是否为重定向视图的名称（视图名前缀redirect:）
* 最终==将数据封装在ModelAndView中==进行返回（HandlerAdapter处理器适配器调用目标方法获取该返回对象）
  * ~~此时的ModelAndView中保存的数据有：视图名和模型数据~~
    * ~~注意：若方法的入参是一个自定义类型对象，也会将其放在模型数据中进行存储~~
* 任何方法执行完成，底层在封装一个ModelAndView对象（模型数据和逻辑视图名称）返回
  * 若ModelAndView对象不为空，但视图名为空，就会返回一个默认的视图名---请求映射的资源路径
* 最终通过processDispatchResult方法
  * ==将ModelAndView获取到的视图名称解析成对应的View对象，同时将Model模型数据填充到视图的上下文对象，完成页面的渲染和展示的效果==
  * 调用render方法进行页面渲染逻辑
    * resolveViewName方法==解析视图名，来获取View对象==
      * 通过ThymeleafViewResolver视图解析器，判断MAV对象中的视图名称，创建View视图对象
        * 返回值以redirect: 前缀，new RedirectView重定向视图（视图解析器就是解析视图名来获取View对象）
        * 返回值以forward: 前缀，new InternalResourceView转发视图
        * 返回值为普通字符串，ThymeleafView视图
    * View对象调用render方法==进行页面渲染工作==
      * 进入到对应的视图类（View是一个接口，所有的视图对象都有其实现类）中进行页面渲染
        * 重定向视图RedirectView就会==调用原生的response.sedRedirect方法进行请求重定向==
        * 转发视图InternalResourceView就会==调用原生的req.getDispatcher(path).forward(req,resp)方法进行请求转发==
        * 









##### 模板引擎-Thymeleaf

> Thymeleaf模板引擎是SpringBoot默认的模板引擎

###### Thymeleaf的使用

> 1. 引入对应的starter场景启动器
>
> 2. SpringBoot自动配置好了Thymeleaf模板引擎
>
>    * 引入依赖后可以查看到Thymeleaf的自动配置类ThymeleafAutoConfiguration
>      * 所有的配置项都在ThymeleafProperties
>      * 配置好了SpringTemplateEngine模板引擎
>      * 配置好了ThymeleafViewResolver 视图解析器
>
> 3. 我们只需要直接开发页面即可 
>
>    * ThymeleafProperties中已经对Thymeleaf的页面存放的视图前缀、视图后缀做了默认值
>
>      * ```java
>        @ConfigurationProperties(prefix = "spring.thymeleaf")
>        public class ThymeleafProperties {
>        
>           private static final Charset DEFAULT_ENCODING = StandardCharsets.UTF_8;
>        
>           public static final String DEFAULT_PREFIX = "classpath:/templates/";
>        
>           public static final String DEFAULT_SUFFIX = ".html";
>        }
>        ```



##### 构建后台管理系统

* 登录页跳转

  * 访问当前项目根路径，跳转到登录页面

* 登录成功跳转首页---主页面

  * ==重定向---防止表单重复提交==-重复登录验证
    * 防止用户未登录就能访问该页面，在登录页面将用户信息封装到会话域中
  * 用户登录失败即转发到登录页面

* 主页面

  * 在访问主页面时获取会话域对象，==用户是否登录（拦截器，过滤器更方便）存在即跳转，否则跳转登录页面==（label标签输出错误信息）
    * StringUtils.hasLength(xxx);
  * 主页面右上角用户名更新
    * 注意当前用户名没在任何标签内，使用Thymeleaf相应语法
      * 存在标签时在标签属性使用th:text
      * ==Thymeleaf的行内写法 使用双[ ]即可：[[${xxx}]]==

* 若在页面跳转时，要==使用Thymeleaf模板引擎对视图进行解析，需要将对应的html页面添加Thymeleaf的命名空间==

* table相关页面

  * 抽取公共部分

    * Thymeleaf相关语法：

      * 公共部分，取名th:fragment="xxx"（也可以不去定义，直接对标签声明id之后通过th:insert="页面 :: #标签id"获取）

      * 引用部分，th:insert="~{哪个页面 :: xxx}"或者直接th:insert="哪个页面 :: xxx"

      * ```html
        <!--这是公共common页面-->
        ...
        <div th:fragment="common01">
          ...  
        </div>
        ...
        
        <!--这是需要将公共部分引入的页面-->
        ...
        <div th:insert="~{common :: common01}">
            
        </div>
        <div th:insert="common :: common01"></div>
        ...
        ```






#### 6. 拦截器

> HandlerInterceptor接口
>
> * 处理器执行前执行
> * 处理器执行后执行
> * 页面渲染完成后执行

* 创建拦截器，拦截请求-例如登录拦截-判断用户是否登录

  * ```java
        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
            HttpSession session = request.getSession();
            Object loginSuccessMsg = session.getAttribute("loginSuccessMsg");
            if (loginSuccessMsg != null) {
                return true;
            }
    //        session.setAttribute("loginErrorMsg","用户未登录就访问Table系统");
    //        response.sendRedirect("/");
            request.setAttribute("loginErrorMsg", "用户未登录");
            request.getRequestDispatcher("/").forward(request, response);
            return false;
        }
    ```

  * 符合拦截通过条件即放行->return true

  * 否则跳转到某页面-例如登录页面

* 创建配置类，实现WebMvcConfigurer扩展SpringMVC功能

  * 重写addInterceptors方法

    * 添加拦截器到该方法

      * ```java
        @Configuration(proxyBeanMethods = false)
        public class WebConfig implements WebMvcConfigurer {
            @Override
            //向容器注入拦截器组件
            public void addInterceptors(InterceptorRegistry registry) {
                registry.addInterceptor(new LoginInterceptor())
                        .addPathPatterns("/**")//表示该拦截器拦截所有请求
                        .excludePathPatterns("/","/login","/css/**","/js/**","/fonts/**","/images/**");//登录页面的请求不拦截；静态资源的请求不拦截
            }
        }
        ```

* 思考：由于拦截器已经对所有必须的请求进行了登录拦截，所以之前的Controller里面关于用户是否登录的判断可以清除

* 总结：实现拦截器功能

  * 创建类实现HandlerInterceptor拦截器接口，重写相关方法（处理器执行前、后，页面渲染完成后）
  * 拦截器注册到容器中（配置类实现WebMvcConfigurer接口，并进行功能扩展->addInterceptors）
    * 对自定义请求路径进行拦截（若拦截所有请求，静态资源也会被拦截）
  * 拦截器三个方法执行顺序
    * preHandle方法
      * 正序执行所有拦截器的该方法，
        * 返回true执行下一个拦截器的方法
        * 返回false，倒序执行之前拦截器放行（返回true）的afterCompletion方法；目标方法不执行；
    * postHandle方法
      * 倒序执行所有拦截器的该方法
    * afterCompletion方法
      * 处理器执行前后的拦截器方法，执行异常，触发afterCompletion，倒序执行



#### 7. 文件上传

> 表单标签：
>
> * form：method属性值post enctype属性值multipart/form-data
>
> * input：文件上传type属性值为file
>   * 若想实现多文件上传 增加一个属性 multiple
>
> 控制器方法：
>
> * 针对文件上传type="file"表单字段
>   * 使用MultiPartFile承接；多文件字段使用MultiPartFile[ ] 来承接；对应的对象名和文件上传name一致
> * 文件上传
>   * 获取文件名；
>   * 将文件保存；transferTo
>
> * 配置文件调整文件上传最大值：
>   * 单个文件上传最大值
>   * 所有文件上传最大值



> 底层原理：
>
> * SpringBoot默认将文件上传封装了自动配置类MultiPartAutoConfiguration
> * SpringBoot对文件上传的相关属性值封装在了MultiPartProperties类中



#### 8. 异常处理

> SpringBoot默认提供/error处理所有错误的映射
>
> 对于机器客户端，默认生成JSON响应；对于浏览器客户端，默认响应一个whitelabel错误视图，HTML格式呈现数据

![image-20230617104847575](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230617104847575.png)



##### 定制错误处理逻辑 

###### ==定制错误页==

* 可以将常见的错误页面例如404、500等错误页面，存放在==静态资源目录下的error目录内==
* 想要使用模板引擎对响应的页面进行渲染，可以使用SpringBoot默认的==模板引擎Thymeleaf并将error目录及错误页面放置在Thymeleaf模板引擎配置的默认templates目录下==
* ==底层原理：通过DefaultErrorViewResolver默认的异常视图解析器来处理，通过将响应状态码作为错误页的地址，然后由模板引擎进行响应==
* ![image-20230617114833424](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230617114833424.png)



###### ==@ControllerAdvice + @ExceptionHandler==

> ==处理全局异常（推荐使用该方式来定制错误处理的逻辑）==
>
> ==底层原理：调用ExceptionHandlerExceptionResolver异常处理器异常解析器来处理异常请求；专门用来处理@ControllerAdvice类配置了@ExceptionHandler注解的方法==

* 创建exception包，创建异常处理类；

  * 处理整个Web Controller的异常

  * ```java
    @ControllerAdvice
    @Slf4j
    public class GlobalExceptionHandler{
        @ExceptionHandler({NullPointException.class,...})
        public String handleException(Exception ex){
            log.info("异常信息{}",ex);
            return "login";
        }
    }
    ```



###### ==@ResponseStatus + 自定义异常==

> ==处理自定义的异常---自定义类型异常，并设置异常状态码和异常原因==
>
> ==底层原理==：调用ResponseStatusExceptionResolver响应状态异常解析器来处理异常请求；专门用来处理抛出异常类型为@ResponseStatus注解标注的异常类；将@ResponseStatus注解的信息，调用了response.sendError方法直接将异常发送给Tomcat处理，最终也是映射到/error目录下

* 创建专属的异常类（继承运行时异常）

  * ```java
    @ResponseStatus(value=HttpStatus.FORBIDDEN,reason="传输信息过多")
    public class TooManyInfoException extends RuntimeException{
        public TooManyInfoException(){}
        public TooManyInfoException(String message){
            super(message);
        }
    }
    
    
    ---在控制器方法中抛出使用@ResponseStatus注解修饰的自定义的异常类型
        @GetMapping("/xxx")
        public String getInfo(){
        	if(xxxxxx){
                throw new TooManyException();
            }
    	}
    ```



###### Spring底层的异常（例如参数转换异常）

> Spring框架底层的异常；例如参数转换异常、参数不存在异常等
>
> 底层原理：会被DefaultHandlerExceptionReolver默认处理器异常解析器来处理框架底层的异常；之后也是调用response.sendError方法（执行该方法表示此次请求立即结束，异常交由Tomcat进行处理，Tomcat会在转发一个/error请求，若未无法该请求，Tomcat会响应一个原生的错误页；SpringMVC底层是由BasicErrorController处理/error请求）



###### ==自定义异常解析器==

> 对SpringMVC提供的异常解析器不满意，自定义异常解析器;
>
> 自定义的异常解析器默认优先级最低，通过@Order(Ordered.HIGHEST_PRECEDENCE)设置为最高优先级
>
> @Component注解修饰，实现HandlerExceptionResolver异常解析器，重写抽象方法
>
> * ```java
>   @Component
>   @Order(Ordered.HIGHEST_PRECEDENCE)//最高优先级
>   public class CustomExceptionResolver implements HandlerExceptionResolver{//通过实现异常解析器接口来自定义异常解析器
>       @Override
>       public ModelAndView resolveException(HttpServletRequest req,
>                                           HttpServletResponse resp,
>                                           Object obj,
>                                           Exception ex)
>           response.sendError(520,"我喜欢的错误~~~");//由Tomcat处理，最终由BasicErrorController处理将页面跳转到/error/5xx.html页面
>   }
>   ```
>
> 由于自定义异常添加了最高优先级；所以无论是：
>
> * @ControllerAdvice+@ExceptionHandler全局异常还是@ResponseStatus自定义异常；都不会再生效







##### 异常处理自动配置原理

> SpringMVC针对异常处理的自动配置类：ErrorMvcAutoConfiguration
>
> ==ErrorMVCAutoConfiguration异常自动配置类中==
>
> * 类型为==DefaultErrorAttributes==，id为errorAttributes的组件
>
>   * 定义错误页面中包含的数据
>
> * 类型为==BasicErrorController==，id为basicErrorController的组件
>
>   * ```java
>     //该类上方添加的注解：
>     @Controller
>     @RequestMapping("${server.error.path:${error.path:/error}}")
>     //若什么配置都没有，默认与/error的请求做了映射
>     ```
>
>   * 响应
>
>     * 响应页面：在errorHTML控制器方法中，==响应页面==：new ModelAndView("error", model)；
>     * 响应数据：在erroe控制器方法中，==响应JSON数据==：new ResponseEntity<>(body, status);
>
> * 静态内部类，@Configuration( rpoxyBeanMethods=false )修饰的配置类WhitelabelErrorViewConfiguration
>
>   * （配合BasicErrorController组件来响应白页---WhiteLabel）
>   * 类型为View，id为error的组件
>     * 正好对应上了上面BasicController中响应页面时向ModelAndView封装的视图名称
>     * 返回了StaticView视图对象，该视图对象中定义了返回的内容类型是text/html，并添加了了HTML页面的一些默认内容
>       * 响应了默认的错误页（上面提到了浏览器客户端异常响应的WhiteLabel错误视图）
>   * 类型为BeanNameViewResolver，id为beanNameViewResolver的视图解析器组件
>     * 通过BeanName来获取视图解析视图的视图解析器
>
> * 静态内部类，@Configuration( rpoxyBeanMethods=false )修饰的配置类==DefaultErrorViewResolverConfiguration==
>
>   * 类型为DefaultErrorViewResolver，id为conventionErrorViewResolver的默认错误视图解析器
>
>     * 该类静态代码块实例一个Map
>
>       * 客户端的错误，4xx；
>       * 服务端的错误，5xx。
>
>     * resolveErrorView方法调用resolve方法返回一个ModelAndView对象
>
>       * resolve方法中固定写死了错误视图名
>
>         * ```java
>           String errorViewName = "error/" + viewName;//这里的viewName就是静态代码块的value
>           ```
>
>   * 该配置类呼应了：可以将自定义的错误页存放在error目录下，浏览器响应时会将对应的错误页带出：
>
>     * 有精确的错误状态码页面就匹配精确地页面；
>     * 没有精确状态码页面，匹配4xx、5xx这种页面；
>     * 以上页面都未匹配，响应空白页面--->WhiteLabel白页



 MVC九大组件

* Model 模型
* View 视图
* Controller 控制器
* HandlerMapping 处理器映射器
* HandlerAdapter 处理器适配器
* ViewResolver 视图解析器
* Interceptor 拦截器----------------------HandlerInterceptor
* ArgumentResolver 参数解析器-----HandlerMethodArgumentResolver
* ExceptionResolver 异常解析器-----HandlerExceptionResolver





#### 9. Web原生组件注入（Servlet、Filter、Listener）

* 使用原生ServletAPI对Web原生组件进行注入（推荐）
  * 在主程序上添加@ServletComponentScan注解指定包路径
    * @ServletComponentScan( basePackages = "com.lixc.boot" )
  * @WebServlet、@WebFilter、@WebListener注解注册JavaWeb的三大组件；必须配合@ServletComponentScan注解使用
    * 创建继承了HttpServlet类的Servlet类，添加@WebServlet注解设置匹配路径，重写doGet、doPost相关方法
      * @WebServlet( urlPatterns = "/test" )；没有经过设定的Spring拦截器
    * @WebFilter( urlPatterns = {"/css/*"} )；注意一个 * 是Servlet的写法，两个 * 是Spring的写法
      * 实现Filter接口
    * WebListener
      * 可以选择实现较为常用的ServletContextListener接口；监听ServletContext上下文对象的状态（随Web服务创建、销毁）
* 使用RegistrationBean方式对Web原生组件进行注入
  * 细节：
    * @Configuration注解未设置proxyBeanMethods=false--->保证依赖的组件始终唯一，避免创建对象实例出现冗余
  * ![image-20230617234502450](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230617234502450.png)







#### 10. 嵌入式Servlet容器









#### 11. 定制化原理

> 原理分析：
>
> 1. 首先导入场景启动器
> 2. XxxAutoConfiguration自动配置类
> 3. 导入Xxx组件
> 4. 绑定XxxProper。ties类
> 5. 绑定配置文件项

> 定制化常见方式：
>
> * 自定义@Configuration配置类，@Bean组件注册方法来替换、增加容器中的组件（例如异常解析器是向容器进行增加）
> * ==直接修改配置文件项==
> * XxxCustomizer定制化器也可以做定制化操作
> * ==Web应用 通过实现 WebMvcConfigurer 来定制化Web功能（例如添加拦截器、矩阵变量的支持等）==
> * @EnableWebMvc + WebMvcConfigurer 全面接管SpringMVC，所有SpringMVC提供的默认配置、支持全部失效（例如静态资源访问路径、欢迎页。。。）；慎用，所有规则都需要重新配置，实现定制化功能





### 6. 数据访问

#### 1. SQL

##### 1.1 数据源自动配置---HikariDataSource

> ==导入JDBC场景：spring-boot-starter-data-jdbc==
>
> * 该场景又导入了
>   * HikariCP数据源（DataSource）
>   * jdbc
>   * tx事务
> * 发现没有自动导入驱动
>   * 因为不清楚操作的数据库类型，需要手动引入例如MySQL的驱动
>
> ==导入驱动（例如MySQL）：mysql-connector-java==
>
> * 注意SpringBoot对数据库做了自动版本仲裁；需要确认是否和本地数据库版本对应
> * 方式一：导入依赖时直接确定具体版本（Maven就近依赖原则）
> * 方式二：在properties标签重新声明版本（Maven属性的就近优先原则）



> ==分析自动配置：==
>
> * 自动配置的类
>
>   * DataSourceAutoConfiguration：数据源的自动配置类
>
>     * 数据库连接池，是容器中没有DataSource才会自动配置
>     * 底层默认配置的连接池：HikariDataSource
>     * 修改数据源相关的配置：spring.datasource
>
>   * DataSourceTransactionManagerAutoConfiguration：事务管理自动配置类
>
>   * JdbcTemplateAutoConfiguration：JdbcTemplate自动配置类（Spring提供的JdbcTemplate组件方便操作数据库）
>
>     * ```java
>       //通过在配置文件中spring.jdbc来对JdbcTemplate模板配置修改
>       @ConfigurationProperties(prefix = "spring.jdbc")
>       public class JdbcProperties { ... }
>       ```
>
>   * XADataSourceAutoConfiguration：分布式事务相关的自动配置类

> ==修改配置项：==
>
> 若没在SpringBoot配置文件中对数据源配置项进行配置，启动主程序会报错
>
> * ```yaml
>   spring:
>     datasource:
>       driver-class-name: com.mysql.jdbc.Driver
>       url: jdbc:mysql:///boot
>       username: root
>       password: lz001214
>   #    type: com.zaxxer.hikari.HikariDataSource 默认就是HikariDataSource类型的数据源
>   ```

> ==测试数据库操作：==
>
> * ```java
>   @SpringBootTest
>   @Slf4j
>   class Boot06WebSevletComponentApplicationTests {
>       //@Autowired 使用这个注解编译会报红，不是代码问题；但是影响观感
>       @Resource
>       JdbcTemplate jdbcTemplate;
>   
>       @Test
>       void contextLoads() {
>           Long aLong = jdbcTemplate.queryForObject("SELECT COUNT(*) FROM `boot_01`;", Long.class);
>           log.info("boot_01表数据总量：{}", aLong);
>       }
>   }
>   ```





##### 1.2 使用Druid数据源

> SpringBoot整合Druid第三方技术：
>
> * 方式一：自定义
> * 方式二：寻找引入starter场景
>
> 德鲁伊官方Github地址：https://github.com/alibaba/druid

###### ==自定义整合Druid数据源：==

> 1. Maven引入Druid相关依赖
>
>    * ```xml
>      <dependency>
>          <groupId>com.alibaba</groupId>
>          <artifactId>druid</artifactId>
>          <version>1.1.17</version>
>      </dependency>
>      ```
>
> 2. 向容器注入DruidDataSource数据源组件
>
>    * ```java
>      @Bean
>      @ConfigurationProperties(prefix = "spring.datasource")//配置属性--->绑定配置文件；表示从配置文件中获取配置项，此方式正好和Druid数据源所需设置信息名称一致，可完美匹配；
>      public DataSource dataSource() {
>          /*原始方式配置数据源连接信息
>          druidDataSource.setDriverClassName("");
>          druidDataSource.setUrl("");
>          druidDataSource.setUsername("");
>          druidDataSource.setPassword("");*/
>          System.out.println("使用的是德鲁伊数据源");
>          return new DruidDataSource();
>      }
>      ```
>
>    * 为何配置了DruidDataSource之后SpringBoot中HikariDataSource数据源不会生效
>
>      * 在DataSourceAutoConfiguration自动配置类对数据库连接池组件进行装载前会判断
>        * @ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
>      * 而此时已经自定义了Druid数据源，所以不会对其它数据源导入容器
>
> 3. 完成测试
>
>    * ```java
>      @SpringBootTest
>      @Slf4j
>      class Boot06WebSevletComponentApplicationTests {
>          //@Autowired 使用这个注解编译会报红，不是代码问题；但是影响观感
>          @Resource
>          JdbcTemplate jdbcTemplate;
>          //@Autowired
>          @Resource
>          private DruidDataSource dataSource;
>      
>          @Test
>          void contextLoads() {
>              log.info("数据源类型：{}",dataSource.getClass());//class com.alibaba.druid.pool.DruidDataSource
>              Long aLong = jdbcTemplate.queryForObject("SELECT COUNT(*) FROM `boot_01`;", Long.class);
>              log.info("boot_01表数据总量：{}", aLong);
>          }
>      }
>      ```

###### ==打开Druid监控统计功能==

> 官方提供的方式是在Spring配置文件中对DruidDataSource组件添加filters属性，值为stat
>
> SpringBoot中可以在@Bean注册了DruidDataSource数据源中为filters属性赋值stat
>
> * ```java
>   @Bean
>   @ConfigurationProperties(prefix = "spring.datasource")//配置属性--->表示从配置文件中获取配置项，此方式正好和Druid数据源所需设置信息名称一致，可完美匹配
>   public DataSource dataSource() throws SQLException {
>       /*原始方式配置数据源连接信息
>       druidDataSource.setDriverClassName("");
>       druidDataSource.setUrl("");
>       druidDataSource.setUsername("");
>       druidDataSource.setPassword("");*/
>       System.out.println("使用的是德鲁伊数据源");
>       DruidDataSource druidDataSource = new DruidDataSource();
>       druidDataSource.setFilters("stat");//打开Druid的监控统计功能
>       return druidDataSource;
>   }
>   ```

###### ==使用Druid监控统计功能-StatViewServlet==

> 官方提供的方式是在web.xml中配置StatViewServlet
>
> SpringBoot中配置的方式就是创建一个Servlet组件，该组件类型为StatViewServlet即可
>
> * ```java
>   /**
>    * 使用Druid的内置监控页面
>    *
>    * @return
>    */
>   @Bean
>   public ServletRegistrationBean<StatViewServlet> statViewServlet() {
>       return new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");
>   }
>   ```

###### 测试Duid内置的监控统计页面

> * 创建控制器方法，使用Druid数据库连接池
>
>   * ```java
>     @Resource
>     JdbcTemplate jdbcTemplate;
>     
>     /**
>      * 测试Druid的监控统计功能（StatFilters、StatViewServlet）
>      *
>      * @return
>      */
>     @ResponseBody
>     @GetMapping("/test/statviewservlet")
>     public Long testStatViewServlet() {
>         return jdbcTemplate.queryForObject("SELECT COUNT(*) FROM `boot_01`;", Long.class);
>     }
>     ```
>
> * 访问/druid，可以看到Druid内置的监控统计页面
>
> * 访问控制器方法，可以看到监控统计页面对使用到DruidDataSource的访问进行了实时监控



###### ==使用官方starter方式==

> 将上面自定义的相关信息注释，标注过时注解：@Deprecated
>
> 导入德鲁伊Maven依赖：
>
> * ```xml
>   <dependency>
>       <groupId>com.alibaba</groupId>
>       <artifactId>druid-spring-boot-starter</artifactId>
>       <version>1.1.17</version>
>   </dependency>
>   ```
>
> 查看Druid自动配置类：
>
> * Durid绑定的相关配置项：
>   * DataSourceProperties：spring.datasource
>   * DruidStatProperties：spring.datasource.druid





##### 1.3 整合MyBatis操作

> MyBatis官方Github地址：https://github.com/mybatis
>
> 导入MyBatis场景：
>
> * ```xml
>       <dependency>
>           <groupId>org.mybatis.spring.boot</groupId>
>           <artifactId>mybatis-spring-boot-starter</artifactId>
>           <version>2.1.4</version>
>       </dependency>
>   ```

###### 配置模式

> 在之前学过的MyBatis框架中：
>
> * 创建全局配置文件->SqlSessionFactory->SqlSesison->Mapper
>
> 创建POJO类、Service业务类、Mapper接口( 必须添加==@Mapper注解== )、MyBatis全局配置文件( mybatis-config.xml )、Mapper映射文件( XxxMapper.xml )
>
> SpringBoot配置文件添加MyBatis相关配置：
>
> * ```yaml
>   #SpringBoot整合MyBatis
>   mybatis:
>     #config-location: classpath:mybatis/mybatis-config.xml
>     mapper-locations: classpath:mybatis/mapper/*
>     #可以不写全局配置文件路径；使用configuration代替即可（因为要配置驼峰规则）；注意两者不能同时存在
>     configuration: #指定MyBatis全局配置文件中的配置项即可
>   ```
>
> ==总结：==
>
> 1. 导入MyBatis官方starter场景
> 2. 创建@Mapper注解标识的Mapper接口
> 3. 创建Mapper.xml映射文件
> 4. 在SpringBoot配置文件下添加MyBatis配置项：
>    * Mapper映射文件路径
>    * 全局配置文件信息（不再使用config-location属性；使用configuration属性替代【配置驼峰】）



###### 注解模式

> 不再定义Mapper映射文件，而是在@Mapper注解标识的Mapper接口具体方法定义SQL操作的关键字注解，在注解中写SQL即可
>
> ```java
> @Mapper
> public interface XxxMapper{
>     @select("SELECT * FROM `xxx` WHERE `id`=#{id}")
>     Xxx queryXxxById(@Param("id") Integer id);
> }
> ```



###### 混合模式

> 见名知义；
>
> 既使用配置模式（需要Mapper映射文件）又使用注解模式（无需Mapper映射文件，但需在方法上方添加SQL操作相应注解）：
>
> * ```java
>   @Mapper
>   public interface XxxMapper{
>       @select("SELECT * FROM `xxx` WHERE `id`=#{id}")
>       Xxx queryXxxById(@Param("id") Integer id);
>       
>       int addXxx(Xxx xxx);
>   }
>   
>   <insert id="addXxx" useGeneratedKeys="true" keyProperty="id">//使用这两个属性是在插入成功后返回id
>       INSERT INTO `xxx` VALUES (NULL,#{a},...);
>   </insert>
>   ```



###### ==最佳实战==

1. 引入MyBatis的starter场景
2. 配置SpringBoot配置文件application.yml，指定mapper-locations位置、指定configuration属性下的全局配置属性
3. 创建@Mapper注解标识的Mapper接口
   * 若不想再每一个Mapper接口上方标识@Mapper注解，可以在主程序类上方添加@MapperScan注解注解中指定包扫描位置（不推荐）
4. 简单Mapper方法使用注解方式；复杂方法使用Mapper映射文件方式





##### 1.4 整合MyBatis-Plus 完成CRUD

> MyBatis-Plus是MyBatis的增强工具，在MyBatis基础上只增强不做改变
>
> MyBatis-Plus官网：https://baomidou.com/
>
> 建议IDEA先安装MyBatisX插件

###### 整合MyBatis-Plus

> ==导入MyBatis-Plus的场景依赖==：
>
> * ```xml
>   <dependency>
>       <groupId>com.baomidou</groupId>
>       <artifactId>mybatis-plus-boot-starter</artifactId>
>       <version>3.5.3</version>
>   </dependency>
>   ```
>
> * 查看Maven依赖发现：MyBatis-Plus场景已经引入了MyBatis场景（两个场景都引入了jdbc场景）
>
> ==分析自动配置==
>
> * MyBatisPlusAutoConfiguration 自动配置类；
>
>   * MyBatisPlusProperties配置项绑定：前缀为mybatis-plus
>
>     * 该类默认配置了Mapper映射文件的位置：
>
>       * ```java
>         private String[] mapperLocations = new String[]{"classpath*:/mapper/**/*.xml"};
>         ```
>
>       * 建议之后使用到的Mapper映射文件都放置在类路径下的mapper包下面
>
>   * @Mapper注解标识的接口会被自动扫描；建议直接在主程序类上方定义@MapperScan注解定义Mapper接口扫描包路径
>
> ==创建POJO实体类：==
>
> * 可以使用@TableField( exist = false )表示该属性在表中不存在（添加这个注解的原因是可以在POJO类添加数据库表不存在的字段）
> * 若实体类名和表名不一致，可以使用@TableName( "数据库表名" )显示指定即可
>
> ==创建Mapper接口：==
>
> * 继承BaseMapper类，添加操作的实体类（数据库表）的泛型
>   * 可以看到MyBatis-Plus提供的BaseMapper类提供了各种CRUD功能，无需编写Mapper映射文件
>
> ==创建Service接口及实现类：==
>
> * Service接口继承IService顶级Service类，定义实体类泛型
> * Service实现类不仅需要实现Service接口，还要继承ServiceImpl实现类，同时声明Mapper接口泛型和实体类泛型







#### 2. NoSQL





### 7. 单元测试

> SpringBoot2.2.0开始 引入的Junit5作为单元测试默认库（未移除Junit4的依赖）
>
> * 默认引入
>
>   * ```xml
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-test</artifactId>
>         <scope>test</scope>
>     </dependency>
>     ```
>
> * 默认自动生成
>
>   * ```java
>     @SpringBootTest
>     class Boot08MpApplicationTests {
>         @Test
>         void contextLoads() {
>         }
>     }
>     ```
>
> SpringBoot2.4版本及以上版本移除了默认对Vintage的依赖，如果需要兼容Junit4需要自行引入（@Test不能引入Junit4的依赖）
>
> Junit5依赖：import org.junit.jupiter.api.Test;
>
> SpringBoot整合Junit：
>
> * 使用Spring Initializr来创建SpringBoot的Maven项目，默认会生成测试类（2.2版本之后默认使用Junit5），@Test标识在方法上方即可进行方法测试
> * Junit类具有Spring的功能
>   * 可以直接对该类进行属性的注入（@Autowired等等）---只有添加@SpringBootTest注解的类才生效
>   * 可以在方法上方添加@Transactional注解，标识方法被事务管理



#### JUnit5常用注解





#### 断言-assertions

> 断言是测试方法的核心部分，检查业务逻辑返回的数据是否合理。
>
> 断言方法都是Assertions的静态方法
>
> 在Maven一栏双击test会运行该项目中所有单元测试，结束后会出具一份详细的测试报告
>
> 多个断言（期望值，实际值）存在，靠前的断言失败，后面的断言不再执行

##### 1. 简单断言



##### 2. 数组断言

assertArrayEquals

##### 3. 组合断言

```java
@DisplayName("组合断言")
@Test
public void testZHAssertions(){
    assertAll("组合断言名",
             ()->assertTrue(true&&true,"结果不为true"),
             ()->assertEquals(10,10,"结果不为预期"));
}
```



##### 4. 异常断言

```java
@DisplayName("异常断言")
@Test
public void testAssertThrows(){
    //断定业务一定出现异常
    assertThrows(ArithmeticException.class,()->{int i = 10/0;},"异常断言失败，业务正常运行？？？")
}
```



##### 5. 超时断言

##### 6. 快速失败

fail



#### 前置条件-assumptions

> 类似断言，但单元测试不满足的断言会使得测试方法失效，而不满足的前置条件只会使得测试方法执行中止。
>
> 执行单元测试类，前置条件不满足的测试方法会被skip跳过（@Disable注解标识的方法表示该方法禁用，也会跳过）

Assumptions.assumeTrue()。。。



#### 嵌套测试

> Nested Test嵌套测试：使用@Nested注解标识测试类的内部类为嵌套测试类
>
> 外层的单元测试方法，不能驱动内层的测试方法
>
> 内层的单元测试方法，可以驱动外层的测试方法



#### 参数化测试

> JUnit5非常重要的特性
>
> @ParameterizedTest注解标识在测试方法上，表示该方法是参数化测试
>
> * ```java
>   @ParameterizedTest
>   @ValueSource(ints = {1,2,3,10,12,14})
>   public void testParameterized(int i){
>       System.out.println(i);
>   }
>   //会调用6次参数化测试方法，输出1,2,3,10,12,14
>   //==================
>   
>   public static Stream<String> streamParam(){
>       return Stream.of("lixc","vv","vc");
>   }
>   
>   @ParameterizedTest
>   @MethodSource("streamParam")
>   public void testParameterized2(String str){
>       System.out.println(str);
>   }
>   //会调用三次参数化测试方法，输出lixc，vv，cc
>   ```







### 8. 指标监控

> SpringBoot Actuator：未来每一个微服务都需要对其进行监控、追踪、审计、控制等；SpringBoot就抽取了Actuator场景
>
> 引入场景依赖：spring-boot-starter-actuator
>
> 有两种方式查看端点信息：
>
> * jmx方式：黑窗口jconsole
> * http方式：访问http://localhost:8080/actuator/xxx

* 暴露所有端点（endpoint）信息

  * ```yaml
    management:
    	endpoints:
    		enabled-by-default: true #暴露所有端点信息---默认就是true
    		web:
    			exposure:
    				include: '*' #以web方式暴露
    ```

* 最常用的端点

  * health：健康状况
  * metrics：运行时指标
  * loggers：日志记录

#### Health Endpoint

* 健康检查端点；返回的结果是一系列健康检查后的汇总报告

* 展示详细健康状况

  * ```yaml
    management:
    	endpoints:
    		enabled-by-default: true #暴露所有端点信息---默认就是true
    		web:
    			exposure:
    				include: '*' #以web方式暴露
    
    	# management.endpoint.端点名.xxx 对某个端点的具体配置
    	endpoint:
    		health:
    			show-details: always #默认never永不显示详细信息；更改为总是展示
    ```

#### Metrics Endpoint

* 指标端点；提供详细的、层级的、空间指标信息；这些信息可以被push或pull方式得到
* 例如访问http://localhost:8080/metrics后会返回列举出很多的监控指标，若需要对某一个具体的指标监控信息查看，需要加访问请求后缀
  * http://localhost:8080/metrics/jvm.gc.pause

#### 管理端点

* 开启与禁用Endpoints

  * ```yaml
    将默认暴露所有端点的属性设为false：management.endpoints.enabled-by-default: false
    暴露某些特定端点：management.endpoint.xxx.enabled=true
    
    management:
    	endpoints:
    		enabled-by-default: false #暴露所有端点信息---默认就是true
    		web:
    			exposure:
    				include: '*' #以web方式暴露
    
    	# management.endpoint.端点名.xxx 对某个端点的具体配置
    	endpoint:
    		health:
    			show-details: always #默认never永不显示详细信息；更改为总是展示
    			enabled: true
    		beans:
    			enabled: true
    		metrics:
    			enabled: true
    		info:
    			enabled: true
    ```



#### 定制Endpoints

> ==自定义health信息---定制Health Endpoint==
>
> * 创建组件类（@Component）；创建的类后缀名一定是HealthIndicaptor
>   * 实现HealthIndicaptor；或集成AbstractHealthIndicaptor
>   * 重写doHealthCheck---真实的检查方法
>     * 自定义健康检查条件
>       * 健康则返回builder.up()；//builder是重写方法的参数
>         * 或者可以看源码其实调用了builder.status(Status.UP)；返回健康状态
>       * 不健康则返回builder.down()；
>         * 或者可以看源码其实调用了builder.status(Status.XXX)；返回不健康状态
>     * 可以定义健康或不健康相关信息
>       * 将定义的详细信息通过builder.withDetail方法进行封装返回
> * 访问/actuator/health即可查看到自定义的健康状况，名称为定制类名去除HealthIndicaptor后的名称

> ==定制info信息==
>
> * 访问/actuator/info默认什么信息也没有
>
> * 方式一：可直接通过SpringBoot的配置文件定制info信息
>
>   * ```yaml
>     info:
>     	自定义Key: 自定义Value
>     	appName: boot_actuator
>     	appVersion: 1.0.0.0.0.0.0
>     	#以上内容固定写死的，若值为变化的例如想要引用pom文件的标签版本号：使用双 @@ 进行获取
>     	appVersionByMaven: @project.version@
>     ```
>
> * 方式二：创建组件类（@Component）实现InfoContributor接口，重写contribute方法；
>
>   * builder.withDetail(xxx,xxx).withDetails(Collection.singletonMap(yyy,yyy));
>   * 使用该方式来定制info信息，是因为可能定制的info信息需要调用某些对象、某些类某些方法来实现

> ==定制metrics信息==
>
> * 通过MeterRegistry来定制指标信息
>
>   * 例如想要记录请求访问了多少次某方法
>
>     * ```java
>       @Service
>       public class XxxServiceImpl implements XxxService{
>           Counter counter;
>           public XxxServiceImpl(MeterRegistry meterRegistry){
>               counter = meterRegistry.counter("自定义XxxService指标信息");
>           }
>           
>           public String xxx(){
>               counter.increment();//累计访问多少次
>           }
>       }
>       ```
>
>     * 



### 9. 原理解析

#### Profile功能

> 获取SpringBoot配置文件中的值，可以使用@Value( "${配置文件属性key:前面属性值未拿到拿这里的默认值}" ) 来获取
>
> SpringBoot配置文件中可以指定当前环境下使用的配置文件：
>
> * spring.profiles.active = xxx（配置文件的后缀；例如application-test.yml;后缀就是test）
>
> 注意：默认配置文件永远会被加载，在默认配置文件中指定激活使用的配置文件



#### 外部化配置

> ==外部配置源：==
>
> * 常见：
>   * Java属性文件（proerties）
>   * YAML文件（yaml）
>   * 环境变量：配置环境变量可以通过@Vlaue( "${xxx}" )来获取，也可以通过主程序类获取IOC容器后获取相关环境变量
>   * 命令行参数：
>     * 例如在使用cmd黑窗口来运行SpringBoot打包的jar文件时，可以通过-- xxx=yyy 的方式来配置参数
>
> ==配置文件（properties、yaml）查找位置==--由高顺序到低顺序，属性名一样的情况，低顺序的配置文件会覆盖前面的：
>
> * classpath根路径：类路径的根路径（java包下、resources包下）
> * classpath根路径下config目录
> * jar包当前目录
> * jar包当前目录的config目录下（新建config文件夹，在里面添加配置文件）
> * jar包当前目录的config目录下的直接子目录下（linux系统才生效）
>
> ==配置文件加载顺序==---低顺序的配置文件会覆盖高顺序的相同属性：
>
> * 当前jar包内部的application.properties或yaml
> * 当前jar包内部的application-{profile}.properties或application-{profile}.yaml
> * 引用外部jar包的properties或yaml
> * 引用外部jar包的application-{profile}.properties或application-{profile}.yaml
>
> ==总结==：
>
> * 外部化配置中，低顺序的配置会覆盖高顺序（先执行查找或加载）的同名配置项
> * 指定环境优先、外部优先



#### 自定义starter

> starter启动原理：
>
> 1. 编写starter，无需编写任何代码，只声明当前场景引入哪些依赖
>    * 引入当前场景自动配置包autoconfigure
>      * 引入底层spring-boot-starter
>
> 自定义starter：
>
> 1. 创建一个自定义的xxx-spring-boot-starter-autoconfigure的SpringBoot项目  相当于自动配置包
>
>    * 自定义Bean：添加@ConfigurationProperties( prefix="xxx" )注解
>      * 添加属性，添加get/set方法
>
>    * 自定义一个功能，例如Service（不能导入到容器，也就是不在类上方添加@Component及衍生注解）
>      * 
>
> 2. 创建一个自定义的xxx-spring-boot-starter的Maven项目 相当于场景启动器
>
>    * 引入自定义的自动配置包---pom文件直接添加依赖



#### SpringBoot原理

> SpringBoot不是一个单纯的框架，它的底层整合了Spring、SpringMVC以及Spring整个系列技术栈的其它技术，甚至一些第三方框架；
>
> * Spring原理
> * SpringMVC原理
> * 自动配置原理
> * SpringBoot原理

##### SpringBoot启动过程

1. 创建SpringApplication
   1. 保存很多信息
   2. 判断当前Web应用的类型：Reactive还是Servlet（用到了ClassUtils工具类）
   3. bootstrappers：初始启动引导器
2. 运行SpringApplication
   1. StopWatch：记录应用启动时间
   2. 















## 末尾

