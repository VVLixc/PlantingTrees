# JavaWeb

## HTML和CSS

* CSS决定页面布局样式效果
* HTML是内容



* JavaSE：是C/S结构
  * Client/Server 客户端/服务端

* JavaWeb：是B/S软件的结构
  * Browser/Server 浏览器/服务器



* 网页组成部分
  * 内容（结构）：页面看到的数据；一般使用HTML技术实现
  * 表现：内容在页面的展现形式；一般使用CSS技术实现
  * 行为：页面中元素和输入设备交互的相应；一般由JS技术实现



### HTML

* Hyper Text Markup Language 超文本标记语言

* 创建HTML文件

  * 创建一个静态的web工程（static web变成了JavaScript）

  * 在工程下创建HTML页面

    * ![image-20230502104407670](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230502104407670.png)

  * ```html
    <!DOCTYPE html>
    <html lang="zh_CN">
    <head>
      <meta charset="UTF-8">
      <title>HTML页面书写规范</title>
      <style type="text/css">
        /*标签选择器*/
        div {
          border: 1px #b3d4fc solid;
          font-size: 20px;
        }
    
        /*id选择器+组合选择器*/
        #span001, #span002 {
          border: 3px aquamarine dashed;
          font-size: 25px;
        }
    
        /*class选择器+组合选择器*/
        .p001, .p002 {
          border: 3px cyan dotted;
          font-size: 30px;
        }
      </style>
    </head>
    <body>
    <div>div页面内容1</div>
    <div>div页面内容2</div>
    <span id="span001">span页面内容1</span>
    <span id="span002">span页面内容2</span>
    <p class="p001">p页面内容1</p>
    <p class="p002">p页面内容2</p>
    <ul>
      <li>无序列表1</li>
      <li>无序列表2</li>
      <li>无序列表3</li>
    </ul>
    </body>
    </html>
    ```

  #### HTML标签

  * 标签格式：<标签名>  封装的数据  </标签名>
  * 标签名大小写不敏感
  * 标签拥有自己的属性
    * 基本属性：
      * 可修改简单的样式效果
      * bgcolor="red"  设置背景颜色
    * 事件属性：
      * 可以直接设置事件响应后的代码 
      * onclick="alert('你好！');"   
        * onclick 表示单击/点击事件
        * alert是JavaScript语言提供的一个警告框函数，可接受任意参数
  * 标签又分为单标签和双标签
    * 单标签格式：<标签名/>
      * br 换行
      * hr 水平线
    * 双标签格式：<标签名>  封装的数据  </标签名>



##### 🌕超链接

* 网页中点击后可以跳转的内容都是超链接
* <a>标签（可以定义锚）
  * 通过使用href属性，创建一个到另一个文档的链接
  * 通过使用name或id属性，创建一个文档内部书签
* <a>标签指定跳转方式
  * 写法为
    * <a href="http://vue.ydui.org/docs/#/flexbox" target="_blank"></a>
    * _blank 表示跳到新页面打开，打开一个新窗口
    * _self表示当前页面打开链接
    * _parent表示在父集框架中打开
    * _top表示在整个窗口中打开
    * framename表示在指定的框架中打开
  * 需要哪种方式跳转到页面，即需要在target里面添加属性值



##### 列表标签

* 无序列表
  * 无序列表始于 <ul> 标签（unorder list无序列表）。每个列表项始于 <li>标签。（list item列表项）
* 有序列表
  * 有序列表始于 <ol> 标签（order list有序列表）。每个列表项始于 <li> 标签。（list item列表项）



##### img标签

* 图像标签可在html页面上显示图片：
* 需要使用到源属性src（source---是图像的URL地址）
  * 可设置图像的路径
  * JavaSE中
    * 相对路径：从工程名开始算
    * 绝对路径：盘符:/目录/文件名
  * web中路径分为
    * 相对路径：
      * .                    表示当前目录
      * ..                   表示上级目录
      * 文件名          表示当前文件所在目录的文件，相当于./文件名（./可以省略）
    * 绝对路径：
      * 格式为： http://ip:port/工程名/目录/文件名
* 替换文本属性<alt>
  * 浏览器无法加载图像，将显示这个替代文本



##### 🌕表格标签

* table表格标签 
  * tr 行标签 table rows
  * th 表头标签 table head
  * td 单元格标签 table data
* align 排列属性，对齐方式（left、center、right表格靠左、中、右）
* cellsapcing 单元格间距属性
* <b>是加粗标签

##### 🌖跨行跨列表格

* rowspan 跨行属性
* colspan 跨列属性



##### iframe框架标签

* 可以在一个html页面上开辟一个小区域，显示一个单独的页面
* iframe不能使用单标签，只能使用双标签<iframe ....></iframe>
* iframe标签（框架标签）和a标签（超链接标签）组合使用的步骤
  * iframe标签定义name属性
  * a标签下target属性（链接打开方式）内容填写ifram定义的name

##### 🌕表单标签

表单就是html页面下，用来收集用户信息的所有元素集合；之后把这些信息发送给服务器

* form标签表示表单（表单本身不可见）
  * input输入标签
    * 输入类型由type属性决定
      * type="text"文本域
      * 单选按钮 radio
      * 复选框 checkbox



```
http://localhost:8080/
?
user=lixc&
password=lz001214&
sex=male&
habbit=read&
country=CN&
selfFeel=i+like+read
```





### CSS

Cascading Style Sheets 层叠样式表单

* 用于控制网页样式并允许将样式信息和网页内容分离的一种标记性语言
* CSS层叠样式表单语法规则
  * 选择器 { 键值对 }

### CSS和HTML结合方式

* 第一种：在标签的style属性上设置"key: value value ...;"，修改标签样式

  * <!--为div标签内容设定边框；要求边框：1像素、实线、红色-->

    <div style="border: 1px solid red">div标签样式<div>

  * 该方式缺点：

    * 标签、样式多了；代码量将非常庞大
    * 可读性非常差
    * CSS代码没有复用性可言

* 第二种：head标签中，使用style标签来定义各种需要的样式

  * 格式：xxx { key: vaule value; }
  * **style标签专门用来定义CSS样式代码**
  * 该方式缺点
    * 只能在同一页面复用代码，不能将CSS代码同时作用多个页面
    * 维护不方便，实际项目中页面可能会有成千上万，为每个页面做修改工作量太大

* 第三种：将CSS代码写成一个单独的CSS文件，再通过link标签引入即可
  
  * **link标签专门用来引入CSS样式代码**



### CSS选择器











## JavaScript

* 最初目的就是为了做页面的数据验证；因此运行在客户端，需要运行浏览器来解释执行JS代码
* JS弱类型语言；Java强类型语言
  * 弱类型语言类型可变
  * 强类型就是定义变量。类型已确定。而且不可变
* JS特点
  * 交互性：信息的动态交互
  * 安全性：不允许直接访问本地硬盘
  * 跨平台性：只要求浏览器可以解释运行JS，对运行平台无要求
* JS和HTML的结合
  * 第一种方式
    * 在head标签或body标签中，使用script标签来书写JS代码
  * 第二种
    * 使用script标签来引入 单独的Javascript代码文件
      * 引入js文件使用：src属性



### 变量

* **变量是可以存放某些值的内存的命名**

* JavaScript**变量类型**：

  * 数值类型			number
  * 字符串类型        string
  * 布尔类型            boolean
  * 对象类型            object
  * 函数类型            function

* JS**变量特殊值**

  * undefined
    * 未定义；所有js变量未赋初始值，默认值都是undefined
  * null
    * 空值
  * NaN
    * Not a Number ；非数字、非数值

* **定义变量格式**：

  * var 变量名；
  * var 变量名 = 值；

* ```html
  <script type="text/javascript">
          var v1;
          alert(v1);//undefined
          v1=12;
          alert(v1);//12
          alert(typeof v1);//number
          var v2="abc";
          alert(typeof v2);//string
          alert(v1*v2);//NaN
  </script>
  ```

* 关系（比较）运算

  * 等于       ==
    * 简单的字面值比较
  * 全等于   ===
    * 除了做简单字面值比较之外，还会做数据类型比较

* 逻辑运算

  * 且运算     &&
    * 第一种情况：两边都为真，返回最后一个值
    * 第二种情况：存在假，返回假数据（第一个假）
    * 会短路，有结果后面表达式不再执行
  * 或运算     ||
    * 第一种情况：两边都为假，返回最后一个值
    * 第二种情况：存在真，返回真数据（第一个真）
    * 会短路，有结果后面表达式不再执行
  * 取反运算 ！
  * JS中所有变量，都可以作为一个boolean类型的变量去使用
    * 0、undefined、null、""(空串) 都认为是false；

* ```html
      <script type="text/javascript">
          var v1 = 12;
          var v2 = "12";
          /*关系（比较）运算：==   ===  */
          alert(v1 == v2);//true
          alert(v1 === v2);//false
  
          /*逻辑运算：&&  ||  ！  */
          var v3;
          v3 = null;
          v3 = 0;
          if (v3) {
          } else {
              alert("undefined为假");
              alert("null为假");
              alert("0为假");
          }
  
          v3 = 10;
          var v4;
          var v5 = null;
          var v6 = 100;
          alert(v3 && v4);//undefined
          alert(v4 && v3);//undefined
          alert(v4 && v5);//undefined
          alert(v5 && v4);//null
  
          alert(v3 || v4);//10
          alert(v4 || v3);//10
          alert(v3 || v6);//10
          alert(v6 || v3);//100
      </script>
  ```



### 🌕数组

* 数组语法

  * var v1=[]；表示空数组
  * var v2 = [ 1,"abc",true ]；定义数组同时赋值元素

* ```html
      <script type="text/javascript">
          var arr=[];
          arr[1]=10;
          alert(arr.length);//2
          arr[10]="lixc";
          alert(arr[9]);//undefined；再一次说明了 定义变量时未赋初始值，默认值为undefined
          alert(arr.length);//11
          for (var i = 0; i < arr.length; i++) {
              alert(arr[i]);
          }
      </script>
  ```

* 注意：

  * JS中定义数组，只要显示的通过下标赋值，就会自动的为数组扩容（大小：下标长度+1）；
  * 若显示的为一个数组通过下标赋值后，数组中未赋值的数据都是undefined未定义（因为未赋初始值的变量，默认就是undefined）

### 🌕函数

* 函数定义的两种方式

  * 第一种函数定义方式：

    * function 函数名 （形参列表）{ 函数体 }

    * ```html
          <script type="text/javascript">
              function func1() {
                  alert("调用无参函数func1()");
              }
      
              function func2(a, b) {
                  alert(a + " ❤ " + b);
              }
      
              function sum(a, b) {
                  return a + b;
              }
      
              func1();
              func2("lixc", "miss vv");
              alert(sum(100, 150));
          </script>
      ```

  * 第二种函数定义方式

    * var 函数名 = function (形参列表) { 函数体 }

    * ```html
      <script type="text/javascript">
          var func = function (a, b) {
              alert(a + " ❤ " + b);
          }
          func("lixc", "miss vv");
      </script>
      ```

* 函数注意事项

  * 函数**形参列表**中定义的变量 **不能给定类型**

  * 函数**只有被调用才会执行**

  * 函数想要**返回数据，直接return数据**即可

  * function**函数应避免重载**，一旦重载函数，之前的函数就**会被覆盖**掉。

    * ```html
          <script type="text/javascript">
              function func(a, b) {
                  alert(a + " ❤ " + b);
              }
              function func(a) {
                  alert(a);
              }
      
              func("lixc");//alert("lixc")
              func(100, 150);//函数被覆盖了，只会访问到重载后的函数 alert(100)
          </script>
      ```



* 函数的**隐形参数arguments**（**只在function函数内**）

  * 在function函数定义时无需显示定义，可以用来获取所有参数的变量。称为隐形参数

  * JS中的隐形参数和Java中的Object... params可变参数一样。操作类似数组

  * ```html
    <script type="text/javascript">
        /*隐形参数arguments用途：
            定义一个函数：计算所有参数累计和并返回*/
        function func() {
            alert(arguments);//object Arguments
            alert(arguments.length);
            var paramSum = 0;
            for (var i = 0; i < arguments.length; i++) {
                if (typeof arguments[i] === "number"){
                    paramSum += arguments[i];
                }
            }
            return paramSum;
        }
    
        alert(func(1, 2,"lixc", 3));//3lixc3--优化-->6
    </script>
    ```





### JS中的自定义对象

#### **Object形式的自定义对象**

* 语法：
  * 定义对象：
    * var 变量名 = new Object()；//对象实例（空对象）
    * 定义对象属性：
      * 变量名 . 属性名 = 值；
    * 定义对象函数：
      * 变量名 . 函数名 = function () {}
  * 访问对象：
    * 变量名 . 属性名/函数名；

#### **{}大括号形式的自定义对象**

* 语法：

  * **定义对象：**

    * ```html
      var 变量名 = {
      	属性名：值，
      	属性名：值，
      	...，
      	函数名：function(){}
      }
      ```

  * 访问对象

    * 变量名 . 属性名 / 函数名()；

* ```html
  <script type="text/javascript">
      //通过new Object()；方式创建对象
      var obj1 = new Object();//对象实例（空对象）
      obj1.name = "lixc";
      obj1.age = 18;
      obj1.fun1 = function () {
          alert("姓名：" + this.name + " 年龄：" + this.age);
      }
      obj1.fun1();
  
      //通过{} 大括号的形式创建对象
      var obj2 = {
          name: "vv",
          age: 18,
          func2: function () {
              alert("姓名：" + this.name + " 年龄：" + this.age);
          }
      }
      obj2.func2()
  </script>
  ```





### JS中的事件

* 事件就是计算机输入设备和页面进行交互的响应。
* 常用事件：
  * onload			加载完成事件
    * 页面加载完成后，常用来做JS代码的初始化
  * onclick			点击事件
    * 按钮点击后的响应操作
  * onblur		    失去焦点事件
    * 常用来做焦点移开后校验输入内容是否合法
  * onchange      内容发生改变事件
    * 下拉列表、输入框内容发生改变后操作
  * onsubmit       表单提交事件  
    * 表单提交前，验证所有表单项是否合法
* 事件的注册（绑定）
  * 事件的注册就是通知浏览器，当事件响应后执行相关操作代码。
  * 静态注册：通过**HTML标签的事件属性**直接赋予事件响应后的代码；
  * 动态注册：先**通过JS代码**得到**标签dom对象**，之后通过**对象 . 事件名 = function (){}** 赋予事件响应后的代码；
    * 动态注册基本步骤：
      * 获取标签对象；
      * 标签对象 . 事件名 = function (){}



#### onload加载完成事件

* ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>onload事件注册</title>
      <script type="text/javascript">
          function staticFunc() {
              alert("静态注册：在HTML语言body标签下onload属性操作")
          }
  
          window.onload = function () {
              alert("JS代码中动态注册onload事件---触发响应");
          }
      </script>
  </head>
  <!--静态注册：在HTML语言body标签下onload属性操作-->
  <!--<body onload="staticFunc()">-->
  <body>
  </body>
  </html>
  ```



#### onclick点击事件

* ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>onclick点击事件注册</title>
      <script type="text/javascript">
          function staticFunc() {
              alert("静态注册---onclick点击事件---响应");
          }
  
          window.onload = function () {
              var element = document.getElementById("dynamicsBtn");//HTMLButtonElement对象
              element.onclick = function () {
                  alert("动态注册---onclick点击事件---响应");
              }
          }
      </script>
  </head>
  <body>
  <button onclick="staticFunc()">静态按钮</button>
  <button id="dynamicsBtn">动态按钮</button>
  </body>
  </html>
  ```



#### onblur失去焦点事件

* ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>onblur失去焦点事件注册</title>
      <script type="text/javascript">
          function staticFunc() {
              console.log("静态注册---onblur失去焦点事件---响应");
          }
  
          window.onload = function () {
              var element = document.getElementById("dynamicsPass");// HTMLInputElement
              element.onblur = function () {
                  console.log("动态注册---onblur失去焦点事件---响应");
              }
          }
      </script>
  </head>
  <body>
  用户：<input onblur="staticFunc()" type="text"/><br/>
  密码：<input id="dynamicsPass" type="password"/><br/>
  </body>
  </html>
  ```

#### onchange内容发生改变事件

* ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>onchange事件注册</title>
      <script type="text/javascript">
          function staticFunc() {
              alert("静态注册---onchange内容发生改变事件---响应");
          }
  
          window.onload = function (){
              var element = document.getElementById("dynamicsSelect");//HTMLSelectElement
              element.onchange = function (){
                  alert("动态注册---onchange内容发生改变事件---响应");
              }
          }
      </script>
  </head>
  <body>
  喜爱宠物：
  <select onchange="staticFunc()">
      <option>---动物---</option>
      <option>猫咪</option>
      <option>狗狗</option>
      <option>people</option>
  </select>
  <br/>
  日常爱好：
  <select id="dynamicsSelect">
      <option>---爱好---</option>
      <option>read</option>
      <option>sport</option>
      <option>write</option>
  </select>
  </body>
  </html>
  ```

#### onsubmit表单提交事件

* 若校验后不允许表单提交---onsubmit = return false；

* ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>onsubmit表单提交事件注册</title>
      <script type="text/javascript">
          function staticFunc() {
              alert("静态注册---onsubmit表单提交---响应")
              alert("表单数据验证有误---不允许提交");
              return false;
          }
  
          window.onload = function () {
              var element = document.getElementById("dynamicsSubmit");//HTMLFormElement
              element.onsubmit = function () {
                  alert("动态注册---onsubmit表单提交---响应")
                  alert("表单数据验证成功---允许提交");
                  return true;//允许表单提交只要不是return false皆可
              }
          }
      </script>
  </head>
  <body>
  <form action="http://localhost:8080" method="get" onsubmit="return staticFunc();">
      <input type="submit" value="静态提交"/><br/>
  </form>
  <form action="http://localhost:8080" method="get" id="dynamicsSubmit">
      <input type="submit" value="动态提交"/><br/>
  </form>
  </body>
  </html>
  ```

  

### DOM模型

* Document Object Model 文档对象模型
* 把文档中的标签、属性、文本转换为**对象**来管理

#### 🌕Document对象

* Document对象代表整个HTML文档，可用来访问页面中的所有元素

* Document对象的理解
  * Document**管理了所有HTML文档内容**
  * Document是一种**树结构的文档，有层级关系**
  * Document让我们**把所有的标签都 对象化**
  * 可通过Document**访问所有标签对象**



#### 🌕Document对象的方法介绍

* document . getElementById( "id属性值" );
  * 通过标签id获取标签dom对象
* document . getElementsByName( "name属性值" );
  * 通过标签name属性获取标签dom对象集合（会返回多个）
* document . getElementByTagName( "标签名" );
  * 通过标签名 获取标签dom对象集合（多个）
* ducument . createElement( "要创建的标签名" );
  * 通过标签名 创建标签对象



* 注意：
  * 使用document对象获取标签dom对象的三个方法
    * 有id先用id属性值，其次是name属性值，最后才考虑标签名
  * 获取dom对象的三个方法，必须要在页面加载完成后执行才能获取标签dom对象







## JQuery

* JQuery就是JavaScript+Query查询；辅助JavaScript开发的js类库

* JQuery核心思想：write less，do more；实现了很多浏览器兼容问题

* 好处

  * 免费、开源、
  * JQuery的语法设计使开发更便捷
    * 操作文档对象、选择DOM元素、制作动画效果、事件处理、使用Ajax等等其他功能

* ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>JQuery体验demo</title>
      <!--使用JQuery的js类库 需要先src引入-->
      <script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
      <script type="text/javascript">
          //使用$()代替window.onload
          $(function () {
              //使用选择器(下面示例id选择器)获取按钮对象，随后绑定单击响应函数
              $("#btnId").click(function () {
                  alert('Hello');
              });
          });
      </script>
  </head>
  <body>
  <button id="btnId">SayHello</button>
  </body>
  </html>
  ```

* JQuery注意

  * **$表示一个函数**（alert($)；）
  * 使用JQuery**必须引入JQuery库**：
    * jquery-1.7.2.js
    * jquery-1.7.2.min.js
    * 两个JQuery库js文件比较：**min.js 是将 js文件 段落和函数变量等数据精简后的文件（文件内存也会缩小）**；一般本地开发使用js文件即可；**上线服务使用min.js 加载会相对快些**
  * 怎样为按钮添加响应函数的：
    * 使用JQuery获取到标签对象
    * 使用标签对象 . 事件名 ( function(){  } )；
    * 细节：
      * script标签（HTML专门为JS提供的标签）引入（src）JQuery类库
      * 另起一个script标签；$就代表了JQuery函数
      * $()  和  window . onload 效果一致；表示页面加载完成；
        * 在**$( function(){ 里面写入页面加载完成后的业务代码 } )**
        * 在页面加载$(function(){ }) 函数体中 使用**$ ( "JQuery选择器" ) . 事件名 ( function(){ 写入事件响应代码 } )**



### JQuery核心函数

* $就是JQuery的核心函数；$ ( ) 就是调用这个$核心函数。

* 核心函数作用：向$( )传入数据时：

  * 传参为【函数】：

    * 表示页面加载完成之后。（相当于window . onload = function(){} ）

  * 传参为【HTML标签字符串】：

    * 会创建这个HTML标签对象

  * 传参为【选择器字符串】：

    * 会查询获取标签对象

  * 传参为【DOM对象】：

    * 会将DOM对象转为JQuery对象

  * ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>$核心函数作用测试</title>
        <script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
        <script type="text/javascript">
            /*核心函数的4个作用*/
            //传入参数为[函数]时：在文档加载完成后执行这个函数
            $(function () {
                alert("页面加载完成");
                //传入参数为[HTML字符串]时：根据这个字符串创建元素节点对象
                $("<div>div标签1</div><div>div标签2</div><div>div标签3</div>").appendTo("body");
                //传入参数为[选择器字符串]时：根据这个字符串查找元素节点对象
                alert($("#btn01").length);//1
                alert($(".btn02").length);//1
                alert($("button").length);//3
                //传入参数为[DOM对象]时：将DOM对象包装为jQuery对象返回
                var buttonDOM = document.getElementById("btn01");
                alert(buttonDOM);//object HTMLButtonElement
                alert($(buttonDOM));//object Object
            })
        </script>
    </head>
    <body>
    <button id="btn01">按钮1</button>
    <button class="btn02">按钮2</button>
    <button>按钮3</button>
    </body>
    </html>
    ```



### JQuery对象和DOM对象区分

* DOM对象
  * getElementById()查询出的是DOM对象
  * getElementsByName()查询出的是DOM对象
  * getElementsByTagname()查询出的是DOM对象
  * createElement()创建的是DOM对象
  * alert方法下DOM对象：object HTML标签名Element
* JQuery对象
  * 通过JQuery包装的DOM对象，是JQuery对象
  * 通过JQuery提供的API创建的对象，是JQuery对象
  * 通过JQuery提供的API查询的对象，是JQuery对象
  * alert方法下DOM对象：object Object



* JQuery对象本质

  * JQuery对象是 **DOM对象的数组** + **JQuery提供的一系列功能函数**

  * ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JQuery对象本质</title>
        <script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
        <script type="text/javascript">
            $(function () {
                var $btns = $("button");//JQuery对象
                alert($btns);//[object Object]
                for (var i = 0; i < $btns.length; i++) {
                    alert($btns[i]);//[object HTMLButtonElement]
                }
            });
        </script>
    </head>
    <body>
    <div id="testDiv">Atguigu is Very Good!</div>
    
    <button id="dom2dom">使用DOM对象调用DOM方法</button>
    <button id="dom2jQuery">使用DOM对象调用jQuery方法</button>
    <button id="jQuery2jQuery">使用jQuery对象调用jQuery方法</button>
    <button id="jQuery2dom">使用jQuery对象调用DOM方法</button>
    </body>
    </html>
    ```

* JQuery对象和DOM对象两者不能使用对方的属性和方法。



#### 🌗DOM对象和JQuery对象互转

* DOM对象，使用 $ ( DOM对象 ) 转换为JQuery对象
* JQuery对象通过下标取出相应的DOM对象



### 🌕JQuery选择器

#### 🌕基本选择器

* id选择器

* class选择器

* 标签名选择器

* 组合选择器

* ```html
  <script type="text/javascript">
      $(function () {
          //1.选择 id 为 one 的元素 "background-color","#bbffaa"
          $("#btn1").click(function () {
              $("#one").css("background-color", "#bbffaa");
          });
          //2.选择 class 为 mini 的所有元素
          $("#btn2").click(function () {
              $(".mini").css("background-color", "#bbffaa");
  
          });
          //3.选择 元素名是 div 的所有元素
          $("#btn3").click(function () {
              $("div").css("background-color", "#bbffaa");
          });
  
          //4.选择所有的元素
          $("#btn4").click(function () {
              $("*").css("background-color", "#bbffaa");
          });
  
          //5.选择所有的 span 元素和id为two的元素
          $("#btn5").click(function () {
              $("span,#two").css("background-color", "#bbffaa");
          });
      });
  </script>
  ```



#### 🌕层级选择器

* 后代选择器：ancestor descendant

  * 给定的祖先元素下匹配所有的后代元素

* 子元素选择器：parent > child

  * 给定的父元素下匹配所有的子元素

* 相邻元素选择器：prev + next

  * 匹配紧接在prev元素后的next元素

* 之后的兄弟元素选择器：prev ~ sibings

  * 匹配prev元素之后的所有siblings元素

* ```html
  <script type="text/javascript">
      // $(document).ready(function(){   这条函数表达式就是 页面加载完成的完整语句；下面的$(function(){})是简化版
      $(function () {
          //1.选择 body 内的所有 div 元素
          $("#btn1").click(function () {
              $("body div").css("background", "red");
          });
          //2.在 body 内, 选择div子元素
          $("#btn2").click(function () {
              $("body > div").css("background", "blue");
          });
  
          //3.选择 id 为 one 的下一个 div 元素
          $("#btn3").click(function () {
              $("#one + div").css("background","green");
          });
          //4.选择 id 为 two 的元素后面的所有 div 兄弟元素
          $("#btn4").click(function () {
              $("#two ~ div").css("background","yellow");
          });
      });
  </script>
  ```













#### 过滤选择器

##### 基本的过滤选择器

* ```html
  <script type="text/javascript">
      $(document).ready(function () {
          function anmateIt() {
              $("#mover").slideToggle("slow", anmateIt);
          }
  
          anmateIt();
      });
  
      $(document).ready(function () {
          //1.选择第一个 div 元素
          $("#btn1").click(function () {
              $("div:first").css("background", "#bbffaa");
          });
  
          //2.选择最后一个 div 元素
          $("#btn2").click(function () {
              $("div:last").css("background", "#bbffaa");
          });
  
          //3.选择class不为 one 的所有 div 元素
          $("#btn3").click(function () {
              $("div:not(.one)").css("background", "#bbffaa");
          });
  
          //4.选择索引值为偶数的 div 元素
          $("#btn4").click(function () {
              $("div:even").css("background", "#bbffaa");
          });
  
          //5.选择索引值为奇数的 div 元素
          $("#btn5").click(function () {
              $("div:odd").css("background", "#bbffaa");
          });
  
          //6.选择索引值为大于 3 的 div 元素
          $("#btn6").click(function () {
              $("div:gt(3)").css("background", "#bbffaa");
          });
  
          //7.选择索引值为等于 3 的 div 元素
          $("#btn7").click(function () {
              $("div:eq(3)").css("background", "#bbffaa");
          });
  
          //8.选择索引值为小于 3 的 div 元素
          $("#btn8").click(function () {
              $("div:lt(3)").css("background", "#bbffaa");
          });
  
          //9.选择所有的标题元素
          $("#btn9").click(function () {
              $(":header").css("background", "#bbffaa");
          });
  
          //10.选择当前正在执行动画的所有元素
          $("#btn10").click(function () {
              $(":animated").css("background", "#bbffaa");
          });
  
          //10.选择没有执行动画的最后一个div
          $("#btn11").click(function () {
              $("div:not(:animated):last").css("background", "#bbffaa");
          });
      });
  </script>
  ```

##### 内容过滤选择器

* ```html
  <script type="text/javascript">
     $(document).ready(function(){
        function anmateIt(){
           $("#mover").slideToggle("slow", anmateIt);
        }
  
        anmateIt();             
     });
     
     /** 
     :contains(text)   
     :empty             
     :has(selector)     
     :parent          
     */
     $(document).ready(function(){
        //1.选择 含有文本 'di' 的 div 元素
        $("#btn1").click(function(){
           $("div:contains('di')").css("background", "#bbffaa");
        });
  
        //2.选择不包含子元素(或者文本元素) 的 div 空元素
        $("#btn2").click(function(){
           $("div:empty").css("background", "#bbffaa");
        });
  
        //3.选择含有 class 为 mini 元素的 div 元素
        $("#btn3").click(function(){
           $("div:has(.mini)").css("background", "#bbffaa");
        });
  
        //4.选择含有子元素(或者文本元素)的div元素
        $("#btn4").click(function(){
           $("div:parent").css("background", "#bbffaa");
        });
     });
  </script>
  ```



##### 属性过滤选择器

* ```html
  <script type="text/javascript">
      /**
       [attribute]
       [attribute=value]
       [attribute!=value]
       [attribute^=value]
       [attribute$=value]
       [attribute*=value]
       [attrSel1][attrSel2][attrSelN]
       */
      $(function () {
          //1.选取含有 属性title 的div元素
          $("#btn1").click(function () {
              $("div[title]").css("background", "#bbffaa");
          });
          //2.选取 属性title值等于'test'的div元素
          $("#btn2").click(function () {
              $("div[title='test']").css("background", "#bbffaa");
          });
          //3.选取 属性title值不等于'test'的div元素(*没有属性title的也将被选中)
          $("#btn3").click(function () {
              $("div[title!='test']").css("background", "#bbffaa");
          });
          //4.选取 属性title值 以'te'开始 的div元素
          $("#btn4").click(function () {
              $("div[title^='te']").css("background", "#bbffaa");
          });
          //5.选取 属性title值 以'est'结束 的div元素
          $("#btn5").click(function () {
              $("div[title$=est]").css("background", "#bbffaa");
          });
          //6.选取 属性title值 含有'es'的div元素
          $("#btn6").click(function () {
              $("div[title*='es']").css("background", "#bbffaa");
          });
  
          //7.首先选取有属性id的div元素，然后在结果中 选取属性title值 含有'es'的 div 元素
          $("#btn7").click(function () {
              $("div[id][title*='es']").css("background", "#bbffaa");
          });
          //8.选取 含有 title 属性值, 且title 属性值不等于 test 的 div 元素
          $("#btn8").click(function () {
              $("div[title][title!='test']").css("background", "#bbffaa");
          });
      });
  </script>
  ```





##### 表单过滤器&表单对象属性过滤器

* ```html
  <script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
  <script type="text/javascript">
      $(function () {
          /**
           :input
           :text
           :password
           :radio
           :checkbox
           :submit
           :image
           :reset
           :button
           :file
           :hidden
  
           表单对象的属性
           :enabled
           :disabled
           :checked
           :selected
           */
          //1.对表单内 可用文本框 input 赋值操作
          $("#btn1").click(function () {
              $(":text:enabled").val("可用text重新赋值");
          });
          //2.对表单内 不可用文本框 input 赋值操作
          $("#btn2").click(function () {
              $(":text:disabled").val("不可用text重新赋值");
          });
          //3.获取多选框选中的个数  使用size()方法获取选取到的元素集合的元素个数
          $("#btn3").click(function () {
              alert($(":checkbox:checked").size());
          });
          //4.获取多选框，每个选中的value值
  		$("#btn4").click(function () {
              var allVals = "";
              var $checkBoxVals = $(":checkbox:checked");
              //老式fori遍历
              // for (var i = 0; i < $checkBoxVals.length; i++) {
              //     allVals += $checkBoxVals[i].value + " ";
              // }
              // alert(allVals);
  
              //each方法时JQuery对象提供用来遍历元素的方法
              //遍历的function函数中，this对象就是JQery遍历后的DOM对象
              $checkBoxVals.each(function () {
                  allVals += this.value + "~";
              });
              alert(allVals)
          });
          //5.获取下拉框选中的内容
          $("#btn5").click(function () {
              var allSelects = "";
              var $selects = $("select option:selected");
              //老式fori遍历
              // for (var i = 0; i < $selects.length; i++) {
              //     allSelects += $selects[i].value + " ";
              // }
              // alert(allSelects);
              $selects.each(function () {
                  allSelects += this.innerHTML + "~";
              });
              alert(allSelects);
          });
      })
  </script>
  ```





### JQuery元素筛选



### JQuery属性操作

* HTML的代码、文本、值
  * html()；可 设置 和 获取 起始标签到结束标签的内容；类似DOM对象的innerHTML
  * text()；可 设置 和 获取  起始标签到结束标签的文本；类似DOM对象的innerText
  * val()；可 设置 和 获取  表单项的value属性值；类似DOM对象的value
    * val() 可同时设置多个表单项的选中状态

* ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <script type="text/javascript" src="script/jquery-1.7.2.js"></script>
      <script type="text/javascript">
          /*JQuery的属性操作*/
          $(function () {
              // var $divJq= $("div");
              // alert($divJq.html());//div标签<span>div内嵌的span标签</span>
              // $divJq.html("<p>html属性方法</p>");
              // alert($divJq.text());//html属性方法
              // $divJq.text("123");
  
              /*操作表单项 对value属性对应的标签对象 选中*/
              // $(":radio").val(["radio1"]);
              // $(":checkbox").val(["checkbox1","checkbox2"]);
              // $("#single").val(["sin3"]);
              // $("#multiple").val(["mul1","mul3"]);
              $(":radio,:checkbox,#single,#multiple").val(["radio2", "checkbox2", "checkbox3", "sin1", "mul1", "mul2"]);
          });
      </script>
  </head>
  <body>
  <div>div标签<span>div内嵌的span标签</span></div>
  单选：
  <input name="radio" type="radio" value="radio1"/>radio1
  <input name="radio" type="radio" value="radio2"/>radio2
  <br/>
  多选：
  <input name="checkbox" type="checkbox" value="checkbox1"/>checkbox1
  <input name="checkbox" type="checkbox" value="checkbox2"/>checkbox2
  <input name="checkbox" type="checkbox" value="checkbox3"/>checkbox3
  <br/>
  下拉多选 ：
  <select id="multiple" multiple="multiple" size="4">
      <option value="mul1">mul1</option>
      <option value="mul2">mul2</option>
      <option value="mul3">mul3</option>
      <option value="mul4">mul4</option>
  </select>
  <br/>
  下拉单选 ：
  <select id="single">
      <option value="sin1">sin1</option>
      <option value="sin2">sin2</option>
      <option value="sin3">sin3</option>
  </select>
  </body>
  </html>
  ```



* 属性

  * attr()    

    * 可以设置和获取 属性 的值；不推荐操作checked、selected、disabled、readOnly等属性
    * 还可操作非标准属性（例如自定义属性并赋值）

  * prop()

    * 可以设置和获取属性的值；推荐操作checked、selected、disabled、readOnly等

  * ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <script type="text/javascript" src="script/jquery-1.7.2.js"></script>
        <script type="text/javascript">
            /*JQuery的属性操作*/
            $(function () {
                //attr、prop属性操作
                alert($(":checkbox:last").attr("name"));//checkbox
                $(":checkbox:last").attr("name", "checkbox03");//为复选框最后一个元素 属性名为name赋值，会替换之前的数据
                $(":checkbox:last").attr("addAttr", "addAttrVal");//为复选框最后一个元素 属性名为name赋值，没有该属性就会新建并赋值
    
                alert($(":checkbox:last").attr("checked"));//undefined  结果不明确 不推荐使用
                alert($(":checkbox:last").prop("checked"));//false      结果准确   推荐使用
            });
        </script>
    </head>
    <body>
    多选：
    <input name="checkbox" type="checkbox" value="checkbox1"/>checkbox1
    <input name="checkbox" type="checkbox" value="checkbox2"/>checkbox2
    <input name="checkbox" type="checkbox" value="checkbox3"/>checkbox3
    </body>
    </html>
    ```



### JQuery练习

### DOM的增删改

* 注意PDF文件中的
  * replaceWith
    * a.replaceWith(b)；用一个b替换掉所有的a----所有的a只生成一个b
  * replaceAll
    * a.replaceAll(b)；用a替换掉b---有几个b生成几个a标签





## XML

### XML简介

* Extensible Mark-up Language 
* XML是可扩展的标记性语言（可扩展---数据保存的XML文件中的标签名，不是标准的HTML标签，可以是我们定义的对象、属性等标签名）
* XML作用
  * 用来保存数据，而且数据具有自我描述性
  * 用作项目或模块的配置文件
  * 用作网络传输数据的格式（现在以JSON为主）



### XML语法

* 创建Java项目
* 语法规则：
  * XML元素必须有关闭标签（也就是**闭合**）
  * XML元素标签对**大小写敏感**
  * 标签必须**正确嵌套**
  * XML文档必须有根元素（顶级元素；没有父标签），且**根元素必须唯一**
  * XML**属性值必须加引号**
  * XML**特殊字符需要转义（可以放在CDATA文本区）**

* XML语法

  * 1）文档声明

    * ```xml
      <?xml version="1.0" encoding="utf-8" ?>
      <!--
      	?xml 连在一起写
      	version 版本号
      	encoding 是xml的文件编码
      -->
      ```

  * 2）元素（标签）

    * 指的从开始标签直到结束标签的部分
    * 元素可包含其他元素、属性、文本
    * XML命名规范：
      * XML元素名称可包含字母、数字等
      * 名称不能以数字、标点符号开始
      * 不能包含空格

  * 3）XML属性

    * 和HTML属性类型；可提供额外的元素信息
    * 每个属性的值必须用引号包裹

  * 4）XML注释

    * ```xml
      <!--和HTML注释一样-->
      ```

  * 5）文本区域（CDATA区）

    * ```xml
      <![CDATA[<><><>CDATA区表明数据为纯文本，不需要XML解析器对特殊字符处理<><><>]]>
      ```



### XML解析技术介绍

* HTML（超文本标记语言）和XML（可扩展标记语言）它们都是标记性文档；都可使用W3C组织制定的DOM技术来解析
* Document对象表示整个文档：可以是HTML文档，也可以是XML文档
* 早期JDK提供了两种XML解析技术：
  * DOM解析技术
    * W3C组织制定的；Java也对DOM解析技术根据自己语言特点做出了实现
  * SAX（已过时）
    * sun公司在JDK5版本对DOM技术进行升级SAX（Simple API for XML）
    * SAX解析技术和W3C制定的不一样。它以类似事件机制通过回调通知用户正在解析的内容。
      * 一行行读取XML文件来解析，避免了大量DOM对象的创建
      * 所以在解析XML时，无论是在内存使用、还是性能方面，都优于DOM解析
* 第三方解析：
  * jdom：在DOM基础进行了封装
  * dom4j：又对jdom进行了封装
  * pull：主要用在Android手机开发，和SAX解析技术类似都是事件机制解析XML文件



### 🌕Dom4j解析技术

* 第三方提供的解析技术，需要使用第三方提供好的类库才可解析XML文件（dom4j.x.x.jar）

* 使用Dom4j技术解析XML

  * 通过SAXReader输入流读取XML文件来获取Document对象
  * 通过Document对象来获取根元素Element对象（getRootElement）
  * 通过根元素来获取子元素（存储的类对象），一般有多个返回集合；// elements( "子标签名" ) 
  * 迭代获取到的element子元素，拿到想要操作的每一个标签内容

* ```java
  @Test
  public void test1() throws Exception{
      List<Element> elementArrs = new SAXReader().read("存放的xml文件路径").getRootElement().elements("根元素下的标签元素");
      for(Element element:elementArrs){
          String name = element.elementText("标签名/类的属性");
      }
  }
  ```





## Tomcat

### JavaWeb概念

* 1，什么是web
  * **Web（World Wide Web）即全球广域网，也称为万维网**，它是一种基于超文本和HTTP的、全球性的、动态交互的、跨平台的分布式图形信息系统。是建立在Internet上的一种网络服务，为浏览者在Internet上查找和浏览信息提供了图形化的、易于访问的直观界面，其中的文档及超级链接将Internet上的信息节点组织成一个互为关联的网状结构。
* 2， 什么是javaWeb:
  * **使用java语言开发基于互联网的项目,所有通过 Java 语言编写可以通过浏览器访问的程序的总称,即JavaWeb.**
  * **JavaWeb是基于请求和响应来开发的**
* 请求Request
  * 请求就是客户端向服务端发送数据
* 响应Response
  * 服务端向客户端回传数据
* 请求和响应的关系
  * 成对出现；有请求就会有响应

### Web资源的分类

* 按实现的技术和呈现的效果不同，可将Web分为静态资源和动态资源
* 静态资源：HTML、CSS、JavaScript、txt、mp4、jpg
* 动态资源：JSP页面、Servlet程序



### Tomcat的使用

* Tomcat是Apache提供的一种Web服务器，提供对JSP和Servlet的支持。是一种轻量级的Java容器（服务器）
* Tomcat安装：直接解压到要安装的目录即可
* Tomcat目录介绍
  * bin                     专门用来存放Tomcat服务器的**可执行程序**
  * conf                  专门用来存放Tomcat服务器的**配置文件**
  * lib                     专门用来存放Tomcat服务器的**jar包**
  * logs                  专门用来存放Tomcat服务器**运行时输出的日记信息**
  * temp                存放**运行时产生的临时数据**
  * webapps         专门用来**存放部署的web工程**
  * work                 Tomcat**工作时的目录**，用来存放Tomcat**运行时JSP翻译成Servlet的源码**，和**Session钝化（序列化）的目录**
* Tomcat服务器启动：
  * startup.bat，直接双击启动即可
  * cmd黑窗口启动：进入bin目录后，startup.bat；（若服务启动失败使用该方式可看到错误信息）
* 如何判断Tomcat启动成功：
  * http://localhost:8080
  * http://127.0.0.1:8080
  * http://本地IPV4地址:8080
* 停止Tomcat服务：
  * 直接关闭cmd窗口；
  * cmd黑串口 ctrl + c ；
  * 执行shutdown.bat
* 修改Tomcat端口（默认8080）：
  * conf 目录下 server.xml文件 将8080修改即可
  * 重启服务即可
* 访问Tomcat
  * 当访问http://ip:port/  
    * 没有工程名，默认访问Tomcat服务器的ROOT工程（webapps是专门存放web工程的包，所以ROOT工程就在webapps包下）
  * 当访问http://ip:port/工程名
    * 添加工程名后，默认访问的就是该工程下的index.html页面



### IDEA整合Tomcat

* 新建工程前
  * Settings---Application Servers---左上角+号（添加Tomcat Server）---选中Tomcat安装包（不要包含bin目录）

### IDEA创建动态Web工程

* 新建动态Web工程
  * New Project/Module---Java---next---Finish
  * 在新建的模块（或工程）---右键---Add Framework Support（添加框架支持）---勾选Web Application （Create web.xml）

* 新建目录
  * WEB-INF目录下---新建lib目录---用来存放jar包
* Web工程目录介绍
  * src               存放**Java源代码**
  * web             存放**web工程资源文件**；（html页面、css样式、js代码。。。）
  * WEB-INF     受服务器保护的目录，**浏览器无法直接访问该目录**的内容
  * lib                 存放**第三方jar包**
  * web.xml       是整个web工程的**配置部署描述文件**；可**配置web工程的组件**；（Servlet程序、Filter过滤器、Listener监听器、Session超时等等）
* 为动态web工程添加jar包
  * ![image-20230506224221950](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230506224221950.png)
  * ![image-20230506224317797](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230506224317797.png)

* IDEA中部署工程到Tomcat运行







平时上百度：http://www.baidu.com:80  （HTTP协议默认的端口号80）

## Servlet

* javax.servlet.Servlet

* Servlet是JavaSE的规范之一。（规范就是接口）
* JavaWeb的三大组件
  * Servlet程序
  * Filter过滤器
  * Listener监听器
* Servlet是运行在Web服务器上的一个Java小程序；通常通过HTTP协议（Hyper Text Transfer Protocol）来接收和响应Web客户端的请求

### Servlet技术

#### 实现Servlet程序

* 类实现Servlet接口，重写service方法（处理请求，响应数据）

* web.xml配置文件下配置Servlet程序的相关信息

  * ```xml
    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>com.lixc.servlet.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
    ```

#### Servlet生命周期

* 生命周期方法调用顺序

  1. 构造servlet---Servlet构造器

  2. 使用init方法进行初始化

  3. 执行service方法---处理来自客户端对service方法的所有调用

  4. 从服务中取出servlet，然后使用destroy方法销毁它，最后进行垃圾回收并终止它

* 构造servlet 和 对servlet初始化 两个方法是在第一次对Web工程进行访问时执行的；且整个Web服务运行期只会执行一次
* 客户端对service方法调用：每发起一次请求就会调用一次service方法
* destroy方法只有在Web工程停止时才会调用执行。



#### 通过继承HttpServlet实现Servlet程序

* 一般实际开发中，通常使用继承HttpServlet的方式来实现Servlet程序
* 继承HttpServlet实现Servlet程序（运行在Web服务器上的Java程序）
  * 类继承HttpServlet类
  * 重写doGet和doPost方法；分别进行两种HTTP两种访问方式的实现



#### Servlet类的继承体系

* javax.servlet.Servlet 接口：定义了Servlet程序的访问规范
  * javax.servlet.GenericServlet 类：实现了Servlet接口的空方法；并维护了一个ServletConfig类引用
    * javax.servlet.http.HttpServlet 类：重写了service方法---实现了请求的分发处理
      * 自定义类（继承HttpServlet）：重写请求的doGet、doPost等方法



### ServletConfig接口

* ServletConfig接口是**servlet容器使用的servlet配置对象，该对象在初始化期间将信息传给servlet。**
* Sevlet程序和ServletConfig对象都是由Tomcat负责创建的
* Servlet程序默认第一次访问该程序时创建，每个Servlet程序的创建伴随着一个ServletConfig配置对象的创建。
* ServletConfig接口的三大作用
  * 获取Servlet程序的别名servlet-name值
  * 获取Servlet程序的初始化参数init-param
  * 获取ServletContext对象
  * ![image-20230507165724900](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230507165724900.png)



### ServletContext接口

* 每个Java虚拟机的每个Web应用程序都有一个上下文
* ServletContext表示Servlet上下文对象
* 一个web工程只有一个ServletContext对象实例
* ServletContext对象是一个域对象
  * 域对象：可以和Map一样存取数据的对象，称为域对象
  * 这里的域指的是存取数据的操作范围；默认是整个Web工程



* ServletContext上下文对象作用

  1. 获取web.xml配置文件中的上下文参数context-param

  2. 获取当前的工程路径（格式：/工程路径）

  3. 获取工程部署后在服务器硬盘上的绝对路径

     * ```java
       //斜杠’/‘ 在服务器地址解析为：http://ip:port/工程名/    映射到IDEA代码的web目录！！！
       System.out.println(servletContext.getRealPath("/"));
       ```

  4. 和Map一样存取数据

* ![image-20230507182045637](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230507182045637.png)





### HTTP协议

* HTTP协议就是指客户端和服务端通信时，发送的数据需要遵守特定规则，就是HTTP协议
* HTTP协议中的数据又叫做报文

#### 请求的HTTP协议格式

* 客户端给服务器发送的数据就是请求
* GET请求
  * 请求行
    * 请求的方式                   GET
    * 请求的资源路径
    * 请求的协议的版本号    当前是HTTP/1.1
  * 请求头
    * key：value组成；不同键值对有不同含义
* POST请求
  * 请求行
    * 请求方式                           POST      
    * 请求的资源路径               
    * 请求的协议的版本号       当前是HTTP/1.1
  * 请求头
    * key：value组成
  * 空行
  * 请求体
    * 发送给服务器的请求数据



* 常用请求头说明
  * Accept
    * 表示客户端可以接收的数据类型
  * Accept-Language
    * 表示客户端可接受的语言类型
  * User-Agent
    * 客户端浏览器的信息
  * Host
    * 请求的服务器IP和端口号



* 当前使用到的GET、POST请求
  * GET
    * form标签中method="get"
    * img标签引用图片
    * a标签跳转
    * iframe标签引用html页面
    * link标签引用css样式
    * script标签引用js文件
    * 网页访问地址，直接回车
  * POST
    * form标签中method="post"



#### 响应的HTTP协议格式

* 响应格式
  * 响应行
    * 响应的协议和端口号
    * 响应的状态码
    * 响应的状态描述符
  * 响应头
    * key：value 不同响应头，对应不同含义
  * 空行
  * 响应体
    * 服务器返回的数据



* 常见响应码
  * 200   响应成功
  * 302   表示请求重定向
  * 404   服务器已接收到，但请求资源路径无法匹配
  * 500   服务器已接收到，但发生内部错误（一般是代码异常）





#### MIME类型说明

* MIME是HTTP协议中数据类型
* 格式：大类型/小类型

![image-20230507232326615](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230507232326615.png)



### 接口HttpServletRequest

* javax.servlet.http.HttpServletRequest;

* 每次只要有请求进入 Tomcat 服务器（Apache提供的轻量级Web服务器），Tomcat 服务器就会把**请求过来的 HTTP 协议信息**解析好封装到 **Request 对象**中。然后**传递到 service 方法**（doGet 和 doPost）中。我们可以通过 **HttpServletRequest 对象，获取**到所有请求的信息。



#### HttpServletRequest接口常用方法

* req . setCharacterEncoding("UTF-8");
  * 设置编码格式（POST请求参数会有中文乱码）

* getRequestURI()

  * 获取客户端请求资源路径

* getRequestURL()

  * 获取客户端请求地址

* getRemoteHost() + getRemotePort()

  * 获取客户端IP地址，端口号（端口号一直在变）

* getRequestHeader("User-Agent")

  * 获取客户端请求头（浏览器信息）

* getMethod()

  * 获取客户端请求范式

* getParameter("xxx")

  * 获取客户端请求参数

* getParameterValues("xxx")

  * 获取客户端请求参数（值为多个---例如复选框checkbox）

* ```java
  @Override
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      System.out.println("资源路径==>" + req.getRequestURI());//---/07_servlet/httpServletRequestUse
      System.out.println("绝对路径==>" + req.getRequestURL());//---http://localhost:8080/07_servlet/httpServletRequestUse
      System.out.println("客户端IP:PORT==>" + req.getRemoteHost() + ":" + req.getRemotePort());//---127.0.0.1
      System.out.println("客户端浏览器信息==>" + req.getHeader("User-Agent"));//
      System.out.println("客户端请求方式==>" + req.getMethod());//
  
      //获取请求参数
      System.out.println("==>" + req.getParameter("username"));
      System.out.println("==>" + req.getParameter("password"));
      System.out.println("==>" + Arrays.asList(req.getParameterValues("hobby")));
  }
  ```



#### 请求转发

* 请求转发就是：服务器收到请求后，从一个资源跳转到另一个资源（可以跳转到Servlet程序、html页面等）

* setAttribute("key","value")

  * 设置域对象

* getAttribute("key")

  * 获取域对象

* getRequestDispatcher("/xxx")

  * 获取请求转发对象（Servlet程序）
  * req.getRequestDispatcher("/servlet02") . forward(req,resp);   请求转发

* ```java
  public class Servlet1 extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          System.out.println("进入Servlet1程序");
          req.setCharacterEncoding("UTF-8");
          String param = req.getParameter("param");
          System.out.println("访问Servlet1传参：" + param);
  
          req.setAttribute("servlet1", "PASS");
          RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");//获取另一个servlet程序的调度
          requestDispatcher.forward(req, resp);
          System.out.println("Servlet1程序完成");
      }
  }
  
  public class Servlet2 extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          System.out.println("进入Servlet2程序");
          Object servlet = req.getAttribute("servlet1");
          System.out.println("获取Servlet1域数据："+servlet);
          try {
              Thread.sleep(2000);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
          System.out.println("Servlet2程序完成");
      }
  }
  ```



* 请求转发的特点

  * 浏览器地址栏没有发生变化（跳转到其他资源显示的地址还是 请求访问的第一个资源路径）

  * 请求转发从开始到结束都是一次请求

  * 请求转发涉及到的多个资源共享一个Request域中的数据

  * 可以转发到WEB-INF目录下

    * ```java
      RequestDispatcher requestDispatcher = req.getRequestDispatcher("/WEB-INF/provide.html");//获取另一个servlet程序的调度
      requestDispatcher.forward(req,resp);
      
      //会跳转到provide.html页面
      ```

  * 不可以访问Web服务工程外的资源

    * ```java
      RequestDispatcher requestDispatcher = req.getRequestDispatcher("http://www.baidu.com");//获取另一个servlet程序的调度
      requestDispatcher.forward(req, resp);
      
      //请求转发后 跳转404：HTTP Status 404 - /07_servlet/http://www.baidu.com
      ```

  * 请求转发涉及到的多个资源会顺序执行，请求者会等待转发的资源对象执行完毕后再执行

#### base标签

* ![image-20230508175711936](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230508175711936.png)





#### Web服务路径解析

* Web中的相对路径

  * ```
    .    	  表示当前目录
    ..		  表示上级目录
    资源名		表示同级目录下的资源
    ```

* Web中的绝对路径

  * http://ip:port/工程路径/资源路径

* 实际开发中，路径**都要使用绝对路径**

  * 绝对路径
  * base标签 + 相对路径

* " / " 斜杠的不同意义

  * ```html
    web中 /是一种绝对路径
    
    <!--被浏览器解析为：http://ip:port/-->
    <a href="/">斜杠</a>
    
    <!--被服务器解析为：http://ip:port/工程路径-->
    <url-pattern>/servlet01</url-pattern>
    getServletContext().getRealPath("/");
    req.getRequestDispatcher("/");
    
    <!--特殊情况：response.sendRedirect("/")；把/发送给浏览器解析，得到http://ip:port/-->
    ```





### 接口HttpServletResponse

* javax.servlet.http.HttpServletResponse；
* 每次有请求进入Tomcat服务器，Tomcat就会创建一个Response对象提供给Servlet程序使用进行数据的响应

#### Response响应数据用到的输出流

* resp . getOutputStream()；字节输出流，常用于下载（传递二进制数据）

* resp . getWriter()；字符输出流，常用与回传字符串

* 注意，两个输出流不能同时使用；使用字符流即不能再使用字节流，反之亦然。（IllegalStateException非法状态异常）

  * ```java
    HTTP Status 500 - getWriter() has already been called for this response
    ```

#### 响应的乱码处理

* 方式一（不推荐）：设置编码类型+设置响应头；

  * resp . setCharacterEncoding("UTF-8");
  * resp . setHeader( "Content-type","text/html" );

* 方式二：设置内容类型

  * **resp . setContentType( "text/html;charset=UTF-8" )；//先设置Content-Type，之后;分号隔开设置charset**

* 注意两种解决响应数据乱码问题，**无一例外都需要设置在 未创建输出流之前（POST请求设置编码也是在获取参数之前）**

* ```java
  @Override
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      System.out.println(resp.getCharacterEncoding());//ISO-8859-1
      
      //注意：两种解决响应乱码的方式；无一例外都需要设置在 未创建输出流之前
      
      //（不推荐）方式一：设置字符编码：UTF-8 ；设置响应头：Content-type  text/html
      /*resp.setCharacterEncoding("UTF-8");
      resp.setHeader("Content-type", "text/html");*/
  
      //方式二：设置内容类型：text/html;charset=UTF-8
      resp.setContentType("text/html;charset=UTF-8");
  
      PrintWriter writer = resp.getWriter();
      writer.println("HTTP Status 500---IllegalStateException：非法状态异常");
  }
  ```

#### 请求重定向

* 请求重定向（状态码302情况发生）；
* 指客户端向服务器发送请求时，接收到的302状态码表示之前地址可能已被废弃，可根据新提供的Location地址重定向访问资源路径

* ```java
  @Override
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      System.out.println("进入废弃资源Response1");
  
      //方式一： 设置响应码：302 设置响应头：Location  请求重定向访问地址
      /*resp.setStatus(302);
      resp.setHeader("Location", "/07_servlet/response2");*/
  
      //方式二： 设置发送重定向 /工程路径/资源路径 (web服务：浏览器解析路径http://ip:port)
      resp.sendRedirect("/07_servlet/response2");
  }
  ```

* 请求重定向特点
  * 浏览器地址栏发生变化
  * 请求重定向是多个客户端请求
  * 请求重定向涉及到的多个资源，Request域对象不共享
  * 不可重定向到WEB-INF目录下
  * 可以访问Web服务工程外资源
  * 请求重定向涉及到的资源会依次执行；每个资源执行结束才可重定向访问其他资源







## JSP

* Java Server Pages Java服务器页面
* 主要作用就是代替Servlet程序回传HTML页面的数据
* 由sun公司提供动态解决生成HTML文档的技术
* Servlet程序输出HTML页面---》引出JSP
  * Servlet输出页面复杂困难，不利于维护和调试
  * JSP动态页面技术帮助实现页面输出工作
* JSP页面访问不能向HTML页面一样，需要通过浏览器访问Tomcat服务器再访问JSP页面？？？？？？？？



### JSP本质

* JSP本质就是一个Servlet程序

  * 创建好的JSP页面我们第一次访问时，Tomcat服务器会自动将JSP页面翻译为一个.java源文件，并会对它进行编译生成.class字节码文件。

  * 查看Tomcat为JSP生成的.java源文件发现

    * ```java
      public final class testJSP_jsp extends org.apache.jasper.runtime.HttpJspBase...{
          ...
          out.write("\r\n");
            out.write("\r\n");
            out.write("<html>\r\n");
            out.write("<head>\r\n");
            out.write("    <title>Title</title>\r\n");
            out.write("</head>\r\n");
            out.write("<body>\r\n");
            out.write("这是由JSP输出的页面---请求转发\r\n");
            out.write("</body>\r\n");
            out.write("</html>\r\n");
          ...
      }
      
      public abstract class HttpJspBase extends HttpServlet implements HttpJspPage {}
      ```

    * JSP翻译而来的.java文件继承了HttpJspBase抽象类，而HttpJspBase又直接继承了HttpServlet抽象类；所以证明JSP本质上就是一个Servlet文件。

    * 而由JSP翻译而来的.java文件里底层实现中，JSP中的HTML页面内容在_jspService方法中通过输出流直接响应给客户端。



### JSP的语法

#### JSP头部的page指令

* ```jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  ```

* JSP中的**page指令可以修改JSP页面中的一些重要的属性或行为**

* page指令可修改的属性

  * language     		 default="java"

    * JSP翻译后的代码语言文件---默认且当前只支持Java

  * extends

    * 和使用Java一样，翻译出的 “jsp文件名_jsp.java”代码文件默认继承谁

  * contentType    	default="text/html; ISO-8859-1"

    * JSP返回的数据类型是什么；源码中的response . setContentType();

  * pageEncoding 

    * 表示当前JSP页面文件本身的字符集

  * import 

    * 和使用Java一样，导包、导类

  * session                 default="true"

    * 会话

  * buffer                   default="8kb"

    * out输出流使用；设置out缓存区大小

  * autoFlush            default="true"

    * out输出流使用；缓存区满后是否自动刷新缓存区

  * errorPage 

    * 设置当JSP页面运行时出错，自动跳转的页面路径

  * isErrorPage         default="false"

    * 设置当前页面是否为错误跳转页面，默认false。true可以获取异常信息

  * ```jsp
    <xsd:element name = "page">
        <xsd:complexType>
          <xsd:attribute name = "language" default = "java" type = "xsd:string"/>
          <xsd:attribute name = "extends" type = "TypeName"/>
          <xsd:attribute name = "contentType" default = "text/html; ISO-8859-1" type = "ContentType"/>
          <xsd:attribute name = "pageEncoding" use = "optional" type = "PageEncoding"/>
          <xsd:attribute name = "import" type = "ImportList"/>
          <xsd:attribute name = "session" default = "true" type = "Bool"/>
          <xsd:attribute name = "isELIgnored" default = "true" type = "Bool"/>
          <xsd:attribute name = "buffer" default = "8kb" type = "BufferSize"/>
          <xsd:attribute name = "autoFlush" default = "true" type = "Bool"/>
          <xsd:attribute name = "isThreadSafe" default = "true" type = "Bool"/>
          <xsd:attribute name = "info" type = "xsd:string"/>
          <xsd:attribute name = "errorPage" type = "RelativeURL"/>
          <xsd:attribute name = "isErrorPage" default = "false" type = "Bool"/>
    
          <!-- @since JSP 2.1 -->
        <xsd:attribute name = "deferredSyntaxAllowedAsLiteral" use = "optional" type = "Bool"/>
        <!-- @since JSP 2.1 -->
        <xsd:attribute name = "trimDirectiveWhitespaces" use = "optional" type = "Bool"/>
        </xsd:complexType>
      </xsd:element>
    ```



#### JSP的常用脚本

##### 声明脚本（极少使用）

* ```jsp
  <%! 声明Java代码 %>
  ```

* 声明脚本作用

  * 可以声明类属性、代码块、方法、内部类

* ```jsp
  <%--声明属性--%>
  <%!
      private int id;
      private String username;
      private String password;
  %>
  <%--声明代码块--%>
  <%!
      {
          System.out.println("普通代码块");
          System.out.println(new innerClass().name);
      }
      static {
          System.out.println("静态代码块");
          System.out.println(new staticInnerClass().name);
      }
  %>
  <%--声明方法--%>
  <%!
      public void commonMethod() {
      }
      public static void staticMthod() {
      }
  %>
  <%--声明内部类--%>
  <%!
      public class innerClass {
          private final String name = "innerClass";
      }
      public static class staticInnerClass {
          private final String name = "staticInnerClass";
      }
  %>
  ```





##### 表达式脚本（常用）

* ```jsp
  <%= 表达式 %>
  ```

* 表达式脚本作用：**在JSP页面上输出数据**

* ```jsp
  <%= 123 %><br/>
  <%= "123" %><br/>
  <%= "lixc ❤ vv" %><br/>
  <%= true %><br/>
  ```

* **表达式脚本特点：**

  * 表达式脚本会在.java源文件中_jspService() 方法中出现
  * 输出页面的方式：out . print( 表达式脚本中的数据 )；//out对象是JspWriter输出流对象
  * 由于表达式脚本出现在_jspService() 方法中，所以该方法的对象都可以直接使用
  * 表单式脚本的内容不能以；分号结尾



##### 代码脚本（最常用）

* ```jsp
  <% Java语句 %>
  ```

* 代码脚本作用：**在JSP页面，使用Java编写需要的业务**

* ```jsp
  <%--代码脚本--%>
  <%
      System.out.println(Arrays.asList(request.getParameterValues("params")));
  %>
  
  
  <%--代码脚本 配合 表达式脚本一起使用 向JSP页面输出数据--%>
  <%
      for (int i = 0; i < request.getParameterValues("params").length; i++) {
  %>
  <%= request.getParameterValues("params")[i] %>
  <%
      }
  %>
  ```

* **代码脚本特点：**

  * 代码脚本出现在.java源文件的_jspService() 方法中
  * _japService() 方法中的现有对象可直接使用
  * 可以有多个代码脚本组成一个完整的Java业务
  * 代码脚本可以和表达式脚本配合使用，在JSP页面输出数据



#### JSP的注释

* HTML语言注释
  * <!-- 书写HTML注释 -->
  * out.write("<!--html注释-->\r\n");   
  * **HTML注释会通过out.write();方式响应到客户端**
* Java代码注释
  * //Java单行注释
  * /* Java多行注释 */
  * **Java注释会被翻译到.java源代码中（ _jspService()方法中 ）**
* JSP语言注释
  * <%-- JSP语言注释 --%>
  * **可以注释掉JSP页面所有代码---包括HTML注释、Java注释**



### JSP九大内置对象

* JSP内置对象，指的是Tomcat服务器在将JSP页面翻译为Servlet程序后，内部提供的九大对象，叫做内置对象

  * _jspService() 方法中体现

  * ```
    request			HttpServletReuqest 请求对象
    response		HttpServletResponse  响应对象
    config			ServletConfig     配置对象
    application		ServletContext     上下文对象
    session			HttpSession    会话对象
    out				JspWriter    输出流对象
    page			Object   指向当前JSP的对象
    pageContext     PageContext  JSP的上下文对象
    exception		Throwable  异常对象
    ```

### JSP四大域对象

* 域对象指的是可以向Map一样存取数据的对象。
  * 四大域对象功能一样
  * 四大域对象对数据的存取范围不同
    * pageContext--->PageContext：JSP上下文对象
      * 仅在当前JSP页面生效
    * request--->HttpServletRequest：请求对象
      * 仅在一次请求生效（客户端向服务器发起一次请求）
    * session--->HttpSession：会话对象
      * 一次会话范围内有效（打开浏览器来访问服务器，直到关闭浏览器；算作一次会话）
    * application--->ServletContext：Servlet上下文对象
      * 整个Web工程运行期有效
* 使用的优先级
  * 虽然四个域对象都可以存取数据，但使用的优先级不同
  * 根据存取数据的作用范围 从小到大；使用的优先级 从大到小（性能考虑）



### JSP中out和resp.getWriter()输出数据区别

* ![image-20230509193809668](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230509193809668.png)

* 若在JSP页面只做了out.write和response.write操作，未对out输出流对象进行flush刷新操作，则response写入的数据无论在JSP页面何处进行数据，呈现在客户端的数据都是先将response数据输出，再轮到out对象数据输出

* 由于JSP页面翻译后得出的.java源文件中使用的都是out输出流的形式进行输出，所以我们在JSP页面对客户端进行数据输出时，也是用out输出流对象。避免打乱页面输出内容的顺序（out对象未flush，response输出流会先输出）



* JSP页面out输出流对象两种数据输出方式
  * out.write
    * 输出字符串没问题
    * 输出int类型数据异常---(char)i 会将int类型数据转为ASCII码表对应的字符，数据失准
  * out.print
    * 输出任何数据类型都没问题（底层都将数据进行了String.valueOf()转为字符串）
  * 深入源码，浅出结论
    * JSP页面下，对数据进行输出可以统一使用out.print()；方法



### JSP的常用标签

#### 静态包含

* ```jsp
  <%--主体JSP页面--%>
  <body>
  <h1>页行首数据</h1>
  <h1>页主题数据</h1>
  <%@ include file="/ReferencePage.jsp" %>
  </body>
  
  <%--被静态包含的JSP页面--%>
  <body>
  <h1>页脚数据</h1>
  </body>
  
  
  输出：
  页行首数据
  页主题数据
  页脚数据
  
  
  底层：
        out.write("<html>\r\n");
        out.write("<head>\r\n");
        out.write("    <title>标签</title>\r\n");
        out.write("</head>\r\n");
        out.write("<body>\r\n");
        out.write("<h1>页行首数据</h1>\r\n");
        out.write("<h1>页主题数据</h1>\r\n");
        out.write("<html>\r\n");
        out.write("<head>\r\n");
        out.write("    <title>被包含JSP页面</title>\r\n");
        out.write("</head>\r\n");
        out.write("<body>\r\n");
        out.write("<h1>页脚数据</h1>\r\n");
        out.write("</body>\r\n");
        out.write("</html>\r\n");
        out.write("</body>\r\n");
        out.write("</html>\r\n");
  ```

* 静态包含语法：

  * <%@  include file="/xxx.jsp" %>

* 静态包含特点：

  * 使用**静态包含标签，不会触发Tomcat服务器对被包含的JSP页面进行翻译**（不会生成.java、.class字节码文件）
  * 将被包含JSP页面的数据拼接在out.write() 输出数据中

#### 动态包含

* ```jsp
  <body>
  <h1>页行首数据</h1>
  <h1>页主题数据</h1>
  <jsp:include page="/ReferencePage.jsp">
      <jsp:param name="username" value="lixc"/>
      <jsp:param name="password" value="lz001214"/>
  </jsp:include>
  </body>
  
  <body>
  <h1>页脚数据</h1>
  <%= "包含该页面传参：" + request.getParameter("username")%>
  <%= "包含该页面传参：" + request.getParameter("password")%>
  </body>
  
  输出：
  页行首数据
  页主题数据
  页脚数据
  包含该页面传参：lixc 包含该页面传参：lz001214
  
  底层：
  JspRuntimeLibrary.include(request, response, "/ReferencePage.jsp" + "?" + org.apache.jasper.runtime.JspRuntimeLibrary.URLEncode("username", request.getCharacterEncoding())+ "=" + org.apache.jasper.runtime.JspRuntimeLibrary.URLEncode("lixc", request.getCharacterEncoding()) + "&" + org.apache.jasper.runtime.JspRuntimeLibrary.URLEncode("password", request.getCharacterEncoding())+ "=" + org.apache.jasper.runtime.JspRuntimeLibrary.URLEncode("lz001214", request.getCharacterEncoding()), out, false);
  ```

* 动态包含语法：

  * <jsp:include page="/xxx.jsp" >  可传参  < /jsp:include>

* 动态包含特点

  * 使用动态包含标签，会对被包含JSP页面进行翻译生成相应.java、.class文件
  * 对被包含页面的数据，底层源码使用如下代码进行JSP页面调用
    * JspRuntimeLibrary.include(request, response, "/ReferencePage.jsp"......., out, false);
  * 动态包含可传递参数

#### JSP标签-转发

* ```jsp
  <body>
  <h1>页行首数据</h1>
  <h1>页主题数据</h1>
  <jsp:forward page="/ReferencePage.jsp">
      <jsp:param name="username" value="lixc"/>
      <jsp:param name="password" value="lz001214"/>
  </jsp:forward>
  </body>
  
  <body>
  <h1>页脚数据</h1>
  <%= "包含该页面传参：" + request.getParameter("username")%>
  <%= "包含该页面传参：" + request.getParameter("password")%>
  </body>
  
  输出：
  页脚数据
  包含该页面传参：lixc 包含该页面传参：lz001214
  ```

* JSP页面转发标签语法

  * <jsp:forward page="/xxx.jsp">  可传参 < /jsp:forward>

* 使用JSP页面转发标签特点：

  * 使用页面转发标签，会对转发页面和被转发页面都会被Tomcat服务器自动翻译.java、.class文件
  * 不会再对转发页面内容进行响应
  * 地址是转发页面地址，页面内容是被转发页面内容



### JSP练习

* 九九乘法口诀表

  * ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>九九乘法口诀表</title>
    </head>
    <body>
    <table>
        <%
            for (int i = 1; i < 10; i++) {
        %>
        <tr>
            <%
                for (int j = 1; j <= i; j++) {
            %>
            <td>
                <%=
                j + "*" + i + "=" + (i * j)
                %>
            </td>
            <%
                }
            %>
        </tr>
        <%
            }
        %>
    </table>
    </body>
    </html>
    ```

* ```
  
  stuInfo=赵一-18-90&stuInfo=钱儿-18-90&stuInfo=孙三-18-90&stuInfo=李四-18-90&stuInfo=周五-18-90&stuInfo=吴六-18-90&stuInfo=郑七-18-90&stuInfo=王八-18-90&stuInfo=柴九-18-90&stuInfo=历史-18-90
  
  
  
  ```





### Listener监听器

* Listener是JavaSE的规范之一（接口）

* JavaWeb三大组件

  * Servlet程序
    * Servlet程序就是在Web服务上运行的一个Java小程序；通常通过HTTP协议进行接收和响应客户端的请求
  * Filter过滤器
  * Listener监听器
    * 监听运行在Web服务上某种事务的变化，通过监听程序的状态进行相关的业务操作

* 创建Servlet上下文对象监听器（监听Web服务的启停）

  * 实现ServletContextListener监听器接口
    * 实现ContextInitialized方法
    * 实现ContextDestroyed方法
  * web.xml配置监听器

* ```java
  public class ContextListener implements ServletContextListener {
      @Override
      public void contextInitialized(ServletContextEvent servletContextEvent) {
          System.out.println("Web服务启动---Servlet上下文对象创建");
      }
  
      @Override
      public void contextDestroyed(ServletContextEvent servletContextEvent) {
          System.out.println("Web服务停止---Servlet上下文对象销毁");
      }
  }
  
  
  web.xml:
  <listener>
          <listener-class>com.lixc.listen.ContextListener</listener-class>
  </listener>
  ```





## EL表达式

* Expression Language 表达式语言；
* EL表达式作用：使用EL表达式来替代JSP（Java服务器页面---动态页面技术）页面中的表达式脚本进行数据输出。（更加简洁）
* 主要使用EL表达式在JSP页面进行数据输出（主要输出域对象中的数据）
* EL表达式格式：${ 表达式 }
* EL表达式输出值为null时，会以空串形式输出；JSP表达式脚本输出null值，输出的就是null字符串

### EL表达式搜索域数据顺序

* EL表达式根据域对象的作用域大小来进行搜索

  * 当四个域对象含有相同key数据时，EL表达式按相同key来搜索域数据，会按照域对象作用范围从小到大依次所搜
  * 域对象作用范围越小，EL表达式越先搜索

* ```jsp
  <body>
  <%
      pageContext.setAttribute("theSameKey", "pageContextVal");
      request.setAttribute("theSameKey", "requestVal");
      session.setAttribute("theSameKey", "sessionVal");
      application.setAttribute("theSameKey", "applicationVal");
  %>
  <%--
      四个域对象存在相同key,搜索顺序依次是：
          pageContext
          request
          session
          注意会话对象较为特殊，若只存在session和application域对象且之前已获取到域对象的数据，则即使注释掉session相关内容也会搜索到
          因为此时浏览器尚未关闭，一次会话尚未结束
          application
  --%>
  EL表达式搜索到的数据是：${theSameKey}
  </body>
  ```





### EL表达式输出JavaBean普通、数组、List集合、Map集合数据

* ```jsp
  <body>
  <%
      Person person = new Person();
      person.setName("lixc");
      person.setTitle(new String[]{"handsome", "smart", "kind", "..."});
      ArrayList<String> strings = new ArrayList<>();
      strings.add("list1");
      strings.add("list2");
      strings.add("list3");
      person.setList(strings);
      HashMap<String, String> hashMap = new HashMap<>();
      hashMap.put("key1", "val1");
      hashMap.put("key2", "val2");
      hashMap.put("key3", "val3");
      person.setMap(hashMap);
  
      pageContext.setAttribute("person", person);
  %>
  <%--
      EL表达式获取Bean对象属性对应数据时，会去获取该属性的get方法
      所以即使一个对象没有维护属性，只要有getXxx方法，即可获取数据
  --%>
  使用EL表达式获取Person对象（JavaBean）name属性值：${person.name}<br/>
  使用EL表达式获取Person对象（JavaBean）title属性值：${person.title[0]}<br/>
  使用EL表达式获取Person对象（JavaBean）list属性值：${person.list[0]}<br/>
  使用EL表达式获取Person对象（JavaBean）map属性值：${person.map.key1}<br/>
  使用EL表达式获取Person对象（JavaBean）getAge方法数据：${person.age}<br/>
  </body>
  ```



### EL表达式运算

#### 关系运算

* ```jsp
  EL表达式关系运算：<br/>
  ${18==18} 或 ${18 eq 18}<br/>
  ${18 != 18} 或 ${18 ne 18}<br/>
  ${18 > 18} 或 ${18 gt 18}<br/>
  ${18<18} 或 ${18 lt 18}<br/>
  ${18>=18} 或 ${18 ge 18}<br/>
  ${18<=18} 或 ${18 le 18}<br/>
  <hr/>
  ```

#### 逻辑运算

* ```jsp
  EL表达式逻辑运算<br/>
  ${true && true} 或 ${true and true}<br/>
  ${true || true} 或 ${true or true}<br/>
  ${! true} 或 ${not true}<br/>
  <hr/>
  ```

#### 算数运算

* ```jsp
  EL表达式算数运算<br/>
  ${12+14}<br/>
  ${12-14}<br/>
  ${12*14}<br/>
  ${12/14} 或 ${12 div 14}<br/>
  ${12%14} 或 ${12 mod 14}<br/>
  <hr/>
  ```

#### empty

* EL表达式empty运算判断一个数据是否为空（空返回true）
  * null、“”空字符串、空数组、空List集合、空Map集合

* ```jsp
  EL表达式empty运算<br/>
  <%
      String[] strings = new String[]{};
      ArrayList<String> arrayList = new ArrayList<>();
      HashMap<String, Object> hashMap = new HashMap<>();
      pageContext.setAttribute("strings", strings);
      pageContext.setAttribute("arrayList", arrayList);
      pageContext.setAttribute("hashMap", hashMap);
  %>
  ${empty null}<br/>
  ${empty ""}<br/>
  ${empty strings}<br/>
  ${empty arrayList}<br/>
  ${empty hashMap}<br/>
  ```

#### 三元运算

* ```jsp
  EL表达式三元运算<br/>
  ${12==14?"forgetIt":"I miss u >>>--❤❤❤-->"}
  ```

#### . 运算和 [ ] 运算

* ```jsp
  EL表达式.运算和[]运算<br/>
  <%
      HashMap<String, String> hashMap1 = new HashMap<>();
      hashMap1.put("123", "123valhashMap1");
      hashMap1.put("1-2-3", "1-2-3val");
      hashMap1.put("1+2+3", "1+2+3val");
      hashMap1.put("1.2.3", "1.2.3val");
      pageContext.setAttribute("hashMap1", hashMap1);
  %>
  ${123}<br/>
  ${hashMap1["1-2-3"]}<br/>
  ${hashMap1["1+2+3"]}<br/>
  ${hashMap1["1.2.3"]}<br/>
  ```





### EL表达式11个隐含对象

* ```
  对象名					对象数据类型		 
  pageContext			PageContextImpl    		可以获取JSP中九大内置对象
  
  pageScope			Map<String,Object>		获取PageContext域对象
  requestScope		Map<String,Obejct>		获取Request域对象
  sessionScope		Map<String,Obejct>		获取Session域对象
  applicationScope	Map<String,Object>		获取ServletContext域对象
  
  param				Map<String,String>		获取请求参数
  paramValues			Map<String,String[]>	获取请求参数---多个值
  
  header				Map<String,String>		获取请求头
  headerValues		Map<String,String[]>	获取请求头---多个值
  
  cookie				Map<String,Cookie>		获取当前请求的cookie信息
  
  initParam			Map<String,String>		获取web.xml配置文件中Context-param参数
  ```

#### 获取EL表达式四大域隐含对象数据

* ```jsp
  <body>
  <%
      pageContext.setAttribute("page", "pageContextVal");
      request.setAttribute("req","requestVal");
      session.setAttribute("sess","sessionVal");
      application.setAttribute("app","ServletContextVal");
  
      pageContext.setAttribute("key", "pageContextVal");
      request.setAttribute("key","requestVal");
      session.setAttribute("key","sessionVal");
      application.setAttribute("key","ServletContextVal");
  %>
  ${page}<br/>
  ${req}<br/>
  ${sess}<br/>
  ${app}<br/>
  <hr/>
  ${pageScope.key}<br/>
  ${requestScope.key}<br/>
  ${sessionScope.key}<br/>
  ${applicationScope.key}<br/>
  </body>
  ```



#### EL表达式pageContext对象使用

* ```jsp
  <body>
  <%--1. 协议：
      2. 服务器 ip：
      3. 服务器端口：
      4. 获取工程路径：
      5. 获取请求方法：
      6. 获取客户端 ip 地址：
      7. 获取会话的 id 编号：
  --%>
  ${pageContext.request.scheme}<br/>
  ${pageContext.request.serverName}<br/>
  ${pageContext.request.serverPort}<br/>
  ${pageContext.request.contextPath}<br/>
  ${pageContext.request.servletPath}<br/>
  ${pageContext.request.method}<br/>
  ${pageContext.request.remoteHost}<br/>
  ${pageContext.session.id}<br/>
  <%--http
  localhost
  8080
  /09_el_jstl
  /ELUsePageContextObj.jsp
  GET
  127.0.0.1
  BD5EA7185A21949283CBB07C284FC939--%>
  </body>
  ```

  

#### EL表达式其他隐含对象使用

* ```jsp
  <body>
  ${param.name}<br/>
  ${paramValues.hobby[1]}<br/>
  ${header}<br/>
  ${headerValues}<br/>
  ${cookie}<br/>
  ${initParam["Context-param"]}<br/>
  </body>
  
  
      <context-param>
          <param-name>Context-param</param-name>
          <param-value>conParam</param-value>
      </context-param>
  
  http://localhost:8080/09_el_jstl/ELUseContainObj.jsp?name=lixc&hobby=100&hobby=read
  ```



## JSTL标签库



## 文件上传和下载

### 文件上传

* 步骤
  * 工程或模块中需要导入两个jar文件
    * commons-fileupload-1.2.1.jar
    * commons-io-1.4.jar
    * 两个Jar包中，常用类
      * ServletFileUpload类；用来解析上传的数据
        * isMultipartContent( HttpServletRequest req )
          * 判断上传的数据格式是否为多段格式（多段表示上传文件）
        * parseRequest( HttpServletRequesr req )
          * 解析上传数据
      * FileItem类；表示每一个表单项
        * isFormFiled()
          * 判断当前表单项是否为普通表单类型（false表示上传的文件类型）
        * getFiledName()
          * 获取表单项name属性值
        * getString()
          * 获取当前表单项的值
        * getName()
          * 获取上传的文件名
        * write(File file)
          * 将上传的文件写入到file参数指向的硬盘位置
  * 设置JSP动态页面
    * 使用form标签
      * method=“post”
      * enctype="multipart/form-data"
      * input标签
        * 上传附件字段属性 type="file"
  * 设置Servlet程序
    * 判断上传的数据是否为多段数据（只有多段数据才表示上传的是文件）
    * 创建FileItemFactory工厂实现类
    * 创建用于解析上传数据的工具类ServletFileUpload
    * 解析上传数据，得到每一个表单项FileItem
    * 判断每一个表单项是否为文件类型
      * 普通类型注意乱码问题



* ```java
  @Override
  protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      //上传附件
      /*
       * 判断上传的数据是否为多段数据（只有多段数据才表示上传的是文件）
       * 创建FileItemFactory工厂实现类
       * 创建用于解析上传数据的工具类ServletFileUpload
       * 解析上传数据，得到每一个表单项FileItem
       * 判断每一个表单项是否为文件类型
       * 普通类型注意乱码问题
       * */
      try {
          req.setCharacterEncoding("UTF-8");
          if (ServletFileUpload.isMultipartContent(req)) {
              FileItemFactory fileItemFactory = new DiskFileItemFactory();
              ServletFileUpload servletFileUpload = new ServletFileUpload(fileItemFactory);
              List<FileItem> fileItemList = servletFileUpload.parseRequest(req);
              for (FileItem fileItem : fileItemList) {
                  if (!fileItem.isFormField()) {
                      System.out.println("上传文件---name属性值：" + fileItem.getFieldName());
                      System.out.println("上传文件---文件名：" + fileItem.getName());
                      fileItem.write(new File("D:\\workDevelop\\PlantingTrees\\JavaWeb\\09_el_jstl\\web\\file\\" + fileItem.getName()));
                  } else {
                      System.out.println("普通数据类型name属性值：" + fileItem.getFieldName());
                      System.out.println("普通数据类型值：" + fileItem.getString());
                  }
              }
          }
      } catch (Exception e) {
          e.printStackTrace();
      }
      /*
       * ServletFileUpload类；用来解析上传的数据
       *      isMultipartContent( HttpServletRequest req )
       *      判断上传的数据格式是否为多段格式（多段表示上传文件）
       *      parseRequest( HttpServletRequesr req )
       *      解析上传数据
       * FileItem类；表示每一个表单项
       *      isFormFiled()
       *      判断当前表单项是否为普通表单类型（false表示上传的文件类型）
       *      getFiledName()
       *      获取表单项name属性值
       *      getString()
       *      获取当前表单项的值
       *      getName()
       *      获取上传的文件名
       *      write(File file)
       *      将上传的文件写入到file参数指向的硬盘位置
       * */
  }
  ```



### 文件下载

* ![image-20230510232444436](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230510232444436.png)

* 下载常用API

  * response . getOutputStream()
  * servletContext . getResourceAsStream()
  * servletContext . getMimeType()
  * response . SetContentType()
  * response.setHeader( "Content-Disposition","attachment; file=1.jpg" );

* 文件下载步骤

  * 获取下载文件名
  * 获取文件内容
  * 设置响应头，告知客户端返回的数据类型
  * 设置响应头，告知客户端返回的数据如何处理
  * 将数据输出

* ```java
  @Override
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
      //1）获取要下载的文件名
      String fileName = "爱心.jpg";
  
      //2）读取要下载的文件内容
      ServletContext servletContext = getServletContext();
      InputStream resourceAsStream = servletContext.getResourceAsStream("/file/" + fileName);
  
      //3）下载内容回传客户端之前：通过响应头告诉客户端返回的数据类型
      String mimeType = servletContext.getMimeType("/file/" + fileName);
      resp.setContentType(mimeType);
  
      //4）下载内容回传客户端之前：通过响应头告诉客户端收到的数据需要下载使用；并处理中文乱码问题
      if (req.getHeader("User-Agent").contains("FireFox")) {
          //resp.setHeader("Content-Disposition", "attachment; filename=" + fileName);//Content-Disposition表示内容数据怎样处理  attachment表示附件，表示下载使用；filename=文件名
          resp.setHeader("Content-Disposition", "attachment; filename==?charset?B?" + new BASE64Encoder().encode(fileName.getBytes(StandardCharsets.UTF_8)) + "?=");
      } else {
          resp.setHeader("Content-Disposition", "attachment; filename=" + URLEncoder.encode(fileName, "UTF-8"));
      }
  
      //5）下载内容回传客户端
      ServletOutputStream outputStream = resp.getOutputStream();
      IOUtils.copy(resourceAsStream, outputStream);//读取输入流中全部数据，复制到输出流中
  }
  ```







## MVC概念

* MVC全称：Model模型、View视图、Controller控制器
* MVC最早出现在JavaSE的web层。有效的指导web层的代码进行分离、单独工作
* Model模型：
  * 将和业务逻辑相关的数据封装为JavaBean，不掺杂任何与数据处理相关代码
* View视图：
  * 只负责数据和页面的显示，不接收任何与显示数据无关的代码
* Controller控制器：
  * 只负责接收请求，调用业务层代码处理请求，然后派发页面
  * 是一个调度者的角色---Servlet，通过转发或重定向来调度页面
* MVC是一种思想，目的为了解耦合



## Cookie

* Cookie是服务器通知客户端保存键值对的一种技术
* 客户端有了Cookie后，每次请求都发送给服务器
* 每个Cookie大小不能超过4kb
* JavaSE文档介绍Cookie
  * implements Cloneable
  * 创建一个cookie，cookie是Servlet程序发送到Web浏览器的少量信息，信息由浏览器保存，然后发送回服务器
  * cookie的值可以唯一的标识客户端，因此常用作会话管理

### 创建Cookie

* 浏览器（客户端）访问Servlet创建Cookie，发送到客户端

  * ```java
    Cookie cookie = new Cookie( "key1" , "value1" );
    response.addCookie(cookie);//new Cookie之后，必须通过HttpServletResponse对象添加cookie响应头，客户端才会收到
    ```

### Cookie的获取

* 通过request.getCookies()；方法获取所有Cookie

* 可以封装获取特定Cookie的Util类

  * ```java
    public static Cookie findCookie(Cookie[] cookies,String cookieKey){
        if( cookieKey==null || cookies==null || cookies.length==0 ){
            return null;
        }else{
            for(Cookie cookie: cookies){
                if(cookieKey.equals(cookie.getValue())){
                    return cookie;
                }
            }
        }
        return null;
    }
    ```

### Cookie值的修改

* 无论是创建还是修改cookie的值，都不支持中文和特殊字符，可以考虑使用Base64编码

* ```java
  protected void updateCookieValue(HttpServletRequest req,HttpServletResponse resp){
      //方式一：直接新建想要修改某个key对应的Cookie对象，并为value赋值
      Cookie cookie = new Cookie("key","newValue");
      resp.addCookie(cookie);
      
      //方式二：获取到想要修改某个key的Cookie对象，设置新value
      Cookie cookie = CookieUtils.findCookie(req.getCookies(),"key");//使用自定义查询Cookie对象工具类---上面有写
      cookie.setValue("newValue");
      resp.addCookie(cookie);
  }
  ```

### 浏览器查看Cookie

* 谷歌浏览器
  * F12
    * 查看Application
      * 查看Cookie---会有对应的访问IP地址点击即可
        * 包含：key、value、path、MAX-AGE（默认session）

### Cookie生命控制

* 就是控制Cookie对象/键值对数据 何时销毁（清除）
* 通过cookie . setMaxAge(int类型数据)；方法来设定Cookie对象的存活时间
  * 正数 表示存活多久，单位为秒
  * 负数 默认值就是-1；
    * 表示Cookie存活时间为客户端浏览器打开后第一次访问获取到Cookie键值对数据，直到浏览器关闭。期间Cookie一直存活
  * 0     表示立即销毁

### Cookie有效路径Path的设置

* Path就是设定获取Cookie的权限访问路径；默认是工程路径
* cookie.setPath("");

### Cookie免用户名登录





## Session会话

### 什么是Session会话

* Session是一个接口（HttpSession）
* Session就是会话，用来维护一个客户端和服务器之间关联的一种技术
* 每个客户端都有一个本身的Session会话
* Session会话中，常用来保存用户登陆之后的信息
* Cookie是维护在客户端的，Session是维护在服务器的



### 如何创建和获取Session会话

* 创建和获取Session会话，它们的API一致
  * request . getSession()
    * 第一次调用，创建Session会话
    * 之后调用，获取之前创建的Session会话对象
* 判断Session会话对象是否为新创建的
  * session.isNew();返回true表示新创建的会话对象，false表示为获取的会话对象（不是新创建的）
* 获取Session会话对象的ID
  * 每个会话都有一个唯一的Session会话对象id
  * session.getId()；获取Session对象的会话ID值

### Session域数据的存取

* setAttribute
* getAttribute

### Session生命周期的控制

* Session超时指的是，客户端两次请求之间的最大时长

  * 设定Session超时时长为3秒，但客户端一直在向服务器发送请求，则Session超时时长一直被重置，只有客户端停下请求之后，距离下次请求超过3秒，该Session会话对象才会销毁

* 设置及获取超时时长

  * Tomcat中Session对象的超时时长默认为30分钟

    * 在Tomcat整合Java程序的Web服务后，conf目录下的web.xml文件中

      * ```xml
        <session-config>
            <session-timeout>30</session-timeout>
        </session-config>
        ```

    * 若想配置整个Web工程的创建的所有Session会话对象的超时时长，可在该Web工程下web.xml文件中进行如上配置

    * 若只是指定某些、某个Session会话对象超时时长，使用setMaxInactiveInterval( int )方法设定即可

  * setMaxInactiveInterval( int类型 )；以秒为单位，超过制定时长，session被销毁

    * 正数：以秒为单位，设定超时时间
    * 负数：永不超时，极少使用

  * getMaxInactiveInterval()；以秒为单位；

  * invalidate()；

    * 立即超时，使此会话无效，然后取消对任何绑定它的对象的绑定
    * 若对已经无效的会话调用此方法，报异常IllegalStateException



### Session与浏览器之间关联的技术内幕

* Session技术底层是基于Cookie技术实现的

* ![image-20230515112813991](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230515112813991.png)





## Filter过滤器

* JavaWeb三大组件
  * Servlet程序
  * Filter过滤器
  * Listener监听器
* JavaSE的规范之一；（接口）
* Filter过滤器作用：拦截请求，过滤响应
  * 拦截请求常见应用场景
    * 权限检查
    * 日记操作
    * 事务管理

### 初体验

* 引出

  * 业务场景：
    * web工程下，某一目录下的文件，只有用户登录才可访问
  * 现有技术考虑
    * 将用户登录后的信息保存到Session域中，在权限目录下对Session域中的用户数据做非空判断（未登录跳转到首页）
    * 该方案仅仅可用在JSP页面中

* Filter过滤器使用步骤

  1. 实现Filter接口

  2. 实现doFilter过滤方法

  3. 配置文件web.xml中配置过滤器信息

     1. ```xml
        <filter>
            <filter-name>过滤器别名</filter-name>
            <filter-class>过滤器路径</filter-class>
        </filter>
        <filter-mapping>
            <filter-name>过滤器别名名称映射</filter-name>
            <url-pattern>配置拦截路径信息</url-pattern>
        </filter-mapping>
        ```



### 完整的用户登录

利用Filter过滤器做出完整的用户登录

1. 创建一个Filter过滤器
   * 实现Filter接口、实现doFilter方法
2. 创建Servlet程序
3. web.xml文件做出Servlet程序、Filter过滤器配置
4. 创建Filter配置拦截路径相关的文件
   * 直接访问该文件在Web工程下的路径，会被Filter过滤器拦截
   * Filter过滤器检测用户是否已通过Servlet完成用户登录及保存至session域操作
     * 检测通过---允许访问
     * 检测失败---跳转到用户登录JSP页面，用户登录操作完成后Servlet程序保存登录信息

### Filter生命周期

* Filter过滤器的生命周期包含方法：
  * 构造函数
  * init初始化方法
  * doFilter过滤方法
  * destroy销毁过滤器方法
* 生命周期特点：
  * 构造函数、init初始化在Web工程创建就会运行（Filter过滤器被创建）
  * doFilter过滤方法，在客户端访问过滤器拦截路径时触发
  * destroy过滤器销毁方法，伴随Web工程关闭而触发销毁（Filter过滤器被销毁）



### FilterConfig接口

* FilterConfig是Filter过滤器的配置文件对象
* Tomcat每次创建Filter时，FilterConfig会伴随创建；每次创建Filter就会有其对应的FilterConfig配置文件对象
  * 类似ServletConfig的创建
* FilterConfig作用：获取Filter过滤器的配置信息
  * filterConfig.getFilterName()；获取过滤器别名
  * fiterConfig.getInitParam( "初始化参数key" )；获取web.xml中过滤器对应配置信息
  * filterConfig.getServletContext()；获取ServletContext上下文对象



### FilterChain过滤器链

* 多个过滤器可联系工作
* 多个Filter过滤器执行特点：
  * 默认情况下在一个线程下执行
  * 共享一个request域对象
* filterChain.doFilter() 方法作用
  * 按照web.xml配置文件中定义的前后顺序，进行Filter过滤器的先后访问
  * 调用该方法的过滤器后面还有Filter过滤器，则会流转向下一过滤器
  * 调用该方法的过滤器后面没有过滤器，拦截请求效果放开---执行目标资源

### Filter过滤器的拦截路径

* 精准匹配
  * < url-pattern >/login.jsp< /url-pattern >
* 目录匹配
  * < url-pattern >/login/*< /url-pattern >
* 后缀匹配
  * < url-pattern >*.jsp< /url-pattern >
  * 注意：前面不能跟 / 斜杠
* Filter过滤器，只会考虑浏览器请求地址是否匹配拦截路径，不会考虑请求资源是否存在



### ThreadLocal使用

* ThreadLocal作用：可以解决多线程的安全问题，可以给当前线程关联一个数据（变量、数组、对象、集合。）
* 特点：
  * 可为当前线程关联一个数据。（可以像Map一样存取数据，key是当前线程）
    * threadLocal.set("一个数据");
    * threadLocal.get();获取数据
  * 每**一个ThreadLocal对象**，**只能**为当前**线程关联一个数据**；若要为当前线程**关联多个数据**，需**创建多个ThreadLocal对象**
    * threadLocal.set("一个数据");
    * threadLocal.set("xx数据");
    * threadLocal.get();获取数据---xx数据---会被覆盖替换
  * 每个ThreadLocal对象定义时，一般设为**static静态**
  * ThreadLocal中保存数据，线程销毁时，会由**JVM虚拟机垃圾回收自动释放**









## JSON

* JavaScript Object Notation 是一种轻量级的数据交换格式
  * 轻量级是和XML进行比较
  * 数据交换格式：客户端和服务器之间业务数据的传递格式

### JSON在JavaScript中使用

* **JSON定义**
  * 由花括号进行整体数据的包裹，里面的数据由键值对组成，键使用引号，键和值之间使用冒号，多个键值对之间使用逗号分隔
* **JSON访问**
  * JSON本身就是对象
  * JSON中的key可以理解为对象中的属性，通过JSON对象 . key即可获取到对应的值
* **JSON两个常用方法**
  * JSON的两种存在形式
    * JSON对象
      * 对象的形式存在
      * 操作JSON数据，使用JSON对象
    * JSON字符串
      * 字符串的形式存在
      * 要在客户端和服务器之间进行数据交换，使用JSON字符串
  * JSON.stringify( JSON对象 )
  * JSON.parse( JSON字符串 )

### JSON在Java中使用

JavaBean和JSON互转

List和JSON的互转

Map和JSON的互转



## AJAX

* Asynchronous JavaScript And XML 异步JavaScript 和 XML	
* 指一种创建交互式网页应用的网页开发技术
* **AJAX是一种浏览器通过JS异步发起请求，局部更新页面的技术**
* **AJAX请求的局部更新**
  * **浏览器地址栏不会发生变化**
  * **局部更新不会舍弃原来页面的内容**





### 原生的AJAX请求

使用JavaScript语言发起AJAX请求，访问服务器Servlet程序

1. 创建XMLHttpRequest对象
   * XMLHttpRequest对象用于和服务器交换数据
   * 向服务器发起请求，使用XMLHttpRequest对象的open和send方法
   * XMLHttpRequest对象三个重要的属性
     * onreadystatechange
     * readyState：0-4
     * status：
       * 200
       * 404
2. 调用open方法设置请求参数
   * open(method,url,async)    规定请求的类型、URL以及是否异步处理请求
     * method-请求类型、url-文件在服务器的位置、async-true（异步）或false（同步）
   * 使用GET还是POST：
     * GET请求更简单更快，并且大部分情况下可使用
     * POST请求使用场景：
       * 加密信息
       * 无法使用缓存文件（更新服务器上的文件或数据库）
       * 向服务器发送大量数据（POST没有数据量限制）
3. 在send方法前绑定onReadyStateChange事件，处理请求完成后的操作
   * readyState属性存有XMLHttpRequest对象的状态信息
   * 每当readyState改变就会触发onReadyStateChange事件
   * readyState属性存储XMLHttpRequest状态值
     * 0 请求未初始化
     * 1  服务器连接已建立
     * 2  请求已接收
     * 3  请求处理中
     * 4  请求已完成，且响应已就绪
   * 在onreadystatechange事件中，当readyState值为4，且status值200表示响应已就绪
4. 调用send方法发送请求
   * send()   将请求发送到服务器
     * send(string) ---string：仅用于POST请求



服务器响应

需要获取来自服务器的响应，使用XMLHttpRequest对象的responseText或responseXML属性

responseText 获取字符串形式的响应数据

responseXML 获取XML形式的响应数据





### JQuery中的AJAX请求

* $.ajax函数
  * url   请求地址
  * type   请求方式
  * data   请求参数
  * success   请求成功回调函数
  * dataType   响应的数据类型
* $.get函数
  * **url**:待载入页面的URL地址
  * **data**:待发送 Key/value 参数。
  * **callback**:载入成功时回调函数。
  * **type**:返回内容格式，xml, html, script, json, text, _default。
* $.post
* $.getJSON











🌕🌖🌗🌘



## i18n









JQuery---需要引用js类库

XML---需要引入DOM4J技术的jar包











