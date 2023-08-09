# Maven

* P48老师讲的Import导入项目，如果不想每次导入都要给项目设置maven的地址。可以设置Idea新打开项目的默认配置，我的2021.2版本具体位置：File->New Projects Setup -> Settings for New Projects，在这里面配置好maven，以后再有新项目都不用配置了。

## Maven概述

### 为什么学习和使用Maven

1. Maven作为依赖管理工具
2. Maven作为构建管理工具
3. 结论：
   * 管理规模庞大的jar包，需要专门的工具
   * 脱离IDE环境执行构建操作（编译、打包等）；需要专门的工具

### 什么是Maven

* Maven是Apache推出的**专门为Java提供依赖和构建管理支持的工具**

#### 构建

* **Java项目开发中，构建指的是使用【原材料生产产品】的过程**
  * 原材料
    * Java源代码
    * 图片
    * 配置文件
    * 基于HTML的Thymeleaf文件
    * 。。。
  * 产品
    * 一个可以在服务器运行的项目

* 构建过程包含的主要环节
  * 清理：删除上一次构建结果
  * 编译：Java源程序编译成字节码文件
  * 测试：运行提前准备好的测试程序
  * 报告：针对测试结果生成一个全面的信息
  * 打包：Java工程---jar包；Web工程---war包
  * 安装：把一个Maven工程经过打包生成的jar包或war包存入Maven仓库
  * 部署：
    * jar包：将jar包部署到Nexus私服服务器上
    * war包：借助相关Maven插件（例如cargo），将war包部署到Tomcat服务器上



#### 依赖

* 例如A工程中使用到了B工程中的接口、类等资源，就可以说是A依赖B
  * 例如junit的jar包依赖了hamcrest的jar包
* 依赖管理中要解决的具体问题
  * jar包下载：
    * 使用Maven，jar包会从规范的远程仓库下载到本地
  * jar包之间的依赖
    * 通过依赖的传递性自动完成
  * jar包之间的冲突
    * 通过对依赖的配置进行调整，让某些jar包不会被导入



#### Maven的工作机制

![image-20230526120603394](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230526120603394.png)





## Maven核心程序解压和配置

* Maven地址：
  * 官网：https://maven.apache.org/

1. ==Maven下载==：~~https://dlcdn.apache.org/maven/maven-3/3.9.2/binaries/apache-maven-3.9.2-bin.zip~~
   * 注意 .zip 是windows系统压缩包；tar.gz是Linux系统压缩包
   * Maven版本过高可能和IDEA不匹配，选用低版本Maven或升级IDEA
     * https://dlcdn.apache.org/maven/maven-3/3.6.3/binaries/

2. ==Maven解压==
   * 解压到非中文、无空格的目录
   * 解压目录中，着重关注核心配置文件：conf/settings.xml

3. ==指定本地仓库==

   * 本地仓库默认值：用户家目录/.m2/repository。将来Maven仓库中的jar包越来越多会拖累C盘，所以要自定义

   * 自定义Maven本地仓库：

     * ```xml
       <localRepository>D:\PlantingTrees\java_install\3-Maven_Repository</localRepository>
       ```

4. ==配置阿里云提供镜像仓库==

   * Maven下载jar包默认访问的国外的中央仓库，很慢；

   * 自定义使用阿里云的镜像仓库，配置方式

     * 将原有mirrors下的mirror例子注释；

     * ```xml
       <!-- 由阿里云提供的Maven中央仓库镜像 -->
       <mirror>
       	<id>nexus-aliyun</id>
       	<mirrorOf>central</mirrorOf>
       	<name>Nexus aliyun</name>
       	<url>http://maven.aliyun.com/nexus/content/groups/public</url>
       </mirror>
       ```

5. ==配置Maven工程的基础JDK版本==

   * Maven默认JDK1.5版本；

   * 修改profiles标签默认使用JDK版本

     * ```xml
       <profile>
       	<id>jdk-1.8</id>
       	<activation>
       		<activeByDefault>true</activeByDefault>
       		<jdk>1.8</jdk>
       	</activation>
       	<properties>
       		<maven.compiler.source>1.8</maven.compiler.source>
       		<maven.compiler.target>1.8</maven.compiler.target>
       		<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
       	</properties>
       </profile>
       ```



6. ==配置环境变量==（HOME指向bin目录上一级，PATH是指向bin目录）

   1. 检查JAVA_HOME配置

      * Maven本身也是Java语言开发的程序，必须基于JDK来运行，通过JAVA_HOME来确认JDK安装位置

      * ```
        JAVA_HOME
        D:\javadevelop\jdk-1.8
        ```

   2. 配置MAVEN_HOME

      * ```
        MAVEN_HOME
        D:\PlantingTrees\java_install\3-Maven\apache-maven-3.9.2
        ```

   3. 配置PATH

      * ```
        %MAVEN_HOME%\bin
        ```

      * 

   4. 验证

      * cmd黑窗口

        * java -version

        * mvn -v

        * ```
          //mvn-v
          C:\Users\Administrator>mvn -v
          Apache Maven 3.9.2 (c9616018c7a021c1c39be70fb2843d6f5f9b8a1c)
          Maven home: D:\PlantingTrees\java_install\3-Maven\apache-maven-3.9.2
          Java version: 1.8.0_40, vendor: Oracle Corporation, runtime: D:\javadevelop\jdk-1.8\jre
          Default locale: zh_CN, platform encoding: GBK
          OS name: "windows 8.1", version: "6.3", arch: "amd64", family: "windows"
          
          //echo %MAVEN_HOME%查看MAVEN_HOME配置的信息（echo %JAVA_HOME%查看JAVA_HOME的配置信息）
          C:\Users\Administrator>echo %MAVEN_HOME%
          D:\PlantingTrees\java_install\3-Maven\apache-maven-3.9.2
          //echo %PATH%查看PATH配置的信息
          C:\Users\Administrator>echo %PATH%
          E:\vmachine\machine\bin\;C:\WINDOWS\system32;C:\WINDOWS;C:\WINDOWS\System32\Wbem;C:\WINDOWS\System32\WindowsPowerShell\v1.0\;C:\WINDOWS\System32\OpenSSH\;D:\Mysql\bin;C:\Program Files\Git\cmd;C:\Program Files\MySQL\MySQL Server 5.5\bin;D:\javadevelop\jdk-1.8\bin;D:\javadevelop\jdk-1.8\jre\bin;D:\googleDownload\tortoiseSVN\bin;D:\mysql\MySQL Server 5.5\bin;D:\PlantingTrees\java_install\3-Maven\apache-maven-3.9.2\bin;C:\Users\Administrator\AppData\Local\Microsoft\WindowsApps;
          ```





## 使用Maven：命令行环境

### 根据坐标创建Maven工程



### 在Maven工程中编写代码



### 执行Maven构建命令

mvn clean install

### 创建Maven版本的Web工程



### 让Web工程依赖Java工程



### 测试依赖的范围

* scope标签

* ```xml
  <dependency>
      <groupId>...</groupId>
      <artifactId>...</artifactId>
      <version>...</version>
      <!-- 默认就是compile的依赖方法，还有provided/test等 -->
      <scope>compile</scope>
  </dependency>
  ```

### 测试依赖的传递性



### 测试依赖的排除

* exclusions / exclusion / groupId   artifactId

### 继承

* 创建父工程，设置打包方式packaging为pom

  * 只有打包方式为pom的Maven工程能够管理其他的Maven工程。
  * 打包方式为pom的Maven工程中不写业务代码，只专门用来管理其他Maven工程

* 创建模块工程

  * 类似于IDEA中的module
  * 需进入创建的父工程的根目录，来创建模块工程

* 父工程pom.xml文件会自动发生变化

  * ```xml
    <!-- 聚合的配置 -->
    <modules>
        <module>模块工程名---artifactId</module>
        <module></module>
        <module></module>
    </modules>
    ```

* 模块工程/子工程pom.xml文件自动发生变化

  * ```xml
    <!--为当前工程配置/指定父工程-通过指定父工程完整坐标匹配父工程-->
    <parent>
        <groupId>父工程groupId---com.lixc.项目名称</groupId>
        <artifactId>父工程artifactId---工程名</artifactId>
        <version>版本---1.0-SNAPSHOT</version>
    </parent>
    ```

  * 模块工程/子工程的groupId和version和父工程一致的话，可以省略

    * 一般来讲子工程groupId和version是一致的，所以子工程会不写这两个坐标，只保留artifactId（工程名）





* 根本目的是：父工程为模块工程/子工程做统一的依赖信息的管理

  * 父工程：

    * ```xml
      <properties>
          <!-- 创建自定义的属性标签 -->
          <!-- 下面依赖标签中可使用${标签名}来动态获取标签值 -->
          <spring5.version>5.2.6.RELEASE</spring5.version>
      </properties>
      
      
      
      <!-- 在父工程中统一管理依赖信息 -->
      <!-- 被管理的依赖并没有真正被引入到工程 
      		即使父工程配置了依赖的管理，子工程需要使用哪一个依赖还是要明确
      -->
      <dependencyManagement>
          <dependencies>
              <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-core</artifactId>
                  <version>${spring5.version}</version>
              </dependency>
              
              <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-context</artifactId>
                  <version>${spring5.version}</version>
              </dependency>
          </dependencies>
      </dependencyManagement>
      ```

  * 模块工程/子工程：

    * ```xml
      <!-- 被管理的依赖并没有真正被引入到工程 
      		即使父工程配置了依赖的管理，子工程需要使用哪一个依赖还是要明确
      -->
      <!-- 子工程还是要对依赖进行配置，只是无需进行版本号的确定；版本号不是不能写只是不需要写 -->
      <!-- 子工程不配置 或 配置和父工程管理的version一致的版本号，不影响
      	 子工程配置和父工程管理的version不一致，覆盖
      	 绝大情况还是使用父工程统一管理的版本
      -->
      ```

  

  * 





### 聚合









## 使用Maven：IDEA环境

* 创建Maven工程---父工程
  * pom文件中打包方式packaging可以不指定pom，IDEA会在 该工程创建子模块后自动 配置
    * 而且本来也会自动创建modules进行聚合配置（命令行、IDEA都会自动配置）
* 创建Maven工程---模块工程/子工程



* 进行测试
  * 可以直接在Maven下lifeCycle下面点击test或更大的生命周期（例如install）
  * Plugins也可以双击执行
  * 想测试时clean之后再install
    * 双击clean完成后，双击install
    * 或者点击M图标（相当于执行命令行），输入mvn clean install
      * 注意选中右上角执行的具体工程
  * 跳过测试，点击蓝色闪电

* 在pom.xml文件，右键选中Open in Terminal  
  * 在终端打开---相当于在命令行打开
  * 因为执行Maven命令行时，cmd黑窗口需要在pom.xml同级目录下才能执行
  * 就可以执行mvn命令了





* 创建Web工程
  * pom文件中打包方式配置war
    * packaging---war















## 其他核心概念

