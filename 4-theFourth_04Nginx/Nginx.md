# Nginx/1.24.0   2022

> Nginx在互联网项目中担当者高性能的Web服务器、负载均衡器；
>
> 在大型的系统架构当中还担任了高性能的流量网关、应用网关、日志服务器、文件存储服务器等
>
> Nginx：服务器端中间件
>
> * 支持模块化扩展
> * 复杂功能的二次开发
>
> 前期准备：
>
> * Linux：
>   * 虚拟机：VMWare17
>   * 操作系统：Linux发行版本CentOS7.9

## Mini版CentOS7.9安装

> 配置完成后修改文件开启网关：
>
> * vim /etc/sysconfig/network-scripts/ifcfg-ens33
>
>   * ONBOOT="yes"：表示系统开机时自动启动网卡
>
>   * ```sh
>     #IP的配置方法【none|static|bootp|dhcp】(引导式不使用协议 | 静态分配IP | BOOTP协议 | DHCP协议)
>     BOOTPROTO="static"
>     #IP地址
>     IPADDR="192.168.222.135"
>     #DNS1:域名解析器
>     DNS1="192.168.222.2"
>     #GATEWAY：网关
>     GATEWAY="192.168.222.2"
>     ```
>
>   * 一些公网的DNS服务器：
>
>     * 阿里：
>       * 223.5.5.5
>       * 223.6.6.6
>     * 腾讯：
>       * 119.29.29.29
>       * 182.254.118.118
>     * 百度：
>       * 180.76.76.76
>     * 114DNS：
>       * 114.114.114.114
>       * 114.114.115.115
>     * 谷歌：
>       * 8.8.8.8
>       * 8.8.4.4



## Nginx安装

> 版本区别（常用版本分为四大阵营）：
>
> * Nginx开源版
>   * http://nginx.org/
> * Nginx plus 商业版
>   * https://www.nginx.com
> * Openresty（重点）
>   * http://openresty.org/cn/
>   * 主要是将Nginx和Lua脚本进行了整合
>
> * Tengine
>   * http://tengine.taobao.org/
>
> Nginx开源版下载安装：
>
> * 编译安装：
>   * cd nginx-1.24.0/
>   * 执行：./configure --prefix=/usr/local/nginx
>     * --prefix指定安装在操作系统哪个目录下
>     * 会提示./configure: error: C compiler cc is not found错误，表示没有C的编译环境
>       * 执行：yum install -y gcc
>     * 安装perl库：
>       * 执行：yum install -y pcre pcre-devel
>     * 安装zlib库：
>       * 执行：yum install -y zlib zlib-devel
>   * 执行：
>     * make
>     * make install
>
> 启动Nginx：
>
> * 由于没有设定环境变量等，需要进入到安装的nginx的sbin目录下执行指令：
>
>   * ```sh
>     cd /usr/local/nginx/sbin
>     
>     ./nginx   启动
>     ./nginx -s stop   快速停止
>     ./nginx -s quit   优雅关闭，在退出前完成已经接受的连接请求
>     ./nginx -s reload 重新加载配置
>     ```
>
> * 本机直接浏览器访问虚拟机IP验证Nginx启动是否成功：
>
>   * 防火墙设置：
>     * 关闭防火墙服务：systemctl stop firewalld.service
>     * 禁止防火墙开机启动：systemctl disable firewalld.service
>     * 放行端口：firewall-cmd --zone=public --add-port=80/tcp --permanent
>     * 重启防火墙：firewall-cmd --reload
>
> 安装成系统服务：
>
> * 创建服务脚本：
>
>   * vi /usr/lib/systemd/system/nginx.service
>
>     * 这里面的配置内容和Nginx安装的位置有关（此处安装目录是/usr/local/nginx）
>
>     * ```sh
>       [Unit]
>       Description=nginx - web server
>       After=network.target remote-fs.target nss-lookup.target
>       
>       [Service]
>       Type=forking
>       PIDFile=/usr/local/nginx/logs/nginx.pid
>       ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
>       ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
>       ExecReload=/usr/local/nginx/sbin/nginx -s reload
>       ExecStop=/usr/local/nginx/sbin/nginx -s stop
>       ExecQuit=/usr/local/nginx/sbin/nginx -s quit
>       PrivateTmp=true
>       
>       [Install]
>       WantedBy=multi-user.target
>       ```
>
> * 重新加载系统服务：
>
>   * systemctl daemon-reload
>
> * 启动服务：
>
>   * systemctl start nginx.service
>
> * 开机启动：
>
>   * systemctl enable nginx.service



## Nginx基础使用

### 目录结构

> 这里将Nginx安装在了/usr/local/nginx目录下：
>
> * /usr/local目录就相当于Windows系统C盘下的Proram Files目录
>
> conf目录：
>
> * 放置Nginx主配置文件
>
> html目录：
>
> * 放置默认网页（就是访问站点时未指定访问某页面而弹出的index页面）和静态资源
>
> logs目录：
>
> * 记录日志：
>   * access.log：记录访问日志（这个是一直增加的，注意磁盘占用！）；
>   * error.log：记录错误日志
>   * nginx.pid：记录Nginx主进程的ID号
>
> sbin目录：这里就一个Nginx主程序文件，用于启停服务



### 基本运行原理

> Nginx启动时不只是启动一个进程，多进程：
>
> * master主进程：
>   * 主进程fork出子进程，负责协调工作进程
> * worker工作进程：
>   * 来完成用户请求



### Nginx配置和应用场景

> nginx.conf就是Nginx的配置文件（这里安装在/usr/local/nginx/conf/nginx.conf）
>
> 最小配置：
>
> * worker_processes 1：默认为1，表示开启一个业务进程（基本的配置就是1个CPU内核对应1个工作进程）
> * location：
>   * URL指的就是完整的访问网址
>   * URI指的就是域名之后的地址，就是资源
>
> 虚拟主机：
>
> * 原本一台服务器只能对应一个站点，通过虚拟主机技术可以虚拟化多个站点同时对外提供服务。
>
> * 域名解析：
>
>   * 可以在本机hosts文件配置Linux虚拟机IP的域名解析，由于Linux开机自启动Nginx服务，访问该域名会自动跳转到Nginx默认网页。
>
> * 虚拟主机的配置在nginx.conf配置文件中进行（/usr/local/nginx/conf/nginx.conf）
>
>   * http模块下 >>> server模块（就是虚拟主机 vhost---Virtual Host）
>     * listen监听的端口
>     * server_name：域名、主机名
>     * location /模块下的root表示访问location后面 / 这个站点时 会进入到root指定目录下 获取资源
>   * 根据访问IP不同端口返回不同页面
>     * 不需要配置多个域名，只需要区分不同端口就行
>       * 192.168.222.135:80 当然也可以通过域名访问 nginx.com:80 也可以通过配置的外网域名进行访问---要购买
>       * 192.168.222.135:88 当然也可以通过域名访问 nginx.com:88
>   * 配置虚拟主机的两种方式
>     * 方式一：区分端口号
>     * 方式二：区分主机名/域名
>     * 端口号 + 主机名 必须保持唯一性，否则修改完Nginx核心配置文件后，重载Nginx后查看状态是错误的
>
> * server_name匹配规则（必须确保设置的域名已经解析到服务器的IP地址）：
>
>   * 需要注意的是server_name匹配分先后顺序，写在前面的匹配上就不会继续向下进行匹配（都未匹配上就匹配第一个）。
>
>   * 完整匹配：
>
>     * 一个server（虚拟主机）模块下可以配置多个server_name，使用空格分隔即可
>       * server_name localhost nginx1.com
>
>   * 通配符匹配：
>
>     * server_name *.mmban.com . *
>     * *星号表示任意字符
>
>   * 正则匹配：
>
>     * ```sh
>       server_name ~^[0-9]+\.mmban\.com$;
>       ```
>
>   * 总结：
>
>     * server_name是一个非常重要的配置，尤其是在配置虚拟主机时。
>
> * 域名解析相关企业项目实战技术架构：
>
>   * 多用户二级域名
>     * ![image-20230831232451053](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230831232451053.png)
>   * 短网址
>     * ![image-20230831233111992](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230831233111992.png)
>   * httpdns
>     * 适用于C/S架构或APP



### 反向代理

> 正向代理和反向代理如何区分：
>
> * 看代理服务器是谁提供的，用户主动搭建代理服务器来访问外网就是正向代理；而服务器搭建Nginx反向代理服务器供用户访问就是反向代理
> * ![image-20230831234035841](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230831234035841.png)
>
> 网关：
>
> * 网关就是访问网络的入口
> * 特点就是作为中间层进行数据的中转；
>   * 该特点就造成了先天的瓶颈：
>     * 流量大，网关服务的带宽不够足时，就有可能产生阻塞
> * Nginx---隧道式代理
> * Lvs---DR模式
>
> 负载均衡：
>
> * 算法---轮询
>
> ==配置Nginx反向代理服务器：==
>
> * 关键字：proxy_pass
>   * 使用位置nginx.conf核心配置文件下的server模块下的location模块内使用该关键字：
>     * location下proxy_pass和root（指定目录）配置项二选一
>       * 所以使用proxy_pass就可以将root和index配置项都注释掉。
>   * 后面参数形式两种：
>     * 代理的地址：主机/网址
>       * proxy_pass http://www.atguigu.com;
>         * 注意若是https 不支持https的 需要证书；需要在编译追加SSL模块
>         * 若地址http://atguigu.com，也可以正常反向代理，但访问Nginx进行反向代理时会接收到302重定向的响应，浏览器地址就会发生变化（正常情况是访问Nginx，代理成功也是Nginx的请求地址）
>     * 一组服务器
>   * html文件要显示中文：
>     * 要在head meta 中定义charset = utf-8



### 基于反向代理的负载均衡

> ==配置负载均衡：==
>
> * server模块下location模块下的proxy_pass配置项对应的参数可以定义一个想要在多台服务器负载均衡
>
>   * proxy_pass http://httplixc
>
> * upstream配置项（和server同一级别）
>
>   * ```sh
>     upstream httplixc{
>     	server 192.168.222.136:80;
>     	server 192.168.222.137:80;
>     }
>     ```
>
> * 整体：
>
>   * ```sh
>         upstream httplixc {
>             server 192.168.222.136:80;
>             server 192.168.222.137:80;
>         }
>     
>         server {
>             listen       80;
>             server_name  localhost;
>     
>             location / {
>                 proxy_pass http://httplixc;
>                 # root   html;
>                 # index  index.html index.htm;
>             }
>     
>             error_page   500 502 503 504  /50x.html;
>             location = /50x.html {
>                 root   html;
>             }
>         }
>     ```
>
> * 这样在访问虚拟主机的server_name时，就会根据proxy_pass和upstream配置项进行访问的负载均衡（upstream两个服务器就会你一次我一次的被访问到）
>
> ==负载均衡策略：==
>
> * 轮询（RoundRobin---RR）：
>
>   * 默认情况下负载均衡的使用的策略就是轮询方式，逐一转发，这种方式适用于无状态请求。
>     * 无法保持回话（用户访问到一台服务器，登录；再次访问，被负载均衡轮询策略分配到了另一台服务器，登录信息cookie没有，就是无法保持会话）
>
> * weight权重：
>
>   * 指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。
>
>   * 可以根据每台服务器的配置/网络出口高低来分配不同大小的权重（千兆带宽和十兆带宽分配的权重肯定会有区别）
>
>   * ```sh
>     # 配置在upstream负载均衡模块的的server配置项中
>         upstream httplixc {
>            server 192.168.222.136:80 weight=7;
>            server 192.168.222.137:80 weight=2;
>            server 192.168.222.138:80 weight=1;
>         }
>         #上面的权重配置就是当浏览器访问server_name和listen组合的网址10次，访问7次是136、2次137、1次138。
>     
>         upstream httplixc {
>            server 192.168.222.136:80 weight=7 down;
>            server 192.168.222.137:80 weight=2 backup;
>            server 192.168.222.138:80 weight=1;
>         }
>         #weight：默认为1。weight越大，负载的权重就越大
>         #down：表示当前的server主机暂时不参与负载
>         #backup：其他所有的非backup机器down或者忙时，请求backup机器。（备用服务器，正常情况不会使用，只有当其他机器都没得用 才会使用到该机器）
>     
>     #weight还是有用的，down和backup不常用。
>     ```
>
> * 不太常用的策略：
>
>   * 以下的负载均衡策略都无法做到：
>   * 服务器的动态上下线
>   * ip_hash
>
>   * 保持会话：判断来源的客户端IP地址，相同的IP访问到相同的服务器
>     * 在当前互联网环境下已经无法适应，手机的移动端，移动过程中切换了移动的基站，IP地址就发生了变化。无法保证会话
>   * least_conn：
>     * 最少连接数访问
>     * 分配了weight肯定就会出现负载分配不一致，所以least_conn不合理
>   * url_hash：
>     * 根据用户访问的url定向转发请求（还需要下载第三方插件）定向流量转发（ip_hash是定向ip转发）
>       * 适用于访问固定资源，而不是维持会话
>     * 用户访问注册，会将网址解析为哈希值，分配到特定服务器；而用户在注册完成登录时，解析的哈希值有可能就会分配到另一台服务器，无法保持会话。
>     * fair：
>       * 根据后端服务器响应时间转发请求（还需要下载第三方插件）
>       * 会有流量倾斜的风险（一台服务器处理时长100ms，一台处理时长5ms，全给5ms的势必会造成流量倾斜）
>
> * 真正生产环境下：
>
>   * 要么使用RR轮询策略（唯一的缺点就是无法保持会话）
>     * 保持会话的策略：
>       * 一个session对应一个cookie，cookie由用户端存储，每次用户携带cookie访问，会找session，这里将所有的session都存储到Redis服务器中，每次轮询分配到不同服务器时需要session时都会去Redis服务器中寻找。
>         * 不适用于大规模的高并发场景。
>         * 高并发场景会用到真正的无状态会话--->下发token
>   * 要么使用LUA脚本自定义转发规则



### 动静分离

> Nginx的动静分离一般适用于中小型的网站（并发量不会特别高，且需要分离出来的静态资源不会特别多）
>
> * 可以起到系统加速的作用。
>
> 原理：
>
> * 将处理静态资源和处理动态请求的功能分别交给不同的服务器或服务进行处理，以提高系统的性能和并发能力。
> * 通过Nginx的动静分离，可有效减轻动态应用服务器的负载，提高系统的性能和并发能力。同时还通过负载均衡实现高可用性，某个服务器出现故障Nginx会自动将请求转发给其他正常服务器，保证系统的可用性。
>
> Nginx动静分离的配置：
>
> * 被反向代理的机器页面：
>
>   * ```html
>     <!DOCTYPE html>   		<!--表示文件为HTML-->
>     <html lang="zh_CN">   		<!--html页面开始-->
>     <head>    			<!--页面头开始-->
>       <meta charset="UTF-8">  		<!--字符集设置-->
>       <title>HTML页面书写规范</title>  	<!--页面标题-->
>     </head>    			<!--页面头结束-->
>     <body>    			<!--页面body体开始-->
>     <h1 align="center">Hello Nginx. This is 138.</h1>
>     <br/>
>     <div style="text-align:center">
>     <img src="/img/heart.jpg" width="500" height="500"/>
>     </div>
>     </body>     			<!--页面body体结束-->
>     </html>     			<!--html页面结束-->
>     ```
>
>   * 这里引用的是在html目录下img目录下的heart.jpg图片；此时将该img目录删除（静态资源）
>
> * 通过访问服务器来反向代理出上面的页面信息：
>
>   * ```sh
>         upstream httplixc {
>            server 192.168.222.136:80 weight=7 down;
>            server 192.168.222.137:80 weight=2 backup;
>            server 192.168.222.138:80 weight=1;
>         }
>     
>         server {
>             listen       80;
>             server_name  localhost;
>     
>             location / {
>                 proxy_pass http://httplixc;
>             }
>     
>             error_page   500 502 503 504  /50x.html;
>             location = /50x.html {
>                 root   html;
>             }
>         }
>     ```
>
>   * 此刻访问135（反向代理了138机器）弹出的界面没有图片---因为在138已经删除掉了静态资源
>
>   * 此时可以在135机器下新建静态资源目录
>
>     * html目录下新加img目录
>
>   * nginx.conf配置文件中新增location模块（和反向代理的location平级）用来指向img目录
>
>     * ```sh
>           location /img {
>               root   html;
>               index  index.html index.htm;
>           }
>           #这里的/img经过root的组合自动解析：/html/img
>       ```
>
>   * 此时再访问135就会反向代理138，然后获取图片静态资源就会在135下寻找index.html页面平级的img目录下的图片，正常显示。
>
> * Nginx动静分离配置解析：
>
>   * location木块的/和/img等等，都要比/优先级高
>
>   * 正则方式匹配静态资源----省去定义每一个location模块
>
>     * ```sh
>           location /img1 {
>               root   html;
>               index  index.html index.htm;
>           }
>       
>           location /img2 {
>               root   html;
>               index  index.html index.htm;
>           }
>           #使用正则方式匹配静态资源：
>           #~使用正则方式；*不区分大小写
>           location ~*/(img1|img2) {
>               root   html;
>               index  index.html index.htm;
>           }
>       ```
>
>   * Nginx动静分离的配置比较简单，但是需要将静态资源传递到前置服务器上
>
>     * 静态、缓存这一类的资源，越往前放越好，减少网络开销



### URLRewrite

> 可以隐藏后端服务器真实的地址。（这里实在不想整理Tomcat，直接使用的html页面代替测试）
>
> 关键字rewrite（和proxy_pass同级）：
>
> * 被代理的服务器
>
>   * html目录下新增dogcome目录，里面有具体的页面
>
>   * conf目录下核心配置文件：
>
>     * ```sh
>       #新增location用来处理访问该资源
>       location /dogcome {
>                   root   html;
>                   index  dog.html index.htm;
>       }
>       
>       ```
>
> * 前置服务器：
>
>   * ```sh
>     # rewrite关键字用来隐藏访问的真实地址：
>     #        rewrite ^将要访问的地址$   隐藏的真实地址  break/redirect
>     location / {
>                 rewrite ^/dog$ /dogcome break;
>                 proxy_pass http://192.168.222.138;
>                 #proxy_pass http://httplixc;
>                 #root   html;
>                 #index  index.html index.htm;
>     }
>     ```
>
>   * 此时访问http://192.168.222.135/dog就可以反向代理到138服务器，并且访问http://192.168.222.138/dogcome目录。
>
> ==负载均衡+URLRewrite：==
>
> * 135服务器反向代理了138服务器（Tomcat），且进行了Nginx的动静分离的配置：
>
>   * 此时直接访问138服务器的服务是无法加载图片等静态资源的；
>   * 就可称之为135服务器是138服务器的网关。
>   * 且真正生产环境下138对应的8080端口肯定不能直接被外网访问到的，想要访问就必须通过135。
>
> * 应用服务器开启防火墙：
>
>   * 开启防火墙：systemctl start firewalld
>
>   * 指定端口和IP访问：
>
>     * ```sh
>       firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.222.135" port protocol="tcp" port="8080" accept"
>       ```
>
>     * 该指定就可指定address的主机IP可以访问该主机下的8080端口（即使该端口并未放开）。
>
>   * 重载防火墙：firewall-cmd --reload
>
>   * 查看已配置规则：firewall-cmd --list-all
>
>   * 此时这个应用服务器（例如Tomcat）就只能够内网的指定的主机访问；用户通过指定的主机访问到应用服务器，这台主机可称之为网关服务器。（反向代理服务器、负载均衡器都太片面了）
>
>     * 集成了反向代理、负载均衡、动静分离、URLRewrite功能与一身的服务器，网关服务器。



### 防盗链配置

> 某台主机内部的资源，若不想让其他主机通过访问该站点（例如反向代理）获取到这些资源，可进行防盗链配置。
>
> * 可配置到任意的location下
>
>   * if和（之间必须有空格
>
>   * ```sh
>     #valid_referers后面指定有效的引用访问域名，注意不一定是IP（可以观察f12中network后第二次的请求头访问refer信息）
>     #动静分离---正则方式匹配静态资源，避免定义多个目录对应的多个location模块
>     location ~*/(img1|img2|img3|img4) {
>     	valid_referers none 192.168.222.135;
>     	if ($invalid_referer) {
>     		return 403;
>     	}
>     }
>     
>     #none， 检测 Referer 头域不存在的情况。
>     #blocked，检测 Referer 头域的值被防火墙或者代理服务器删除或伪装的情况。这种情况该头域的值不以“http://” 或 “https://” 开头。
>     #server_names ，设置一个或多个 URL ，检测 Referer 头域的值是否是这些 URL 中的某一个。
>     ```
>
> * 例如137服务器反向代理了135服务器：
>
>   * 访问137可直接代理到135的资源
>   * 若不想让137访问到135的静态资源，即可在135的动静分离location模块下进行配置。
>     * 在此基础上若想137可单独访问135的静态资源信息而非Refer引用，可配置none参数。
>
> * 返回错误页面：
>
>   * 上面的配置就是返回错误码：return 403；
>
>   * ```sh
>         #动静分离---正则方式匹配静态资源，避免定义多个目录对应的多个location模块
>         location ~*/(img1|img2|img3|img4) {
>         			valid_referers none 192.168.222.135;
>         			if ($invalid_referer) {
>         				return 403;
>         			}
>            root html;
>            index index.html index.htm;
>        }
>     
>         		#通过上面的return对应到这里（注意要在对应的位置定义错误页面，例如这里就需要在/html目录下定义403.html）
>         error_page   403  /403.html;
>         location = /403.html {
>             root   html;
>         }
>     ```
>
>   * 整合rewrite返回报错图片：
>
>     * ```sh
>           #动静分离---正则方式匹配静态资源，避免定义多个目录对应的多个location模块
>           location ~*/(img1|img2|img3|img4) {
>           			valid_referers none 192.168.222.135;
>           			if ($invalid_referer) {
>           			    #通过rewrite返回报错图片：（不再return）
>           				rewrite ^/ /img5/daolian.png break;
>           				#return 403;
>           			}
>              root html;
>              index index.html index.htm;
>          }
>       ```
>
> CURL：
>
> * 安装Linux中curl工具：
>   * yum install -y curl
> * 选项：
>   * -I：携带该选项访问，不会将内容展示，只会返回响应的一些头信息
>     * curl -I http://192.168.222.135
>     * curl -I http://192.168.222.137/img3/dog.png
>   * -e：
>     * 测试带引用（refer）的请求，-e后面携带 需要引用的地址
>     * curl -e "http://192.168.222.137" -I http://192.168.222.137/img3/dog.png





## 高可用配置

### Keepalived安装和配置

> HA：High Availability高可用
>
> keepalived是主机级别的检测，进程级别的检测；不止能够运用到Nginx上，只是检测了机器上keepalived有没有存活，所以可以检测一切（例如两台MySQL、两台Redis、两台RabbitMQ等等；都可以通过脚本检测然后杀死keepalived然后进行ip漂移）
>
> 执行：yum install -y keepalived
>
> 安装完成后，配置文件在：/etc/keepalived/keepalived.conf
>
> 修改主机配置文件：
>
> * 从VI_1之后的模块即自virtual_server ip 443模块起及其后面全部删除掉
> * global_defs模块只保留router_id，可以自定义名称
> * state MASTER表示主机
> * vrrp_instance是内网当中通讯的协议 后面参数名也可自定义
>   * interface 对应网卡名字（一般是ens33）
>   * priority优先级使用默认100
>   * virtual_address表示虚拟IP地址；用来提供给用户访问的，不在访问机器真实IP
> * 启动keepalived：systemctl。。。
> * 之后ip addr查看IP地址：
>   * 发现ens33网卡多了一个IP地址---对应keepalived虚拟出来的IP（vip）
>
> 修改备机配置文件：
>
> * 复制一份主机的配置文件
> * global_defs模块的router_id，自定义名称。
> * state BACKUP表示备机
> * priority优先级设置比主机优先级低些（例如50）
> * 启动
> * 查看ip：
>   * 备机上ens33网卡没有虚拟ip
>
> 本机：
>
> * ping 虚拟ip -t
> * 此刻将主机停止，模仿Nginx宕机
> * 稍等就会发现ping的虚拟ip还是通的
> * 再次查看备机ip：
>   * 备机ens33网卡上有了虚拟ip（通过keepalived：ip漂移）
>   * 





## Https证书配置

### 不安全的http协议

> socket>>>TCP/IP>>>Http：
>
> * 通过客户端最底层socket，也就是基本的网络接口，向外发送数据包；这里就叫做socket
> * TCP/IP就是稍微上层一些的网络协议了（socket基于操作系统更底层的协议）
> * 通常都是在TCP/IP协议上再加一层应用层协议（socket、TPC/IP都是底层协议）---Http：
>   * http协议的不安全就是用的这几个底层协议都没有保证数据传输的安全
>   * 在数据传输的过程当中是不安全的，有很多节点（例如多个路由节点）可能对数据进行拦截，解包获取到数据的明文。
>
> 网络协议是通信两端都要遵守的协议。（例如服务器端要实现http协议，客户端要使用支持http协议的浏览器或app来访问服务器），双方一定要达成共识。
>
> 对称加密（算法安全）：
>
> * 通信双方采用同一种算法，对数据进行加密和解密（例如凯撒加密算法）。
> * 内置到服务器中；导致了对称加密算法一定是固定的，不够灵活，也不安全。
>
> 非对称加密算法（私钥安全）：
>
> * 不使用80，使用443端口访问服务器，下载公钥
> * 客户端访问服务器：公钥加密，私钥解密
> * 服务器响应客户端：私钥加密，公钥解密
> * 公钥加密，公钥解不开；保证私钥安全（不进行传递）
> * 也不能保证安全：网络通信中的拦截节点返回给客户端假的公钥
>
> CA机构（保证互联网安全）：
>
> * 第三方，不再网络传输；
> * 认证公钥：
>   * 服务器创建的公钥传给CA机构进行公钥的认证
>   * 会先认证当前服务器的权限：
>     * CA机构的私钥 + 非对称加密算法 ===证书
> * 客户端访问获取到的就不再是服务器的公钥，而是CA机构下发的证书
>   * 而客户端操作系统中内置了CA机构的公钥
>   * 这样恶意节点即便可以解开CA机构的证书，但不知道CA机构的私钥，就无法对恶意数据进行重新加密
>   * 这样通过操作系统内置的CA机构公钥解开CA机构下发的证书，就可以得到服务器的公钥，这样就可以拿服务器的公钥去加密数据了
>
> 一定要用正版系统、正版浏览器
>
> * win + r：certmgr.msc查看操作系统内置的第三方的CA机构证书
>
> https：21世纪最伟大的互联网发明。
>
> 证书自签名：
>
> * 使用场景非常少，只有在内网环境下、CS连接才有可能会用到自签名。
> * OpenSSL





## 线上实战

> 在线证书申请：
>
> * 中小型企业可能需要做的事情
> * 申请证书，绑定到服务器，服务器安装Nginx，Nginx简单配置，Nginx跑一些互联网开源程序

### 申请域名：

> * aliyun
> * 产品服务  搜索 域名 
> * 注册域名

### 申请服务器：

> * 产品 >>> 云服务器ECS >>> 立即购买
> * 自定义购买：
>   * 地域：选择中国（香港）不需要对域名进行备案
>   * 实例（自己花销凭实力）：
>     * 1CPU  1GiB
>   * 镜像：
>     * CentOS
>     * 版本可以选择自己本地对应的发行版本
>   * 系统盘：
>     * 需要花销，选最低20
>   * 购买时长：
>     * 一周

### 服务器安装Nginx：

> * 这里使用lnmp：集成了Linux、Nginx、MySQL、PHP运行环境
>
>   * https://oneinstack.com/auto/
>
>     * 使用这个来获取自动安装的wget指令：
>
>       * ```sh
>         wget -c http://mirrors.oneinstack.com/oneinstack-full.tar.gz && tar xzf oneinstack-full.tar.gz && ./oneinstack/install.sh --nginx_option 1 --php_option 9 --phpcache_option 1 --db_option 2 --dbinstallmethod 1 --dbrootpwd lz001214. --reboot 
>         ```
>
>
>         ####################Congratulations########################
>         Total OneinStack Install Time: 33 minutes
>             
>         Nginx install dir:              /usr/local/nginx
>             
>         Database install dir:           /usr/local/mysql
>         Database data dir:              /data/mysql
>         Database user:                  root
>         Database password:              lz001214.
>             
>         PHP install dir:                /usr/local/php
>         Opcache Control Panel URL:      http://192.168.222.140/ocp.php
>             
>         Index URL:                      http://192.168.222.140/
>         ```
>
> * 安装完成后，使用申请的服务器的公网IP访问Nginx
>
>   * 访问不到就要去服务器那里配置防火墙
>     * 网络与安全 >>> 安全组 >>> 配置规则 >>>手动添加
>       * 协议类型：自定义TCP
>       * 端口范围：80（HTTP）、443（HTTPS）
>       * 授权对象源：0.0.0.0/0（表示任何访问源都能访问） 
>
> * 在服务器解析之前申请的域名：
>
>   * 解析设置 >>> 添加记录 ：
>     * 记录类型：A
>     * 主机记录：这里添加两个记录，一个不填写，一个*（泛解析）
>     * 记录值：服务器（主机）的IP地址

### 申请证书：

> * 产品 >>> 数字证书管理服务 >>> 选购SSL证书：
>   * SSL证书 >>> 免费证书 >>> 立即购买 >>> DV单域名证书





### Nginx简单配置：

> * 可以先将LNMP的首页更改为Nginx自带的首页index：
>
>   * 在Nginx核心配置文件进行首页引用的修改
>
>     *     #root /data/wwwroot/default;
>           root /usr/local/nginx/html;

### Nginx上跑一些互联网开源程序：

> discuz：
>
> * discuz.net
>
> wordprice











# Nginx/1.24.0 2019

## Nginx基本概念

### Nginx是什么，能做什么

> Nginx作为当下最主流的高性能的HTTP和反向代理的Web服务器；网关服务器
>
> * 反向代理、负载均衡、动静分离、URL重写 功能集于一身的网关服务器。
> * 占有内存少，并发能力强
> * nginx专为性能优化而开发，有报告表明Nginx能支持高达50000个并发连接数



### 反向代理 proxy_pass

> 正向代理和反向代理：
>
> * 看代理服务器是谁提供的，用户主动搭建的代理服务器来访问外网就是正向代理（例如正向代理的路由器）
> * 服务器搭建的反向代理服务器来供用户进行访问就是反向代理（客户端对代理是无感知的，因为客户端无需任何配置即可访问）。
>   * 反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实的服务器IP地址。



### 负载均衡 upstream

> 并发请求相对较小情况下，客户端发送请求到服务器，服务器处理业务后完成响应。
>
> 但随着信息数量不断增长，访问量和数据量的增长；服务器肯定会有瓶颈，可以考虑增加服务器数量：
>
> * 使用Nginx反向代理服务器对多台服务器进行请求访问的负载均衡配置（请求分发到各个服务器）
>   * 将负载分发到不同的服务器，也就是负载均衡。



### 动静分离 location

> 为了加快网站的解析速度（起到系统加速的作用），可以将处理静态资源和处理动态请求的功能分别交给不同的服务器进行处理。



### URLRewrite rewrite

>  





## Nginx安装、常用命令、配置文件

### Linux系统安装Nginx

> Linux版本：CentOS7.9 Minimal
>
> Nginx版本：Nginx/1.24.0
>
> 一键安装gcc、zlib、perl、openssl：
>
> * yum -y install gcc zlib zlib-devel pcre pcre-devel openssl openssl-devel



### Nginx常用命令

> 查看Nginx版本号：
>
> * ./nginx -v
>
> 启动Nginx：
>
> * ./nginx
>
> 停止Nginx：
>
> * ./nginx -s stop
>
> 重载Nginx：
>
> * ./nginx -s reload



### Nginx核心配置文件nginx.conf

> Nginx核心配置文件：
>
> * /usr/local/nginx/conf/nginx.conf
>
> nginx.conf配置文件组成：
>
> 1. 全局块：
>
>    * 从配置文件开始到events块之间的内容，主要设置一些影响Nginx服务器整体运行的配置指令
>    * 例如work_process 1：表示由master_process主进程开启的工作子进程，值越大，可支持的并发处理量越多
>
> 2. events块：
>
>    * 主要影响Nginx服务器与用户的网络连接
>    * 例如work_connections 1024：支持最大连接数
>
> 3. http块：
>
>    * Nginx配置最频繁的部分
>    * http全局块
>
>    * server块：
>      * 与vhost虚拟主机有密切关系
>      * server全局块
>      * location块





## Nginx配置实例

### Nginx反向代理

### Nginx负载均衡

### Nginx动静分离

	# 负载均衡
	upstream httplixc {
		server 192.168.222.136:8080 weight=1;
		server 192.168.222.138:8080 weight=2;
	}
	
	# 虚拟主机
	server {
		listen 80;
		server_name *.lixc.com;
		location / {
			# URLRewrite URL重写;
			rewrite ^/([0-9]+).html$ /index.jsp?pageNum=$1 break;
			# 反向代理
			# proxy_pass http://192.168.222.138:8080;
			proxy_pass http://httplixc;
			root html;
			index index.html index.htm;
		}
		# 动静分离
		location ~*/(img1|img2|img3|img4) {
			root html;
			index index.html index.htm;
		}
	
	    		error_page   500 502 503 504  /50x.html;
	    		location = /50x.html {
	        			root   html;
	    		}
	}


​	

	======
	location块：
	*：不含正则表达式的uri，要求请求字符串与uri严格匹配
	~：用于表示uri包含正则表达式，且区分大小写
	~*:表示uri包含正则表达式，不区分大小写


​	

	location模块中：
	autoindex on; 表示列出当前文件夹下的内容（文件目录）；
	expires设置缓存：会查看服务器这个文件是否被修改，没修改就使用缓存，返回302；修改了获取服务器数据，返回200


### Nginx配置高可用集群 keepalived

> yum -y install keepalived
>
> rpm -q -a keepalived：查看Keepalived工具安装是否成功
>
> 添加检测脚本：
>
> * vrrp_script





## Nginx原理（执行原理）

> master进程负责接收分发任务，worker进程负责处理任务，多个worker（默认一个）通过“争抢”的方式获取任务的处理权。
>
> 一个master多个worker好处：
>
> * 利于nginx热部署
> * 每个worker都是独立的进程，不需要加锁；并且当一个worker进程异常，不会影响其他worker，保障了服务的高可用
>
> 设置多少worker：
>
> * Nginx和Redis类似都采用了IO多路复用机制；每个worker都是一个独立的进程
> * 每个worker都可以将一个CPU 的性能发挥到极致，所以worker和服务器的CPU数量相等是最为适宜的。
>
> 连接数 worker_connection：
>
> * 发送一个请求，占用了worker的几个连接数：
>   * 两个或者四个
> * 支持的最大并发数：
>   * 有几个worker_process进程，每个进程worker又支持的最大连接数worker_connection是多少：
>     * 作为普通的静态访问：worker_process * worker_connection / 2
>     * 作为反向代理服务器：worker_process * worker_connection / 4
>       * 反向代理肯定会去再连接其他的服务器，连接数又会增加两个（来回）





#末尾

