# Spring Security

> Java领域中常用的安全框架；重量级的安全管理框架（Shiro是一个轻量级的安全管理框架）
>
> Spring Security基于Spring框架提供了一套全面而强大的身份认证和授权机制；
>
> 前置知识：
>
> * JavaWeb
> * Spring
> * SpringBoot
>
> 功能：
>
> * 身份验证、访问控制、单点登录、跨站请求伪造防护、会话管理等；支持多种身份认证和授权机制，可轻松地与各种认证服务器和第三方身份验证服务进行集成
>
> 使用方式：
>
> * 使用配置驱动的方式来定义安全规则，通过配置文件或注解来声明身份验证和授权规则
>
> Spring Security和Shiro安全管理框架说明：
>
> * 主要职责就是身份认证和授权机制
>
> * 使用方便程度
>   * Shiro入门简单上手容易；
>   * 最开始的Spring Security在没有SpringBoot（敏捷开发框架）推出前是基于过滤器来进行的构建，大部分还需要通过XML实现，配置非常复杂
> * 社区支持
>   * Spring Security更受支持
> * 功能丰富性
>   * 相同点：
>     * 认证功能、授权功能、加密功能、会话管理、缓存支持、rememberMe功能等等
>   * 不同点：
>     * Spring Security对Oauth、OpenId也有支持，Shiro需要手动实现；且Spring Security的权限细粒度更高（RBAC、ACL）
>       * 该支持就很致命了，微服务的火爆使得Oauth使用广泛
>     * Spring Security必须依托在Spring的IOC容器基础上运行，依赖性高；Shiro不需要任何框架和容器，可独立运行
>     * Shiro不仅可在Web中使用，非Web项目也支持应用
> * 总结：
>   * Shiro相较于Spring Security更容易使用
>   * Shiro满足常用功能，且简单灵活，Apache推出的项目可靠，且无需绑定任何的框架和容器，可独立运行
>   * Spring Security社区支持度更高，且和Spring系列的框架整合容易
>   * Spring Security功能更加强大，使用更为复杂些，但由于SB和SC这些完整的生态Web环境健全，通常都会选用Spring相关框架进行开发，此时spring-security-starter场景启动器优势就体现出来

## 框架简介

> Spring是应用最广泛的Java企业级应用开发框架，SpringSecurity基于Spring框架，提供了一套Web应用开发安全完整解决方案。
>
> 安全方面两个重要区域：
>
> * 认证---身份认证
> * 授权---授权机制-访问控制
>
> SpringSecurity两大核心功能：
>
> * 用户认证：Authentication
>   * 通俗说就是系统判断用户是否能登陆
> * 用户授权：Authorization
>   * 通俗说就是系统判断是否有权限做某些事情

> SpringSecurity特定：
>
> * 重量级安全管理框架
> * 和Spring框架无缝整合
> * 全面的权限控制
> * 专为Web开发而生
>   * 旧版本无法脱离Web环境使用
>   * 新版本对整个框架进行了分层抽取，分为核心模块和Web模块。单独引入核心模块即可脱离Web环境
>
> Shiro特点：
>
> * 轻量级安全管理框架
> * 通用性：
>   * 不局限Web环境开发使用
>   * 在Web环境下某些特定需求需要手动编写代码定制

> 常见的安全管理技术栈的组合：
>
> * SSM + Shiro
> * SpringBoot / SpringCloud + SpringSecurity





## 入门案例

> 创建项目：
>
> * 使用Spring Initializr创建SpringBoot项目，引入Web和Security场景；
> * 创建请求控制器及控制器方法，启动主程序进行访问；
> * 访问控制器方法设定的路径映射，会发现进行了页面重定向（响应时间会久些），此时需要填写用户名和密码
>   * 用户名默认user，密码会在主程序启动时在控制台进行输出



> SpringSecurity基本原理：
>
> * SpringSecurity本质是一个过滤器链（有很多过滤器）
> * 重点查看三个SpringSecurity底层的过滤器
>   * FilterSecurityInterceptor：方法级的权限过滤器，基本位于过滤连的最底部
>   * ExceptionTranslationFilter：异常过滤器，用来处理认证授权过程中抛出的异常
>   * UsernamePasswordAuthenticationFilter：对/login的POST请求做拦截，校验表单提交的用户名、密码



> 过滤器如何加载：
>
> * 使用SpringSecurity配置过滤器
>   * DelegatingFilterProxy（由SpringBoot自动配置了，若不使用SpringBoot需要配置该过滤器）
>     * 调用initDelegate方法，里面调用了FilterChainProxy
>       * 调用doFilterInternal方法，里面得到所有的过滤器，加载到过滤链中。（SecurityFilterChain）





> * SS中的两个重要的接口：
>   * UserDetailsService接口：查询数据库用户名和密码的过程
>     * 定义过滤器，继承UsernamePasswordAuthenticationFilter过滤器
>       * 重写attemptAuthentication方法（自定义用户名密码校验逻辑），自定义用户认证的业务逻辑
>       * 重写successfulAuthentication和unSuccessfulAuthentication方法，来自定义用户认证成功或失败后的处理逻辑
>     * 而用户名和密码一般查询数据库，此时需要使用到SpringSecurity中的UserDetailsService接口来查询
>     * 创建类实现UserDetailsService接口，编写查询数据库的过程，返回User对象（Security安全框架中提供的对象）
>   * PasswordEncoder接口：数据加密接口（官方推荐使用BCryptPasswordEncoder）
>     * Security提供的对数据（密码）进行加密的接口；
>     * 一般配合UserDetailsService接口中返回User对象中的password密码，进行加密操作







## Web权限方案

> Web权限方案主要包含认证和授权两个功能。

### 认证

> 设置登录的用户名和密码（三种方式）
>
> 1. 通过配置文件配置（不实用）
>
>    * ```yaml
>      #spring:
>      #  security:
>      #    user:
>      #      name: lixc
>      #      password: lz001214
>      ```
>
> 2. 通过配置类（不实用）
>
>    * ```java
>      @Configuration
>      public class SecurityConfig extends WebSecurityConfigurerAdapter{//Security的配置类，扩展功能
>          //向容器注入PasswordEncoder组件（对数据进行加密），否则下面重写Security配置类的方法对密码进行加密会报空
>          @Bean
>          public PasswordEncoder passwordEncoder(){
>              return new BCryptPasswordEncoder();
>          }
>      
>          @Override
>          protected void configure(AuthenticationManagerBuilder auth) throws Exception {
>              BCryptPasswordEncoder passwordEncoder=new BCryptPasswordEncoder();
>              String encode = passwordEncoder.encode("001214");//对密码进行加密
>              auth.inMemoryAuthentication()//可以看出这是“内存中的用户认证”，适用于小型应用程序或开发和测试阶段，不适合大型应用程序或需要动态管理用户情况
>                  .withUser("vv")//设置用户认证中的用户名
>                  .password(encode)//认证中的密码信息
>                  .roles("admin");
>          }
>      }
>      ```
>
> 3. 自定义编写实现类
>
>    * ```java
>      @Service
>      public class LoginAuthenticationService implements UserDetailsService {
>          @Override
>          public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
>              //直接查询数据库即可
>              if (!"admin" .equals(username)) {
>                  throw new UsernameNotFoundException("用户名不存在哟~");
>              }
>              String encode = new BCryptPasswordEncoder().encode("001214");
>              return new User(username, encode, AuthorityUtils.commaSeparatedStringToAuthorityList("admin,"));
>          }
>      }
>      
>      //注意必须要向容器注入PasswordEncoder组件，才能实现对数据的加密
>      @Configuration
>      public class SecurityConfig extends WebSecurityConfigurerAdapter {
>          @Bean
>          public PasswordEncoder passwordEncoder(){
>              return new BCryptPasswordEncoder();
>          }
>      ```

#### 实现数据库认证->用户登录

1. pom引入Maven依赖：Web场景、Security场景、MySQL依赖、MP第三方场景、Lombok依赖
2. 创建数据库和数据库表（BIGINT）
3. SB2配置文件配置数据库数据源信息
4. 创建JavaBean实体类（Long）
5. 创建Mapper接口（BaseMapper）；注意@MapperScan注解
6. 创建实现了UserDetailsService接口（SpringSecurity重要的查询数据库用户及密码的接口）；
   1. 注入自定义Mapper属性
   2. 重写loginUserByUsername方法
   3. 查询数据库并对信息添加相关业务操作---身份认证



#### 自定义登录页面（无需认证即可访问）

> 1. 在Security专属的配置类（实现WebSecurityConfigurerAdapter）实现相关配置
>
>    * ```java
>      //重写configre(HttpSecurity http)方法
>      	//自定义登录页面
>      	http.formLogin()
>              .loginPage("/login.html")//登录页面设置
>              .loginProcessingUrl("/login")//登录访问路径---请求
>              .defaultSuccessUrl("").permitAll()//登录成功后跳转路径
>              .and().authorizeRequests()
>              .antMatchers("/","/login").permitAll()//设置路径不需要进行认证，可直接访问
>              .anyRequest().authenticated()
>              .and.csrf().disabled();//关闭csrf防护
>      ```
>
> 2. 创建页面及控制器





### 授权

#### 基于角色或权限进行访问控制

##### hasAuthority单权限的访问控制

> 判断当前主体是否具有指定权限，返回true或false；一般只针对某一个权限操作，多个权限无法完成访问控制操作
>
> 实现步骤：
>
> * 配置类中设置当前访问地址具有哪个权限
>
>   * ```java
>     //表示当前访问路径，只有权限为admins时才可访问
>     .antMathcers("xxx/xxx/...").hasAuthority("admins")
>     
>     //不支持多权限访问控制，下面的编码错误！
>     .antMathcers("xxx/xxx/...").hasAuthority("admins,manager")
>     ```
>
> * 在UserDetailsService接口实现类重写的loginUserByUsername方法返回User对象中，设置用户权限
>
>   * ```java
>     return new User("username...",
>                     new BCryptPasswordEncoder().encode("password"),
>                     AuthorityUtils.comxxxToAutorityList("admins"));
>     ```
>
> 访问测试：
>
> * 若用户认证 成功，但权限不符合设定的访问地址的权限：页面返回403---Forbidden，没有访问权限。



##### hasAnyAuthority多权限的访问控制

> 判断当前主体具有设定的多个权限中任意一个 (以逗号进行分隔的字符串列表) ，返回true；支持多个权限的访问控制操作
>
> 实现步骤：
>
> * 配置类中设置当前访问地址具有哪些权限
>
>   * ```java
>     //表示当前访问路径，主体的权限为设定的多个权限admins,manager中的某一个即可访问
>     .antMathcers("xxx/xxx/...").hasAnyAuthority("admins,manager")
>     ```
>
> * 在UserDetailsService接口实现类重写的loginUserByUsername方法返回User对象中，设置用户权限
>
>   * ```java
>     return new User("username...",
>                     new BCryptPasswordEncoder().encode("password"),
>                     AuthorityUtils.comxxxToAutorityList("admins"));
>     ```
>
> 访问测试：
>
> * 若用户认证 成功，但权限不符合设定的访问地址的权限：页面返回403---Forbidden，没有访问权限。



##### hasRole单角色的访问控制

> 判断当前主体是否具有指定角色，返回true或false；一般只针对某一个角色操作，多个角色无法完成访问控制操作
>
> 实现步骤：
>
> * 配置类中设置当前访问地址具有哪个角色
>
>   * ```java
>     //表示当前访问路径，只有角色为admins时才可访问
>     .antMathcers("xxx/xxx/...").hasRole("sale")
>     
>     //不支持多角色访问控制，下面的编码错误！
>     .antMathcers("xxx/xxx/...").hasRole("sale,devloper")
>     ```
>
>   * hasRole源码中会为角色添加前缀ROLE_ ，所以在配置类设置的角色，在UserDetailsService接口实现类返回User对象的角色要添加 ROLE_
>
> * 在UserDetailsService接口实现类重写的loginUserByUsername方法返回User对象中，设置用户的角色
>
>   * ```java
>     //注意要为用户设定符合某个角色时，要添加ROLE_ 前缀
>     
>     return new User("username...",
>                     new BCryptPasswordEncoder().encode("password"),
>                     AuthorityUtils.comxxxToAutorityList("ROLE_sale"));
>     ```
>
> 访问测试：
>
> * 若用户认证 成功，但角色不符合设定的访问地址的角色：页面返回403---Forbidden，没有访问角色。



##### hasAnyRole多角色的访问控制

> 判断当前主体具有设定的多个角色中任意一个 (以逗号进行分隔的字符串列表) ，返回true；支持多个角色的访问控制操作
>
> 实现步骤：
>
> * 配置类中设置当前访问地址具有哪些角色
>
>   * ```java
>     //表示当前访问路径，主体的角色为设定的多个角色sale,devloper中的某一个即可访问
>     .antMathcers("xxx/xxx/...").hasAnyRole("sale,devloper")
>     ```
>
>   * hasAnyRole源码中会为角色添加前缀ROLE_ ，所以在配置类设置的角色，在UserDetailsService接口实现类返回User对象的角色要添加 ROLE_
>
> * 在UserDetailsService接口实现类重写的loginUserByUsername方法返回User对象中，设置用户的角色
>
>   * ```java
>     //注意要为用户设定符合某个角色时，要添加ROLE_ 前缀
>     
>     return new User("username...",
>                     new BCryptPasswordEncoder().encode("password"),
>                     AuthorityUtils.comxxxToAutorityList("ROLE_sale"));
>     ```
>
> 访问测试：
>
> * 若用户认证 成功，但角色不符合设定的访问地址的角色：页面返回403---Forbidden，没有访问角色。



#### 自定义403-无访问权限页面

> 配置类配置
>
> * ```java
>   //配合无权限访问时自动跳转的页面（默认返回403白页），页面自定义即可
>   http.exceptionHandling.accessDeniedPage("/xxx.html");
>   ```
>
> 当然也要想到SpringBoot提供的默认错误页：/error目录下设定即可进行自动跳转



#### 注解使用

> 一般需要
>
> * @EnableGlobalMethodSecurity注解（主程序类、配置类）；
> * UserDetailsService接口实现类重写loginUserByUsername方法返回User对象对用户授权
> * Controller控制器方法前条件权限判断相关注解

##### @Secured

> 判断是否具有指定角色，此处定义匹配的角色需添加 ROLE_ 前缀
>
> * 注解使用前需开启全局方法安全注解@EnableGlobalMethodSecurity( securedEnabled = true )；设定在主程序类或配置类
>
> * 控制器方法上添加@Scured注解
>
>   * ```java
>     @Secured({"ROLE_admin","ROLOE_xxx",...})
>     ```
>
> * 确保在UserDetailsService接口实现类User返回对象的中添加对应的角色
>
> * 用户认证完成后，直接访问该控制器方法即可进行用户的权限判断相关操作

##### @PreAuthorize

> 进入控制器方法前的权限验证工作
>
> * 注解使用前需开启全局方法安全注解@EnableGlobalMethodSecurity( prePostEnabled= true )；设定在主程序类
>
> * 控制器方法上添加@PreAuthorize注解
>
>   * ```java
>     @PreAuthorize( "hasRole('ROLE_xxx')" )
>     ```
>
> * 用户认证完成后，直接访问该控制器方法即可进行用户的权限判断相关操作

##### @PostAuthorize（少用）

> 控制器方法执行后的权限验证工作；
>
> * 注解使用前需开启全局方法安全注解@EnableGlobalMethodSecurity( prePostEnabled= true )；设定在主程序类
>
> * 控制器方法上添加@PostAuthorize注解
>
>   * ```java
>     @PostAuthorize( "hasRole('ROLE_xxx')" )
>     ```

##### @PreFilter（少用）

> 进入控制器方法前对数据进行过滤
>
> filterObject

##### @PostFilter（少用）

> 权限验证后对数据进行过滤
>
> filterObject





### 用户注销

> 配置类中添加退出的配置（用户注销）
>
> * ```java
>   http.logout()
>       .logoutUrl("/logout")
>       .logoutSuccessfulUrl("/index")//该地址需要映射
>       .permitAll;
>   ```
>
> 详细步骤：
>
> 1. 配置类配置登录登出相关配置
>
>    * ```java
>      @Bean
>      public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
>          httpSecurity.logout().logoutUrl("/logout")//这个表示登出时访问地址，无需创建控制器方法
>                  .logoutSuccessUrl("/web/logout").permitAll();//登出成功自动访问该请求资源（控制器或页面）
>          httpSecurity.formLogin()
>                  .loginPage("/login.html")//设定登录页面
>                  .loginProcessingUrl("/authtication")//设定登录时请求地址，无需创建控制器方法
>                  .defaultSuccessUrl("/web/login").permitAll()//登录成功自动访问该请求资源（控制器或页面）
>          ......
>      }
>      ```
>
> 2. 登录相关配置
>
>    * .loginPage("/login.html")
>
>      * ```html
>        <body>
>        <form action="/authtication" method="post">
>            用户名：<input type="text" name="username"/><br/>
>            密  码：<input type="password" name="password"/><br/>
>            <input type="submit" name="登录认证"/><br/>
>        </form>
>        </body>
>        ```
>
>    * .logoutSuccessUrl("/web/logout")
>
>      * ```java
>        @GetMapping("/logout")
>        public String logoutSuccessfulHandle() {
>            return "forward:/logoutsuccessful.html";
>        }
>        ```
>
>    * return "forward:/logoutsuccessful.html";
>
>      * ```html
>        <body>
>        <h1>用户登录成功</h1>
>        <a href="/logout">登出</a>
>        </body>
>        ```
>
> 3. 登出相关配置
>
>    * .logoutSuccessUrl("/web/logout")
>
>      * ```java
>        @GetMapping("/login")
>        public String loginSuccessfulHandle() {
>            return "forward:/loginsuccessful.html";
>        }
>        ```
>
>    * return "forward:/loginsuccessful.html";
>
>      * ```html
>        <body>
>        <h1>用户登出成功</h1>
>        </body>
>        ```







### 自动登录

> Cookie实现自动登录，但Cookie是一个客户端技术，数据需要存储在浏览器，那像密码这种涉密信息肯定不建议使用Cookie技术。
>
> Spring Security实现自动登录

#### 原理分析

1. SpringSecurity实现自动登录会在认证成功后：
   * 向浏览器存储Cookie加密串信息（设置有效时长）
   * 向数据库存储Cookie加密串信息、用户信息
2. 关闭浏览器后再次访问
   * 获取Cookie信息，查询数据库；
     * 若查询到用户信息，表示认证成功，自动登录
     * 若未查到用户信息，需重新进行用户登录认证



#### 功能实现

1. 创建数据库表

2. 配置类

   * 属性注入：数据源

   * 向容器导入JdbcTokenRepositoryImpl实现的接口PersistentTokenRepository组件（操作数据库对象）

     * ```java
       @Autowired
       private DataSource dataSource;
       @Bean
       public PersistentTokenRepository persistentTokenRepository(){
           JdbcTokenRepositoryImpl jdbc = new JdbcTokenRepositoryImpl();
           jdbc.setDataSource(dataSource);
           jdbc.setCreateTableOnStartUp(true);//自动创建表
           return ;
       }
       ```

   * 在SecurityFilterChain组件中添加rememberMe自动登录

     * ```java
       @Bean
       public SecurityFilterChain se(HttpSecurity http){
           http.xxxx
               .xxxxx
               .and().rememberMe().tokenRepository( persistentTokenRepository() )
               .tokenValiditySeconds(60*60)//设置有效时长
               .userDetailsService(userDetailsService)
               
               
               .and().csrf().disable();
       }
       ```

3. 页面添加自动登录rememberMe复选框

   * name固定为remember-me

4. 之后访问时选中后登录完成用户认证后，浏览器会保留remember-me的Cookie信息





### CSRF功能

> CSRF：跨站请求伪造







## 微服务权限方案













## 原理总结















## 末尾





















