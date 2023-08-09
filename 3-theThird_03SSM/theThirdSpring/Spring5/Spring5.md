# Spring5

* 轻量级、开源的Java框架；解决了企业繁琐的开发---复杂代码



## 1.Spring框架概念

> Spring框架是什么？
>
> 作用是什么？
>
> 有什么好处？

* Spring框架是一个开源的JavaEE应用框架，针对Bean生命周期进行管理的轻量级容器（LightWeight Container）
  * 简单来说：==Spring是一个轻量级的开源的JavaEE框架==
* 目的：==Spring框架解决企业应用开发的复杂性==
* Spring两个核心部分：==IOC和AOP==
  * IOC：控制反转 Inversion Of Control；把创建对象的过程交由Spring进行管理
  * AOP：面向切面 Aspect Oriented Programming；不修改源代码进行功能增强
* Spring特点：
  * 方便解耦（IOC），简化开发
  * AOP编程支持
  * 方便程序测试
  * 方便和其他框架进行整合
  * 方便进行事务操作
  * 降低API开发难度



### 入门案例

> Spring框架下载地址：https://repo.spring.io/ui/native/release/org/springframework/spring/
>
> * Spring5模块：
>   * ![image-20230519152330909](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230519152330909.png)



1. 先下载Spring Framework---Spring框架压缩包

2. 创建java项目工程

3. 导入Spring所需jar包

   * IOC基本包
     * commons-logging-1.1.1.jar
     * spring-beans-5.2.6.RELEASE.jar
     * spring-context-5.2.6.RELEASE.jar
     * spring-core-5.2.6.RELEASE.jar
     * spring-expression-5.2.6.RELEASE.jar

4. 创建普通类，普通方法

5. 创建Spring配置文件，里面配置创建的对象

   * 只有导入核心jar包才会出现Spring Config类型的XML文件

   * Spring配置文件是XML格式

   * bean标签---id、class

   * ```xml
     <bean id="user" class="com.lixc.pojo.User"></bean>
     ```

6. 编写测试代码

   * 加载Spring配置文件

   * 获取配置创建的对象

   * ```java
     ApplicationContext context = new ClassPathXmlApplicationContext("");
     User user = context.getBean("user",User.class);
     ```



![image-20230519155908451](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230519155908451.png)











## 2.IOC容器

> IOC底层原理？
>
> IOC接口（BeanFactory）介绍
>
> IOC操作---Bean管理（基于XML）
>
> IOC操作---Bean管理（基于注解）

### IOC概念和原理

1. 什么是IOC
   * 控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理
   * 使用IOC目的：降低耦合度
   * 上面的入门案例就是IOC的实现
2. IOC底层原理
   * XML解析、工厂模式、反射
3. IOC实现过程
   * 第一步：XML配置文件，配置创建的对象
   * 第二步：创建工厂类
     * XML解析获取class属性值；//例如Dom4j 这种XML解析技术
     * 通过反射创建对象；
       * Class clazz = Class.forName("class值");
       * return (类型强转)clazz.newInstance()；

### IOC（接口）

1. ==IOC思想基于IOC容器完成，IOC容器底层就是对象工厂==
2. Spring提供IOC容器实现的两种方式：（两个接口）、
   * BeanFactory：该接口是IOC容器的基本实现接口，是Spring内部使用接口，不提供开发人员进行实现。
     * 在加载配置文件时不会创建对象，只有在获取（或使用）对象时才会创建对象。
   * ==ApplicationContext==：BeanFactory的子接口，提供了更多更强大的功能，一般由开发人员进行使用。
     * 在加载配置文件时就会创建配置文件中的对象
   * 两个接口都可以做到加载配置文件、创建配置的对象。 
   * ApplicationContext接口实现IOC容器的方式更好些：
     * 例如Context对象在启动服务时进行了创建，此时加载创建所有bean对象，只是增加了服务启动的时间，服务运行时不会再创建bean对象，所以耗时耗资源的过程放在服务启动时要比服务运行时效果更好
3. ApplicationContext接口的实现类（使用Ctrl+H 可以查看类结构）：
   * FileSystemApplicationContext
     * 参数是XML配置文件在硬盘下的真实路径
   * ClassPathXmlApplicationContext
     * 参数是XML配置文件在src下的相对路径



### IOC操作---Bean管理（概念）

1. 什么是Bean管理（Bean管理指两个操作）
   * Spring创建对象
   * Spring属性注入
2. Bean管理操作的两种实现方式
   * 基于XML配置文件方式实现
   * 基于注解方式实现

### IOC操作---Bean管理（基于XML）

1. ==基于XML方式创建对象==

   * 在Spring配置文件中，使用bean标签添加对应的属性，即可实现创建对象
   * bean标签常用属性
     * id：用作唯一标识
     * class：类全路径（包+类路径）
   * 创建对象时，默认执行无参构造

2. ==基于XML方式注入属性==

   * DI：Dependency Injection 依赖注入；就是注入属性

     * DI依赖注入，是IOC中的一种具体实现，但依赖注入需要在创建对象的基础之上进行完成

   * 注入属性的方式

     * - [x] **使用setXxx方法注入属性**

       * 创建类，定义属性及其对应的setXxx方法
       * 在Spring配置文件中配置对象创建，配置属性注入
         * bean--->property（name和value属性）

     * - [x] **使用含参构造注入属性**（Spring使用bean标签创建对象默认调无参构造，若只有含参构造则需要指定constructor-arg标签）

       * 创建类，定义属性及包含属性的类含参构造
       * 在Spring配置文件中配置对象创建，配置属性注入
         * bean--->constructor-arg（name和value属性；index可以替代name属性，表示含参构造中形参列表索引0开始）

     * p名称空间注入（了解）

       * 底层还是用到了setXxx方法注入
       * 使用p名称空间注入，可以简化基于XML配置方式
         * 添加p名称空间在配置文件中
         * 直接在bean标签中 注入属性 p:属性名=“xxx”

3. ==XML注入其他类型属性==

   * - [x] 字面量	

     * null值

       * ```xml
         <bean id="user" class="com.lixc.pojo.User">
             <property name="name">
                 <null/>
             </property>
         </bean>
         ```

     * 属性值包含特殊符号

       * XML配置文件可以特殊符号可以使用 转义符 或 CDATA纯文本域

   * - [x] 注入属性-外部bean

     * 创建service和dao两个类

     * service定义dao属性、并生成setXxxDao相关方法

     * 使用spring配置创建对象和注入属性

     * ```xml
       <bean id="userService" class="com.lixc.service.UserService">
           <!--注入UserDao对象
       		name属性值：UserService类的属性名称
       		ref属性值：引用创建UserDao对象的bean标签的id值
       	-->
           <property name="userDao" ref="userDao"></property>
       </bean>
       <bean id="userDao" class="com.lixc.dao.impl.UserDaoImpl"></bean>
       ```

   * - [x] 注入属性-内部bean

     * 一对多关系：部门和员工

     * 在实体类表示一对多关系，员工表示所属部门使用对象类型表示

     * 在Spring配置文件中配置对象创建，配置属性注入

     * ```xml
       <!--外部bean的格式-->
       <bean id="emp" class="com.lixc.bean.Emp" >
           <!--设置普通属性-->
           <property name="ename" value="lixc"></property>
           <property name="gender" value="male"></property>
           <!--设置对象类型属性-->
           <property name="dept" ref="dept"></property>
       </bean>
       <bean id="dept" class="com.lixc.bean.Dept" >
           <property name="dname" value="it"></property>
       </bean>
       
       
       <!--内部bean的格式-->
       <bean id="emp" class="com.lixc.bean.Emp" >
           <!--设置普通属性-->
           <property name="ename" value="lixc"></property>
           <property name="gender" value="male"></property>
           <!--设置对象类型属性-->
           <property name="dept">
               <bean id="dept" class="com.lixc.bean.Dept" >
           		<property name="dname" value="it"></property>
       		</bean>
           </property>
       </bean>
       ```

   * - [x] 注入属性-级联赋值

     * ```xml
       <!--级联复制写法1-->
       <bean id="emp" class="com.lixc.bean.Emp" >
           <!--设置普通属性-->
           <property name="ename" value="lixc"></property>
           <property name="gender" value="male"></property>
           <!--设置对象类型属性-->
           <property name="dept" ref="dept"></property>
       </bean>
       <bean id="dept" class="com.lixc.bean.Dept" >
           <property name="dname" value="it"></property>
       </bean>
       
       
       <!--级联赋值写法2-->
       <bean id="emp" class="com.lixc.bean.Emp" >
           <!--设置普通属性-->
           <property name="ename" value="lixc"></property>
           <property name="gender" value="male"></property>
           <!--注意：调用对象的属性，首先需要给bean对象（emp）的dept属性增加getDept方法，获取dept后可以获取dname属性-->
           <property name="dept.dname" value="codeDog"></property>
       </bean>
       <bean id="dept" class="com.lixc.bean.Dept" >
           <property name="dname" value="it"></property>
       </bean>
       ```

   * - [x] 注入属性-集合

     * 数组类型属性、List集合类型属性、Set集合类型属性、Map集合类型属性

     * 创建类，封装集合属性，提供集合的setXxx方法

     * 在Spring配置文件中配置对象创建，配置属性注入

       * ```xml
         <bean id="collectionDemo" class="com.lixc.collection.CollectionDemo">
             <property name="arrs">
                 <!--数组属性注入：可以使用array标签，也可以使用list标签-->
                 <array>
                     <value>123</value>
                     <value>456</value>
                 </array>
             </property>
             <property name="lists">
                 <!--List属性注入：使用list标签-->
                 <list>
                     <value>list1</value>
                     <value>list2</value>
                 </list>
             </property>
             <property name="sets">
                 <!--Set属性注入：使用set标签-->
                 <set>
                     <value>set1</value>
                     <value>set2</value>
                 </set>
             </property>
             <property name="maps">
                 <!--Map属性注入：使用map标签-->
                 <map>
                     <entry key="key1" value="value1"></entry>
                     <entry key="key2" value="value2"></entry>
                 </map>
             </property>
         </bean>
         ```

     * 集合类型属性---泛型定义对象类型

       * ```xml
         <bean id="collectionDemo" class="com.lixc.collection.CollectionDemo">
             <property name="lists">
                 <!--List属性注入：使用list标签-->
                 <list>
                     <!--集合中元素为对象类型-->
                     <ref bean="objDemo1"></ref>
                     <ref bean="objDemo2"></ref>
                     <ref bean="objDemo3"></ref>
                 </list>
             </property>
         </bean>
         
         <!--创建多个id不同的相同对象-->
         <bean id="objDemo1" class="com.lixc.collection.ObjDemo">
             <property name="objName" value="读书"></property>
         </bean>
         <bean id="objDemo2" class="com.lixc.collection.ObjDemo">
             <property name="objName" value="冥想"></property>
         </bean>
         <bean id="objDemo3" class="com.lixc.collection.ObjDemo">
             <property name="objName" value="输出"></property>
         </bean>
         ```

     * 集合属性注入部分提取

       * 在Spring配置文件中引入名称空间：util

       * 使用util标签完成list集合注入抽取

         * ```xml
           <?xml version="1.0" encoding="UTF-8"?>
           <beans xmlns="http://www.springframework.org/schema/beans"
                  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                  xmlns:p="http://www.springframework.org/schema/p"
                  xmlns:util="http://www.springframework.org/schema/util"
                  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                      http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-beans.xsd">
               
               
               <!--抽取list集合类型属性注入-->
               <util:list id="bookList">
                   <value>1</value>
                   <value>2</value>
                   <value>3</value>
               </util:list>
               
               <!--抽取list集合类型属性注入使用-->
               <bean id="book" class="com.lixc.spring5.collection.Book">
                   <property name="list" ref="bookList"></property>
               </bean>
               
           </beans> 
           ```



### IOC操作---Bean管理（FactoryBean）

* Spring中有两种类型Bean，一种是普通Bean（我们通过Spring配置文件创建的），还有一种工厂Bean（FactoryBean）

  * 普通bean：在Spring配置文件中定义的bean类型就是返回类型
  * 工厂bean：在Spring配置文件中定义的bean类型可以和返回类型不一样；（Spring中内置的一种bean类型）

* 工厂bean实现

  1. 创建类，实现FactoryBean接口，该类成为工厂bean
  2. 实现接口方法，在实现方法中定义返回的bean类型

  * ```java
    public MyBean implements FactoryBean<泛型>{
        //定义返回bean
        泛型 getObject(){}   
    }
    ```



### IOC操作---Bean管理（bean作用域）

* Spring创建的bean实例，可以设置单实例或多实例（默认bean对象为单实例对象）
* 如何设置单实例还是多实例
  * Spring配置文件中bean标签中scope属性用于设置对象为单实例还是多实例
    * singleton表示单例对象
    * prototype表示多实例对象
  * 单例属性值singleton和多例属性值区别
    * scope=“singleton”：加载Spring配置文件时就会创建bean对象
    * scope=“prototype”：加载Spring配置文件时不会创建bean对象，等待程序调用getBean( “beanId”，bean对象.class )方法时才会创建对象
  * bean标签scope属性其他值（基本不用，了解）
    * request：JavaWeb中表示一次请求的域对象；
      * 每次创建bean对象，会在request域对象中存储
    * session：JavaWeb中表示会话级别的域对象
      * 每次创建bena对象，会在session域对象中存储





### IOC操作---Bean管理（bean生命周期）

* 生命周期：从对象创建到对象销毁的过程

* bean生命周期

  1. 通过构造器创建bean实例（无参构造）

  2. 为bean的属性赋值 或 对其他bean引用（调用setXxx方法）

  3. 调用bean的初始化方法（bean标签属性init-method；需进行配置初始化方法）

  4. bean可以使用（对象获取到）

  5. 容器关闭时，调用bean的销毁方法（需进行配置销毁方法）

  * ```java
    public class BeanDemo {
        private String str;
    
        public BeanDemo() {
            System.out.println("bean生命周期---第一步：执行构造函数（默认无参构造）");
        }
    
        public void setStr(String str) {
            this.str = str;
            System.out.println("bean生命周期---第二步：执行setXxx方法为属性赋值");
        }
    
        public void init(){
            System.out.println("bean生命周期---第三步：执行bean的初始化方法（该方法自定义，且需在bean标签init-method属性中引用该自定义初始化方法）");
        }
        public void destory(){
            System.out.println("bean生命周期---第五步：执行bean的销毁方法（该方法自定义，且需在bean标签destroy-method属性中引用该自定义bean销毁方法）");
        }
    }
    
    
    	<bean id="beanDemo" class="com.lixc.spring5.lifecycle.BeanDemo" init-method="init" destroy-method="destory">
        	<property name="str" value="demoStr"/>
        </bean>
        
        
        @Test
        public void test3() {
            //bean声明周期
            ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
            BeanDemo beanDemo = context.getBean("beanDemo", BeanDemo.class);
            System.out.println("bean生命周期---第四步：获取bean实例对象");
            ((ClassPathXmlApplicationContext) context).close();
        }
    ```

* bean的后置处理器（生命周期另外两个执行过程）

  * 加入bean后置处理器后，bean生命周期分7步，除去生命周期提到的五步，还有在bean初始化方法前后执行的两个方法

    1. 通过构造器创建bean实例（无参构造）

    2. 为bean的属性赋值 或 对其他bean引用（调用setXxx方法）
    3. 把bean的实例传递给bean后置处理器的postProcessBeforeInitialization方法
    4. 调用bean的初始化方法（bean标签属性init-method；需进行配置初始化方法）
    5. 把bean的实例传递给bean后置处理器的postProcessAfterInitialization方法
    6. bean可以使用（对象获取到）
    7. 容器关闭时，调用bean的销毁方法（需进行配置销毁方法）

  * 添加后置处理器

    * 创建实现BeanPostProcessor接口的类

    * 重写

    * ```java
      public class PostProcessorDemo implements BeanPostProcessor {
          @Override
          public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
              System.out.println("添加Bean的后置处理器---bean初始化之前执行");
              return bean;
          }
      
          @Override
          public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
              System.out.println("添加Bean的后置处理器---bean初始化之后执行");
              return bean;
          }
      }
      
      <!--注意配置Bean的后置处理器后，整个Spring配置文件中的bean都会受影响（初始化前后执行后置处理器重写方法）-->
      <bean id="postProcessorDemo" class="com.lixc.spring5.lifecycle.PostProcessorDemo"/>
      
      //bean生命周期---第一步：执行构造函数（默认无参构造）
      //bean生命周期---第二步：执行setXxx方法为属性赋值
      //添加Bean的后置处理器---bean初始化之前执行
      //bean生命周期---第三步：执行bean的初始化方法（该方法自定义，且需在bean标签init-method属性中引用该自定义初始化方法）
      //添加Bean的后置处理器---bean初始化之后执行
      //bean生命周期---第四步：获取bean实例对象
      //bean生命周期---第五步：执行bean的销毁方法（该方法自定义，且需在bean标签destroy-method属性中引用该自定义bean销毁方法）
      ```



### IOC操作---Bean管理（基于XML自动装配）

* 实际开发中基于XML进行自动装配使用很少，一般使用注解方式进行自动装配

* XML手动装配就是将bean的属性进行value或ref方式进行手动赋值
* 什么是XML自动装配
  
  * 根据指定的装配规则（属性名称或属性类型），Spring自动将匹配的属性值进行注入
* 自动装配实现过程
  * bean标签autowire属性配置自动装配
    * autowire属性两个常用属性值
      * byName：根据属性名称进行自动装配
      * byType：根据属性类型进行注入
  * 方式一：根据属性名称进行自动注入
    * autowire="byName"
    
    * 注入值bean的id值和类属性名称一致
    
    * 存在多个相同类型的bean也可使用，因为bean的id值必须唯一，所以通过byName可以精确匹配对应bean
    
    * ```java
      public class Dept {
          private String dname;
      
          public void setDname(String dname) { this.dname = dname; }
      
          @Override
          public String toString() {
              return "Dept{" +
                      "dname='" + dname + '\'' +
                      '}';
          }
      }
      
      
      public class Emp {
          private String ename;
          private Dept dept;
      
          public void setEname(String ename) { this.ename = ename; }
      
          public void setDept(Dept dept) { this.dept = dept; }
      
          @Override
          public String toString() {
              return "Emp{" +
                      "ename='" + ename + '\'' +
                      ", dept=" + dept +
                      '}';
          }
      }
      
      
          <bean id="emp" class="com.lixc.spring5.autowire.Emp" autowire="byName">
              <property name="ename" value="栗新聪"/>
          </bean>
          <bean id="dept" class="com.lixc.spring5.autowire.Dept">
              <property name="dname" value="财务部"/>
          </bean>
              
              
          @Test
          public void test4() {
              //bean自动装配
              ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
              Emp emp = context.getBean("emp", Emp.class);
              System.out.println(emp);//Emp{ename='栗新聪', dept=Dept{dname='财务部'}}
          }
      ```
  * 方式二：根据属性类型进行自动注入
    * autowire="byType"
    
    * 对bean名称没要求；若存在多个相同类型的bean，会报错；
    
    * ```xml
      <bean id="emp" class="com.lixc.spring5.autowire.Emp" autowire="byType">
          <property name="ename" value="栗新聪"/>
      </bean>
      <bean id="dept1" class="com.lixc.spring5.autowire.Dept">
          <property name="dname" value="财务部"/>
      </bean>
      ```

### IOC操作---Bean管理（XML引入外部属性文件）

* ==该方式经常在数据库配置中进行使用==

* Spring配置文件 直接配置参数

  * ```xml
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/book"></property>
        <property name="username" value="root"></property>
        <property name="password" value="lz001214"></property>
    </bean>
    ```

* 引入外部配置属性文件---将外部properties属性文件引入到Spring配置文件

  * Spring配置文件引入名称空间：context

  * 使用该标签引入外部属性文件

  * ```xml
    driverClassName=com.mysql.jdbc.Driver
    url=jdbc:mysql://localhost:3306/book
    username=root
    password=lz001214
    
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                               http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
        <!--引入外部属性文件-->
        <context:property-placeholder location="classpath:druid.properties"/>
        <!--配置连接池-->
        <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
            <property name="driverClassName" value="${driverClassName}"></property>
            <property name="url" value="${url}"></property>
            <property name="username" value="${username}"></property>
            <property name="password" value="${password}"></property>
        </bean>
    </beans>
    
        @Test
        public void test5() {
            //bean 引入外部属性文件
            ApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");
            DruidDataSource dataSource = context.getBean("dataSource", DruidDataSource.class);
            System.out.println(dataSource);
        }
    ```







### IOC操作---Bean管理（基于注解方式）

* ==什么是注解==：

  * 注解是代码特殊标记；格式：@注解名称 ( 属性名称=属性值，属性名称=属性值... )
  * 注解可以作用在类、属性、方法定义的上面
  * 使用注解的目的：简化XML配置

* ==Spring针对  Bean管理  中 创建对象  的操作提供的注解==：

  * @Component   组成部分、组件
    * 普通注解，使用该注解都可创建对象
  * @Service
    * 一般使用在业务层
  * @Controller
    * 一般使用在Web层
  * @Repository   仓库、资源库
    * 一般使用在Dao持久层上
  * 四个注解功能是一样的，都可以用来创建bean实例

* ==基于注解方式实现对象创建==

  1. 引入依赖

     * spring-aop-5.2.6.RELEASE.jar

  2. 开启组件扫描

     * 引入名称空间：context

     * 使用context相关标签

       * ```xml
         <?xml version="1.0" encoding="UTF-8"?>
         <beans xmlns="http://www.springframework.org/schema/beans"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xmlns:context="http://www.springframework.org/schema/context"
                xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
             <!--开启组件扫描
                 方式一：多个不同包下的文件需要开启组件扫描，base-package属性值之间可用逗号隔开
                 方式二：可直接将上级包目录进行赋值
             -->
             <!--<context:component-scan base-package="com.lixc.spring5.dao,com.lixc.spring5.service"/>-->
             <context:component-scan base-package="com.lixc.spring5"/>
         </beans>
         ```

  3. 创建类，上方添加创建对象注解

  * ```java
    //基于注解方式实现Bean管理中创建对象操作，必须要引入依赖---aop
    
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                               http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
        <!--开启组件扫描
            方式一：多个不同包下的文件需要开启组件扫描，base-package属性值之间可用逗号隔开
            方式二：可直接将上级包目录进行赋值
        -->
        <!--<context:component-scan base-package="com.lixc.spring5.dao,com.lixc.spring5.service"/>-->
        <context:component-scan base-package="com.lixc.spring5"/>
    </beans>
       
            
    
     @Service
    //@Component(value = "serviceDemo")
    //@Controller
    //@Repository   //四种注解方式皆可使用；注解的value属性默认就是“类名首字母小写”的形式，可以不写
    public class ServiceDemo {
        public void serviceMethod() {
            System.out.println("service业务层方法被调用");
        }
    }
    
    
    
    public class AnnotateTest {
        @Test
        public void test1(){
            //测试 基于注解方式实现对象创建
            ApplicationContext context=new ClassPathXmlApplicationContext("beanDemo.xml");
            ServiceDemo serviceDemo = context.getBean("serviceDemo", ServiceDemo.class);
            System.out.println(serviceDemo);
            serviceDemo.serviceMethod();
        }
    }
    ```



* ==开启组件扫描的细节配置（组件扫描中比较**常见**的做法）==

  * 设置扫描哪些内容

    * include

  * 设置不扫描哪些内容

    * exclude

  * ```xml
    <!--设置扫描哪些内容
        设置不使用默认过滤进行扫描use-default-filters="false"
        include包含过滤扫描的类型是 annotation注解类型，表达式是Service类型注解
    -->
    <context:component-scan base-package="com.lixc.spring5" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Service"/>
    </context:component-scan>
    <!--设置不扫描哪些内容
        exclude不包含过滤扫描的类型是 annotation注解类型，表达式是Controller类型注解
    -->
    <context:component-scan base-package="com.lixc.spring5">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    ```





* ==Spring针对  Bean管理  中 属性注入  的操作提供的注解==（基于注解方式实现Bean管理的属性注入操作）：
  * @Autowired   自动装配
    * 根据属性类型进行属性自动注入
    * spring提供的注解
  * @Qualifier      修饰符、限定符
    * 根据属性名称（属性名称就是注解的value==bean标签下的id）进行属性自动注入
    * 经常和@Autowired注解一起使用---弥补@Autowired注解不足之处（若接口存在多个实现类，无法确定是哪个可以使用@Qualifier注解配合查找）
    * spring提供的注解
  * @Resource     资源
    * 可以根据属性类型、属性名称进行属性自动注入
      * 根据属性类型注入：@Resource
      * 根据属性名称注入：@Resource( name="创建对象注解定义的value值" )
    * javax扩展包提供的注解，所以Spring官方更建议使用Spring提供的注解
  * 以上三个注解都是针对对象类型进行属性注入-----不是普通类型
  * @Value
    * 注入普通类型属性
    * @Value( value="123" )；//为普通类型属性提供属性注入



1. 在service和dao都创建对象（创建对象注解）
2. 在service注入dao对象
   * service中封装dao属性；
   * 在dao属性上加上 属性注入的注解（不再需要加setXxx方法）



* ==完全注解（纯注解）开发==
  1. 创建配置类，替代Spring的XML配置文件
     * 创建类，上方添加@Configuration注解；代表该类为配置类，替代Spring的XML配置文件
     * 再添加@Component-scan( base-package = { "com.lixc.spring5" } )
       * 该方式和Spring配置文件中< context: component-scan base-package="com.lixc.spring5" >扫描效果一致
  2. 加载配置类，完成创建对象
     * 之前提到的通过两个接口可以提供IOC容器的实现，分别是BeanFactory和ApplicationContext
       * 加载配置文件：ApplicationContext ac = new ClassPathXmlApplicationContext(“Spring的XML文件相对路径”)
     * 现在使用配置类，不再对XML配置文件进行加载
       * ApplicationContext ac = new  AnnotationConfigApplicationContext( 注解类.class );











## 3.AOP

### AOP-概念

* Aspect Oriented Programming 面向切面编程；不修改源码进行功能增强
* 通过预编译方式和运行期间动态代理（底层）实现程序功能的统一维护的一种技术
* AOP是OOP的延续，是函数式编程的一种衍生范式
* AOP可以对业务逻辑的各个部分进行隔离，使得业务逻辑的各部分耦合度降低，提高程序可重用性，提高开发效率



* 通俗描述：不通过修改源代码方式，在主干功能新增新的业务功能
* 可以将用户登录主干功能+权限判断新功能 联想（不修改源码，新增功能，在主干配置即可。面向切面编程）

### AOP-底层原理

* AOP底层使用动态代理
* 动态代理的两种情况
  1. 有接口的情况，使用JDK动态代理（增强类中的方法）
     * JDK动态代理：创建==接口实现类代理对象==（不是new），增强类中的方法
  2. 没有接口的情况 ，使用CGLIB动态代理（增强类中的方法）
     * 现有技术：创建子类，重写父类原始方法，重写方法中调用父类原始方法super.xxx()；之后新增扩展功能
     * CGLIB动态代理：创建当前类==子类的代理对象==（不是new），增强类中的方法



### AOP-JDK动态代理

* 使用JDK动态代理，需要使用java.lang.reflect.Proxy（翻译：代理）类中的方法创建代理对象（代理对象不是new新建对象）

  * Proxy提供了创建动态代理类和实例的静态方法，它也是由这些方法创建出的所有动态代理类的超类
  * Proxy类提供的newProxyInstance( ClassLoader loader, Class<?>[] interfaces, InvocationHandler h  )方法
    * 返回指定接口代理类的实例---等同于---创建了接口实现类的代理对象
    * 方法参数：
      * 第一个：当前类的类加载器；
      * 第二个：增强方法所在的类实现的接口（支持多个接口）
      * 第三个：实现InvocationHandler接口，创建代理对象，写增强的方法

* JDK动态代理代码

  * 定义接口、接口实现类

  * 调用Proxy类newProxyInstance方法创建接口实现类的代理对象（不是new）

  * ```java
    public interface UserDao {
        int sum(int i1, int i2);
    
        int div(int i1, int i2);
    }
    
    
    public class UserDaoImpl implements UserDao {
        @Override
        public int sum(int i1, int i2) {
            System.out.println("两数sum方法执行ing");
            return i1 + i2;
        }
    
        @Override
        public int div(int i1, int i2) {
            System.out.println("两数div方法执行ing");
            return i1 / i2;
        }
    }
    
    
    
    /*该类实现了InvocationHandler接口---用作JDK动态代理（创建接口实现类的代理对象）的参数*/
    public class JDKDynamicProxyDemo implements InvocationHandler {
        //定义Object类型，用来更加宽泛的接收 “待动态代理的接口的实现类”
        private final Object obj;
    
        //定义含参构造，用来传递Proxy类newProxyInstance方法中动态代理的接口的实现类
        public JDKDynamicProxyDemo(Object obj) {
            this.obj = obj;
        }
    
        /**
         * 实现InvocationHandler接口重写的方法，该方法用来处理 “JDK动态代理生成的接口实现类的代理对象” 的方法调用
         *
         * @param o       接口实现类---千万不要输出，异常
         * @param method  代理对象的方法
         * @param objects 代理对象的方法的传参
         * @return 自定义
         */
        @Override
        public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
            Object invoke = null;
            if ("sum".equals(method.getName())) {
                System.out.println("增强方法/方法扩展---before两数sum方法执行");
                invoke = method.invoke(obj, objects);
                System.out.println("增强方法/方法扩展---after两数sum方法执行");
            } else if ("div".equals(method.getName())) {
                System.out.println("增强方法/方法扩展---before两数div方法执行");
                invoke = method.invoke(obj, objects);
                System.out.println("增强方法/方法扩展---after两数div方法执行");
            }
            return invoke;
        }
    }
    
    /*单纯的为main方法（也是为了newProxyInstance方法中参数---当前类加载器）新建的类*/
    class Test {
        public static void main(String[] args) {
            //newProxyInstance方法参数解析
            //      第一个参数ClassLoader类型：             传入当前类（注意不是接口实现类！）的类加载器
            //      第二个参数Class【】数组类型：            传入需要动态代理的接口类型--->接口名.class
            //      第三个参数InvocationHandler接口类型：    传入实现了InvocationHandler接口的实现类（可以是匿名实现类，但当前需要作出较多业务，所以新建一个类）
            //      返回值类型： Class数组类型参数类型
            UserDao userDao = (UserDao) Proxy.newProxyInstance(Test.class.getClassLoader(), new Class[]{UserDao.class}, new JDKDynamicProxyDemo(new UserDaoImpl()));
            int sum = userDao.sum(1, 10);
            System.out.println(sum);
            //输出结果：
            //  增强方法/方法扩展---before两数sum方法执行
            //  两数sum方法执行ing
            //  增强方法/方法扩展---after两数sum方法执行
            //  11
    
            int div = userDao.div(199, 10);
            System.out.println(div);
            //输出结果：
            //  增强方法/方法扩展---before两数div方法执行
            //  两数div方法执行ing
            //  增强方法/方法扩展---after两数div方法执行
            //  19
        }
    }
    ```





### AOP-术语

1. 连接点
   * 一个类或接口中，哪些方法可以被增强（代理对象对方法进行增强），那么这些方法就叫做连接点
2. 切入点
   * 真正被代理对象进行增强的方法，被称为切入点
3. 通知（增强）
   * 方法中实际被代理对象增强的逻辑部分，就被称为通知（增强）
   * 通知有多种类型
     * 前置通知：代理对象增强方法执行 之前执行
     * 后置通知：代理对象增强方法执行 之后执行
     * 环绕通知：代理对象增强方法执行 前后都执行
     * 异常通知：代理对象增强方法执行出现异常 执行
     * 最终通知：相当于finally最终代码块；无论怎样都执行
4. 切面
   * 切面表示 动作
   * 将“通知（增强）”应用到“切入点”的过程称为 “切面”



### AOP操作-准备工作

* Spring框架一般都是基于AspectJ实现AOP操作
* 什么是AspectJ：
  * AspectJ不是Spring框架组成部分，是一个独立的AOP框架，通常将AspectJ和Spring组合使用，进行AOP操作

* 基于AspectJ实现AOP操作（两种方式）：
  * 基于XML配置文件实现
  * 基于注解方式实现（常用）

* 准备工作
  * 在工程中，除去引入使用Spring框架的核心基本jar包（bean、core、context、expression；外加日志jar包commons-logging）外
    * aop---基于注解方式创建对象时引入的依赖（很常用；但不能算作真正的Spring核心jar包）
    * beans
    * context
    * core
    * expression
    * 外加日志依赖 commons-logging
  * 还要引入AOP相关依赖（在以上jar包基础上引入）
    * aspects
    * cglib
    * aopalliance
    * aspect.weaver
* 切入点表达式
  * 切入点表达式作用：清楚针对哪个类的哪个方法进行增强
  * 语法结构：execution ( [访问修饰符] [返回类型-可省略] [类全路径] [方法名称] ([参数列表]) )
    * 举例1：对com.lixc.spring5.dao包下的DemoDao类demoMethod方法进行增强
      * execution (* com.lixc.spring5.dap.DemoDao.demoMethod(..))
    * 举例2：对com.lixc.spring5.dao包下的DemoDao类所有方法进行增强
      * execution (* com.lixc.spring5.dap.DemoDao.*(..))
    * 举例3：对com.lixc.spring5.dao包下的所有类所有方法进行增强
      * execution (* com.lixc.spring5.dap.* .*(..))









### AOP操作-AspectJ注解方式（常用）

1. 创建类（被增强类），定义方法
2. 创建增强类（编写增强逻辑）
   * 增强类中创建不同方法，不同方法对应不同的通知（增强）类型（前置、后置、环绕、异常、最终）

3. 进行通知的配置
   * Spring配置文件中开启组件扫描（注解扫描）---也可以使用配置类
   * 为 增强类 和 被增强类 通过注解方式创建对象（bean管理操作）
   * 增强类添加@Aspect注解（翻译：切面、方面）
   * Spring配置文件中 开启AspectJ生成自动代理对象---配合@Acpect注解使用
4. 配置不同类型的通知
   * 在增强类（创建对象注解、生成代理对象注解）中，在作为通知方法之上添加通知类型注解，并使用切入点表达式赋值
5. 进行测试---创建对象及调用方法要使用到的是被增强类



基于AspectJ通过注解方式实现AOP操作细节：

* 公共（相同）切入点抽取
  * 将相同的切入点表达式（execution(....)）进行抽取，使用@Pointcut(value="execution(...)")注解进行进行抽取
  * 其他通知注解调用@Pointcut注解修饰的方法即可：@Around(value="samePoint()")

* 多个增强类对方法进行增强---设置增强类优先级
  * 类上方定义@Order注解，@Order(数字类型值)，数值越小优先级越高
* 基于AspectJ框架 纯注解方式 实现AOP操作
  * 配置文件改为配置类
    * @Configuration
    * @ComponentScan( basePackages="扫描路径" )
    * @EnableAspectJAutoProxy
      * @EnableAspectJAutoProxy( proxyTargetClass=true )；该属性及属性值可不写；默认就是这种形式
  * 之后创建对象时不再使用加载配置文件方式，改为加载配置类即可






### AOP操作-AspectJ配置文件方式

* 创建增强类和被增强类，并定义相关方法
* Spring配置文件中创建两个类对象
* Spring配置文件中配置切入点







## 4.JdbcTemplate

### JdbcTemplate概念和准备

* 什么是JdbcTemplate

  * Spring框架对JDBC进行了封装，使用JdbcTemplate方便实现数据库操作

* 准备工作

  1. 引入相关jar包

     * 原始jar包基础
     * druid、mysql
     * spring-jdbc、spring-orm（Spring框架整合其他框架（hibernate、mybatis）~~操作数据库~~需要导入该依赖）、spring-tx（针对事务相关依赖）

  2. Spring配置文件配置数据库连接池

     * ```xml
       <!--数据库连接池-->
       <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
           <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
           <!--<property name="url" value="jdbc:mysql://localhost:3306/book"/>-->
           <property name="url" value="jdbc:mysql:///book"/>
           <property name="username" value="root"/>
           <property name="password" value="lz001214"/>
       </bean>
       ```

  3. 配置JdbcTemplate对象，注入DataSource

     * ```xml
       <!--JdbcTemplate对象-->
       <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
           <!--调用JdbcTemplate父类setDataSource方法-注入DataSource数据源信息-->
           <property name="dataSource" ref="dataSource"/>
       </bean>
       ```

  4. 创建Service类、Dao类，在dao注入JdbcTemplate对象

     * ```java
       <!--开启组件扫描-->
       <context:component-scan base-package="com.lixc.spring5"/>
           
       @Repository
       public class UserDao {
           @Autowired
           private JdbcTemplate jdbcTemplate;
       }
       
       @Service
       public class UserService {
           @Autowired
           private UserDao userDao;
       }
       ```



### JdbcTemplate操作数据库

#### JdbcTemplate 添加、修改和删除

1. 创建Bean模型（Model）对应数据库
2. 编写service和dao
   * jdbcTemplate.update(String sql, Object... args)；方法接收DML语句完成增删改操作
3. 测试类





#### JdbcTemplate 查询

##### 返回某个值

* jdbcTemplate.queryForObject( String sql,Class<T> requiredType )
  * 参数一：DQL语句
  * 参数二：返回类型class

##### 返回对象

* jdbcTemplate.queryForObject( String sql, RowMapper<T> rowMapper, Object... args )
  * 参数一：DQL语句
  * 参数二：RowMapper
    * Spring框架中封装的接口，针对返回的不同类型，使用该接口实现类（Spring提供，也可自定义）完成对应数据封装
      * new BeanPropertyRowMapper<Book>(Book.class)
  * 参数三：可变参数---对应DQL语句中？占位符的具体值

##### 返回集合

* List<T>  XXX =  jdbcTemplate.query( String sql, RowMapper<T> rowMapper, Object... args )
  * 参数一：DQL语句
  * 参数二：使用RowMapper接口实现类完成不同数据类型（泛型）对应数据封装---封装为List<T>
    * List<Book>  bookList =  jdbcTemplate.query( sql, new BeanPropertyRowMapper<Book>(Book.class) );
  * 参数三：可变参数





#### JdbcTemplate 批量操作

* jdbcTemplate.batchUpdate( String sql, List<Obejct[]> batchArgs );
  * 参数一：DML语句
  * 参数二：添加多条记录数据的**Obejct数组的集合**







## 5.事务管理

### 事务概念

* 什么是事务：事务保证了数据的一致性，是数据库操作的最基本单元；逻辑上的一组操作，all success or all fail
* 事务特性---ACID：
  * Atomicity：原子性
  * Consistency：一致性
  * Isolation：隔离性
  * Durability：持久性



### 搭建事务操作环境

1. 创建数据库表

2. 创建业务层、dao层；完成对象创建和注入

   * JdbcTemplate注入dataSource

   * dao注入JdbcTemplate
   * service注入dao



### Spring事务管理介绍

* 事务添加在JavaEE三层结构中的service层（业务逻辑层）
* Spring框架中事务管理操作的方式
  * 编程式事务管理（不去使用，操作繁琐）
    * 类似在一个方法中手动开启事务，异常回滚，正常提交
  * 声明式事务管理
* 声明式事务管理实现：
  * 基于注解方式
  * 基于XML配置文件方式
* Spring中进行声明式事务管理，底层就使用到了AOP原理
* Spring 事务管理API
  * Spring中提供了PlatFormTransactionManager接口，代表事务管理器，该接口针对不同框架提供了不同实现类
  * PlatFormTransactionManager接口---事务管理器
    * 实现类DataSourceTransactionManager
    * 使用JdbcTemplate、Mybatis框架可以使用DataSourceTransactionManager实现类
    * 整合Hibernate框架可以使用HibernateTransactionManager实现类





### 声明式事务管理（基于注解）

1. Spring配置文件中配置事务管理器---创建PlatFormTransactionManager实现类对象
   * DataSourceTransactionManager---属性DataSource
2. Spring配置文件中，开启事务注解
   * 引入名称空间：tx
   * 开启事务注解
     * tx:annotation-driven transaction-manager=""
3. 在业务类之上（或方法之上）添加@Transactional事务注解
   * 注解在类上面，整个类的所有方法开启事务
   * 注解在方法上面，该方法开启事务

* ```java
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:tx="http://www.springframework.org/schema/tx"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                             http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                             http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
  
      <!--创建数据库连接池DataSource-->
      <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
          <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
          <property name="url" value="jdbc:mysql:///dbtest"/>
          <property name="username" value="root"/>
          <property name="password" value="lz001214"/>
      </bean>
      <!--创建JdbcTemplate对象-->
      <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
          <property name="dataSource" ref="dataSource"/>
      </bean>
      <!--开启组件扫描-->
      <context:component-scan base-package="com.lixc.spring5"/>
      <!--创建事务管理器-->
      <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <property name="dataSource" ref="dataSource"/>
      </bean>
      <!--开启事务注解-->
      <tx:annotation-driven transaction-manager="transactionManager"/>
  </beans>
  ```





### 声明式事务管理参数配置

* @Transactional事务注解事务相关参数配置
  * propagation：事务传播行为
    * REQUIRED：
      * 两个事务，事务A中的方法1调用事务B中的方法2，方法2会加入到事务A中
    * REQUIRED_NEW：
      * 事务A方法A，事务B方法B；方法A执行会开启事务A，在方法A中调用方法B，方法B会单独开启事务B，此时方法A的事务是外层事务，方法B的事务是内层事务；两者互不相干（任何事务异常回滚不会影响到另一个事务的正常提交）
    * SUPPORTS：
      * 调用它的如果有事务在运行，该SUPPORTS属性值对应的方法会加入到事务中；否则可以不运行在事务中（例如普通方法调用SUPPROTS修饰的事务方法，不会开启事务）
  * isolation：事务隔离级别
    * 通过设置事务隔离级别，解决读问题（脏读、不可重复读、幻读）
  * timeout：超时时间
    * 事务在指定时间内若未结束，则超时处理---事务回滚
    * Spring框架中默认事务超时时间为-1；永不超时
    * 设置时间：秒级单位
  * readOnly：是否只读
    * 默认false：DQL、DML语句都可执行
    * 设置为readOnly = true：只可执行DQL语句
  * rollbackFor：回滚
    * 设置出现哪些异常进行回滚
  * noRollbackFor：不回滚
    * 设置出现哪些异常不进行回滚





### 声明式事务管理（基于XML）



### 完全注解声明式事务管理---SpringBoot对Spring做封装

* 创建配置类---替代Spring配置文件
  * @Configuration
  * @ComponentScan（basePackages=“com.lixc.spring5”）
  * @EnableTransactionManagement    开启事务
  * 添加方法创建数据库连接池对象---方法注解@Bean
  * 添加方法创建JdbcTemplate对象---方法注解@Bean
  * 添加方法创建DataSourceTransactionManager事务管理器对象---方法注解@Bean









Java8API文档：https://www.matools.com/api/java8

ctrl+H查看类结构

## 6.Spring5新功能

1. Spring5框架的代码基于Java8，运行时兼容JDK9，许多不建议使用的类和代码在代码库中删除
2. Spring5.0框架自带了通用的日志封装
   1. Spring5官方建议使用Log4j2
   2. Spring5框架整合Log4j
      * 引入jar包
      * 创建log4j2.xml配置文件（文件名称固定！）
      * private static Logger log = LoggerFactory.getLog(类名.class)；//注意使用到的包为org.slf4j

3. Spring5核心容器支持@Nullable注解
   * @Nullable注解可以作用在方法、属性、参数之上，表示方法返回、属性值、参数值可以为空
4. Spring5核心容器支持函数式风格GenericApplicationContext
5. Spring5框架支持整合JUnit5
   * Spring整合JUnit4
   * Spring整合JUnit5



### Spring框架新功能 SpringWebFlux

* SpringWebFlux介绍

* 响应式编程
  * Java中实现
  * Re
* WebFlux执行流程和核心API
* SpringWebFlux（基于注解编程模型实现）
* SpringWebFlux（基于函数式编程模型实现）









## Spring Framework

* Spring Framework特性
  * 非侵入式
  * 控制反转
  * 面向切面编程
    * Java是纵向继承，Spring提供了面向切面编程概念，是面向对象的补充；横向对方法进行增强
  * 容器
    * Tomcat服务器中就包含Servlet容器：帮助管理我们的Servlet及Servlet生命周期
    * Spring IOC就是一个容器
    * 屏蔽了组件创建过程中的大量细节
  * 组件化
    * Spring中交给IOC容器所管理的对象，在IOC容器中就称为组件
  * 声明式
    * 编程式：手写业务代码逻辑
    * 声明式：声明需求后由框架实现某些功能
  * 一站式
* Spring Framework五大功能模块
  * Core Container
  * AOP&Aspect
  * Testing
  * Data Access/Integration
  * Spring MVC

## IOC

### IOC容器

> 容器表示可以帮助我们管理对象的生命周期

* DI：Dependency Injection 依赖注入

  * 依赖注入就是Spring所管理的对象中的属性进行赋值
  * DI是IOC的另一种表达方式：即组件以一些预定好的方式接收来自容器的资源注入。
  * IOC是控制反转的一种思想，DI是对IOC的一种具体实现

* Maven工程中，导入Spring最核心的IOC基本依赖

  * 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包
  * 导入junit测试依赖

* 获取Bean的三种方式(ioc.getBean(...);)

  * 根据id获取---需要强转
  * 根据类型获取---注意Spring配置文件中会出现id不同，Bean类型相同的情况
    * 该方式使用最多
    * 因为Spring配置文件中无需设置相同类型的多个Bean（目的是为了获取多个不同的bean实例），可以设置bean标签为多例
  * 根据id和类型获取

* 扩展

  * 若组件类（IOC容器管理的bean对象）实现了接口，根据接口类型可以获取bean吗

    * 可以；前提是bean唯一---只能有一个类实现接口

  * 一个接口有多个实现类，且都配置了bean，不能根据接口类型获取bean---bean不唯一

  * ```java
    interface Person{}
    
    class Student implements Person{}
    
    <bean id="student" class="com.lixc.spring.pojo.Student"/>
    
    ApplicationContext ioc = new ClassPathXMLApplicationContext("applicationContext.xml");
    Person person = ioc.getBean(Person.class);
    ```

  * 即bean唯一的前提下，bean的类型、bean所继承的类的类型、bean所实现的接口的类型 都可以获取bean实例



### 基于XML实现Bean管理（管理Bean）

* 依赖注入
  * set方式注入
    * property标签：通过成员变量对应的set方法进行赋值
  * 含参构造注入
    * constructor-arg标签
* bean生命周期
  * ConfigurableApplicationContext是ApplicationContext的子接口，扩展了IOC容器的刷新和关闭的方法
  * bean的后置处理器
    * 实现BeanPostProcessor接口，重写初始化前后两个方法；
    * 需要将后置处理器添加到Spring配置文件，进行IOC容器的监听；
      * 注意不是针对一个bean生效，而是对整个IOC容器管理的bean都会执行
* Bean的自动装配
  * 根据指定的装配规则，对IOC容器中的bean对象的属性进行赋值；
  * bean标签中autowire属性
    * no、default属性值：不自动装配
    * byType：根据属性类型，去寻找IOC容器中符合条件的bean对象进行自动装配
      * 注意IOC容器中若存在多个相同bean类型，则会报错
    * byName：根据属性名（setXxx），去寻找IOC容器中符合条件的bean对象进行自动装配





### 基于注解实现Bean管理（管理Bean）

* 标识组件的常用注解
  * @Component：类标识为普通组件
  * @Controller：类标识为控制层组件
  * @Service：类标识为业务层组件
  * @Repository：类标识为持久层组件
* 组件扫描标签---包含和排除组件
  * include包含
    * 组件扫描标签中use-default-filters="false"
    * type = annotation 根据注解进行包含：expression = 注解的全路径
    * type = assignable 根据类类型进行包含：expression = 类的全路径
  * exclude排除
    * type = annotation 根据注解进行排除：expression = 注解的全路径
    * type = assignable 根据类类型进行排除：expression = 类的全路径
* 自动装配
  * 基于XML实现自动装配，还需要对被自动装配的属性进行set方法
  * 基于注解实现自动装配，无需定义set方法
    * @Autowired
      * 可以定义的位置：成员变量、set方法、构造器





## AOP

* AOP是OOP的补充和完善；面向对象编程采用纵向继承机制，面向切面标签是横向抽取机制
* 目的
  * 将非核心代码抽取出来交给切面进行管理
  * 还要将抽取出来的 作用在目标对象的方法
* 作用
  * 简化代码：抽取非核心代码、重复代码
  * 增强代码



### 术语

* 横切关注点

  * 从目标对象中抽取出的非核心业务

* 通知

  * 每个横切关注点上要做的业务都需要定义方法来进行实现，这样的方法就是通知方法

  * 前置通知：被代理的目标方法**前**执行

  * 返回通知：被代理的目标方法**成功结束后**执行

  * 异常通知：被代理的目标方法**异常结束后**执行

  * 后置通知：被代理的目标方法**最终结束后**执行

  * 环绕通知：使用try...catch...finally结构围绕**整个**被代理的目标方法，包括上面四种通知对应的所

    有位置

* 切面

  * 封装通知方法的类（封装横切关注点的类）

* 目标

  * 被代理的目标对象

* 代理

  * 向目标对象应用通知方法 所创建的对象

* 连接点

  * 抽取横切关注点的位置就是连接点（纯逻辑概念）

* 切入点

  * 本质就是一个表达式
  * 定位连接点的方式



### 基于AspectJ框架基于注解方式实现AOP

* 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包；添加依赖 spring-aspects
* Spring配置文件
  * 开启组件扫描context:component-scan
  * 开启自动生成代理对象 aop:aspectj-autoProxy
* 定义目标类、代理类；添加创建对象注解
* 代理类定义@Aspect注解
* 将切面中的通知方法添加通知注解（定义切入点表达式；将每个通知方法切入点表达式使用@Pointcut抽取出来）



* 切面优先级
  * 多个切面可以使用其中一个切面设定的公共的切入点表达式：全类路径+公共切入点方法名
  * @Order注解设定切面优先级：数值越小优先级越高 0-int最大值；默认优先级是Integer的最大值





## 声明式事务

### Spring整合junit4测试类

* 在Spring测试路径下创建测试类

* 添加两个注解

  * @Runwith(SpringJUnit4ClassRunner.class)

    * 指定当前测试类在Spring的测试环境中运行，此时可以通过注入方式直接获取IOC容器中的bean

      * 类中就可以注入bean：

        * ```java
          @Autowired
          private JdbcTemplate jdbcTemplate;
          ```

  * @ContextConfiguration("classpath:Spring配置文件")

    * 设置Spring测试环境的配置文件

* ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration("classpath:applicationContext.xml")
  public class JdbcTemplateTest {
      @Autowired
      private JdbcTemplate jdbcTemplate;
  
      @Test
      public void springTest() {
          String sql = "SELECT * FROM `t_user` WHERE `id`=?;";
          User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class), 1);
          System.out.println(user);////User{id=1, username='薇薇', password='lz001010', age=18, gender='女', email='vv@yeah.net'}
      }
  }
  ```





### JdbcTemplate

### 声明式事务

* 编程式事务：事务相关操作都需要通过编写代码来完成

* 步骤

  * Spring配置文件中
    * 开启组件扫描---通过注解创建对象
    * 创建数据源---使用德鲁伊数据源实现类
    * 创建Spring封装的JDBC的JdbcTemplate类---数据源依赖注入；数据库操作
    * 创建事务管理器实现类DataSourceTransactionManager---数据源依赖注入；用来配置注解驱动
    * 开启注解驱动tx:annotation-driven transaction-manager="transactionManager"
      * 注意transaction-manager这个属性的属性值默认就是transactionManager；
      * 若配置的事务管理器定义的bean的id就是transactionManager，注解驱动的该属性可以不写
    * 在类或方法上添加@Transactional注解即可开启事务管理

* 声明式事务的属性（@Transactional注解配置）

  * 只读

    * @Transactional( readOnly = true )
    * 该类或方法中只能进行DQL语句，若涉及DML操作则异常
      * java.sql.SQLException: Connection is read-only. Queries leading to data modification are not allowed

  * 超时

    * @Transactional( timeOut = 3 )
    * 事务执行过程出现问题（Java代码、数据库、网络连接等等），应该超时回滚，释放资源
      * org.springframework.transaction.TransactionTimedOutException: Transaction timed out: deadline was Thu Jun 01 22:56:58 CST 2023

  * 回滚策略

    * 声明式事务默认针对运行时异常，编译时异常比做会馆

    * rollbackFor：回滚涉及的Class类型的对象（一般不去使用）

    * rollbackForClassName：回滚对象涉及的全类名（一般不去使用）

    * noRollbackFor：不回滚涉及的Class类型的对象

      * ```
        @Transactional(noRollbackFor = ArithmeticException.class)
        
        //配合方法中的数学异常进行回滚
        int i=10/0;
        
        不会进行回滚
        ```

    * noRollbackForClassName：不回滚对象涉及的全类名

  * 事务隔离级别

    * 

















* 命名空间、名称空间、约束
  * * util命名空间
      * list、map
    * p命名空间
      * 在bean标签中，通过bean的class属性值，一并设置 p:属性名（属性值为字面量） 或 p:属性名-ref（属性值为类类型，引用）
    * context命名空间
      * 引入配置文件（外部属性文件）
      * 开启组件扫描



* IDEA快捷键
  * XML文件中大写CD，回车就是CDATA区
  * Ctrl+H 查看类的结构
  * Shift+Alt+↑ 代码上移
  * lambok技术-简化实体类开发

