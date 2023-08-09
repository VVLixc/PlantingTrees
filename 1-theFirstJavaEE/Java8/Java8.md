# Java8新特性

* Java8两个最重要的改变：
  * Lambda表达式
  * Stream API





* 速度更快
* 代码更少（Lambda）
* 强大的Stream API
* 便于并行
* 最大化减少空指针异常：Optional
* Nashorn引擎，允许在JVM上运行JS应用



## Lambda表达式

* 函数式接口、方法引用和构造器引用可以看做是Lamda表达式的一个延续

* 为什么使用Lambda表达式

  * Lambda是一个**匿名函数**；可以把Lambda表达式理解为**一段可传递的代码**（将代码向数据一样传递）。
  * 使用Lambda方便写出更简洁、更灵活的代码
  * 更紧凑的代码风格，使Java语言表达能力得到提升

* 格式

  * ```java
    //Lamda表达式：
    举例：(o1,o2) -> Integer.compare(o1,o2);
    
    ->：表示Lamda操作符 或 箭头操作符
    ->左边：表示Lambda形参列表（其实就是接口中抽象方法的形参列表，参数类型被省略了）
    ->右边：表示Lambda体（其实就是重写的抽象方法的方法体）
    ```

* **Lambda表达式的使用（6种情况）**

  * **无参，无返回值**

    * ```java
      Runnable runnable = () -> {System.out.println("......");};
      ```

  * **需要一个参数，无返回值**

    * ```java
      Consumer<String> consumer = (Stirng str) -> {System.out.println(str);};
      ```

  * **数据类型可以省略**，因为可由编译器推断得出，称为**“类型判断”**

    * ```java
      Consumer<String> consumer = (str) -> {System.out.println(str);};
      
      //再提一句 
      ArrayList<String> arrayList = new ArrayList<>(); //后面的空泛型符默认就是String->类型推断
      int[] i = {1,2,3};//类型推断
      ```

  * 若**只需一个参数**时，**参数的小括号可以省略**

    * ```java
      Consumer<String> consumer = str -> {System.out.println(str);};
      ```

  * 需要**两个或两个以上参数，多条执行语句，并且可以有返回值**

    * ```java
      Comparator<Integer> comparator = (i1,i2) -> {
          System.out.println("多条执行语句");
          return Integer.compare(i1,i2);
      };
      ```

  * 当**Lambda体只有一条语句**时，**return和{ } 大括号都可以省略**

    * ```java
      Comparator<Integer> comparator = (i1,i2) -> Integer.compare(i1,i2);
      ```





* Lambda表达式使用 **总结：**
  * ->左边：
    * 表示Lambda参数列表；
    * 参数类型可以省略；
    * 参数若有一个参数，()小括号可以省略；
    * 参数若没有参数或包含两个及两个以上，不能省略 () 小括号。
  * ->右边：
    * 表示Lambda体
    * 通常使用 {} 大括号包裹；
    * 若只有一条语句，return 和 {} 大括号都可省略。



* Lambda表达式本质：
  * 作为接口的实例
  * 作为**函数式接口**的实例
  * 什么时间使用Lambda表达式：
    * 当需要对一个函数式接口实例化时，可使用Lambda表达式。	
    * 所以之前使用匿名实现类（匿名内部类）来表示的，现在都可以用Lambda表达式来写





## 函数式（Functional）接口

* 只包含一个抽象方法的接口，就称为函数式接口（一般会带有@FunctionalInterface注解）
* 接口上使用@FunctionalInferface注解，可以检验是否为函数式接口。同时javadoc也会包含该接口为函数式接口的声明；
* java.util.function包下提供了Java8丰富的函数式接口
* 所以之前使用匿名实现类表示的现在都可以用Lambda表达式来写



* Java内置四大核心函数式接口
  * ![image-20230426155104237](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426155104237.png)
  * ![image-20230426160540582](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426160540582.png)



* 何时使用函数式接口：
  * 开发中若需要定义一个函数式接口（只能有一个抽象方法），首先查询已有的JDK8提供的函数式接口是否有提供满足需求的函数式接口，满足直接使用即可



## 方法引用与构造器引用

### 方法引用

* 方法引用使用情景

  * 当要传递给Lambda体的操作，已经有实现的方法，可使用方法引用

* 方法引用使用要求

  * 要求接口中的抽象方法的参数列表和返回值，和方法引用中的参数列表和返回值保持一致。
    * 主要针对 对象 :: 实例方法 和 类 :: 静态方法
  * 当函数式接口方法第一个参数是要引用方法的调用者，第二个参数是要引用方法的参数（或无参数）时；
    * 类 :: 非静态方法

* 方法引用本质就是Lambda表达式，而Lambda表达式是作为函数式接口的实例；

  * 所以方法引用也是函数式接口的实例

* 使用格式

  * 类（或对象） :: 方法名

* 使用格式分为如下三种情况

  * 对象 :: 非静态方法（实例方法）

  * 类 :: 静态方法

  * 类 :: 非静态方法

  * ```java
    //Lambda表达式
    Consumer<String> consumer = str -> System.out.println(str);
    consumer.accept("Java");
    
    //方法引用
    //因为println方法非静态方法，可以使用 对象::方法名； 或 类名::方法名
    PrintStream printStream = System.out;
    Consumer<String> consumer1 = printStream :: println;
    consumer1.accept("Java1");
    Consumer<String> consumer2 = PrintStream :: println;
    consumer2.accept("Java2");
    
    //静态方法 
    //Lambda表达式
    Comparator<Integer> comparator = (i1,i2) -> Integer.compare(i1,i2);
    System.out.println(comparator.compare(11,12));
    //方法引用
    Comparator<Integer> comparator = Integer::compare;
    System.out.println(comparator.compare(11,12));
    //Lambda表达式
    Function<Double,Long> function = d -> Math.round(d);
    System.out.println(function.apply(12.3));
    //方法引用
    Function<Double,Long> function = Math::round;
    System.out.println(function.apply(12.6));
    
    
    //类::实例方法(非静态方法)
    Comparator<String> com = (str1,str2) -> str1.compareTo(str2);
    Comparator<String> com = String::compareTo;
    System.out.println(com.compare("ads","adv"));
    ```





### 构造器引用

* 和方法引用类似，	
  * 函数式接口抽象方法的形参列表 和 构造器的形参列表一致；
  * 抽象方法的返回类型 即为 构造器所属类的类型

* ```java
  //Supplier<T>  T get();
  //调用Employee类的空参构造器
  //Lambda表达式
  Supplier<Employee> sup = () -> new Employee();
  System.out.println(sup.get());
  //构造器引用
  Supplier<Employee> sup=Employee::new;
  System.out.println(sup.get());
  
  //Function<T,R>  R apply(T t);
  //Lambda表达式
  Function<Integer,Employee> fun = id -> new Employee(id);
  System.out.println(fun.apply(12));
  Function<Integer,Employee> fun =Employee::new;
  System.out.println(fun.apply(12));
  ```



数组引用

* 可以把数组看作是一种特殊的类，写法就和构造器引用一致

* ```java
  //Lambda表达式
  Function<Integer,String[]> fun = length -> new String[length];
  String[] strArrs = fun.apply(5);
  System.out.println(Arrays.toString(strArrs));
  
  //数组引用
  Function<Integer,String[]> fun = String[]::new;
  String[] strArrs = fun.apply(10);
  System.out.println(Arrays.toString(strArrs));
  ```

  







## 强大的Stream API

* java.util.stream包下
* 使用Stream API可以对集合数据进行操作，就类似于使用SQL执行的数据库查询
* 简言之，Stream API提供了高效且易于使用的处理数据的方式



* 向MySQL、Oracle都属于关系型数据库
* MongDB、Redis等属于NoSQL的数据就需要Java层面来处理

* Stream和Collection集合的区别：
  * Collection集合属于静态的内存数据结构；主要面向内存，存储在内存中
  * Stream是有关计算的；主要面向CPU，通过CPU实现计算
  * Collection讲的是数据；Stream讲的是计算。
* 注意
  * Stream自己不会存储数据
  * Stream不会改变源对象，它们会返回一个持有结果的新Stream
  * Stream操作时延迟执行的。意味着会等到需要结果的时候才执行



* Stream操作的三个步骤
  * 创建Stream
  * 中间操作
    * 一个中间操作链，对数据源的数据进行处理
  * 终止操作（终端操作）
    * 一旦执行终止操作，就执行中间操作并产生结果（所以说Stream是延迟执行的），之后就不能再使用



* **创建Stream（四种方式---Java8新特性）**

  * 通过集合

    * Collection接口被扩展，提供了两个获取流的方法
      * stream()；返回一个顺序流
      * parallelStream；返回一个并行流

  * 通过数组

    * Arrays类的静态方法stream() 可以获取数组流
    * stream方法重载形式，处理对应基本数据类型的数组

  * 通过Stream类的of() 方法

    * 静态方法，可以接收任意数量的参数（of(T... values){...}）；

  * 创建无限流（使用比较少）

    * 使用静态方法

      * Stream . iterate();

      * Stream . generate();

      * ```java
        Stream.iterate(0,t -> t+2).limit(10).forEach(System.out::println);
        
        Stream.generate(Math::random).limit(10).forEach(System.out::println);
        ```

* **Stream的中间操作**

  * 多个中间操作可连接起来形成一个流水线，触发终止操作中间操作才会执行；在终止操作时会一次性全部处理，称为 “惰性求值”。
  * 筛选与切片
  * 映射
  * 排序
  * ![image-20230426182814693](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426182814693.png)
  * ![image-20230426184544821](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426184544821.png)
  * ![image-20230426190506004](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426190506004.png)



* **Stream的终止操作**
  * 匹配与查找
  * 归约
  * 收集
  * ![image-20230426203924242](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426203924242.png)
  * ![image-20230426205402581](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426205402581.png)
  * ![image-20230426205526782](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426205526782.png)
  * ![image-20230426210900953](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426210900953.png)
  * ![image-20230426211159791](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426211159791.png)







并行流

串行流

可通过parallel()  和sequential() 方法进行并行流和串行流之间的切换

* Stream API理解
  * Java8提供了一套API，通过这套API可对内存中的数据进行过滤、排序、映射、归约等操作。类似于SQL对数据库的相关操作





## Optional类

* Optional<T>：泛型T必须要是引用类型（通用）
* Optional 空指针异常

* java.util.Optional类可以说是一个容器类
  * 可以保存类型 T 的值，代表值存在；
  * 保存null，表示值不存在。
  * 可以更好避免空指针异常
* Opetional类
  * 就是为了在程序中规避空指针异常而创建的
  * 常用方法：
    * ofNullable(T t);
    * orElse(T t);
* ![image-20230426211750359](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230426211750359.png)

* 规避空指针

  * ```java
    Employee employee = null;
    Opetional<Employee> empOpetional = Opetional.ofNullable(employee);//ofNullable 传参可为null；会返回Opetional.empty
    Employee emp = emoOpetional.orElse(new Employee());//使用该方法避免空指针---空则新建一个指定的对象
    ```

* 









## 其他新特性

* 第三代日期类
* 接口增强：静态方法、默认方法
* JVM中使用Metaspace取代PermGen空间



GitHub进不去的同学可以在码云（gitee）搜索DevSidecar工具哦，一般都附带的教程，而且本人亲测有效

搜索dev-sidecar也是一样的哦

MySQL、ORACLE都属于关系型数据库







## 代理模式

静态代理：

* 实现Runnable接口来创建多线程

  * ```java
    class MyThread implements Runnable{} //被代理类
    class Thread implements Runnable{} //代理类
    
    MyThread mt = new MyThread();
    Thread thread = new Thread(mt);
    thread.start();
    ```

* 静态代理缺点

  * 代理类和目标对象的类都是在编译期确定，不易扩展
  * 同时每一个代理类只能为一个接口服务，开发中必然产生过多的代理

动态代理

* 优点
  * 在运行期间根据情况动态创建目标类的代理对象