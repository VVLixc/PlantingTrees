# SpringMVC

## SpringMVC简介

* MVC

  * MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分
  * Model模型层
    * 指的工程中的JavaBean，作用是处理数据
      * 实体类Bean：专门存储数据
      * 业务类Bean：专门用于处理业务逻辑和数据访问
  * View视图层
    * 指工程中的HTML或JSP等页面，作用是与用户进行交互，展示数据
  * Controller控制层
    * 指工程中的Servlet，作用是接收请求和响应数据
  * MVC工作流程

* SpringMVC

  * SpringMVC是Spring为表述层开发提供的一套完备的解决方案
    * 表述层框架历经Strust、WebWork、Strust2等诸多产品迭代后，目前普遍选择使用SpringMVC用作JavaEE项目表述层开发首选方案

  > 三层架构：三层架构：表述层（表示层）、业务逻辑层（业务层）、数据访问层（持久层）；表述层表示前台页面和后台Servlet

  * 

![image-20230603112247482](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230603112247482.png)



## 入门

### 开发环境

* IDE：IDEA 2020.3
* Tomcat服务器：apache-tomcat-8.0.50
* Maven构建工具：apache-maven-3.6.3
* Spring版本：5.3.1

### 创建Maven工程或模块

* pom文件中设置打包方式为==war==
* 在Project Structure下为该工程（或模块）手动==定义web.xml的创建路径==；及Web工程的资源路径
  * .../工程名/==src/main/webapp/==WEB-INF/web.xml
  * 资源路径IDEA自动定义了

* ==配置web.xml==

  * 配置SpringMVC的==前端控制器DispatcherServlet==

    * 注意web.xml中servlet-mapping标签下

      * servlet-name有一个默认值default；是Tomcat提供的一个默认的Servlet，处理静态资源
      * url-pattern设置所有请求都去定位到该前端控制器：/*或/
        * /*：匹配任意请求
        * ==/==：能匹配浏览器向服务器发送的所有请求，但不能包括.jsp的请求
          * Tomcat服务器的web.xml配置文件：对.jsp结尾的请求做了配置，JspServlet；
          * 我们此时使用/来为前端控制器映射；也就是.jsp结尾的请求还是使用Tomcat提供的JspServlet处理
          * 由于使用的Thymeleaf技术，Thymeleaf中都是以.html的文件作为视图的，不会使用到jsp，所以/和/*都没影响
        * 注意点：后缀匹配*.do-表示只要请求地址以.do结尾符合条件；注意不要有/

    * 如何==调整前端控制器读取SpringMVC配置文件的路径==

      * ```xml
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:自己定义的配置文件.xml</param-value>
        </init-param>
        ```

    * 设置前端控制器初始化场景

      * Servlet程序生命周期中的初始化操作：浏览器第一次请求访问时进行初始化

      * 由于SpringMVC的前端控制器初始化操作繁琐，所以将该控制器初始化场景调整为，服务启动时进行初始化

      * ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
                 version="4.0">
            <servlet>
                <servlet-name>SpringMVC</servlet-name>
                <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
                <init-param>
                    <param-name>contextConfigLocation</param-name>
                    <param-value>classpath:springmvc.xml</param-value>
                </init-param>
                <!--将DispatcherServlet前端控制器初始化时间提前到服务器启动时执行-->
                <load-on-startup>1</load-on-startup>
            </servlet>
            <servlet-mapping>
                <servlet-name>SpringMVC</servlet-name>
                <url-pattern>/</url-pattern>
            </servlet-mapping>
        </web-app>
        ```

      * 

### 创建请求控制器（创建控制层）

* 无需继承、实现任何类、接口；SpringMVC中封装了原生Servlet，无需再次创建，只需要在控制层添加注解
* @Controller注解；将该控制层类标示为控制层组件（依然由IOC容器管理）；此时SpringMVC就能够识别控制器的存在
  * 请求控制器中每一个方法称为控制器方法



### 创建SpringMVC的配置文件

* SpringMVC的配置文件是在DispatcherServlet前端控制器（配置在web.xml文件-随着服务启动被加载）初始化时就加载的
  * SpringMVC配置文件默认的位置和名称（自定义的话肯定不会将SpringMVC配置文件放在WEB-INF，放在resources目录下）：
    * 位置：WEB-INF下
    * 名称："前端控制器<servlet-name>定义的值"+"-"+"servlet.xml"
* DisPatcherServlet定义的servlet-name的值-servlet.xml    Spring配置文件中
  * 开启组件扫描：扫描controller层组件扫描
  * 配置Thymeleaf视图解析器
    * 配置后可以使用SpringMVC提供的方式实现视图渲染和页面跳转
    * 物理视图和逻辑视图
      * 物理视图是访问的真实地址
      * 逻辑视图是经过Thymeleaf视图解析器配置的视图前缀和视图后缀解析后的地址
      * 物理视图=视图前缀 + 逻辑视图 + 视图后缀

### 启动Tomcat服务

* 请求控制器中要创建处理的方法
  * @RequestMapping注解处理请求和请求控制器方法之间的映射关系
  * value属性值匹配浏览器发送请求的地址："/"表示http://localhost:8080/工程路径(当前工程上下文路径)
  * 若要返回逻辑视图（Thymeleaf视图解析器中定义的），直接return "逻辑视图名"；即可
    * 返回的逻辑视图会被Thymeleaf视图解析器解析（通过视图前缀和视图后缀拼接为物理视图，匹配到具体的资源）；通过Thymeleaf的渲染跳转到指定资源页面
* 页面资源HTML
  * 设定a标签（注意th:  开头的语法是Thymeleaf的语法）
    * 不能乱用；要在html标签中 存在xmlns:th=...的约束（命名空间）
    * 可以直接添加到HTML File模板中（xmlns:th="http://www.thymeleaf.org"）；之后创建HTML文件自带该约束
    * th:href=@{/资源名}；/被Thymeleaf解析为工程路径（上下文路径） （使用原生href=/...；跳转的是8080/）



### 总结

> 浏览器向服务器发送请求，服务器会判断请求地址是否和web.xml文件中配置的前端控制器的url-pattern匹配；
>
> 如果匹配成功前端控制器就会去读取SpringMVC的配置文件（前端控制器有默认值<servlet-name>值-servlet.xml）；
>
> SpringMVC配置文件中开启的组件扫描，确定了我们设定的请求控制器，并通过请求地址确认到控制器方法；
>
> 控制器方法中通过return "逻辑视图"；由视图解析器Thymeleaf进行解析，做出视图渲染，并转发到具体资源下。
>
> 
>
> 浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器
>
> DispatcherServlet处理。前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器，
>
> 将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的
>
> 控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会
>
> 被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视
>
> 图所对应页面（Thymeleaf是服务器中的视图渲染的技术）





## @RequestMapping注解

> 见名知义：该注解就是将浏览器发送来的请求和 处理请求的控制器方法 关联起来，建立映射关系；
>
> SpringMVC接收到指定的请求，就会找映射关系中对应的控制器方法来处理请求。
>
> 该注解可以根据：请求路径、请求方式、请求参数、请求头信息等方式来匹配请求

### 注解标识的位置

* 注解标识在类上：表示请求映射的初始信息

* 标识在方法上：表示请求映射的具体信息

* 实用：类和方法表示注解后：定位到方法需要/类注解的value/方法注解的value

* ```java
  @Controller
  @RequestMapping("/user")
  public class UserController{
      
      //访问该控制器方法的地址：.../user/delete
      @RequestMapping("/delete")
      public String delete{
          ...;
      }
  }
  ```

### 注解的属性

* ==value属性（常用）==

  * String[] 数组类型，可以处理多个请求（只需满足一个条件即可）

  * ```java
    @Controller
    public class UserController{
        //表示以下两个访问路径都可以被映射到该控制器方法，来处理
        @RequestMapping({"/delete","drop"})
        public String delete{
            ...;
        }
    }
    ```

* ==method属性（常用）==

  * RequestMethod数组类型===枚举（只需满足一个条件即可）
  * 若浏览器发送请求的请求路径和@RequestMapping注解value属性匹配，但是method属性不匹配
    * 405异常
  * 常用请求方式：GET、POST、PUT、DELETE
  * 在@RequestMapping注解基础上，结合不同的请求方式的派生注解
    * @GetMapping——只能匹配GET请求方式
    * @PostMapping——只能匹配POST请求方式
    * @DeleteMapping——只能匹配DELETE请求方式
    * @PutMapping——只能匹配PUT请求方式
    * 使用这些@RequestMapping的派生注解，无需再定义请求方式的属性

* params属性

  * 通过请求的请求参数匹配控制器方法（必须满足设置的所有条件）

  * 四种params属性值表达式：

    * "param"

    * "!param"

    * "param=value"

    * "param!=value"

    * ```java
      //请求方式为GET，请求参数key必须有username，且对应的value必须为lixc
      @GetMapping(params = {"username","username=lixc"})
      public String paramsProp(){
          return "params";
      }
      ```

  * 细节：

    * html资源中a超链接标签使用Thymeleaf视图解析器提供的属性 

      * ```xml
        <!--使用？方式虽然使用没问题但是IDEA会报红：优化为()包裹-->
        <a th:href="@{/use?username=admin}">123</a>
        <a th:href="@{/use(username='admin')}">123</a>
        ```

* headers属性（了解）

  * 通过请求的请求头信息匹配控制器方法（必须满足设置的所有条件）
  * 四种headers属性值表达式：
    * "header"
    * "!header"
    * "header=value"
    * "header!=value"

### SpringMVC支持ant风格的路径

* 支持ant风格的路径（即可以通过某个、某些特殊的符号表示特殊的路径含义）
  * ?：表示任意的单个字符
  * *：表示0-多个字符
  * **：表示任意层数的任意目录
    * 使用方式：只能 /**/，/中两个 *前后不能有任意其他字符；会被解析为两个 表示0-多个的字符 
  * 路径尽量不要使用大写字母---可以考虑使用路径分隔符 / 

### ==SpringMVC支持路径中的占位符==

> 路径及请求参数原始方式：/deleteUser?id=1
>
> 路径及请求参数rest方式：/user/delete/1   相当于将参数作为了路径的一部分
>
> ```java
> //对应注解中value属性值为：
> @RequestMapping(value="/user/delete/{id}")
> public String testRest(@PathVariable("id") Integer id){
>     return "ccc";//逻辑视图 由Thymeleaf视图解析器 进行视图渲染 请求转发
> }
> 
> 
> //访问路径：/user/delete?username=admin&id=1 ---> /user/delete/admin/1
> @RequestMapping(value="/user/delete/{username}/{id}")
> public String testRest(@PathVariable("username") String username,@PathVariable("id") Integer id){
>     return "ccc";//逻辑视图 由Thymeleaf视图解析器 进行视图渲染 请求转发
> }
> ```
>
> 





## SpringMVC获取请求参数

### **通过ServletAPI获取请求参数（少用）**

* SpringMVC基于原生的Servlet

* 形参列表添加HttpServletRequest，用来接收请求发送来的相关信息

* ```java
  @GetMapping("/servlet")
  public String getParamByServlet(HttpServletRequest httpServletRequest) {
      String username = httpServletRequest.getParameter("username");
      String password = httpServletRequest.getParameter("password");
      System.out.println("username：" +username+ "\t\t\tpassword：" + password);
      return "servlet";
  }
  ```

### **==通过控制器方法的形参获取请求参数（常用）==**

* 只需要在控制器方法的形参列表中，设置参数 对应 请求发送的请求参数的key的值 即可；

* 若是同一个请求参数名对应多个value；可以使用Stirng[]数组方式或String字符串方式承接即可

  * 承接来的数据分别就是数组类型，和每个值使用逗号分隔的字符串类型

* ```java
  @PostMapping("/paramlist")
  public String getParamByParamList(String username, String password, String[] hobbies) {
      System.out.println("username：" + username + "\t\t\tpassword：" + password + "\t\t\thobbies：" + Arrays.toString(hobbies));
      return "paramlist";
  }
  ```



#### 通过控制器方法的形参获取请求参数---但请求参数和控制器方法形参列表参数不一致时

1. 使用@RequestParam注解
   * 作用：将请求参数 和 控制器方法中配置了该注解的形参进行绑定
   * 注解中的**value属性**，用来将配置了该请求参数注解的形参名 和 注解value属性值（对应请求参数名）进行对应；
   * @RequestParam注解中**required属性默认为true**，表示若浏览器发送请求时未传@RequestParam注解定义的参数名信息，出现异常400；所以若该注解配置时未对required属性赋false，则发送请求时**必须**要传对应参数！
   * 注解中**defaultValue属性**，用来设置当@RequestParam注解定义的value属性值未在请求中获取到时，使用defaultValue属性配置的值；若设置了defaultValue属性，那么required属性的设定就无关紧要了
2. 使用@RequestHeader注解---形参列表只能映射请求参数，所以想要获取请求头信息，就必须要使用该注解
   * 作用：将请求头信息 和 控制器方法中配置了该注解的形参进行绑定
3. 使用@CookieValue注解---形参列表只能映射请求参数，所以想要获取Cookie数据，就必须要使用该注解
   * 作用：将Cookie数据 和 控制器方法中配置了该注解的形参进行绑定



#### 通过控制器方法的形参获取请求参数---将请求参数映射为POJO实体类对象（常用）

* 只要满足请求参数名和实体类对象属性对应即可；直接在控制器方法形参列表定义该实体类对象即可获取请求参数



### 解决获取请求参数乱码问题

* Tomcat7版本的服务器conf目录下server.xml中Connector标签中
  * URIEncoding="UTF-8"
  * 设置了该属性 GET请求就会处理乱码问题
* Tomcat8版本的服务器
  * GET请求没有乱码
  * POST请求会有乱码



* web.xml配置文件中配置Spring的编码过滤器（有默认编码，还需要手动设置UTF-8）

  * CharacterEncodingFilter

  * 过滤所有资源路径---/*

  * ```xml
    <init-param>
        <!--自定义编码
    		配置该属性后，编码效果只作用在请求编码
    	-->
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <!--强制编码-true：
    		配置该属性后，编码效果既作用在请求编码，又作用在响应编码
    	-->
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
    ```

  * SpringMVC中处理编码的过滤器一定要配置在其它过滤器之前，否则无效



## 向域对象共享数据

* 因为使用到了Thymeleaf技术，不再使用JSP，所以只有三个域对象
  * HttpServletRequest类型的域对象
  * HttpSession类型的域对象
  * ServletContext类型的域对象

### 向request域对象共享数据

#### ServletAPI（少用）

* 在控制器方法形参列表中设定HttpServletRequest对象即可，通过request . setAttribute("key","value")方式存储

#### ModelAndView（官方推荐使用）

* ```java
  @RequsetMapping("/xxx")
  public ModelAndView getRequestScopeDataByMAV(){
      ModelAndView mav = new ModelAndView();
      //向请求域共享数据
      mav.addObject("key1","value11111");
      //设置视图名称（逻辑视图），实现页面跳转
      mav.setView("xxx");
      return mav;
  }
  
  
  //以下是Thymeleaf视图解析器定位到的页面
  //使用Thymeleaf视图渲染技术 获取请求域数据
  <p th:text="${key1}"></p>
  ```

* 处理请求的DispatcherServlet控制器底层代码中，无论何种方式存储/共享请求域数据，还有视图，最后都会封装为一个ModelAndView对象

#### Model（讲师常用）

* 该对象就是ModelAndView中的Model，作用一致

* 无需创建该对象，直接在形参列表中Model类型的参数即可

* model . setAttribute( "key2","valuw2" );

* ```java
  @RequsetMapping("/xxx")
  public String getRequestScopeDataByModel(Model model){
      //向请求域共享数据
      model.setAttribute("key2","value2");
      return "xxx";//逻辑视图名称
  }
  
  
  //以下是Thymeleaf视图解析器定位到的页面
  //使用Thymeleaf视图渲染技术 获取请求域数据
  <p th:text="${key2}"></p>
  ```

#### ModelMap

* 无需创建该对象，直接在形参列表中ModelMap类型的参数即可

* modelMap . setAttribute( "key3","value3" );

* ```java
  @RequsetMapping("/xxx")
  public String getRequestScopeDataByModelMap(ModelMap modelMap){
      //向请求域共享数据
      modelMap.setAttribute("key3","value3");
      return "xxx";//逻辑视图名称
  }
  
  
  //以下是Thymeleaf视图解析器定位到的页面
  //使用Thymeleaf视图渲染技术 获取请求域数据
  <p th:text="${key2}"></p>
  ```

#### Map

* 无需创建该对象，直接在形参列表中Map类型的参数即可，但要加泛型

* map . put( "key4","value4" );

* ```java
  @RequsetMapping("/xxx")
  public String getRequestScopeDataByMap(Map<String,Object> map){
      //向请求域共享数据
      map.put("key4","value4");
      return "xxx";//逻辑视图名称
  }
  
  
  //以下是Thymeleaf视图解析器定位到的页面
  //使用Thymeleaf视图渲染技术 获取请求域数据
  <p th:text="${key2}"></p>
  ```



#### Model、ModelMap、Map

* 打印运行类型，发现都是BindingAwareModelMap
* 底层中，这些类型最终都是通过BindingAwareModelMap进行创建
  * 所以说其实不止以上这些方式可以存储请求域对象，但凡是BindingAwareModelMap继承的类或实现的接口，都可以用来存储请求域对象



### 向session域对象共享数据

* SpringMVC提供的方式并没有ServletAPI的方式简单
  * 控制器方法形参列表添加HttpSession类型参数
  * session . setAttribute("key","value")；即可
* Thymeleaf中获取session域数据需要通过${ session.属性名 }​
* 若想要服务器关闭而浏览器未关闭时session域对象数据还在
  * Tomcat服务器 Edit配置（这是IDEA的问题）
    * Preserve sessions across restarts and redeploys 勾选即可
  * 钝化和活化：
    * 服务器关闭，session域数据会被保存磁盘文件中---Tomcat中 work目录
    * 服务器重新启动，会将钝化文件的数据重新加载到session中
    * 若session对象存储的对象数据，需要该对象实现了序列化的接口，因为钝化是一个序列化的过程

### 向application域对象共享数据

* SpringMVC提供的方式并没有ServletAPI的方式简单
  * 控制器方法形参列表添加HttpServletRequest类型参数
  * request.getServletContext().setAttribute("key","value");即可
* Thymeleaf中获取application域数据需要通过${ application.属性名 }









## SpringMVC中的视图

> SpringMVC中的视图是**View接口；**
>
> 视图的作用是**渲染数据**，将Model模型层的数据向用户展示；
>
> SpringMVC中视图种类繁多，**默认有转发视图和重定向视图**
>
> 工程引入JSTL的依赖（使用JSP页面），转发视图会自动转换为JstlView
>
> 若使用的是Thymeleaf的视图（HTML页面），在SpringMVC配置文件中配置有Thymeleaf的视图解析器；由此视图解析器解析到的视图就是ThymeleafView
>
> * SpringMVC配置文件中配置的Thymeleaf视图解析器，class就是ThymeleafViewResolver
>
> 创建的视图只和设置的视图名称有关系：
>
> * ThymeleafView："xxx";
> * InternalResourceView:  "forward:xxx";
> * RedirectView:  "redirect:xxx";



### 转发视图-ThymeleafView

* SpringMVC配置文件
  * 配置Thymeleaf视图解析器：ThymeleafViewResolver
  * 配置了视图前缀、后缀
* 用户通过视图层向服务端发起请求，由前端控制器DispatcherServlet匹配到具体的请求控制器（控制层代码）
* 根据@RequestMapping注解或其派生的注解 和请求路径 的映射关系 匹配到具体方法
* 当控制器方法设置的视图名称没有添加前缀，视图就会被SpringMVC配置文件中配置的视图解析器解析；
  * 视图名称拼接视图前缀和视图后缀所得到的的最终路径，会通过转发方式实现跳转
  * 视图解析器会将视图进行解析
    * 配置的ThymeleafViewResolver视图解析器解析的视图就是ThymeleafView



### 转发视图-InternalResourceView（少用）

> 返回的视图名称需要加上forward: 前缀；例如：return "forward:abc";
>
> 使用InternalResourceView转发视图的方式并不多（使用JSP，视图解析器就会将视图解析为InternalResourceView），因为无论是引入JSTL依赖，还是配置Thymeleaf视图解析器ThymeleafViewResolver实现的都是转发效果

* 为何使用Thymeleaf的视图解析器
  * HTML文件中导入th相关约束后，使用th相关语法可以方便的操作数据，进行视图渲染的效果
  * **需要Thymeleaf的视图解析器来解析当前视图，才可以解析当前视图中th的语法，才可以渲染页面，看到动态数据**
  * InternalResourceView转发视图就是单纯地转发，不够方便



### 重定向视图-RedirectView

> 返回的视图名称需要加上redirect: 前缀；例如：return "redirect:abc";
>
> 视图解析器进行重定向视图解析时，会将redirect: 去除，然后判断视图名称是否以 / 开始；若是则自动拼接上下文路径

* 一般使用转发视图还是重定向视图的业务逻辑是
  * 业务处理失败使用转发视图
  * 业务处理成功使用重定向视图
  * 例如：登陆成功重定向；登陆失败转发



### 视图控制器view-controller

* 若请求控制器（控制层代码）中的控制器方法，只是进行了页面的跳转，没有执行任何业务逻辑；

  * 引出视图控制器（经典场景：跳转到首页、跳转到xx添加页面）

  * 设置视图控制器mvc:view-controller

    * 若只设置视图控制器，则只有视图控制器设置的请求会被处理（其他请求全部404）；此时必须 开启MVC注解驱动mvc:annotation-driven
      * MVC注解驱动 功能非常多
        * 处理静态资源
        * 处理AJAX请求 处理JSON数据

  * ```xml
    <!--开启MVC注解驱动-->
    <!--视图控制器：为当前的请求设置视图名称实现页面跳转-->
    ```



## RESTful

> REST：**Re**presentational **S**tate **T**ransfer 表现层资源状态转移
>
> 看待服务器的一种方式；把服务器中所有的内容看作是资源---一切皆资源
>
> 资源路径一样的情况下，对资源不同的操作方式，使用不同的操作方式的动词：GET、POST、PUT、DELETE

* HTTP协议中，四个表示不同操作的动词：
  * GET：查询资源
  * POST：新建资源
  * PUT：修改资源
  * DELETE：删除资源
* REST风格提倡URL使用统一的风格设计，从前到后各个单词使用**斜杠分开**；**不使用问号键值对方式携带请求参数**，而是将要**发送给服务器的数据作为URL地址一部分**，保证整体风格一致性



* 设置==处理请求方式的过滤器HiddenHttpMethodFilter==
  * 设置PUT、DELETE请求方式：
    1. 表单标签：要求当前请求方式必须为POST
    2. 表单标签内：设置隐藏域，请求参数中必须要有参数名 _method   参数值 put或delete
  * 读源码发现，该过滤器获取了"_method"的请求参数，再次证明了CharacterEncodingFilter字符编码过滤器要设定在所有过滤器之前



* Thymeleaf语法实现循环：th:each

  * ```html
    <!--
    	${域对象key}表示集合；emp表示集合迭代的每一个元素
    	标签内就可以使用该emp元素
    -->
    <tr th:each="emp : ${域对象key}">
        <td th:text="${emp.id}"/>
        <td th:text="${emp.name}"/>
        <td th:text="${emp.xxx}"/>
        
    </tr>
    ```

* DefaultServlet专门用来处理静态资源

  * 但当前被DispatcherServlet前端控制器的url-pattern给囊括了
    * Tomcat服务器web.xml默认配置了默认Servlet，我们的工程中web.xml是继承关系，所以相当于重写
  * 解决方式：
    * SpringMVC配置文件配置默认的Servlet处理静态资源
    * <mvc:default-servlet-handler / >
      * 若不配置该标签，会被工程中的前端控制器来匹配处理
      * 若只配置该标签，所有的请求又都会由默认Servlet来处理（处理静态资源）
        * 还要配置开启MVC注解驱动
      * 两个标签配合使用表示：先由前端控制器来处理，处理不了就由默认Servlet来处理



* 超链接@{}表达式配合${}表达式使用

  * ```html
    <!--其中/emp/是路径 和${}表达式解析后的数据 使用+拼接即可-->
    @{'/emp/'+${emp.id}}
    
    <!--测试：双竖线（管道符）好像也可以-->
    @{|/emp/+${emp.id}|}
    ```

* 单选框回显

  * th:field="${xxx}"
  * 回去和单选框的value做比较 一样则被选中



* 删除功能

  * 通过超链接控制表单提交

  * 引入vue文件

    * <script type="text/javascript" th:src="@{xxx.js}" / >



## SpringMVC处理AJAX请求

* 不再使用JQuery处理AJAX请求

* 改用axios

  * ```html
    <body>
        <div id="app">
            <input type="button" @click="testAjax()"/>
        </div>
        <script type="text/javascript" th:src="@{/xxx/vue.js}"></script>
        <script type="text/javascript" th:src="@{/xxx/axios.min.js}"></script>
        <script type="text/javascript">
            var vue =new Vue({
                el:"#app",
                methods:{
                    testAjax(){
                        /*axios({
                            url:"请求路径",
                            method:"请求方式",
                            // params:{请求参数：name=value&name=value方式发送}
                            //无论请求方式是get还是post，都会将参数拼接到请求地址后
                            //请求参数可通过request.getParameter()获取--->SpringMVC控制器方法形参
                            params:{},
                            
                            // data:{请求参数：以JSON格式发送}
                            //请求参数会保存到请求报文的请求体传输服务器（适用于PUT、POST、PATCH请求，要有请求体）
                            //不可通过request.getParameter()获取
                            //通过Gson，FastJson，Jackson等
                            data:{}
                        }).then(response=>{
                            console.log(response.data);
                        });*/
                        
                        //常用axios发送AJAX请求方式（[]包括的参数可省略不写）：
                        //axios.get(url[,config])或axios.post(url[,data[,config]]) 
                        //JS代码中不能使用Thymeleaf解析技术---所以拼接地址时需要添加上下文路径
                    }
                }
            });
        </script>
    </body>
    
    ```

* 之前使用AJAX技术，在Servlet中响应数据使用response.getWriter().write(...);

因为是AJAX请求，所以控制器方法不能返回字符串进行页面跳转

1. 形参列表封装该参数，用来响应数据即可，方法返回修饰符void

2. @RequestBody注解获取请求体数据

   * @RequestBody注解定义在形参列表参数类型之前即可

   * 可直接将请求报文中的请求体内容和形参进行绑定
     * 将JSON格式的请求体内容 转换为 Java对象
       * 导入jackson的依赖
       * SpringMVC开启MVC的注解驱动（视图控制器、DefaultServlet、JSON转为Java对象）
       * 控制器方法形参，直接设置JSON格式请求体内容要转换的 Java类型（例如实体类对象、Map集合等） 的形参；@RequestBody标识即可

3. @ResponseBody注解响应数据

   * 定义在方法上，return的数据作为响应体的内容；（返回字符串就不再是视图跳转了）
   * 将所表示的控制器方法的返回值 作为响应报文的响应体 响应到浏览器
   * 使用@ResponseBody注解响应浏览器JSON格式的数据 
     * 导入jackson依赖
     * 开启MVC的注解驱动
     * 将需要转换为JSON 的Java对象，直接作为控制器方法的返回值（之前还需要使用Gson来转换为Json）；@ResponseBody注解标识该方法，就可以将Java对象直接转换为JSON字符串，响应到浏览器

* 常见的Java对象转换为Json格式：
  * 实体类--->Json对象
  * Map集合--->Json对象
  * List集合--->Json数组

* @RestController注解

  * ```
    标识在类上；作用就是类相当于添加了@Controller注解，同时为每个方法添加了@ResponseBody注解用来响应数据
    ```





## SpringMVC的文件上传与下载

> ResponseEntity（翻译：响应实体）
>
> 作用在控制器方法返回值；表示响应到浏览器的一个完整的响应报文

* 若方法没有设置返回值，也没有进行数据响应；会将控制器方法@RequestMapping注解的value属性值（路径）作为逻辑视图进行返回



### 文件下载

* getRealPath

  * 默认获取IDEA下面的out下的路径

  * WEB工程就是target下war包下的路径

    * ```java
      //当不确定路径的分隔符使用 / 还是 \ 时，使用以下方式即可；img表示war包下的目录（classes目录、webapp下面的目录）
      String realPath = servletContext.getRealPath("img");
      realPath = realPath + File.separator + "1.jpg";
      
      
      InputStream is = new FileInputStream(realPath);
      byte[] bytes = new byte[is.available()];//available获取字节输入流对应的文件所有的字节数
      is.read(bytes);
      
      MultiValueMap<String,String> headers = new HttpHeaders();
      headers.add("Content-Disposition","attachment;filename=1.jpg");
      
      HttpStatus statusCode = HttpStatus.OK;
      
      ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes,headers,statusCode);
      
      is.close();
      
      return responseEntity;
      ```



### 文件上传

* form表单标签
  * post
  * enctype="multipart/form-data"
  * input：type="file"
* 控制器方法
  * MultiPartFile对象：SpringMVC中封装上传的文件的对象
  
    * 该对象需要SpringMVC配置文件进行配置：文件上传解析器
  
      * ```xml
        <!--配置文件上传解析器-->
        <bean class="CommonsMultipartResolver"></bean>
        配置该文件会报错：缺少commons-fileupload的jar包
        导包后还是会报错：说明不是按照class类型获取的文件上传解析器的，而是根据ID获取的bean，id必须为：multipartResolver
        
        ```
  
      * 
  
    * 定义在形参列表-参数名和input中name属性一致
  
    * . transferTo()；实现上传的方法（SpringMVC将文件上传的方法进行封装了，我们无需手动再对文件进行读写）
  
    * . getOriginalFileName()；获取上传的文件的文件名



* 上传的文件重名问题
  * 思考原来学到的输出流：例如new FileOutputStream()；
    * 里面有两个参数：一个是文件，一个是boolean类型；
      * 该boolean类型是定义文件是否追加；（默认是不追加，进行覆盖的）
  * 解决方式：
    * 截取文件后缀
    * 时间戳或者UUID都是不重复的
      * UUID.randomUUID().toString();





## 拦截器

> 拦截器有三个方法分别在控制器方法执行前后
>
> SpringMVC中创建类实现HandlerInterceptor
>
> interceptor

* 创建类实现HandlerInterceptor拦截器接口
  * 重写方法（控制器方法执行前后，渲染视图完成后）
    * preHandle方法记得放行：return true；否则一直处在控制器方法执行前拦截的状态
      * 观察源码：
        * DispatcherServlet源码中1061行是调用控制器方法的，上面就调用了preHandle类似方法得到返回值
        * 若preHandle返回false直接return
* 配置到SpringMVC配置文件中（属于SpringMVC的一个组件）
  * <mvc:interceptors / >里面配置对应类的bean即可
    * 或者在实现了HandlerInterceptor拦截器接口的类添加注解后，使用ref标签引入也行
  * 以上两种方式：bean或者ref方式管理的拦截器默认对DispatcherServlet处理的所有请求进行拦截
  * 考虑使用<mvc:interceptor / >
    * mvc:mapping path="/**"表示拦截所有目录的请求（ / *表示拦截上下文路径后一层目录的请求）
    * mvc:exclude-mapping 表示不拦截什么资源
    * ref引入对应的拦截器bean
* SpringMVC配置文件中配置多个拦截器
  * 执行顺序和配置顺序相关



## 异常处理器(异常解析器)

> HandlerExceptionResolver：SpringMVC为控制器方法执行过程中出现异常提供的接口；
>
> 接口抽象方法resolveException用来处理该异常，返回值为ModelAndView：向域对象共享数据，并进行页面跳转
>
> 
>
> SpringMVC中使用的默认的异常处理器：DefaultHandlerExceptionResolver

* SimpleMappingExceptionResolver

  * 基于XML配置方式实现异常解析器配置：

    * SpringMVC配置文件中配置这个普通映射异常解析器

    * ```xml
      <bean class="...SimpleMappingExceptionResolver">
          <!--设置某些异常出现跳转的逻辑视图名称-->
          <properties name="exceptionMappings">
              <props>
                  <!--key设置异常类型，标签内容直接标明逻辑视图名称表示出现该异常时视图跳转的位置-->
                  <prop key="...ArithmeticException">这里直接设置逻辑视图名称即可</prop>
                  <prop key="...IndexOfBoundException">这里直接设置逻辑视图名称即可</prop>
                  ...
              </props>
          </properties>
          <!--设置向请求域对象共享异常数据的属性名：在跳转后的页面中就可以通过该属性名获取异常信息-->
          <properties name="exceptionAttribute" value="自定义为域对象数据设置的属性名"></properties>
      </bean>
      ```

  * 基于注解方式实现异常解析器的配置：

    * 相当于是控制器，在控制层定义

    * @ControllerAdvice

      * 将当前类标识为异常处理的组件

    * 方法添加@ExceptionHandler(  )

      * value属性中定义Throwable及其子类异常的class 的数组

    * ```java
      @ControllerAdvice//将当前类标识为异常处理的组件
      public class ExceptionController{
          @ExceptionHandler(ArithmeticException.class)//设置要处理的异常信息
          public ModelAndView handleException(Throwable ex){
              //ex标识控制器方法所出现的异常
              ModelAndView modelAndView = new ModelAndView();
              modelAndView.addObject("ex",ex);
              modelAndView.setViewName("error");
              return modelAndView;
          }
      }
      ```



## 注解配置SpringMVC

### 创建初始化类，替代web.xml

* Servlet3.0中，类路径中若存在类实现了javax.servlet.ServletContainerInitializer接口，可用该类配置Servlet容器

* Spring中提供了该接口的实现：

  * SpringServletContainerInitializer；该类会反过来查找实现了WebApplicationInitializer的类并将配置的任务交由它们完成。

  * Spring3.2引入了便利的WebApplicationInitializer基础实现，名为AbstractAnnotationConfigDispatcherServletInitializer；

  * 当存在类扩展了AbstractAnnotationConfigDispatcherServletInitializer并将其部署到Spring容器时，容器会自动发现并用它来配置Servlet上下文。（实现该扩展类代替web.xml）

  * ```java
    //该初始化类用来代替web.xml
    public class WebInit implements AbstractAnnotationConfigDispatcherServletInitailizer{
        
        @Override
        //设置一个配置类代替Spring配置文件
        ... getRootConfigClasses(){
            return new Class[]{SpringConfig.class};//注意是自定义的Spring配置类
        }
        
        @Override
        //设置一个配置类代替SpringMVC配置文件
        ... getServletConfigClasses(){
            return new Class[]{WebConfig.class};//自定义的SpringMV配置类
        }
        
        @Override
        //设置SpringMVC的前端控制器DispatcherServlet的url-pattern
        ...getServletMappings(){
            return new String[]{"/"};//可以设置多个
        }
        
        
        //以下方法需要手动重写：
        @Override
        //设置过滤器
        ...Filter[] getServletFilters(){
            //创建编码过滤器
            CharacterEncodingFilter cef = new CharacterEncodingFilter();
            cef.setEncoding("UTF-8");
            cef.setForceEncoding(true);
            //创建处理请求方式过滤器
            HiddenHttpMethodFilter hhmf = new HiddenHttpMethodFilter();
            
            return new Filter[]{cef,hhmf};
        }
    }
    
    //Spring的配置类
    public class SpringConfig{}
    //SpringMVC的配置类
    public class WebConfig{}
    ```



### 创建配置类，代替Spring配置文件

* ```java
  @Configuration//将当前类表示为配置类
  public class SpringConfig{
      //暂时还未整合SSM，整合后将Spring配置信息写在此类
  }
  ```

* 



### 创建配置类，代替SpringMVC配置文件

* ```java
  @Configuration//将当前类表示为配置类
  @ComponentScan("com.lixc.springmvc.controller")//配置注解扫描
  @EnableWebMvc//开启MVC注解驱动
  public class WebConfig implements WebMvcConfigurer{
      //重写方法处理静态资源---参数名.enable();表示配置可用
      //重写视图控制器---参数名.addViewController("/").setViewName("index");
      
      //配置文件上传解析器---配置bean
      @Bean
      public CommonsMultipartResolver mpr(){
          return new CommonsMultipartResolver();
      }
      
      //配置拦截器（需要先创建实现了HandlerInterceptor接口的拦截器）
      //	重写方法---参数名.addInterceptor(new 拦截器实现类).addPathPatterns("/**").excludePathPatterns("/test");
      
      //异常解析器---比较麻烦看讲解
      
      //配置视图解析器---直接CV
      
  }
  ```







## SpringMVC执行流程

### SpringMVC常用组件

* DispatcherServlet：前端控制器
  * 由框架提供的，无需工程师开发
  * 作用：统一的处理请求和响应，整个流程控制的中心，由它来对其它组件进行调用处理用户的请求
* HandlerMapping：处理器映射器
  * 由框架提供的，无需工程师开发
  * 作用：根据请求的请求地址、请求方式等信息来找到Handler处理器（控制器方法）
* Handler：处理器
  * 需要工程师记性开发
  * 作用：在前端控制器的控制下，控制器方法对用户的具体请求进行处理
* HandlerAdapter：处理器适配器
  * 由框架提供，无需工程师开发
  * 作用：由处理器适配器去调用Handler处理器（控制器方法）进行执行
* ViewResolver：视图解析器
  * 由框架提供，无需工程师开发
  * 作用：进行视图解析，得到相应的视图；例如ThymeleafView、InternalResourceView、RedirectView
* View：视图
  * 将模型数据通过页面展示给用户



### DispatcherServlet初始化过程

* 阅读源码FrameworkServlet中发现：SpringMVC创建的IOC容器（WebApplicationContext）会设置一个父容器，父容器就是Spring的IOC容器
  * 所以Spring的IOC容器和SpringMVC的IOC容器呈现的是父子关系	

### ==SpringMVC的执行流程==

* URL统一资源定位符：协议 IP地址 端口号 资源具体路径
  * 资源在网络上的路径
* URI 统一资源标识符：资源具体路径
  * 资源在服务器的路径



1) ==用户向服务器发送请求，请求被SpringMVC 前端控制器 DispatcherServlet捕获。==

2) DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI），==判断请求URI对应的映射==：

a) 不存在

i. 再判断是否配置了mvc:default-servlet-handler

ii. 如果没配置，则控制台报映射查找不到，客户端展示404错误

iii. 如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示404

错误

b) 存在则执行下面的流程

3) 根据该URI，调用==HandlerMapping获得该Handler配置的所有相关的对象（通过HandlerMapping匹配到控制器方法）==（包括Handler对象以及

Handler对象对应的拦截器），最后以HandlerExecutionChain执行链对象的形式返回。

4) DispatcherServlet 根据获得的Handler，==选择一个合适的HandlerAdapter（执行控制器方法）==。

5) 如果成功获得HandlerAdapter，此时将==开始执行拦截器的preHandler(…)方法【正向】==

6) 提取Request中的模型数据，填充Handler入参，==开始执行Handler（Controller)方法，处理请求。（调用控制器方法）==

在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作：

a) HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定

的响应信息

b) 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等

c) 数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等

d) 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中

7) Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象。

8) 此时将==开始执行拦截器的postHandle(...)方法【逆向】==。

9) 根据返回的ModelAndView（此时会判断是否存在异常：如果存在异常，则执行

HandlerExceptionResolver进行异常处理）选择一个适合的==ViewResolver进行视图解析，根据Model和View，来渲染视图。（处理ModelAndView，渲染视图）==

10) 渲染视图完毕==执行拦截器的afterCompletion(…)方法【逆向】。==	

11) 将==渲染结果返回给客户端。==





## 末尾