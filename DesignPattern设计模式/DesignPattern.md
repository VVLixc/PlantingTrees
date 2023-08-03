# DesignPattern设计模式

> 软件工程中，设计模式Design Pattern是对软件设计中普遍存在（反复出现）的各种问题，所提出的解决方案。
>
> 设计模式出现在软件的位置：
>
> * 面向对象（oo）>>>功能模块【设计模式+算法（数据结构）】>>>框架【使用到多种设计模式】>>>架构【服务器集群】
>
> 金句：设计模式是面向对象的精髓；“懂了设计模式，你就懂了面向对象的分析和设计的精要”。

## 软件开发原则

> 设计模式的目的：
>
> * 耦合性、内聚性、可维护性、可扩展性、重用性、灵活性等多方面挑战；设计模式是为了让程序/软件具备更好的：
>   * 代码重用性
>   * 可读性
>   * 可扩展性（可维护）
>   * 可靠性
>   * 程序呈现低耦合、高内聚的特性
>
> 而要程序具有以上特性，就需要遵守设计模式的七大原则（23中设计模式的设计即要遵守该七大原则）：
>
> * 单一职责原则
> * 接口隔离原则
> * 依赖倒置原则
> * 里氏替换原则
> * 开闭原则
> * 迪米特法则
> * 合成/聚合复用原则
>
> 设计原则核心思想：
>
> * 找出应用中可能需要变化之处，独立出来，不要和不需要变化的代码混在一起
> * 针对接口编程，而不是针对实现编程
> * 交互对象之间的松耦合设计。

### 单一职责原则（SRP）

> 单一职责原则（SRP：Single Responsibility Principle）
>
> 介绍：
>
> * 对类来说，==一个类应该只负责一项职责==（但要考虑花销，即若少量的多个类似的职责可以考虑在一个类中定义多个方法；一定程度违反了单一职责原则）。
>
> SRP注意事项：
>
> * 降低类的复杂度，即一个类只负责一项职责
> * 提高类的可读性、可维护性
> * 降低变更引起的风险（比如A类有两个不同职责的方法，若需要改变其中一个方法，虽然很小但也可能引发另一个方法异常）
> * 通常情况下，应当遵守单一职责原则SRP：
>   * 只有逻辑足够简单，才可以在代码级违反单一职责原则；
>   * 只有类中方法足够少，才可以在方法级别保持单一职责原则。



### 接口隔离原则（ISP）

> 接口隔离原则（ISP：Interface Segregation Principle）
>
> 介绍：
>
> * 客户端不应该依赖它不需要的接口；即==一个类对另一个类的依赖（使用）应该建立在最小的接口上==。
>   * 比如X接口内有1，2，3，4，5五个方法，分别有B类、C类实现了该接口；
>     * 此时A类通过X接口去使用B类、D类通过X接口使用C类；
>       * 但A类真正需要的是1，2，3三个方法；D类真正需要的是1，4，5三个方法；接口内方法冗余且混乱
>       * 优化：
>         * 将X接口拆分为3个接口：X1接口包含方法1，X2接口包含方法2，3，X3接口包含方法4，5
>         * B类实现X1，X2接口；C类实现X1，X3接口
>         * 此时A类需要1，2，3三个方法可以直接通过使用B类方法完成；D类需要1，4，5三个方法可以直接通过使用C类方法完成。



### 依赖倒置原则（DIP）

> 依赖倒置原则（DIP：Dependence Inversion Principle）
>
> 介绍：
>
> * 高层模块不能依赖低层模块，二者都应依赖于抽象（抽象类、接口）
> * 抽象不应依赖细节，细节应该依赖抽象
> * 依赖倒置原则核心思想就是面向接口编程
> * 依赖倒置原则DIP设计理念：
>   * 相对于细节的多变性，抽象（抽象类、接口）会更稳定
>   * 以抽象为基础搭建的架构要比以细节为基础搭建的架构稳定的多
>   * Java中抽象指的就是抽象类、接口；细节指的就是具体的实现类
>   * 使用接口或抽象类的目的就是制定好规范（设计），而不涉及任何具体操作，将展现细节的任务交由它们的实现类完成
>
> 依赖关系传递的三种方式：
>
> * 接口传递
> * 构造方法传递
> * setter方法传递
>
> 依赖倒置原则注意事项和细节：
>
> * 低层模块尽量要有抽象类或接口（或两者都有），程序稳定性更好
> * 变量的声明类型尽量是抽象类或接口，这样变量引用和实际对象间就存在一个缓冲层，利于程序扩展和优化
>   * A b = new B();
> * 继承时遵循里氏替换原则



### 里氏替换原则（LSP）

> 里氏替换原则（LSP：Liskov Substitution principle）
>
> OO（Object Oriented）中的继承性思考说明：
>
> * 父类实现好的方法，实际就是在设定规范和契约，虽不强制，但若子类对这些方法任意修改，就会对整个继承体系造成破坏
> * 使用继承会给程序带来侵入性，程序可移植性降低，增加对象间耦合性；一个类被其他类所继承，该类做修改时就要时刻考虑到子类可能发生故障的问题
> * 编程中如何正确的使用继承：里氏替换原则
>
> 介绍：
>
> * 所有引用基类的地方，必须可以透明的使用其子类的对象（简单说就是使用子类替换掉父类，之前使用父类的地方不受影响）
> * 使用继承时，若遵守里氏替换原则，子类就要尽量避免重写父类方法
> * 里氏替换原则告诉我们，==继承实际让类之间的耦合性提高；适当情况下可通过继承一个更通俗的基类，原有继承关系去除，采用聚合、组合、依赖来解决问题==
>   * 组合：在A类下方添加B类的成员变量，就形成了组合



### 开闭原则（OCP）

> 开闭原则（OCP：Open Close Principle）
>
> 介绍：
>
> * 开闭原则OCP是编程中最基础、最重要的设计原则
> * 一个软件实体如类、模块和函数应该==对扩展开放（提供方）、对修改关闭（使用方）==；用抽象构建框架，用实现扩展细节
> * 软件需要变化时，尽量通过扩展行为，而不通过修改已有代码
> * 编程中遵循的其他原则，以及使用设计模式的目的就是遵守开闭原则
>
> 



### 迪米特法则（最少知道原则）

> 迪米特法则（Law of Demeter）又叫作最少知道原则（The Least Knowledge Principle）
>
> 介绍：
>
> * ==一个对象和其他对象应该保持最少的了解（类之间关系越密切，耦合度越大）==
> * ==只和直接的朋友通信==
>   * 对象与对象之间有耦合关系；其中在类的成员变量、方法参数、返回值的类称为直接朋友；局部变量出现的类就不是直接朋友，换言之陌生的类最好不要以局部变量形式出现在类的内部。
>
> 迪米特法则注意事项和细节：
>
> * 核心就是降低类之间的耦合
> * 但是注意，迪米特法则只是要求降低类间耦合关系，并不是要求完全没有依赖关系



### 合成/聚合复用原则（CARP）

> 合成复用原则（CARP：Composite/Aggregation Reuse Principle）
>
> 介绍：
>
> * 尽量使用合成/聚合的方式，而不是使用继承。
>   * 依赖：A类作为B类的方法参数，B依赖A
>   * 聚合：A类作为B类的属性、set方法；将A聚合到B
>     * Aggregation聚合：A类有一个B类的成员变量，B类通过set方法声明；那么A类就聚合了B类
>   * 组合：A类作为B类的属性（直接new创建好了）；A组合到B
>     * Composite组合：A类有一个B类的成员变量（直接new；耦合性要高于聚合）





## 类之间的关系

依赖、泛化（继承）、实现、关联、聚合与组合

> 依赖关系：
>
> * Dependency依赖：==A类使用到了B类，A依赖B==
>   * 只要在类中用到了对方，依赖关系就成立（成员变量、方法返回值、方法参数、方法中使用到）
>
> 泛化关系：
>
> * Generalization：==泛化关系实际就是继承关系==，是依赖关系的一种特例
>   * A类继承了B类，就可以说A和B存在泛化关系
>
> 实现关系：
>
> * Implementation/Realization：==实现关系实际就是A类实现B接口==，依赖关系的一种特例
>
> 关联关系：
>
> * Association：==关联关系实际就是类与类之间的联系==，依赖关系的一种特例
>   * 具有导航性，即双向关系或单向关系
>
> 聚合关系：
>
> * Aggregation聚合：==表示的是整体和部分的关系，整体和部分可分开（部分聚合到整体）==；是关联关系的一种特例，所以也具有关联的导航型和多重性
>   * A类有一个B类的成员变量，B类==通过set方法==声明；那么A类就聚合了B类
>
> 组合关系：
>
> * Composite组合：==表示的是整体和部分的关系，整体和部分不可分离（部分组合到整体）则升级为组合关系==；是关联关系的一种特例，所以也具有关联的导航型和多重性
>   * A类有一个B类的==成员变量（直接new；耦合性要高于聚合）==
>   * 人、脑袋、身份证之间的关系：脑袋和人就是组合关系；身份证和人就是聚合关系
>     * 但要注意的是，若人和身份证在代码中进行了级联删除（删除人的同时也删除身份证），那么两者就是组合关系





## 主流设计模式

> 设计模式的七大原则：
>
> * 单一职责原则SRP
> * 接口隔离原则ISP
> * 依赖倒置原则DIP
> * 里氏替换原则LSP
> * 开闭原则OCP
> * 迪米特法则（最少知道原则）
> * 合成/聚合复用原则CARP



### 创建型模式

> 针对对象的创建 考虑怎样设计代码

#### :full_moon: 单例模式

> 介绍：
>
> * 保证整个软件系统中，对某个类只能存在一个对象实例；且该类只提供一个获取其对象实例的静态方法
>
> 单例模式的八种方式：
>
> * 饿汉式：静态常量
> * 饿汉式：静态方法
> * 懒汉式：线程不安全
> * 懒汉式：线程安全，同步方法
> * 懒汉式：线程安全，同步代码块
> * 双重检查
> * 静态内部类
> * 枚举
>
> 注意事项和细节：
>
> * 保证系统内存只存在一个对象，节省系统资源，对于一些频繁创建销毁的对象，单例可提高性能
> * 实例化一个单例类；使用相应的获取对象的方法而不是new
> * 单例模式使用场景：
>   * 需要频繁创建和销毁对象
>   * 创建对象时耗时过多或耗费资源过多（重量级对象）但又经常用到的对象、工具类对象、频繁访问数据库或文件的对象（数据源、session工厂等）
> * 饿汉式（单线程下、肯定会使用到而不会导致资源浪费）、双重检查、静态内部类、枚举；这些实现单例模式的方式都推荐使用。



##### 饿汉式---静态常量

> 实现步骤：
>
> 1. 构造器私有化
>
> 2. 类的内部创建对象
>
> 3. 向外暴露一个公共静态方法（getInstance）
>
> 4. ```java
>    public class Singleton01 {
>        private Singleton01() {
>        }
>        
>        private static final Singleton01 instance = new Singleton01();
>    
>        public static Singleton01 getInstance() {
>            return instance;
>        }
>    
>        public static void main(String[] args) {
>            // true
>            System.out.println(Singleton01.getInstance() == Singleton01.getInstance());
>            System.out.println(Singleton01.getInstance().hashCode());
>            System.out.println(Singleton01.getInstance().hashCode());
>        }
>    }
>    ```
>
> 优缺点：
>
> * 简单，类装载/加载时就完成了实例化。避免线程同步问题
> * 类加载完成实例化，未完成Lazy Loading懒加载的效果。若从始至终未使用到该实例对象，造成内存浪费



##### 饿汉式---静态代码块

> 实现步骤：
>
> 1.  饿汉式---静态常量 通过成员变量创建实例
>
> 2. 饿汉式---静态代码块  通过静态代码块创建实例
>
> 3. ```java
>    public class Single02 {
>        private Single02(){
>    
>        }
>        
>        private static final Single02 instance;
>        
>        static {
>            instance=new Single02();
>        }
>        
>        public static Single02 getInstance(){
>            return  instance;
>        }
>    
>        public static void main(String[] args) {
>            System.out.println(Single02.getInstance()==Single02.getInstance());
>            System.out.println(Single02.getInstance().hashCode());
>            System.out.println(Single02.getInstance().hashCode());
>        }
>    }
>    ```
>
> 优缺点：
>
> * 和静态常量的方式一致；该种单例模式可用，但可能造成内存浪费



##### 懒汉式：线程不安全

> 实现步骤：
>
> 1. ```java
>    public class Singleton01 {
>        private Singleton01(){}
>
>        private static Singleton01 instance;
>
>        public static Singleton01 getInstance(){
>            if (instance==null){
>                instance=new Singleton01();
>            }
>            return instance;
>        }
>
>        public static void main(String[] args) {
>            System.out.println(Singleton01.getInstance()==Singleton01.getInstance());
>            System.out.println(Singleton01.getInstance().hashCode());
>            System.out.println(Singleton01.getInstance().hashCode());
>        }
>    }
>    ```
>
> 优缺点：
>
> * 懒加载；但只能在单线程下使用
> * 多线程不能使用，未考虑到线程同步问题
>   * 多线程下，在未创建对象实例时，多个线程都通过了if判断，就会产生多个实例对象



##### 懒汉式：线程安全，同步方法

> 步骤：
>
> 1. ```java
>    public class Singleton02 {
>        private Singleton02(){}
>        private static Singleton02 instance;
>        public static synchronized Singleton02 getInstance(){
>            if (instance==null){
>                instance=new Singleton02();
>            }
>            return instance;
>        }
>    
>        public static void main(String[] args) {
>            System.out.println(Singleton02.getInstance()==Singleton02.getInstance());
>            System.out.println(Singleton02.getInstance().hashCode());
>            System.out.println(Singleton02.getInstance().hashCode());
>        }
>    }
>    ```
>
> 优缺点：
>
> * 懒加载；添加线程同步，解决线程安全问题
> * 效率太低



##### 懒汉式：线程安全，同步代码块

> 步骤：
>
> 1. ===不靠谱，没用
> 2. 本意是对线程同步方法级别进行优化，但无法起到线程同步作用



##### 双重检查

> 步骤：
>
> 1. 需要注意成员属性添加volatile关键字（private volatile static ...）；保证多线程下修饰的变量的可见性。
>
> 2. ```java
>    public class Singleton {
>        private Singleton() {}
>    
>        private volatile static Singleton instance;
>    
>        public static Singleton getInstance() {
>            if (instance == null) {
>                synchronized (Singleton.class) {
>                    if (instance == null) {
>                        instance = new Singleton();
>                    }
>                }
>            }
>            return instance;
>        }
>    
>        public static void main(String[] args) {
>            System.out.println(Singleton01.getInstance()==Singleton01.getInstance());
>            System.out.println(Singleton01.getInstance().hashCode());
>            System.out.println(Singleton01.getInstance().hashCode());
>        }
>    }
>    ```
>
> 优缺点：
>
> * Double Check概念是多线程开发经常用到的；进行了两次检查，即可保证线程安全
> * 线程安全；延迟加载；效率较高。实际开发推荐使用



##### 静态内部类

> 类被装载，类中的静态内部类不会被装载；类中使用到静态内部类才会被装载。（类装载线程是安全的）
>
> ```java
> public class Singleton {
>     private Singleton(){}
>     private static class SingletonInstance{
>         private static final Singleton instance=new Singleton();
>     }
>     public static Singleton getInstance(){
>         return SingletonInstance.instance;
>     }
>     public static void main(String[] args) {
>         System.out.println(Singleton.getInstance()==Singleton.getInstance());
>         System.out.println(Singleton.getInstance().hashCode());
>         System.out.println(Singleton.getInstance().hashCode());
>     }
> }
> ```
>
> 特点：
>
> * 静态内部类不会随着类加载而加载，待被调用才会被加载（例如获取类实例时，静态内部类被加载）；
>   * 所以实现了延迟加载、线程安全
> * 采用类装载机制保证初始化实例只有一个线程



##### 枚举

> 最安全的单例；其他方式都可以通过反射破坏。
>
> ```java
> enum Singleton {
>     INSTANCE;
> 
>     public void doSomething(){
>         System.out.println("Singleton instance is doing something.");
>     }
>     public static void main(String[] args) {
>         // 获取单例实例
>         Singleton instance = Singleton.INSTANCE;
>         // 调用单例方法
>         instance.doSomething();
>     }
> }
> ```
>
> 特点：
>
> * 借助JDK1.5添加的枚举实现单例模式，不仅保证了线程安全，还防止反序列化创建新的实例对象
> * 推荐





#### :waning_gibbous_moon: 工厂模式

> 简单工厂模式（静态工厂模式）：
>
> * 介绍：
>   * 简单工厂模式由一个工厂对象决定创建出哪一种产品类的实例（是工厂模式中最简单实用的模式）
>   * 定义一个创建对象的类，封装实例化对象的行为（代码）
>   * 软件开发中，当会用到大量的创建某种、某类或某批对象时，就会使用到工厂模式





#### :last_quarter_moon: 建造者模式

#### 抽象工厂模式

#### 原型模式

> Spring中原型bean的创建，应用的就是原型设计模式。







### 结构型模式

> 站在软件结构角度进行思考设计。

#### :last_quarter_moon:适配器模式（类/对象）

#### :waning_gibbous_moon:代理模式

#### :waning_gibbous_moon:装饰器模式

#### 组合模式

#### 享元模式

#### 外观模式

#### 桥接模式





### 行为型模式

> 站在方法的角度进行思考设计

#### :last_quarter_moon:迭代器模式

#### :last_quarter_moon:模板方法模式

#### :last_quarter_moon:策略模式

#### :last_quarter_moon:责任链模式

#### :waning_gibbous_moon:观察者模式

#### 命令模式

#### 状态模式

> 举例：
>
> * 借贷平台的订单，有审核-发布-抢单等等步骤，随着不同操作，订单状态也不同；该模块就会使用到状态模式。
>   * 不使用设计模式，面对不同状态可能想到的解决方案就是if/else。

#### 备忘录模式

#### 访问者模式

#### 中介者模式

#### 解释器模式

> Spring框架SpelExpressionParser使用到了解释器设计模式。







# 末尾