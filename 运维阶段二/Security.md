# SECURITY

## 监控概述

- 对服务的管理，不能仅限于可用性。
- 还需要服务可以安全、稳定、高效地运行。
- 监控的目的：早发现、早治疗。
- 被监控的资源类型：
  - 公开数据：对外开放的，不需要认证即可获取的数据
  - 私有数据：对外不开放，需要认证、权限才能获得的数据

## 监控命令

```bash
# uptime可以查看系统已经运行了多久。最后的3个数字分别是CPU最近1分钟、5分钟、15分钟的平均负载。平均负载的值，不应该大于总核心数。
[root@myhost ~]# uptime 
 09:28:25 up 53 days, 27 min,  0 users,  load average: 0.09, 0.08, 0.03

[root@myhost ~]# free -h      # 主要用于查看内存
[root@myhost ~]# swapon -s    # 查看swap空间
[root@myhost ~]# df -h        # 查看硬盘使用情况
[root@myhost ~]# ping -c2 www.baidu.com   # 发2个包，测试网络
```

## zabbix知识点速成

1. **核心组件**
   - **Zabbix Server**：负责接收监控数据、触发告警、存储数据到数据库13。
   - **Zabbix Agent**：部署在被监控主机上，采集本地数据（如CPU、内存、磁盘等）并发送给Server13。
   - **Zabbix Web**：图形化管理界面，用于配置监控项、查看数据和告警1。
   - **Zabbix Proxy**：分布式监控中用于分担Server压力，负责局部数据收集并转发给Server35。
   - **数据库**：存储配置和监控数据（推荐MySQL、PostgreSQL）1。
2. **监控模式**
   - **主动模式**：Agent主动向Server发送数据，适合大规模监控36。
   - **被动模式**：Server主动向Agent拉取数据3。
3. **关键术语**
   - **Host**（主机）: 被监控的目标设备（如服务器、交换机）。
   - **Item**（监控项）: 具体要监控的内容，如 CPU 使用率、内存使用量、磁盘空间等。
   - **Trigger**（触发器）: 基于监控项的条件判断，当条件满足时触发告警。
   - **Action**（动作）: 触发器触发后执行的操作，如发送邮件、短信通知。
   - **Template**（模板）: 包含一组预定义的监控项、触发器、图形等内容，用于快速应用到多个主机。
   - **Graph**（图形）: 可视化展示监控数据的趋势。
   - **Map**（拓扑图）: 显示主机及其关系的网络拓扑图。
4. **通信方式**
   Zabbix支持多种协议监控设备，包括Agent、SSH/Telnet、SNMP（网络设备）、IPMI（硬件传感器）、JMX（Java应用）等35。

**环境准备**

```bash
#安装依赖
sudo yum install -y epel-release httpd mariadb-server php php-mysqlnd php-gd php-xml
##添加Zabbix仓库并安装Server
sudo rpm -Uvh https://repo.zabbix.com/zabbix/6.0/rhel/7/x86_64/zabbix-release-6.0-4.el7.noarch.rpm
sudo yum install -y zabbix-server-mysql zabbix-web-mysql zabbix-agent
##配置数据库
##修改/etc/zabbix/zabbix_server.conf，设置数据库连接信息。

##启动服务
sudo systemctl start zabbix-server zabbix-agent httpd mariadb
sudo systemctl enable zabbix-server zabbix-agent httpd

##浏览器访问http://<服务器IP>/zabbix，按向导完成配置（默认账号：Admin，密码：zabbix）
```

**添加监控主机**

```bash
##在被监控主机安装Agent
sudo yum install zabbix-agent
##配置Agent，
##修改/etc/zabbix/zabbix_agentd.conf，设置Server（Server IP）和Hostname（需与Web端一致）12。

##Web端添加主机
##进入“Configuration > Hosts > Create Host”，填写主机名、IP地址和分组1。
```



## zabbix

- 实施监控的几个方面：
  - 数据采集：使用agent（可安装软件的系统上）、SNMP（简单网络管理协议，用于网络设备的数据采集）
  - 数据存储：使用mysql数据库
  - 数据展示：通过web页面
- zabbix通过在远程主机上安装agent进行数据采集，存储到mysql数据库，通过web页面进行展示。

### 安装zabbix 6.0

- 安装说明：https://www.zabbix.com/download?zabbix=6.0&os_distribution=rocky_linux&os_version=8&components=server_frontend_agent&db=mysql&ws=nginx
- 中文手册：https://www.zabbix.com/documentation/6.0/zh

- 安装zabbix

```bash
# 1. 拷贝zabbix软件包到pubserver
[root@zabbixserver ~]# scp /linux-soft/s2/zzg/zabbix_soft/*.rpm 192.168.88.240:/var/ftp/rpms
# 2. 更新yum仓库配置
[root@pubserver ~]# createrepo -v /var/ftp/rpms/
# 3. 在pubserver上创建ansible工作环境
[root@pubserver ~]# mkdir -p zabbix/files
[root@pubserver ~]# cd zabbix/
[root@pubserver zabbix]# vim ansible.cfg 
[defaults]
inventory = inventory
host_key_checking = false

[root@pubserver zabbix]# vim inventory 
[zabbix]
zabbixserver ansible_host=192.168.88.5

[webservers]
web1 ansible_host=192.168.88.100
web2 ansible_host=192.168.88.200

[all:vars]
ansible_ssh_user=root
ansible_ssh_pass=a

# 4. 为各台主机配置yum
[root@pubserver zabbix]# vim files/local88.repo 
[BaseOS]
name = BaseOS
baseurl = ftp://192.168.88.240/dvd/BaseOS
enabled = 1
gpgcheck = 0

[AppStream]
name = AppStream
baseurl = ftp://192.168.88.240/dvd/AppStream
enabled = 1
gpgcheck = 0

[rpms]
name = rpms
baseurl = ftp://192.168.88.240/rpms
enabled = 1
gpgcheck = 0

[root@pubserver zabbix]# vim 01-upload-repo.yml 
---
- name: config repos.d
  hosts: all
  tasks:
    - name: delete repos.d
      file:
        path: /etc/yum.repos.d
        state: absent

    - name: create repos.d
      file:
        path: /etc/yum.repos.d
        state: directory
        mode: '0755'

    - name: upload local88
      copy:
        src: files/local88.repo
        dest: /etc/yum.repos.d/
[root@pubserver zabbix]# ansible-playbook 01-upload-repo.yml 

# 5. 在zabbixserver上安装相应软件包
[root@pubserver zabbix]# vim 02-inst-zabbix.yml
---
- name: install zabbix
  hosts: zabbix
  tasks:
    - name: install zabbix    # 安装软件包
      yum:
        name:
          - zabbix-server-mysql
          - zabbix-web-mysql
          - zabbix-nginx-conf
          - zabbix-sql-scripts
          - zabbix-selinux-policy
          - zabbix-agent
          - mysql-server
          - langpacks-zh_CN
        state: present
[root@pubserver zabbix]# ansible-playbook 02-inst-zabbix.yml

# 6. 启动mysqld服务
[root@pubserver zabbix]# vim 03-start-mysqld.yml
---
- name: config mysqld
  hosts: zabbix
  tasks:
    - name: start mysqld      # 启动mysqld服务
      service:
        name: mysqld
        state: started
        enabled: yes
[root@pubserver zabbix]# ansible-playbook 03-start-mysqld.yml 

# 7. 在zabbix上创建连接数据库的用户
[root@zabbixserver ~]# mysql
mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
# 在binlog日志开启的情况下，打开函数生成器
mysql> set global log_bin_trust_function_creators = 1; #允许执行创建函数
mysql> create user zabbix@localhost identified by 'zabbix'; #创建用户指点密码
mysql> grant all privileges on zabbix.* to zabbix@localhost; #授予用户操作数据库zabbix的全部权限
mysql> quit;

# 8. 验证数据库配置，如果登陆不到数据库表示上一步配置不正确
[root@zabbixserver ~]# mysql -uzabbix -pzabbix -hlocalhost zabbix

# 9. 在数据库中创建表并导入数据
[root@zabbixserver ~]# cp /usr/share/zabbix-sql-scripts/mysql/server.sql.gz .
[root@zabbixserver ~]# gzip -d server.sql.gz 
[root@zabbixserver ~]# mysql -uzabbix -pzabbix zabbix < server.sql

# 10. 配置zabbix_server
[root@zabbixserver ~]# vim +129 /etc/zabbix/zabbix_server.conf
DBPassword=zabbix

# 11. 配置zabbix_agent
[root@zabbixserver ~]# vim /etc/zabbix/zabbix_agentd.conf 
182 Hostname=zabbixserver

# 12. 配置nginx展示zabbix
[root@zabbixserver ~]# vim /etc/nginx/conf.d/zabbix.conf
# 打开第2、3行的注释，结果如下所示：
server {
        listen          8080;
        server_name     example.com;
...以下省略...

# 13. 启动相关服务
[root@pubserver zabbix]# vim 04-start-zabbix.yml 
---
- name: config zabbix
  hosts: zabbix
  tasks:
    - name: start service    # 循环启动多个服务
      service:
        name: "{{item}}"
        state: started
        enabled: yes
      loop:
        - zabbix-server
        - zabbix-agent
        - nginx
        - php-fpm
[root@pubserver zabbix]# ansible-playbook 04-start-zabbix.yml 
```



## 配置zabbix监控web1服务器

### 在web1上安装agent

```bash
# 1. 安装agent
[root@pubserver zabbix]# vim 05-inst-agent.yml
--- 
- name: install agent
  hosts: webservers
  tasks:
    - name: install agent    # 安装agent
      yum:
        name: zabbix-agent
        state: present
[root@pubserver zabbix]# ansible-playbook 05-inst-agent.yml 

# 2. 修改web1配置文件
[root@web1 ~]# vim /etc/zabbix/zabbix_agentd.conf 
117 Server=127.0.0.1,192.168.88.5
182 Hostname=web1

# 3. 起动服务
[root@pubserver zabbix]# vim 06-start-agent.yml 
---
- name: config agent
  hosts: web1
  tasks:
    - name: start agent      # 启动服务
      service:
        name: zabbix-agent
        state: started
        enabled: yes
[root@pubserver zabbix]# ansible-playbook 06-start-agent.yml 
```

### 在web页面中添加对web1的监控

- 主机：安装了agent，被监控的主机
- 主机组：根据需求，将多台主机加入到一个主机组中，方便管理。系统默认已经创建了一些主机组。
- 模板：是监控项的集合。将模板应用到主机，主机就可以直接拥有模板中的所有监控项。系统中默认已经创建了一些模板。

![image-20221231115246687](images/image-20221231115246687.png)

![image-20221231115521481](images/image-20221231115521481.png)

![image-20221231115633064](images/image-20221231115633064.png)

![image-20221231115722043](images/image-20221231115722043.png)

#### 常用监控指标

- Space utilization：以百分比显示的磁盘利用率
- Used space：已用磁盘空间
- Available memory：可用内存
- CPU idle time：CPU空闲时间。不宜过低。
- Load average (1m avg)、Load average (5m avg)、Load average (15m avg)：CPU1分钟、5分钟、15分钟的平均负载。这个值不应长期大于核心数。
- Interface eth0: Bits received：网卡接收到的数据量
- Interface eth0: Bits sent：网卡发送的数据量
- Number of processes：系统运行的进程数
- Number of logged in users：已登陆的用户数

## 自定义监控项

### 实现监控web1用户数量的监控项

1. 在被控端创建key。被控端被监控的内容叫作key，可以理解为它就是一个变量名，具体的名字自己决定。
2. 在web页面中创建监控项。监控项对应key值。

#### 在被控端创建key

- 创建key的语法

```bash
UserParameter=自定义key值,命令
# 命令的执行结果，是key的value
```

- 创建自定义配置文件。文件名自定义。

```bash
[root@web1 ~]# vim /etc/zabbix/zabbix_agentd.d/usercnt.conf
UserParameter=usercnt,sed -n '$=' /etc/passwd
[root@web1 ~]# systemctl restart zabbix-agent.service 
```

- 验证自定义监控项

```bash
# 1. 安装zabbix-get
[root@zabbixserver ~]# yum install -y zabbix-get
[root@web1 ~]# yum install -y zabbix-get

# 2. 获取监控项的值
[root@web1 ~]# zabbix_get -s 127.0.0.1 -k usercnt
46
[root@zabbixserver ~]# zabbix_get -s 192.168.88.100 -k usercnt
46
```



#### 创建模板

- 创建模板

![image-20221231154102681](images/image-20221231154102681.png)

![image-20221231154617325](images/image-20221231154617325.png)

- 创建监控项

![image-20221231154957701](images/image-20221231154957701.png)

![image-20221231155026124](images/image-20221231155026124.png)

![image-20221231155250061](images/image-20221231155250061.png)

#### 应用模板到主机

![image-20221231155447846](images/image-20221231155447846.png)

![image-20221231155533022](images/image-20221231155533022.png)

知识点思维导图：https://flowus.cn/share/7875c0e1-ebbd-4dad-aa7e-f40e6ed6c6d3