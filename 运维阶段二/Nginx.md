# NGINX



## day01 02

***\*源码安装\*******\*nginx\****

```bash
yum -y install gcc make pcre-devel openssl-devel    #安装编译工具，正则表达式依赖包，SSL加密依赖包
tar -xf nginx-1.22.1.tar.gz
cd nginx-1.22.1/
./configure		--prefix=/usr/local/nginx	--user=nginx	--group=nginx --with-http_ssl_module  #指定安装路径，指定用户，指定组，开启SSL加密功能
make       #编译
make install   #安装


***\*报错解决：\****
./configure: error: C complier cc is not found
原因：未安装 gcc
```

 

 

 

 

***\*ss  -antlp  |  grep 80\****  ***\*#\*******\*查看\*******\*80端口的信息\****

```bash
##ss命令可以查看系统中启动的端口信息，该命令常用选项如下：
-a显示所有端口的信息

-n以数字格式显示端口号

-t显示TCP连接的端口

-u显示UDP连接的端口

-l显示服务正在监听的端口信息，如httpd启动后，会一直监听80端口
-p显示监听端口的服务名称是什么（也就是程序名称）
```

 

***\*SSL虚拟主机\****

***\*加密算法\****

```bash
[root@proxy nginx]# md5sum /root/test

60b725f10c9c85c70d97880dfe8191b3  /root/test
```

***\*md5sum\*******\*可以实现什么功能？\****

**<u>检测数据是否完整与是否被篡改</u>**

 

***\*Nginx配置\**** ***\*必须使用\*******\*--with-http_ssl_module\*******\*参数\****

```conf
server {
​     listen    443 ssl;   #监听端口443，ssl使用安全加密技术
​    server_name       localhost;
​    ssl_certificate    cert.pem;                #这里是证书文件
​    ssl_certificate_key  cert.key;            #这里是私钥文件
​    ssl_session_cache   shared:SSL:1m;
​    ssl_session_timeout  5m;
​    ssl_ciphers  HIGH:!aNULL:!MD5;
​    ssl_prefer_server_ciphers  on;
​    location / {
​    root  https;             #加密网站根目录，更改，也可以自行定义\****
​     index  index.html index.htm;
   }
  }
```

 

 

 

 

 

***\*安装MariaDB，php和php-fpm\****

yum -y install mariadb  mariadb-server mariadb-devel 

php php-mysqlnd php-fpm

mariadb（数据库客户端软件）、mariadb-server（数据库服务器软件）、mariadb-devel（依赖包）、php（识别php语言）、php-fpm（进程管理器服务）、php-mysqlnd（PHP的数据库扩展包）

 

***\*Nginx配置文件解释\****

\# 虚拟主机

server {

​    listen    8080;

​    server_name  localhost;     #浏览器访问域名

rewrite  /a.html  /b.html;    #新添加地址重写，a.html重定向到b.html  

​    \# 路由

  location / {   #定义匹配规则，处理以 / 开头的请求路径，优先级最低

​      root  www; # 访问根目录，即网页文件路径

​      index  index.html index.htm;  #入口文件，即默认访问的网页文件

​    }

}

 

***\*动静分离\****

***\*#nginx\****

location ~ \.php$ {   #~是使用正则表达式匹配以.php结尾，\ 转义

​    root      html;

​    astcgi_pass  127.0.0.1:9000;  #将请求转发给本机php-fpm的9000端口

​    fastcgi_index  index.php;    #网站默认页

​    include     fastcgi.conf;   #加载fastcgi配置文件

} 

***\*#php-fpm\****

[root@proxy nginx-1.22.1]# vim /etc/php-fpm.d/www.conf

 38 listen = 127.0.0.1:9000   #更改php-fpm端口号（使用网络通信）

 \#38 listen = /run/php-fpm/www.sock  #socket方式（使用进程通信）

***\*使用socket方式连接\****

***\*php-fpm配置\****

 38 listen = /run/php-fpm/www.sock       #socket方式（使用进程通信）

 55 listen.acl_users = apache,nginx,nobody     #添加

***\*Nginx配置\****

65     location ~ \.php$ {   #匹配以.php结尾

66       root      html;

67       fastcgi_pass  unix:/run/php-fpm/www.sock;  #将请求转发给php-fpm进程

 68       fastcgi_index  index.php;

 69       include     fastcgi.conf;     #加载

***\*地址重写\****

\#redirect  临时重定向，状态码302

\#permanent 永久重定向，状态码301

\#last 不再读其他语句，但还会继续匹配其他location语句

\#break 不再读其他语句，结束请求

***\*例子展示：\****

rewrite ^/a\.html$  /b.html  redirect;

rewrite  /(.*)  http://www.tmooc.cn/$1;

if ($http_user_agent ~* firefox) {  #如果用户使用了火狐浏览器

  	rewrite /(.*)  /firefox/$1;   

\#就进行地址重写，让用户看到火狐专用页面，否则就是其他页面；#$http_user_agent是nginx的内置变量，包含了发起 HTTP 请求的客户端的#用户代理（User-Agent）字符串，比如用的什么浏览器

}

rewrite /a.html /b.html last;    #新修改

rewrite /a.html /b.html break;     #break可以阻止后面的语句

 

***\*正则表达式匹配模式\****

-区分大小写匹配: ~

-不区分大小写匹配: ~*

-区分大小写不匹配: ! ~

-不区分大小写不匹配: !~*

 

| ***\*语句\****            | ***\*描述\****                                  |
| ------------------------- | ----------------------------------------------- |
| ***\*Description\****     | ***\*描述信息\****                              |
| ***\*After\****           | ***\*在哪个服务之后启动\****                    |
| ***\*Before\****          | ***\*在哪个服务之前启动\****                    |
| ***\*type\****            | ***\*服务类型，默认为simple\****                |
| ***\*EnvironmentFile\**** | ***\*定义变量文件\****                          |
| ***\*ExecStart\****       | ***\*执行systemctl start需要启动的进程名称\**** |
| ***\*ExecStop\****        | ***\*执行systemctl stop需要停止的进程名称\****  |
| ***\*ExecReload\****      | ***\*执行systemctl reload需要执行的命令\****    |

***\*Unit文件语法格式参考表\****

***\*例子：\****

[Unit]

Description=The Nginx HTTP Server    #描述信息

After=network.target remote-fs.target nss-lookup.target   #在网络程序，网络文件系统，域名解析等服务启动之后，再启动nginx  

[Service]

Type=forking   #forking多进程类型服务

ExecStart=/usr/local/nginx/sbin/nginx

ExecReload=/usr/local/nginx/sbin/nginx -s reload

ExecStop=/bin/kill -s QUIT $MAINPID     #kill给程序发送QUIT退出信号，关闭nginx

[Install]

WantedBy=multi-user.target

 

***\*day 03\****

***\*Nginx反向代理（七层代理）\****

![img](file:////tmp/wps-student/ksohtml/wpsATapPg.png)![img](file:////tmp/wps-student/ksohtml/wpsdz1c1Y.jpg) 

![img](file:////tmp/wps-student/ksohtml/wpsrKQaXX.jpg) 

![img](file:////tmp/wps-student/ksohtml/wpsGksJCY.jpg) 

 

***\*相关配置\****

***\*#使用\*******\*upstream\*******\*定义后端服务器集群，集群名称任意(如webserver)\****

***\*#使用server定义集群中的具体服务器和端口\****

***\*upstream webserver {\****

   ***\*server 192.168.99.100\*******\*:80\*******\*;\****

   ***\*server 192.168.99.200\*******\*:80\*******\*;\****

 ***\*}\****

 ***\*location / {\****

   ***\*root  html;\****

   ***\*index  index.html index.htm;\****

   ***\*proxy_pass http://webserver;\****  ***\*#通过proxy_pass将用户的请求转发给webserver集群\****

 ***\*}\****

 

 

***\*ip_hash\*******\*配置\****  

***\*#相同客户端访问相同Web服务器\****

***\*upstream webserver {\****

​    ***\*ip_hash;\****

​    ***\*server 192.168.99.100:80;\****

​    ***\*server 192.168.99.200:80;\****

  ***\*}\****

 

 

 

***\*Nginx的TCP/UDP调度器（四层代理）\****

***\*#需要模块  --with-stream\****

***\*相关配置\****

***\*stream {\****

​    ***\*upstream backend {       #创建集群，名称为backend\****

​      ***\*server 192.168.99.100\*******\*:22\*******\*;  #后端SSH服务器IP和端口\****

​      ***\*server 192.168.99.200\*******\*:22\*******\*;\****

​     ***\*}\****

​     ***\*server {        #调用集群\****

​      ***\*listen 12345;    #Nginx代理监听的端口，可以自己定义\****

​      ***\*proxy_pass backend;\**** ***\*#调用backend集群\****

​     ***\*}\****

   ***\*}\****

 

 

 

 

***\*HTTP常见状态码列表：\****

200 正常

301 & 302 重定向

400 请求语法错误

401 访问被拒绝

403 禁止访问

404 资源找不到

414 请求URI头部太长

500 服务器内部错误

502 代理服务器无法正常获取下一个服务器正常的应答

 

 

 

 

***\*查看nginx服务状态信息\****

[root@proxy nginx]# killall  nginx

[root@proxy nginx]# cd /root/lnmp_soft/nginx-1.22.1/

[root@proxy nginx-1.22.1]# ./configure --with-stream --with-http_stub_status_module     #--with-stream开启4层代理模块，--with-http_stub_status_module开启status状态页面

[root@proxy nginx-1.22.1]# make   #编译，不用执行make install安装，之间已经安装过

[root@proxy nginx-1.22.1]# cp  objs/nginx /usr/local/nginx/sbin/ #覆盖原文件

cp: overwrite '/usr/local/nginx/sbin/nginx'? y  

***\*修改Nginx配置文件，定义状态页面\****

 ***\*location /status {  #定义状态页面\****

​    ***\*stub_status on;\**** ***\*#定义后可以看到页面的状态信息，第一个为当前访问用户数\****

 ***\*}\****

***\*查看状态页面信息\****

Active connections：当前活动的连接数量，有多少人访问网站

Accepts：已经接受客户端的连接总数量，有多少人曾经来过

Handled：已经处理客户端的连接总数量

Requests：客户端发送的请求数量

Reading：当前服务器正在读取客户端请求头的数量，请求头：客户现在正在发的请求，要看什么页面，要求服务器传过去

Writing：当前服务器正在写响应信息的数量，指服务器正在给客户回应信息

Waiting：当前多少客户端在等待服务器的响应，

 

***\*优化Nginx并发量\****

ab -n 100 -c 100 http://192.168.99.5/   #-n任务量，-c是连接数

 

***\*永久\*******\*/临时\*******\*设置最大文件数量\****

***\*[root@web1 ~]# vim /etc/security/limits.conf\****

***\*.. ..\****

***\**        soft   nofile       100000\****

***\**        hard   nofile       100000\****

***\*[root@web1 ~]#\**** ***\*ulimit -n      #查看最大文件数量\****

***\*1024\****           

***\*[root@web1 ~]# ulimit -n 100000   #临时设置最大文件数量\****

***\*修改Nginx配置文件，增加并发量\****

 ***\*#user  nobody;\****

 ***\*worker_processes  2;   #与CPU核心数量一致\****

 ***\*events {\****

   ***\*worker_connections  50000;\****

 ***\*}\****

 

***\*day 04\****

***\*uWSGI\****

***\*一种\*******\*web服务器，用于连接Nginx与Python动态网站\****

***\*配置uWSGI\****

vim myproject.ini  #要以ini结尾，文件名随意

[uwsgi]

socket=127.0.0.1:8000          #与web服务(nginx)通信的接口

chdir=/root/python/python-project-demo  #网站的工作目录

wsgi-file=learning_log/wsgi.py      #定义网站运行时，uwsgi调用的脚本文件

daemonize=/var/log/uwsgi.log       #指定日志文件位置

***\*运行uWSGI\****

uwsgi --ini myproject.ini

***\*nginx配置文件，添加uWSGI转发\****

***\*vim /usr/local/nginx/conf/nginx.conf\****

***\*...\****

​    ***\*location / {\****

​      ***\*uwsgi_pass 127.0.0.1:8000;   #动态页面交给uWSGI\****

​      ***\*include uwsgi_params;      #加载调用uWSGI配置文件\****

​      ***\*root  html;\****

​      ***\*index  index.html index.htm;\****

​    ***\*}\****

​    ***\*location ~\* \.(jpg|jpeg|gif|png|css|js|ico|xml)$ {\****

​      ***\*expires 30d;\****

​     ***\*}\****

***\*...\****

***\*Nginx实现用IP测试灰度发布\****

***\*proxy配置\****

***\*[root@proxy nginx]# vim conf/nginx.conf\****

***\*http {\****   

***\*...\****

  ***\*upstream default {        #正常业务集群\****

​    ***\*server 192.168.99.100:80;\****

​    ***\*server 192.168.99.200:80;\****

  ***\*}\****

  ***\*upstream s8001 {\****

​    ***\*server 192.168.99.100:8001;  #新版本业务集群1\****

  ***\*}\****

  ***\*server {\****

​    ***\*listen    80;\****

​    ***\*server_name  localhost;\****

​    ***\*set $group "default";    #自己定义变量$group，值为default，访问default集群\****

​    ***\*if ($remote_addr ~ "192.168.99.1"){  #如果客户机ip是包含192.168.99.1就访问新版本业务集群1，$remote_addr为nginx的内置变量，代表访问者的ip\****

​       ***\*set $group s8001;  #变量重新赋值\****

​    ***\*}\****

​    ***\*#charset koi8-r;\****

 

​    ***\*#access_log  logs/host.access.log  main;\****

 

​    ***\*location / {\****

​      ***\*proxy_pass http://$group;    #调用集群\****

​      ***\*root  html;\****

​      ***\*index  index.html index.htm;\****

​    ***\*}\****

***\*...\****

 

 

 

***\*定义limit_rate限速\****

***\*http {\****

***\*...\****   

***\*limit_rate 100k;     #全局限速，速度限制100k，最大100k的速度传输数据\****

***\*server {\****

  ***\*listen 80;\****

***\*...\****

 

 

***\*Nginx实现跨域\****

***\*当一个请求url的协议、域名、端口三者之间任意一个与当前页面url不同即为跨域\****

 

***\*[root@proxy nginx]# vim conf/nginx.conf\****

***\*server {\****

***\*listen 8080;\****

***\*add_header 'Access-Control-Allow-Origin' 'http://192.168.99.5';\****

***\*root html;\****

***\*index index.html;\****

***\*}\****

 

 

***\*day 05\**** ***\*06\****

 

***\*Jmeter\****

 

 

 

 

 

***\*Tomcat\****

 

***\*bin\****	***\*存放启动、停止和管理 Tomcat 的脚本文件。\****

***\*webapps\****	***\*存放 Web 应用程序的部署文件（WAR 或解压目录）。\****

***\*conf\****	***\*存放 Tomcat 的配置文件（如 server.xml、web.xml）。\****

***\*logs\****	***\*存放 Tomcat 的日志文件（如访问日志、错误日志）。\****

***\*temp\****	***\*存放运行时生成的临时文件。\****

***\*work\****	***\*存放 JSP 编译后的 Servlet 类文件。\****

***\*lib\****	***\*存放 Tomcat 和所有 Web 应用共享的 JAR 文件。\****