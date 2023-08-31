# Nginx

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

















# 末尾