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
> 配置Nginx反向代理服务器：
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







# Nginx/1.24.0 2019











# 末尾