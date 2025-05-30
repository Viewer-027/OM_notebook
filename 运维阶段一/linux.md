```bash

```











# **Linux基础**

https://docs.qq.com/s/JjkMzhF_1JIIkKVWNc7gnq

 alsamixer

 

 

**day2、3 rm 、ls 、cp 、grep、echo**

​            \1.     shutdown -h now  ：立刻关机

​            \2.     rm -rf [目录或文件] ：递归目录、强制删除ssh

​            \3.     mkdir -p [目录]  ：递归地创建目录

​            \4.     ls -lhdAR [目录或文件] 

-l：以长格式显示，显示详细信息

-h：提供易读的容量单位（K、M等）

-d：显示目录本身（而不是内容）的属性

-A：显示所有内容,包含隐藏数据

-R：递归显示目录内容

​            \5.     cp -r [原目录或文件] [目标目录或文件] ：-r 递归，复制目录时必须要有。

​		-p:保持权限不变的复制

​      \cp   ;临时取消别名，实现不询问强制覆盖

​            \6.     grep -i 忽略大小写  

​            \7.     echo #将字符串回声到标准输出  # -e :启用反斜杠转义的解释  

 

 

**Day4 Tar 、| 、find、du、ln**

格式：tar 选项 /路径/压缩包名字 /源数据…….

-c:动作为创建

-f:指定压缩包名字(必须在所有选项最后)

-v:显示处理过程中的文件信息

-z、-j、-J：调用 .gz、.bz2、.xz 格式工具进行处理

-C 指定路径，只压缩文件

1、tar **-**zcf **/**root**/**yy**.**tar**.**gz  **-**C  **/**etc**/**   passwd  **-**C  **/**usr   games

​            2、     tar **-**zcf **/**root**/**zz**.**tar**.**gz   **-**C  **/**etc  passwd   shells    hosts   fstab

​            3、     tar **-**xf  **/**root**/**haha**.**tar**.**bz2    **-**C     **/**nsd11

tar命令也可以单独进行归档不进行压缩

]# tar  -cf  /root/abc.tar  /etc/passwd   /etc/fstab 

 

-r 压缩目录  -l查看压缩内容  -d指定解压路径

zip -r /xx/xx.zip /xx/xx/ /xx/xx/

unzip -l xx.zip    -d /xx/

不支持管道 " | " 的情况

​            \1.     双参数以上命令

​            \2.     没有参数也能运行的命令

​            \3.     交互式命令

-newrmt 时间之后  ; ! -newrmt 时间之前 ;-iname 忽略大小写

find /var -newermt '2025-3-5 10:00:00' ! -newermt '12:30:00'

du，统计目录占用硬盘的空间

-s：只统计每个参数所占用的总空间大小

-h：提供易读容量单位（K、M等）

ln   -s   /路径/源数据    /路径/快捷方式名字   #软链接

软链接可以跨文件系统，也可以跨磁盘分区

源数据消失，硬链接依然可用

软链接可以建立对于文件和目录的链接

硬链接不可以跨区，只能建立对文件的链接

 

**Day5、6 Rpm、Yum、User、Cron**

​            1、     rpm 命令					  || yum命令

​	rpm -ivh 安装				  ||  install   

​	-uvh 升级					  ||  update

​	-qa 查询全部				  ||  list

​	-qf	查询程序来自			  ||  provides

​	-qi	查询软件包详情			  ||	 info			

-ql	查询软件包安装清单		  ||  

-e 卸载						  ||  remove

-V	

--import

​            2、     yum list

| **特性**               | **rpm**                    | **yum**                  |
| ---------------------- | -------------------------- | ------------------------ |
| **主要用途**           | 处理单个 .rpm 文件         | 管理软件包及其依赖关系   |
| **依赖关系处理**       | 不自动处理依赖关系         | 自动处理依赖关系         |
| **软件源**             | 本地 .rpm 文件             | 远程仓库或本地仓库       |
| **安装命令**           | rpm -ivh package.rpm       | yum install package_name |
| **升级命令**           | rpm -Uvh package.rpm       | yum update package_name  |
| **卸载命令**           | rpm -e package_name        | yum remove package_name  |
| **查询已安装的软件包** | rpm -qa                    | yum list installed       |
| **查询可用的软件包**   | 不直接支持                 | yum list available       |
| **搜索软件包**         | 不直接支持                 | yum search keyword       |
| **验证软件包**         | rpm -V package_name        | 不直接支持               |
| **批量操作**           | 需要脚本或其他工具辅助     | 内置批量操作功能         |
| **安全性**             | 手动处理，可能需要额外注意 | 自动处理，更安全和便捷   |

 

​            3、     uname -r    #查看内核版本命令

cat /etc/redhat-release

​            4、     zip [-r]  备份文件.zip   被归档的文件    

 \#归档压缩文件，被归档的数据有目录必须加上 [-r]

​            5、     unzip [-l]    #查看压缩包内容    [-d] #指定解压缩路径

​            6、     **useradd**   #

useradd  -u 1200 -G nb -d /rhome/hakeem -s /sbin/nologin  hakeem

-u：指定 用户UID

-d：指定宿主目录（家目录），默认为 /home/用户名

-G：指定所属的附加组，-g指定基本组

-s：指定用户的登录解释器，默认为/bin/bash

 

​            7、     usermod     # 

​	usermod -l vivian -G adm thomas  #

-l 修改用户名 ，-u修改uid，-s修改解释器

​	-md 移动并修改家目录; -d只修改，原家目录还在;

​	-G 修改附加组，不是添加;

​            8、     userdel  -r  # 带-r 家目录/用户邮件也一并删除; 不带只删除用户信息。

​            9、     **gpasswd** [选项]...组名     #

​	gpasswd  -M  'jack,kenji'   tarena    #定义组成员列表

​	gpasswd  -A 'nb,kenji'   tarena         #定义组管理员列表

-a：添加组成员，每次只能加一个

-d： 删除组成员，每次只能删一个

-M：定义(重置)组成员用户列表，可设置多个用户

-A： 添加成员为组管理员，拥有-a、-d权限

​            10、     **crontab** #

–编辑：crontab -e [-u 用户名]

–查看：crontab -l [-u 用户名]

–清除：crontab -r [-u 用户名]

\*  *  *  *  *  /usr/bin/date  >>   /opt/time.txt

 

 

 

总结：Linux系统执行userdel -r命令，会完成那些操作？

1.会在/etc/passwd删除一行信息

2.会在/etc/shadow删除一行信息

3.会在/home删除用户家目录

4.会在/var/spool/mail删除用户邮件文件

5.会在/etc/group删除一行组信息

6.会在/etc/gshadow删除一行组的管理信息

删除组账号：删除组的时候，不可以删除基本组

[root@localhost ~]# groupdel   tarena    

[root@localhost ~]# groupdel   tmooc

[root@localhost ~]# grep   tmooc  /etc/group

[root@localhost ~]# grep  tarena  /etc/group

 

 

 

 

 

**Day7 chmod、chown、setfacl**

对于文本文件：

r读取权限：cat、less、grep、head、tail

w写入权限：vim、> 、 >>

x可执行权限：Shell与Python

对于目录：

r读取权限：ls命令查看目录内容

w写入权限：能够创建、删除、修改等目录的内容

x 执行权限：能够cd切换到此目录下（进入此目录）

组命令

groupadd 

groupdel

​            1、     gpasswd -a pyf tmooc

-a：添加组成员，每次只能加一个

-d： 删除组成员，每次只能删一个

-M：定义(重置)组成员用户列表，可设置多个用户

​            2、     chown

chown newuser:newgroup file.txt

chown -R newuser:newgroup /path/to/directory

chown -v newuser file.txt  #启用详细模式

​            3、     chmod -R u=rw,g=rw,o=rw /up/

– r、w、x分别对应4、2、1，后3组分别求和

分组： User权限 Group权限 Other权限

字符： r w x r - x r - x

数字： 4 2 1 4 0 1 4 0 1

求和： 7 5 5

**附加权限：了解**

​                ● 利用数字4表示suid

​                ● 利用数字2表示sgid

​                ● 利用数字1表示t

4、setfacl命令

–格式：

setfacl [选项] u:用户名:权限 文件...

setfacl [选项] g:组名:权限 文件...

•常用命令选项

-m：修改ACL策略

-x：清除指定的ACL策略

-b：清除所有已设置的ACL策略

-R：递归设置ACL策略

 

**day8、9 磁盘空间MBR、GPT; 逻辑卷、进程、visudo**

​            1、     df -h   ;查看设备挂载信息

​            2、     mkfs.ext4    #格式化ext4

msdos或者MBR分区表最多可以分4个主分区，创建5个以上主分区，需要使用GPT分区表格式。

执行（df -h）或（mount）可以查看系统中已挂载的分区设备信息。执行（partprobe）命令可以重新识别分区表信息，使用（blkid）命令可以查看块设备的UUID标识符。

vgdisplay

​            3、     vgcreate  systemvg   **/**dev**/**vdb**[****1-2****]  ##创建卷组**

​            4、     vgextend    systemvg     **/**dev**/**vdb**{****3****,****5****,****6****}  ##拓展**

​            5、     vgchange  **-**s  1M  systemvg   #修改PE时，只能修改为创建时的PE的公因数

​            6、     lvcreate   **-**L  16G  **-**n  vo   systemvg   ##创建逻辑卷; # -l 200 

​            7、     lvextend  **-l**  +100%FREE   **/**dev**/**systemvg**/**vo ##拓展逻辑卷

​            8、     xfs_growfs  **/**dev**/**systemvg**/**vo  ##刷新xfs文件系统

​	resize2fs						##刷新ext4、ext3文件系统

​	mkswap    swapon    swapoff    #交换分区命令

​            9、     命令格式：pkill -9 -u 用户名 -t 终端

干掉进程的不同方法

–Ctrl+c 组合键，中断当前命令程序

–kill [-9] PID... 、kill [-9] %后台任务编号

–killall [-9] 进程名...

–pkill [-9] 查找条件  [-nouUtfx] #包含就算

​            10、     ps aux | grep 'Z'  ##查找僵尸进程

​            11、     ps -ef   ##显示完整的进程信息

​            12、     pgrep -lx    #选项-l显示PID号、-x精确匹配进程名

1）RAID：廉价冗余磁盘阵列，指通过硬件/软件技术将多个较小/低速的磁盘整合成一个大磁盘使用的一种存储技术，其不仅可存储数据，还可以实现一定程度的冗余保障，具有“速度快、安全性高”的优势。

2）RAID0、RAID1、RAID5的含义及特点如下：

RAID0：条带模式，由两个或两个以上的磁盘组成，同一份文档分散在不同的磁盘中，并行写入，提高写效率。

RAID1：镜像模式，由至少两个磁盘组成，同一份文件被分别写入到不同的磁盘中，每份磁盘数据一样，实现容错，提高读效率。

RAID5：分布式奇偶校验的独立磁盘模式，结合RAID0和RAID1的好处，同时避免它们的缺点。由至少3块以上大小相同的磁盘组成，实现冗余。

RAID 0+1/RAID 1+0，整合RAID 0、RAID 1的优势，并行存取提高效率、镜像写入提高可靠性，至少需要四块磁盘组成，磁盘利用率50%

visudo

pyf       ALL=(root) NOPASSWD:/usr/bin/mkdir,/usr/bin/cat

%sysmgrs ALL=(ALL) NOPASSWD: ALL

 

 

**day10 、11网络nmcli、ssh、scp**

​            1、     nmcli connection add type ethernet ifname eth1  con-name eth1

​            2、     nmcli connection modify  eth0    

pv4.method    manual                          

ipv4.addresses   192.168.88.77/24                

ipv4.gateway   192.168.88.200               

autoconnect    yes

​            3、     scp  [-r]  用户名@服务器:路径  /本地路径  #路径可对掉，传输数据，前面传后面

​            4、     ssh-keygen   #生成公钥私钥，ls  /root/.ssh/ 查看生成的文件

​            5、     ssh-keygen -R 192.168.88.2  #删除目标的密钥

​            6、     ssh-copy-id   root@192.168.88.2 #发送公钥 

 

•常见的日志文件

/var/log/messages 记录内核消息、各种服务的公共消息

/var/log/dmesg 记录系统启动过程的各种消息

/var/log/cron 记录与cron计划任务相关的消息

/var/log/maillog 记录邮件收发相关的消息

/var/log/secure 记录与访问限制相关的安全消息

 

users、who、w 命令

–查看已登录的用户信息，详细度不同

last、lastb 命令

–查看最近登录成功/失败的用户信息

 

root密码破解操作 

虚拟机开机界面   按 e

在linux开头的该行，将此行的ro修改为rw 然后空格输入 rd.break

按 ctrl x 启动，会看到switch_root:/#

chroot    /sysroot   #切换环境，切换到硬盘操作系统的环境

echo   1    |    passwd   --stdin    root

vim  /etc/selinux/config  #查看SELinux开机的运行模式

reboot -f

 

 

**day12、13 Web、NFS、FTP、DNS**

**WEB服务搭建命令**

yum -y install httpd

vim /etc/httpd/conf/httpd.conf

vim /etc/httpd/conf.d/*.conf

systemctl restart httpd

 

**NFS服务搭建命令**

rpm -q nfs-utils

vim /etc/exports

/abc  *(ro)

systemctl restart nfs-server

showmount -e 192.168.88.240

mount 192.168.88.240:/abc /mnt/mynfs

 

**FTP服务搭建命令**

yum -y install vsftpd

vim /etc/vsftpd/vsftpd.conf

anonymous_enable=YES

cp  -r  /tools/other/    /var/ftp/rpms

systemctl restart vsftpd

 

**DNS服务搭建命令**

yum -y install bind bind-chroot

vim /etc/named.conf

options  {

​        directory       "/var/named";  

};

zone  "tedu.cn"  IN  {       #定义负责的解析tedu.cn域名

​        type  master;        #权威主DNS服务器

​        file   "tedu.cn.zone";     #地址库文件名称

};

cp -p named.localhost  tedu.cn.zone   #保持权限，备份文件

vim /var/named/tedu.cn.zone    #地址库文件

NS  nsd2408   #声明DNS服务器为nsd2408

nsd2408  A   192.168.88.240   #nsd2408解析结果为192.168.88.240

www    A   1.1.1.1

ftp     A   2.2.2.2

systemctl restart named

nslookup xixi.pyf.com

echo nameserver 192.168.88.240 > /etc/resolv.conf

 

DNS服务器资源解析记录的类型：

NS：DNS服务器声明记录

A：正向解析记录

CNAME：解析记录的别名

 

 

**day14 源文件、rsync 、mysql**

源文件安装相关命令

yum -y install gcc make ncurses-devel

tar -xf /×/×/in×.tar.gz -C /usr/src

cd /usr/src/*

./configure  --prefix=/*     #配置，--prefix指定安装目录 /功能模块，默认（/usr/local/*/）

make                    #编译，生成可执行的二进制程序文件

make install              #安装，将编译好的文件复制到安装目录

 

RSYNC

rsync -avX --delete /mydir/ /todir

-a, --archive：归档模式，表示递归传输并尽可能保持文件属性（等同于 -rlptgoD）。

-r, --recursive：递归处理子目录。

-v, --verbose：详细模式输出，显示传输过程中的信息。

-q, --quiet：静默模式，减少输出信息。

 

 

Mysql

yum -y install mariadb-server

systemctl start mariadb

mysqladmin -u root -p456 password '123'

 

**day15 podman容器、Ubuntu系统、NTP时间服务**

podman load -i /root/img.tar.gz

podman images

podman rmi [ID]

podman run --name abc01 -it localhost/rockylinux:8.6 /bin/bash

podman start abc01

podman exec -it abc01 /bin/bash

podman rm -f abc01

\-----------------------------

 

\------------------------------

yum -y install chrony

vim /etc/chrony.conf

末行模式  :set  nu     #服务端配置

3    #pool 2.pool.ntp.org iburst     

23   allow  all

26   local  stratum  10

\----------------

3    server materials.example.com iburst  #客户端配置

systemctl restart chronyd

 

**网络常忘命令**

[Huawei]vlan batch 10 20  //批量创建vlan

[Huawei]port-group 1    //创建(进入)1号接口组

group-member Ethernet 0/0/1 Ethernet 0/0/2  //给接口组添加成员1口和2口

port link-type access   //设置接口类型为接入链路

port default vlan 10   //将接口组中的所有接口加入vlan10

\------------------------------------

[Huawei-Ethernet0/0/6]port link-type trunk 

[Huawei-Ethernet0/0/6]port trunk allow-pass vlan all 

[Huawei]display ip interface brief

\-----------------------------------

[Huawei]ospf

[Huawei-ospf-1]area 0

[Huawei-ospf-1-area-0.0.0.0]network  192.168.3.0  0.0.0.255

[Huawei-ospf-1-area-0.0.0.0]network  200.168.1.0  0.0.0.255

\----------------------------------

Huawei]acl 2000

[Huawei-acl-basic-2000] rule permit source any

[Huawei-acl-basic-2000] quit

[Huawei]interface GigabitEthernet 0/0/1

[Huawei-GigabitEthernet0/0/1]nat outbound 2000  #启用Easy IP地址转换

\-------------------------------------

[Huawei]ip  route-static  0.0.0.0  0.0.0.0   GigabitEthernet 0/0/1

[Huawei]ospf

[Huawei-ospf-1]default-route-advertise #宣告本机的默认路由给所有ospf的其他路由器或三层交换机

\----------------------------------------

[Huawei]interface Vlanif 10

]vrrp vrid 1 virtual-ip 192.168.1.252  #组织名称为1，虚拟IP地址为1.252

]vrrp vrid 1 priority 200  #优先级200

[Huawei]interface Vlanif 20

]vrrp vrid 2 virtual-ip 192.168.2.252  #组织名称为2，虚拟IP地址为2.252

]vrrp vrid 2 priority 200   #优先级200 越大越优先

\---------------------------

 

 

 

 

**网络基础知识**

​            1、     计算机网络的功能

资源共享、信息传递、增加可靠性、提高系统处理能力

​            2、     网络标志技术

60年代：分组交换 ; 70-80年代：TCP-IP ;90年代：web技术

​            3、     网络拓扑结构：星型、网型

​            4、     OSI的七层与TCP/IP的五层参考模型

物理层，数据链路层，网络层，传输层，会话层，表示层，应用层

物理层，数据链路层，网络层，传输层，应用层

5、华为路由交换设备常见的命令行视图

用户视图，系统视图，接口视图，协议视图

6、交换机工作原理

学习、广播、转发、更新

​            7、     valn作用

广播控制，增加安全性，提高带宽利用，降低延迟

8、TRUNK的作用是什么？

为数据帧打上VLAN标识，使不同VLAN数据可以用一条链路传递

9、链路聚合的作用是什么？

链路聚合为交换机提供了接口捆绑的技术，允许两个交换机之间通过两个或多个接口并行连接，同时传输数据，以提供更高的带宽和可靠性

10、网络层的功能有哪些？

定义了IP地址、连接不同的媒介类型、选择数据通过网络的最佳路径

11、ping工具与协议ICMP有关

12、获取路由表的方式有哪些？

直连路由、静态路由、动态路由、默认路由

14、请描述TCP/IP五层参考模型以及对应的典型设备

物理层，数据链路层，网络层，传输层，应用层

网卡、交换机、路由器、防火墙、web服务器

（传输层定义的端口号的范围： 0～65535 ）

15、在接口可以定义的类型中，trunk与access各有什么特点？

trunk：可以承载多个 VLAN 的流量

数据帧会被打上 VLAN 标签

通常用于连接交换机之间或交换机与路由器之间的链路

access：只能属于一个 VLAN。

数据帧不会被打上 VLAN 标签

通常用于连接终端设备

16、vrrp的作用网关的冗余备份，可以保障网关设备出现故障的情况下不会对网络造成重大影响。

17、VRRP中路由器身份有哪些？

主路由器，备份路由器，虚拟路由器

18、NAT的作用

通过将内部网络的私有IP地址翻译成全球唯一的公网IP地址，使内部网络可以连接到互联网等外部网络上。

19、私有IP地址分类有哪些？

A类 10.0.0.0~10.255.255.255

B类 172.16.0.0~172.31.255.255

C类 192.168.0.0~192.168.255.255

20、NAT常用实现方式有哪些？

静态转换、Easy IP

21、子网划分的原因

节约IP 、满足不同网络对IP地址的需求

22、IPv4 32位 IPv6(常用16进制配置) 128位

23、访问广域网时只有默认路由方便匹配任意网段

 

### **2 描述TCP三次握手以及四次断开的过程**

**参考答案**

三次握手,如图-1所示

![img](data:image/png;base64,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)

四次断开,如图-2所示

![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAZkAAAEDCAIAAABoOu68AAAgAElEQVR4Ae2dB1wUR/vH50AQVKRJFdi9O4oiFkCsKEgTsJCosRs1AdHEaNTE6Cu+ibGkvImxcRhfS4zlfe0m9gN7iUlMjHIYFaKJeRPvUtUUC3D7/9+Njuve3nHg3bG7PH72k8zO7s7M/naeL88zO7eDlBQNGygACoACYlcAif0GoP2gACgACigpGlgGbikoAApIQQFgmRSeIvxZBgVAAWAZsAwUAAWkoACwTApPEf4mgwKgALAMWAYKgAJSUABYJoWnCH+TQQFQAFgGLGtACuTn5ul0OmL2RSpVmUZDdmubKD6gZh78K1Kpans5nG9bBYBlQrFkjl2VqItL1MWP+bB1Ot3jF/KYbRDU5Y/DMr1eH07L2bdTfEBdoi4Op+W4WM5R9pmQdoACwDLRs4xjn6TT4Hy2G0IONdgERyvO3w/LslhgGS4HWGZZQHsfBZZJlmUl6uIilUqn0+Xn5tm7G4ml/PzcvAdB4f3/kxhTp9PhrDKNBlNJq9XiHPZV7FiSHWMCyOq9DwDLBMQyjpmR8JDkm+awzYzDLIZhlBRdoi4m5lrvva3eG2DOLyvTaIi2mtLSIpXK1NWy4JcpKdr0aL3fbENrALBMQCxjQ4eMl+l0OuILYCeLHMKdlWOfOJNETxh2Da1bm7tfjlZEJbb3WqRSYa4VqVQMw+h0OuxzmdKKjJcpKVqr1XL+lphrA+TbSQFgmdBZht0r/Phx2Ii9LYZhMPs49onPLNNoiDfHMAyhoZ26kViK5WhFWGbql5E7wm4ar+dFWJafm2dKOlICJByjALBM6Cwz9ctIz8DeBMc+8VE2AYnFkgsbbIKjFVsZMl7GdsrYfhkePmP/VWCPl7HzG6y89XvjwDKhs0xJ0cTDwgZToi7GOSQmxbskxmGbKO5eDMOQo/Xb4aB2UMBOCgDLhMIyOz1gKBYUaCAKAMuAZaAAKCAFBYBlUniKDeQPL9wmKGBBAWAZsAwUAAWkoACwTApP0cIfKzgECjQQBYBlwDJQABSQggLAMik8xQbyhxduExSwoACwDFgGCoACUlAAWCaFp2jhjxUcAgUaiALAMmBZw1VAEUZFKSPaRLXu2b3XE/0HLlhQ+NK0md26dI1QKBuI/UvpNoFlQrfkcFpeMF+lPvODNdvmE1d7jnlZSh3UtvcSqVB2Segx4MkxL06evWTx1k2bjh4+fOWLL7QXLvxdUXHv8uW7331X/f33lefO/bBhw8e5z05IiEuwbQOgNPspACwTOMvkw0ZNWLHlxEenvqtx++/RKwNUR6OfmmS/7iL2klOTU/78k9m378q6dWc3bTq/Y8fX27eX7dz59e7dF/fvLy8puXL48LfHj3//+ec/f/31X999p6+o+OvYsQtvLFjSr09OdFQrRRgldgUk3H5gmaBZFt+x+4z3Ni3fe9Gaber6Mz4vbW0zdk5UxyQJd9nHubXOHbv++Sdz/XrlkSNXli4tXrBgZ2HhwZUrT65de3rDhjObNp3dvr30448v7Nt3Wa3+5uDBq8eOfX/mzK+XL9+9erXq3Dnd1i2HX5g4vXuXxAi54nGaAdfaQwFgmaBZ1m/YhCmrP5uy+vOpaz6f9gH/NnXN58ZzPoss+BhN3Nj9n5vaDH3FHn1FAmW2bd2uouLv8vI/dbrq8vI/duw4O2fOpn/8Y93bb3+8ePGBwsKD779/bPXqU+vWffaf/3y5dev5nTvLdu++uG9feXHxlWPH/nf27I3y8nuXLv195MjFhQvXDhk0qnVkKwnIIo1bAJYJmmV9Rk4avujY8EXHclUnn1/xCe/2TOFJfE7Q9G1o4sYmkzc2H/a6NHqnze+iVXhkWdlPavU3+/dfvHTp1rVr986cuV5UtP+FF5ZNmqT65z83zJ+/5a23di5cuHfp0uKioiMrV55Yu/b0xo1ntmw5t3Xr+c2bz23efG7Xrotnzvz000+GLy1NfmGazRsJBdZNAWCZsFk2avLghUenrvls/eHy/x6t4N3WHbo8aeXpwQuPBry8DU3cgCZucB82r269QfJXRSoU58//cOnSn3v3fr18+eG9ezVff/3nhQu31OqLc+asHzp0zujRb02evHz69NUFBevmzt38xhs7FizY+frrW+fO3V5YWPzRzq/On7/+y8+3q6sMIKuuZtJTekteNLHcILBMBCxbtvvC2cu6s+X825eXdf/acR5YZo3JRSmVX3557auvfjt//tdjx64tXrz7X//aolZf+vLLG598cn3dupO5uf9KT5+Uk/OPAQNefeKJWSNHzpkxo+jDD9VffPHN77/9jb95yTDM7duVV67c+PFHpmdiD2vqhXMcoACwTAQs23Hy6tnyn8yx7Gy5bt3hcmCZNdYSIVceOVL++ec/nzjx/YkT/zt9Wvfhh8emTVv8zjubS0quHjnyw969l99+e1u/fi8+99yr//3vnu++vU74xTCMvrr6h//9evqTq2r1N6dOXb9w4Y/YdnHW1AvnOEABYBmwTNAK2NwGtm8/88kn2oMHr6jV3+zbd7mk5Ns9ey7Nnr3imWf+sWzZ7t27K3bs+HrVqiPPP//a8uXb1q/fsWbNprVrt6xfv2PLlhNFRfuXLTu0YcOX+/eXHz587cSJb1qFR9i8hVBg3RQAlgnakvsYx8t2nLpqwSkz+mWXwS+zxgAUYdS//73v6NEf9uy5+PHHX+/cWWYc0f/q448vq1T7hw9/MTe3YMWKI0uXHkhIGNyx46iePZ/v23fG8OHzxo9fNGPGunfe2btixbE1az7ZsOGLkye/P3jwQiT8QoASigUBy4TyJHhNEVjGK0udMxVh1KJFGw8c+A4j7D//+WL9+s8++OD0qlUnVq8+vXbt5+PGzRk69KVFi/a2a5faoUPfHj3GZmVNHTRoztix70yYsHTq1BWvvvrfwsKSLVvO6HR3Vq7cowgTdP+ps1BivBBYJui+CCyzuVG9886aHTsuPUDYyRUrjhUVHV66tGTRov0LF+6bP3/zwIET33hjc0REXHBwdMuW8dHRmYmJIwYOfGXq1CUq1Z4dO87s339u374zly//+PLLb9m8eVBgnRUAlgHLBK1AnXu2uQvnz1uxceNXK1YcU6kOL1tWsmjRgXff3fP22x8tWLB9/vyt06apevce8eqraz09A/v3H7BnT/GBA5+d/bL8woX/Xbr0a5lGd+7cT5cv//bLL/e+++7mkMHwczEBdR5gmYAehqn5gV9mqslj5kyYMHfVqtOLFz9E2Ny5m197bWNBwYczZ37w3HMLU1OHTJ1a5OTkOnnyVONav/euXvnj6tVb33//562b1VWVzM0bVTrd7VOnznXulPGYjYHLbagAsEyCLPN9aqYNu4jEihr81AuFhYcXLNg+d+4mjLAZM1ZPm7Zi0iTV+PGLJ0xYmJ09Mi/vbZnMaezYPJ3u3vXrf9y5XW2YGVvFaLV/lJVdLyvTarV/HDhwon27rhITR9S3AywTAcu2nbjylcX5ZWsPPXyP6TxxQ8xTkzq0jY1u017UXdNOjc/OGvHOO3sKCj4kCJswYUlu7sIxY94eNmz+8OFzkpMHDh/+KkIoP38cnlz2229/ff31j+fOfX/5su733+/PmF26dENIcLidGgnF1kEBYJmgWZYz9qXBC4+uLblkmWXL910gczK8p24Z9a+Plm8snrB4Wx06hOQvSUt98tVXN02apCIIGzFiweDBrz/55Oy+fV/p129qYmKf/v0nIYTGjn3m6tXfv/zyWlnZjz///IderyfzZm/e/L1//6epEGCZgMwHWCagh8HhSO+sJ15etG3wwqOvfHhm07Fvtp28wrv952jF1DWfYZY5TdwY99red3ecf2XjZ72XFHMKhF0lRaem5EyZsmr06LcxwgYMmN2v34ysrKlpaROTk/N69nw6NrZXYuIIhNCoUWO02j/v3jX+9pJgjGF+/PHH5OT0oKB2Cgq+/CMg8wGWCehhENZEyBUpqZmz5qsW7tQMXnh02HvHZm34Ys6ms7zbzHVnhr53dPDCo0Evb2v5yvb8laff3FGaMH9f1NxdpEBIEAUSu/UeM+a9J5+8j7D09Od79crr0WNM167DExIGxsb2jYhIiI3NQgiNH5/PIpghWVlZtWbNGm9vn+TkrJSUPmEtQ0ixkKh3BYBlgmNZOC2Pax8/593V245/+97OsqfePTLIuk0+Y8eA9w5/UFye9k6JbOLGyNd21nv3EmADWkV0GDDgn48ibFBcXP/27TNjYlJbteoRGhodFdUVITR1quE9ZnV19U8//VxcXDxhwnMBAQEIoSlTXrp9uzI5OY0ODRPgDTbYJgHLBMeyDu06zHl3zfoDpduOf7t878W+bx/KfPOgNVvOwsPv77s0fPlxj6mbZRM3hk9c2mC7tYUbV1BU5879e/QYm5Aw0IiwrJiYtNate0ZGdgsP7ySXd/DxCQ4Jae3k5Ny2bbvk5F5eXp7Ozo0QQn5+/s8998LZs2UMw2i1t5TKCPhktgWdHX8IWCYslrWKbD3qhTmLt5xeuf/Syv2Xlu26MO2Dz6es+cyabeb6L6atP0PN2imbuNFznCo8+QnH9ydR1EiHhLVrl96uXe8HCEuQy2Mpqm1oaOvg4IjmzX39/cOcnAz86tEjLS9vwoIF7xw6dOq33+4wDPP338zdu8wnp8+7uroqKPj8v4DMB1gmoIehpOiYtvGTC4sLNnxZty3xjQOyiRubTFgV2m9CuBwWRjP7cENbhkVFdVUo4iiqbUiIAWGBgUp/f9rPL6RpU09vb0MsGRMTg99d6vXMnTvMjRv6Gzeq//zD8Dbz0qUr7m7uogB3w2kksMxsd6+XTtAmttvghYaBfCu3Ye8dG7bo/jZk4dHg6dsbTVwfOvr1cAVMF6jhyQYFBIeGtg4MVAYEUH5+ob6+wd7egZ6efm5uTT08vBBCbdrE3LhR9dtvVb/9eu/mzeo//2Tu3GYqKw0vAY4ePebRtFm99BCo1JwCwLIaerw54eyUX1uWzVh3ZvbGs3jLLToZPH172IQl4e06KSm5nVoopWJ9vP38/EK8vQM8Pf2aN/f18PBu2tTT1dXNza0pZtmtW9V//sH89Rdz9w5TVcnoDfP/Df8++GCtp0dzKUkhgXsBlgmLZTEduowvOvnK2jNWbm9tO//O9lK85RWdaD9tVUSvJ8NhxTPrPqolDw3z8PBq3tynWTPPpk093N2bubk1adTIxcXFFbPszh19VaWRYg/nyRpYNn78eF9vHwnYv5RuAVgmLJZ1iO86b+PnK/Zesm67uGLv/e2t7aU587d1GvxCuAImcNbimYYEBbu7N3Vza+rq6ubi0rhRIxcn4z/MsurqRxlmdMrWrFnj5OQU4OcnJRBI4F6AZbXo9w543klJqev3nd9+/Fvrt23Hv9167OpLG85EjnnVAS2UXhV+Pi0aNXJ1dm7k5OQskzkhJDNuKCamLft3SwzDnDt3bujQocj4r2VgkPSkEPUdAcsExLLoyKh5i9ZtPfKN9SDbfvzbbce/Xam+3Gr29qjkHFH3xXpsfLOmzR4gDINMhhCKjm5z+/btixcvHTx48LU5ryX1TMIUM/5XFhbcsh4bDFWbKmCWZeE0Hd8hbP5cn09Pu+FtucqzW+fQcFpAxm96P6LOGTRkzNo9Z7cd+7ZW2+ajV55efowaMDk8Gj6MUcfOqQijGjVy4eDMxcU1NDTU1bUxQZizqzsGGUIyOiRU1J1Neo3nZ1k4TY8cFnjzphPDIPZ2545syiQ/wJmd+sG0N9cs33vp/VpuS3ZdiJ20LKIDfEurjiDDT5MOCeWwjCAMJwaNGHHx2x9dXNyQ4TwnmPRvJyuoc7H8LHsu3//OHRmbYuz0K7PkgLM6K27hwunrvpi06rPabmOXn4rLGmyhWDhkpQLBAYEmOHsYcvbo0WPpskInJ2eEUKCfv5VlwmkOU4CHZb3TWpp6ZGyWMQx65oV4hzURKgIFHKaAr5e3BZzhANPX29th7YGKrFeAh2XF6iYccpnuVlzzVbaF1ecfK6ix/iHBmY5UwNPTNNi8753JZE7BAYGObAzUZb0CXJYlJYaYkos3J3/uQKWytfU1wZmggCgUyMpcj1AAxzuTyZx8vX3kYfBjcuH+/eaybM2q5rzkMs1Un4qkOw0SRe+UTCPHJ3RanJLaN6atZO5IgDfSr+8WhH5AqAlCMnc39xbePmHBLeGTGAJ8UpwmPcKyNlGUhSF/U5ylPTNWScPHGBz3l2pLnz56hKoR6hoUzHmQsGsrBYwsYxBi3N1m2apMKMcBCjzCsvF5/qbAspAz5/10eWyWA1oJVWAFtvbtazAyhGa5uYEmdlIAWGYnYe1d7CMsmzfHxwK5TA+t2tGJTp5g7ybWS/llGo2SootUqhK1gFYAISwrAJZZ9+vxOnQeYFkdRBPCJY+wbMN6D1NgWcg5ckZBZc22cBs6nU5J0fm5eRgNZRoNQQPJVFI0PorLwZewy9TpdMaf9Br+k5+bh9OYMjgHn1ykUrHLyc/N4xTF2eVUwTm/TKMp02hwLTqdrkyjIW0oUqnYTSL5OKGk6BJ1cZFKRVpFbpnUWKbR5OfmkV0LDSPn4ASwjCOIPXaBZfZQ1QFlPsKyo0fcecnVvTv6978f+QEAPq3imq+BZRH8P53Jz83DZsz2bkrUxcS22TaPMcRr1Tqdjm35hFkl6mKd8R+WieQT1TAuOaxhGMa0FpzDbifGLvkvm5KkfHY5nBPwhaZV4xvBLCtRF3NOIAQkVXASwDKOIPbYBZbZQ1UHlPkIyyoqXDgsq6gwTA/897/5WXbnrjOVNVvRphtvQ4tUKo6tkl1CgRJ1MdvlwSdwuGCBZfhyjABTluFWmZaPGUoaY5ogPiO+BbLLuU1yFxzXkpyGL+RcTnw6jE5MdjbiyeWmCWCZqSY2zwGW2VxSxxT4CMt0OmcOy/DuoUP8LNPrkYFlHTIst5Vt8+RMjBi2J8JBGDnTMstIbMhhGS7fFGSYXDgSJB4iO1bF9bIv5MSY5Cr2fZHG4ygYH8L+F4dlGHwkWGaTlBRC7p2TAJZxBLHHLrDMHqo6oMxHWPbJKbdasezadS8qa7a8Y38LDTU1WmKxRSoV79gTmxFKiiaODM4nzCK+DPazSD5pDMln84KNLVICDjwxvzCqcCNx1aTkEnVxfm6eBX+TYRhMZ9xUTr2kalwRbieH1KTxvAlgGa8sts0EltlWT4eV9gjLtmxuViuWnfqKMrCsQ6aF5pJBdHwOe4wMs4xcy0EYO9/ceBlxkTBECCXZdZmih10acaNwO0mleBf/lwyxYZaRc9gN5lTNPsTxyzDgTFvFMAynEFIRSQDLiBT2SwDL7KetXUt+hGVvv+ldK5at2x1vYFmbRHNNJB4NeSdI6INnPJAYU6fT4bFwNgVwsRzPhZRJvCpcFI4E2cEj8cvYlbLfIRJI4Uo57hJpM/YNcclsDrKbysGQTqfjpRVx+nA5pAQ24s2J+f8rzgHLLIhjq0PAMlsp6eByHmHZpOf9asWyN1anGMbLImN5G41dHnII+yMcFmBnhARfeAIHsXB8LZtlJGJlD5yT0zBQCK3wmwH2yBdpAwYNaRupkeSTwJZhGFwOh2WYVqQENstwIeROOX4ZGS/Lz80jt8OmLSnTNAEsM9XE5jnAMptL6pgCH2FZ5/jQ6mru3Av2F+kOHXp4VK9Hg6c/HZZZoKD4F8vATCFjRvh+MCPwlAVzY0+ELHWTgGCFhLeYFGQOBLtY0h52JidN2kNiTDZ88cmkUvJOk01DIgIZH+RQDLetRqKZY9kz8R3n9+g5MjauW2RUJC2PpOgIWh5B0eF2m1DKkUhKu8AykT7NR1impOj9+2r+4A/23UrLA6ms2VTqiyK9czE22xzLPurXTx8RwUREMHFx9zIyzqembevTZ0mvlGfj4nNi2hroZtww3QBwlh89sMyyPoI9ymXZgJwgvf6h88UbcjIM0uvRjMXZYZkFdMITgr036TXMHMte6tr1npOTHiGyMQjd8/CoCgpi4uKYpKQfUlM/feKJJb1S/tk9cWRsXIxcQegG7hunnwDLOIKIZZfLMiVFl553NYcwkq/9pVlE/5lhvWcqFK3EcqsSaKc5lrUOo6Z4ex9Ess+Q7CpCOoSqjJ/TqGbRzfCBDWfney1aMK1bM3Fxf/XO/CojY2Nm5pJeKSNj4zKj23CC0wbrvgHLRGopPCwbPTLAdNSMUIxhULUeLVhlGPWnuo0U6W2LtNnmWEZup2tQcKp/wCgvr+ebNVvYuPE2mexTJDuP0DWE/n4AOOK76Y2f3Ljn4VEdHIzdt297pRwzum9TOnUeGRsXSdHEfQtvMKNvwDLSncSV4GGZkqKXFfqaw1l1Ndp7ohWVNdsw6m/mDaa4JBBRa2tkmem9RIeGJQUEDvD1HeXlNdfNbYNzoyNG9+0aQr88oBvbfWMQqnZ1rfTzY2JimISE39PTz/buvSY9452k5JGxcb1atea4b6Y1ij0HWCbSJ8jPMiVFLyxUVFUZXDDiken1qKpatnZXR0N0mVlgebq/SOUQeLPrwDLeO0oMDOrt7z/Ky2tS06ZLGjfebaTbBaP7xhucMgjd9fLSh4UZ3LdevcqTk3f37bukV8pzHRMGt2svMfcNWMbbZ4SfaZZlSooeMi5x7/GoyioZ3j7XhDw7Z3BYZoFhyL/rUOHfm/RaaCuWmSrTNiQ0KSDwKR+f3ObN33Bz+6+z83EkO2Ok2+/m3beqB+7bT2lpX2RnL09Nk8DUEImxrOEs2m2JZUqKlndIp9Knh2XOwggLy5xFZcwEj8yUBY7JsR/LeNufFBDYx89vlJfX1CZN3nd13Wd03yoQ+pGPbnj07Y6PD6NQMHFxlampmuTkR6aGiGT0TUosw4t237jBs2j31MlSW7S7BpYZurgiUhGbJe+YI++Yo4jNUioiefs9ZDpAAQezzPSOYluGJAUEjvLyGu/h8XZjt81OTqeQ7KzRfbv1AHCcdwtV7u5V/v5M+/ZMly4/pKZ+kZ3NnRpC0RHGTSBvTqXEsufy/W/fNrto94wCSS3abQXLYO64YBSod5aZ0i0yjEoKCMxp0WKUl9d0d/c1Li41Tg0xzH1r0QK7b3+npZ1PTbs/NaRDrGFqCEWzf7fgeMBJhmW901qaemRk+Bsnnp0knUW7gWWOW0XJFAS1zREgy3hvoQ5TQyqbNq0OCDC8W+je/dteKaeM7tuMLl0fTg154LvZm26SYZn6QM2/4am45hveTiKLdgPLgGWOUKC2U0MMgapMZpgaYvxh1o2UlPOpafenhnSItevUEGmwLCkxxJof8DAMGj9voDJcCot2A8scYcm8zksdMsXil1l5axamhtzl+93C/y+mV9m0qT4wEM/sLU9OPtjH2qkhscrwbpFR1vh00mDZ6pW1WLRb3lkKi3YDy4BlAlKATA0Z4+lp7dSQRo0MU0OMP8z6KS3tq4wMnqkhFP1MfMffMzIKWkdH1jT6KQGWtYmiLAz5c4bMGAalPztGAot2A8sEZMk1ujMS88tqvF98Au/UkEsWp4bc8/BgQkKw+3ahZ8+dmVnF/fvrY2KqZbJP5fIYucKCgyYBlo3P87cywMRce31FuiJO9It2A8uAZWJSANMNTw0Z5u1tYWoI94dZjRrp3dzwqu+/eniMjm5jDmcSYNm8OT61YtnqHZ3kvUS/aDewTEyW3DD9MmvcN9OpIWqTr4ZgkDHGb4f8hdBUf3/ekq1nmVarDafl+BPH4bRcq9WSb74XH1CTtKa0NJyW4y+5l6iLcZpddX5unl6vH583jp2pKS0ln/AcnzcOfxoeX6vVass0GtNyyOXr1/Es2j1lCpLJDF9WPXjw4Q8TsV925IyCzra0aDcpWcgJYBmwTEwK1MqWEoJbkq+GFPv44Em81QjdRuhrhD5ycurj52daoJUsIyvvcLBFEIZJR9a1wJ9K5wVQ8QE1Xu+CHNWUlnJopSosxDmmh0xv4chh7qLdhw4h/FHoigoD0TheW8U1Xzq7wNyi3ablCzMHWCYmSwa/rM5W9GZMzM9OTqUI7UXoPSfn55s16/14fhn55DrxnkgCQ6dIpcIOF8nHCQI7ci9arXZ83jjCvvzcPJIm52CWlaiLdTodQR45ykmUl3MX7WaP95uy7M5dZzq7wNyi3ZzCBbsLLAOWiUmBOhtS6zAqp0WL8R4eqf4Blgux0i8jhZhyR0nRer2eYRgCnfzcPN7oEnttmH0kRC0+oDY9WVVYiFFIyiQNME1otfyLdjMMqqhAU6Zw/TK9HhlYVtOi3aYVCSoHWCYmSwa/zAHGUyuWma4cSDCkKS3l9cvICfheNKWl2FMrPqDGUDPHMrwaTpFKVSPOTp3kX7SbYXgCTIZB16570dmi/4oXsAxYJiYFhMYyPAxP1g/E/CKQItCxMFiGPTgSh+KXCRhq7JvFMSZGJymWfQI7vXkT/6LdMhkqL+cO/DMMOvUVZWCZxUW72eULMw0sE5Mlg1/mACuy3i8jDpRWq8W0Yo+FkdeX2DsjywayScR+O6mkaOKjabVa4r4VH1Dj1ReJ11bjkNlbb3hzRvfNeWR4HG3d7ng629Ki3Q6Q/fGrAJYBy8SkwOP3+BpLsJJleKlTDCYSSxJO4Ry8zjQbPQR/uBkEXniXXaZWq8XOGoYaeY+JkccejDO9o0nP+3FYRiZk4OVuOUffXJNCm1+027R8YeYAy8RkyeCXOcCKrGQZ9sVwhEhohXfxQvfhtJw9R4xEkcThst+9dI4PrariiSXZbzNJWq9HQ14ZZWHRbvu107YlA8uAZWJSwLa9n7c0K1nGe61wMvftrfmDPxhnpeWBdHaBBBbtBpaJyZLBL3MALKTBsgE5QeaWUiMeGV60e+YSiSzaDSwDlolJAWCZ9QqcP2fVot2ROTOksWg3sExMlgx+mfWWXOczpeGXKSl69MgAy6Nm1Xr0xmrDqL80Fu0GlgHLxKRAnQll/YWSYZmSopcu87Th8nIAAB3SSURBVDWHM7xoN50tnUW7gWVismTwy6xHUp3PlBLLlBT97jJFZSX/ot2ROZJatBtYBiwTkwJ1JhT7wth2T/bq+Xp0ZAI7k6QlxjLeRbtzpbhoN7BMTJYMfhkhTq0SUcp2neJHZ6Srnhr0yZgxTFYWEx/PeHtd5C1EeixrIIt2A8uAZWJSgJc+vJntY7KTEmf167tlxIiKgQOZxEQmMpLx8iIfZDQkGrsuM71Wkiwz3KbUF+0GlonJksEvM0UPycHOV1rKoqcGfTJ27J2+fQ3OV3Aw4+z8CL8QYhD6AaGzCB2QyVaSy0lCsiyradEWooBIE8AyYJmYFOCYGdv5GjyYSU5moqMZX19TeN1CqAKhUwjtcHZe2LTJNF+vYcEBPTml4V1gGa8sws8ElonJksEvi5BHdYofzXG+QkMZV1dTft13vhBa59b4n57NcwNaZFMt29dok8CyGiUS5gnAMmCZ0BVoF52a2PWl7Kz1w4eV2cr5smCNwDIL4gj5ELBM6JbM7j0NxC+LkEd1jB2alrJo4IAjo0ffwiNfZpwvHR75qq3zxVaVkwaWcQQRyy6wDFgmCAWsdr7+QugqQp8itKvGka+6GSGwrG661ftVwDJBWLKV/UBiflltnK/zCJUgtLFWI19Wqso5DVjGEUQsu8AyYJnjFIhpldit8wuZvVcNHfKlxZEvuztfFuwTWGZBHCEfApY5zpIfvx+I0S/rGDs0NfmtJ584MHLkzzWNfDnO+bLwLIBlFsQR8iFgGbDMxgqwna9hw5jUVKZtW945X3cRuobQGYR2OTkttjzny5EmBCxzpNo2rAtYZmNLtuGzMS1KsH4Zcb5Gj/6pb1+mUyeGppkmTUznfP2CUBlChxHa1Nj19eYe4/19+4cFx5veaT3mAMvqUfzHqRpYBiyriwLRkQldEsbhX2sT58vf3xRebOersIn7K96eo4IDeirCwh+n19r1WmCZXeW1X+HAsrpYsv2eh+WS69cvw5/Kyen/8ahR34vd+bKgM7DMgjhCPgQsA5aZVUDCzpcFmwSWWRBHyIeAZWYtWYCPzQF+mdXO1+8IXULoOEJbBTvyVbcnCCyrm271fhWwrKGzjP2pHIsjX1XGT+WcQWivk9NyUYx81c26gGV1063erwKWNUSWsT+VY3HkS7LOlwXDA5ZZEEfIh4BlUmNZlLJdUuIsTp9jO19Dh97Fc774Xjs2FOeLow97F1jGVkNEaWCZpFjWPiZ71Kjvu3T+zdf7Nbbz9cQTTNeujFLJeHiYTpvA3yk0jHy5uCzwaPa8AOd8OdKigGWOVNuGdQHLpMOyLgnjRo++FR1toJWzs7Zr11sdOhg+Em38KjTnv+Qj0Wvc3WZ5e44K8k+Rh7ayYccSb1HAMpE+O2CZRFiWlrJo5EiGL2zEFHv4kWjsfPn5PBkS1FmkvdauzQaW2VVe+xUOLJMCy9rHZPdK/tLdXf+oC/bLgxU6wPmqxVMGltkPN3YtGVhWi15u1ydhTeEW5pcF+Y9yddlj/LV2FSaaTKYG58saVTnnAMs4gohlF1gmEZYZ1nMNbRXol+HZPNel0Wrjl1cPwxBYHewQWFYH0YRwCbBMOixj9yfMNWuWHWJfBWklRQPL6qUbhNN0fIewea/7fHraDW/LVZ7du4SG09ZaKLDMWqXq5QFzKrUQY3LOhN06KwAsq7N0db4wnKZHDgu8ccOJYRB7u3NHNnWyX4TcKiMFllklU50fkm0vBJbZVk/e0oBlvLLYNfO5fP/bt2VsirHTMwrk1uAMWAYsE5MCdrUoXDiwzAEis6vondbS1CNjs4xh0LOT4pU1BZvAMjFZMvhlbBuwUxpYZidhzRWrPtCEQy7T3YprvuHtepgrAecDy4BlYlLAcm+2yVFgmU1ktLKQpMQQvf6RMTJTkOGc8fMGKMNbWygWWCYmSwa/zEJXttUhYJmtlLSmnNUrm1vJMvWpSHnnQRbKBJYBy8SkgIWubKtDwDJbKVljOW2iKAtD/qYOWvqzY5S00lyxwDIxWTL4Zeb6sQ3zgWU2FNNyUePz/K10yjDXXl+RrojLMlcmsAxYJiYFzPVjG+YDy2wopuWi5r7mUyuWrd7ZSd5rgrkygWVismTwy8z1YxvmA8tsKKblotav86gVy46cUdDZBUpKzlsssAxYJiYFeDuxbTMlzzJNaWk4Lc/PzcMJ26pXq9KOHHbnDIpVVCAnJ4QQmjwZmWKu4pqvgWURHXhrAZaJyZLBL+PtxLbNtJJlmtLS/Nw8JUVrtdpwWl58QF2m0YTT910GnKmk6OIDanwaOZPdWk1pKfPg3/i8cXq9nmGYEnVxfm6eXq8vUReTAvV6PUnjosbnjSNFkcaQHJLALWFfriktLdNoxueNw4kyjeZBEwxVl6iLyS4nsbyoqEilIq3ipWHxAXWRSkWaSnQg7WEnystdOCxbufI+whITUXk5d67GnbvOdHaBok03diEkDSwDlolJAdJx7ZewnmXYuyEIK1Kp2Gli89j94cWNprS0SKUi95Kfm4eNHycYhiHAYsMIn6/VatkYItwhl5DTOF4YrrRMo8EbRidpA06wkcTGMaYzL++WFxVhphepVEUqFeYyaRVRg12RVuvMYRnZdXJC1dVclun1SJ5doOiQwS6EpIFlYrJk8MtIx7VfwhqW5efmESs1TWCvpPiA2tTmdTod8VmUFG2ZZWw4mrJMSdGqwkJO7Tqdjvh3nEN4d3lREQYudv2WFxXVlmVEeYxdtudF3MwSdTFpvKqwkCCeXIsTp066EXjhBIkxTUHGMOjadS86u0DesT+nHLwLLAOWiUkB3k5s20xrWEZq5PW2ig+ocahIsGVuZMoyy8JpuVarxX4Wm2UYYbxOGcMwOBIk+NBqteygDwNUp9MR0rGBS64y55dhb4v4X7iF5Dax11akUrHLxxVxII4F3LypmemgGIaakxO6fJnrl536ijKwrEMm0Z+dAJaJyZLBL2P3XTula8UyTiRFLDY/N69EXcyLG7blE0eGYRgcCbJjTHYMy2aZkqLzc/MwdzgNIKEc8Ya0Wi0GK8Mw+BIS8+J87JfhwDCclnMKJMgjlxN3jH0IMxQzjoTGmNTs++U8srfe8DbHspUr0aFDXJat3xNvYFmbRE45eBdYBiwTkwK8ndi2mdazDI/3E3xwxsVJPnaFyBsAdmtr9MtIHIphR66tkWX4TAymEnUxgSwuEHMWoxaHpYRl+EJzfpnpGwx2jIm5ubyoyBSI7AaQu5j0vB+HZVOmWBr7f3NNCp09WxEZR0pgJ4BlYrJk8MvYfddOaStZlp+bh+0Tj4uR4SHcKuyXYZcEvy7EYRfHSbGGZRgfmGXE2yIsY6NEVVhIAIqxQmJAHPbicFVTWor5hR00XIKqsJAdimpKS8k7BM7YPw57TWmFnT4CQdIwdqs4j6xzfGhV1SPO18qVSCYzzMlYsYI7J0OvR0NeGRWWWaCg+H/GBCwDlolJAY4x2GPXSpbp9Xpi7Rgc7DeSOHjEjg87nxMqclhG3CjyQhPfICYRe5QKs8w0hiXzOdhtwwDFg/14KgYJD4nLxmYZZ34Jm2XkjQGBMmEWaSpmIj6TRKbmntS+vTV/8AePoJWWB9LZBVTqizBXVgoWC36ZOZOwIl8eTivCaaWSVsa2G5Tcc150VBLvVdawDL+nIyPcxN9hGEan0+F4jRgzAQdOEArw1m45k7xDwMP/xO0ideGJEbgQzFPe4I7UQvwvwjJNaSlx7vBpbJaxx9o490XGB8mLVOypqQoL9Xo9TpN6SWJAThDvK0vO+029Hs1ckkVlFtAJT5BrOQnwy8TEOGAZp/ta3JUrqfvwimmd1K3zi73T1wwccHr4cKZ3byYujvH2uqSkFKYlWMMy06sgp84KnD/nyiGX6a72l2aROTPCes9UKMx+wgxYBiwTkwIWDeYhvKLCYzvGjkjtteTJJ46OGXMzO5vp2JGhKKZZMwYh/YOt2q3xItMCgWWmmtg1Z/TIAM6oGYdl1Xr0xmrDqD/VbaSFlgDLxGTJ4JeZdGVu5Ni3z9ZhwyqefJLp2ZNp3Zrx8WGMKx9jflUjVI3Q38YVkT+VoV3OTg/n3JOSgWVECoclli7zNYez6mq090QrOrvAMOpv5g0mbiewDFgmJgWM4778kWNaGtO+PRMUxDg5EecLw6sKoR8ROitDB2Wyja4u85o3e87PNyckkP9nfcAyhyGMXdG7yxSVlaia9b1svR5VVcs+3N0xMmdmWKbZ6f6kEGCZmCy5Qfpl/JHjqFG3SOTo5kbgpTd6XtUI/YLQJRk6LpPtbNTonWZNpvh4jQgO6KEI43+dT+wB1vplS+Hg9JBxiXuORVVWyfD2uSYk9/XBYZkGj4zuOrTGxgDLgGUCVMBs5Nitm4XI8SpCn8pku2Wyle5uM709nw70T6datqvRBjgngF/GEcSRu/IO6VT69LDMWRhhYZmzqIyZ5n6AyWkYsEyAlmy2SdL1yx46X+3aZJB3jkOGMCRyNBn2qjIOe52VoQMy2cbGrq95eozz880JDerE6eK13QWW1VYxG5+viFTEZsk75sg75ihis5SKSCvLB5aZBYeVCjryNAmx7CG8osJjO8U/g9854sgxLs7wztFM5HjBGDluxZGjr/cQKyNH6x8TsMx6rQR1JrAMWOYwBbiRY/9+u4YNq+jfn+nWjYmIYDw9Td853kLoqswQOX7kJHufRI50SLT9rAhYZj9t7VoysMxhlmyDisTmlz10vkjkOPips0OGML163X/naCZyPGOMHNfhyNG/Rd/HjxyttyJgmfVaCepMYJkNEOOwJyp4lj2EFztyHD7c8M4xLo4JCWFcXDjvHKsQ0iF0QYaOymRbXRq92azJFBw5OkxVTkXAMo4gYtkFlgHLHlMBs5Fj584kcnw41d44Z+IWQhXGyHGnk5PK3W2mj9eIQP90u0aO1hsksMx6rQR1JrDsMS3ZoZcLwy976HyxI8dBgwyRY0wM4+dnOux11/jO8YwM7ZPJ1rk1LvD0GOffom9YMP+HqOrXQoBl9at/nWsHljkURnV+TvjCemLZQ3hxIkf8I+1HI0cy1V6H0Hlj5LjJpdGbHk0n1W/kaL3ywDLrtRLUmcAyYBmvAg8jx46xI5J7zuvfb9eIEd9nZzOdOzMKBfdH2sbI8Xdj5PiJTLbT2WkpjhyD/HvJQ6ME1eNrbAywrEaJhHkCsIzXkgWaaU+/7KHz1a5NRveur/ROXzP4qbODBhl+pG0xcvzUGDl+4Na4wKv5M4KNHK03P2CZ9VoJ6kzpsCycpuM7hM2f6/PpaTe8LVd5duscGk4LFEx16Ac2ZdlDeJHIceCA00OG/IEjR/M/0j5v/JH2JleXeR5NJ7XwGRgc0KMO9yLYS4Blgn00lhsmEZaF0/TIYYE3bzpxvnx0545syiQ/yeDMapbdXz3b5Nk/Ejkm9XgYOXbsaCFyvCRDxsjR+b0m7i/7eI0QY+RoIoXZv3DAMuu1EtSZEmHZc/n+d+7IOCAju6/MkksDZ9awLEIRndh1+oMvpj50vtiRo5Wf95LJ7keOAX6ZwnznaA9bApbZQ1UHlCkFlvVOa2nqkRGQ4cQzL8Q7QE17V1ETy+QxrZOGDS3rlPBbC+9XIxQJ+HeOOHJMSzPMVjUfOT7yea8WPgPNfd7L3vdY7+UDy+r9EdStAVJgWbG65qVcKq75KtuKfljHIssUCXEjRo261bq1YXpXo0bfd+78C/4wtLs7Z6p9NUK/k897OT+IHIMDeojunWPdOr3lq4BllvUR7FHRsywpMYTjgpnbzZ87UKk0u/CBYJ8Qu2EWWJbcc+7w4WSequls1b8RMn7eC+0in/cK8Musw+e92O2RZBpYJtLHKnqWrVnV3By8OPnqU5F0p0EifU642eZY1r5NVlLPz93dq42rcmCQMQhdN34Y2vB5rxo/DC1qWWzbeGCZbfV0WGniZlmbKMrCkD+HZQyD0p4Zq6Rr/kqyw9SvbUXmWKak6CD/kY1dd8vQdwhVGmeu6mWyXdZ/GLq2LZHw+cAykT5ccbNsfJ6/KbAs5Mx5P10em2X5UeFlXJUUrdPplBRdpFLhBL6KpPEC0ZxMvFum0ZRpNOxa8nPzyMKo+FCZRoOXs8aH2Etbsy/kpC2wTEnR8tCoQP90T498l0YbZDLDZ3Pkoa04JcBujQoAy2qUSJgniJtl8+b4WCCX6aFVOzrRyRMsPwmCIYKt/Nw8ki5SqUrUxbgEQqX83Dx2mXg5a5KDacU5h7CMYRgrQaakaMssIzUqKZoOiYbhMLYg1qeBZdZrJagzxc2yDes9TIFlIefIGQWVNdvyAyAOlGkC86hEXVyiLuYc5cCOoEpJ0ew0qRpn6nQ6QkZyyELCepZZKAQOWVYAWGZZH8EeFTfLjh5xt0Au00MV13wNLItoX+PzIJEm+0yMMDZ9iBNHTtPpdPm5eezIlGEYcpQkyjQahmFMLycn8CaAZbyy2DYTWGZbPR1WmrhZVlHhYgoshkGHDiGEkOmhO3edqazZijb8i7yyRcdxItv5wkfzc/NK1MWYROyjGFjsaJRhGOzHmWMZJxRl124uDSwzp4wN84FlNhTTkUWJm2U6nbMpsBgGde9u2EwP6fXIwLIOGZYlLlKp2OP3+bl5xIHCLCOXl2k07IEwTuyJPTjsqZFLcALHmOxaOCfw7gLLeGWxbSawzLZ6Oqw0cbPsk1NupsB68UVUUcHPsmvXvais2TUuHYo9KUw0tqulpGg2yzCJ2O4VG1vkwiKViu2akTcGeMi/VkNmwDIHGAawzAEi26MKcbNsy+ZmHJYdOoRefNHgkfH6Zae+ogws65BpQUqdTkdeLOJYkjNAhse5OO8fcURJ3gDg8klRGGc4JsWH2C8ErB84A5ZZeHC2OgQss5WSDi5H3Cx7+01vDsvIMBkvy9btjjewrE2iOZXxlAsyHIahRiLHIpWqSKViTxZjD5mZK9OG+cAyG4pprihgmTllBJ4vbpZNet6PzTI85G8Y9n/wj32UYdAbq1MM42WRsQJ/KuaaBywzp4wN84FlNhTTkUWJm2Wd40Orq3nG+HljTL0eDZ7+dFhmgYJSOFJiG9YFLLOhmOaKApaZU0bg+eJmmZKi9+/j/+CPaYxZWh5IZc2mUl8U+COx0DxzLBvYrv3kzl1ileEWroVDVioALLNSKKGdJnqWDcgJ0uv5XTN2gKnXoxmLs8MyC+iEJ4T2DKxvjzmWbenTRx8Tw7RvfykltSgtbWC7micDW19pQzsTWCbSJy56likpuvS8KxtbvGntL80i+s8M6z1ToRDxz63Nsey1bt2qZLL7i4PLZJUtW1Z2734yJWV298SM6DYi7Zr11WxgWX0p/5j1SoFlo0cGmBs1w1yr1qMFqwyj/lS3kY+pV/1ebo5lHULD5nh6ahD6zfgBs/tQQ6jSza06MvLXxMQtqamTO3WGINSaxwcss0YlAZ4jBZYpKXpZoa85nFVXo70nWlFZsw2j/qJ9g4m7jjmW4aPRoWF9Wvj9s0nTA07O5QjdYXGNQeietzfTvv3VtPSitLThsXEC7IsCaRKwTCAPorbNkAjLlBS9sFBRVYWqWWNnej2qqpat3dXREF1mFtQ43b+22jn+fMssY7cnIbjlYG+fJW5up2QyLQtqBpfNGIRWde5yMiVlXo+eEISydVNSNLCMI4hYdqXDMiVFDxmXuPd4VGWVDG+fa0KenTM4LLPAMOTfdahYHomFdlrPMnYhiYFBec09N7q4nOMNQpXKXxMTP8rIgCAUiwYsY3ceEaUlxTLDt1U7pFPp08MyZ2GEhWXOojJmSsAjw12qbiwj3REHoQVNm+115glCK5s3Z9q3/7ZXSlFa2tg4KSzBR268VglgWa3kEs7JUmOZQVlFpCI2S94xR94xRxGbpVRECkfux2zJY7KMXXtCcMsBPr7vurmdkMn+ZxKE3gsOrurc5bO09AYYhALL2P1ERGkpsoyiRfQAatVUG7KMXW9iYNCznl78QWjjxtVK5c0ePXanpU3u1LlLZBT7QkmmgWUifazAMjGBz04sI303OjSst5//9Gb3g9A/Wf6a4U2ohwfTvv21pCRpB6HAMtIfxJUAlgHL+BWIbRlCgtDvWFDDb0LvBQQwXe4HoX1j2oqr01tuLbDMsj6CPQos47dkYT4we/tl5u46MTBotJf3h3xvQqtcXEgQOr1LVwkEocAyc91A4PnAMmBZLRSIDKNwELrL2fkiQuaC0NUZGWPj4qPlovweCbBM4Mwy1zxgWS0s2ZyIDsuvL7+M9wZxEPq2u/sRmYwThBoG14xB6Be9ev0rKVlcQSiwjPdxCz8TWAYss4ECiYFBw729P3B1PYfQz48OrlW5uFQqFLe6dVNnZIgiCAWWCR9bvC0EltnAknmVtUemoPwy3hvEQei0Zh6mQagevwmNifmxVy8hB6HAMt4nK/xMYBmwzF4KxLYMyfFtMf9BEFrJ8tcMQai/P5OQcD41TWhBKLBM+NjibSGwzF6WzCv3Y2YK3y8zd4Ndg4JxEPqlSRBa6eJSRVF/JybiIDSpVWtzhTgmH1jmGJ1tXguwDFjmUAUiw6hU/4BpzTx2GN+E3mQ5a4YgtEkTxhiEfpiaNr5jQr28CQWW2ZwyjikQWOZQS37Mhypev4z3xtuGhOIg9LCT0xWELAShjvzqN7CM92EJPxNYBiwThAI4CH2/cWMLQejB1LTZ3RPtHYQCy4SPLd4WAssEYcm8z8Y0U2J+mekNKikaB6GTPTy2Nmp0ESHTIFQfHa3t0eO/6Rl2CkKBZbzPRfiZwDJgmXAVwEHo602aFPMGoS1aMAkJF9Mz/pWUbMMgFFgmfGzxthBYJlxLNn1gDcEvM71rnNM1KHiwt0+hMQjlfPW72tm5Mizs78TE4+npjx+EAsvMPQKB5wPLgGViUgCbU6p/wPPNm29t1EhjJgj9JSkJB6Hspae29u3bJ6LmD3MCywTOLHPNA5aJyZIbsl/G24PbhoT2aeFHglDO0lOV3t5MQsKllNTFvVLGxndknnqqslmz/PAaVncHlvFKLfxMYBmwTEwKWLAovPRUYePGn5ksPVVlDEIZimIQ+huh+UqlhXKAZRbEEfIhYJmYLBn8MittCQehmx4NQhmE8HYXoQ9pubmigGXmlBF4PrAMWCYmBWprTmT9489cXTHI8KLulQgtbt6ctzRgGa8sws8ElonJksEvq5tFdVAoizt1whS7iVA5QqcR+hihHN8WpgUCy0w1EUUOsAxYJiYF6mxUAwKDFjVpUuTkNMvN7Wkvr95+/rEtQ3hLA5bxyiL8TGCZmCwZ/LLHsajo0DBrLgeWWaOSAM8BlgHLxKSAA0wIWOYAke1RBbBMTJYMfpk9bIBTJrCMI4hYdoFlwDIxKeAAuwKWOUBke1QBLBOTJYNfZg8b4JQJLOMIIpZdYBmwTEwKOMCugGUOENkeVQDLxGTJ4JfZwwY4ZQLLOIKIZRdYBiwTkwIOsCtgmQNEtkcVwDIxWTL4ZfawAU6ZwDKOIGLZBZYBy8SkgAPsCljmAJHtUQWwTEyWDH6ZPWyAUyawjCOIWHaBZcAyMSngALsCljlAZHtUASwTkyWDX2YPG+CUCSzjCCKWXWAZsExMCjjAroBlDhDZHlUAy8RkyeCX2cMGOGUCyziCiGUXWAYsE5MCDrArYJkDRLZHFcAyMVky+GX2sAFOmcAyjiBi2QWWAcvEpIAD7ApY5gCR7VEFsExMlgx+mT1sgFMmsIwjiFh2gWXAMjEp4AC7ApY5QGR7VAEsE5Mlg19mDxvglAks4wgill1gGbBMTAo4wK6AZQ4Q2R5VAMvEZMngl9nDBjhlAss4gohlF1gGLBOTAg6wK2CZA0S2RxXAMjFZMvhl9rABTpnAMo4gYtkFlgHLxKSAA+wKWOYAke1RBbBMTJYMfpk9bIBTJrCMI4hYdoFlwDIxKeAAuwKWOUBke1QBLBOTJY9P6MQgxCBU4OZmj94AZSopukvCOKPGjLvbLBBERAoAy8TEMiVFdw8Knu3mlte8uYg6meiaGhLU1d2twLN5ruha3pAbDCwTGcuUFB1u3Bpyr3XIvcuVlNwhFYmvBwpTFmAZ9CRQABSQggLAMik8RWH+nYRWgQKOVABYBiwDBUABKSgALJPCU3TkXz+oCxQQpgLAMmAZKAAKSEEBYJkUnqIw/05Cq0ABRyoALAOWgQKggBQU+D8sNPZExI62cwAAAABJRU5ErkJggg==)

传输层有哪些协议，各有什么特点？

TCP（Transmission Control Protocol）

传输控制协议

可靠的、面向连接的协议

传输效率低

UDP（User Datagram Protocol）

用户数据报协议

不可靠的、无连接的服务

传输效率高

 

交换机工作在数据链路层，通过MAC地址学习和转发数据帧来实现局域网内的设备通信，而路由器工作在网络层，依据路由表进行路由选择以实现不同网络之间的数据包传输。

 

 

 

 

**常用配置文件信息及操作**

\------------------------------

head -1  /etc/passwd   #存储用户账户信息

root:x:0:0:root:/root:/bin/bash                    

用户名:密码占位符:UID:基本组GID:用户描述信息:家目录:解释器

\----------------------------

/etc/shadow       #存储加密后的用户密码以及密码过期信息

字段1：用户账户的名称；字段2：加密后的密码；字段3：上次修改密码的时间（1970-01-01至上次修改密码时间之间的秒数）；字段4：密码最短有效天数；字段5：密码最长有效期天数；字段6：密码过期前的警告天数；字段7：密码过期后多少天禁用此用户；字段8：账户失效时间；字段9：保留（未使用）

\------------------------------

grep  tarena  /etc/group   #查看组信息

tarena:x:1607:

组名字:密码占位符:GID:组成员列表 

\-----------------------------------

grep   tarena  /etc/gshadow    

tarena:!:nb:     

组名:密码加密字符串:组的管理员列表:组成员列表

\-----------------------

开机自动挂载/etc/fstab

设备路径（/设备UUID）  挂载点   文件系统类型    参数   备份标记   检测顺序

\------------------------------------------------

\# vim   /etc/default/grub      #修改内核引导文件，

GRUB_CMDLINE_LINUX="……quiet  net.ifnames=0  biosdevname=0"       

\# grub2-mkconfig  -o  /boot/grub2/grub.cfg  #重新生成网卡命名的规则

\-------------------------------------------------------------

vim /etc/sysconfig/network-scripts/ifcfg-eth0   #配置网卡ip信息

\--------------------------------------------------

vim /etc/hostname     #永久配置主机文件

\--------------------------------------------------

vim /etc/selinux/config  #配置selinux策略

\---------------------------------------------------

vim /etc/httpd/conf/httpd.conf    #

\------------------------------------------------

rm  -rf   /home/student/.ssh/known_hosts #删除记录远程管理机器的配置文件

\-----------------------------------------------

服务启动配置文件目录：/lib/systemd/system/

\---------------------------------------

vim   /etc/exports

文件夹路径      客户机地址(权限)  

/abc       *(ro)      #允许所有客户端进行只读访问

\------------------------------------

vim   /root/.bashrc  #给root定义永久别名、umask码

vim   /etc/bashrc	   #给所有用户定义的别名、umask码

vim   /home/pyf/.bashcr #给指定用户定义别名、umask 码

\----------------------------------

vim /etc/login.defs  #定义用户账户和密码创建时的各种默认设置

\----------------------

vim /etc/skel    #存储新用户账户的默认配置文件和目录，创建用户时从这里复制文件

\--------

 

 

 

**VIM的两种常用模式操作**

命令模式常用操作：

​                ● 1G 或 gg ，跳转到文件的首行

​                ● G ，跳转到文件的末尾行

​                ● yy、#yy ，复制光标处的一行、#行

​                ● p、P ，粘贴到光标处之后、之前

​                ● x 或 Delete键 ，删除光标处的单个字符

​                ● dd、#dd ，删除光标处的一行、#行

​                ● d^、d$ ，从光标处之前删除至行首/行尾

​                ● /word 向后查找字符串“word”，再按n/N跳至后/前一个结果

​                ● u ，撤销最近的一次操作

​                ● U ，撤销对当前行的所有修改

​                ● Ctrl + r 取消前一次撤销操作

​                ● ZZ 保存修改并退出

​                ● C 从光标处删除至行尾，并进入插入模式

末行模式常用操作：

​                ● :s/old/new ，替换当前行第一个“old”

​                ● :s/old/new/g ，替换当前行所有的“old”

​                ● :n,m s/old/new/g ，替换第n-m行所有的“old”

​                ● :% s/old/new/g ，替换文件内所有的“old”

​                ● :w /root/newfile ，另存为其它文件

​                ● :r /etc/filesystems ，读入其他文件内容

​                ● :set nu|nonu ，显示/不显示行号

​                ● :set ai|noai ，启用/关闭自动缩进

 

**快捷键**

​            \1.     Ctrl **+** c：结束正在运行的命令

​            \2.     Esc **+** **.**或Alt **+** **.**：粘贴上一个命令的参数

​            \3.     **[**root@localhost **~]**# ls  **-**l    Alt **+** **.** 

​            \4.     **[**root@localhost **~]**# cat     Alt **+** **.** 

​            \5.     **[**root@localhost **~]**# cat   **-**n    Alt **+** **.**

​            \6.     Ctrl **+** l：清空整个屏幕

​            \7.     Ctrl **+** u：从光标处清空至行首

​            \8.     Ctrl **+** k：从光标处清空至行尾

​            \9.     Home键：从光标处快速到行首

​            \10.     end键：从光标处快速到行尾

​            \11.     Ctrl **+** w：往回删除一个单词（以空格界定）

 

 

 

**OpenEuler系统的Finalshell安装**

​            1、     [www.hostbuf.com/downloads/;](https://www.hostbuf.com/downloads/finalshell_install_linux.sh;)下载对应安装包

​            2、     安装alien

http://ftp.de.debian.org/debian/pool/main/a/alien/  ; alien安装网址，选择.tar.xz后缀，解压到桌面; cd 进入到alien文件中，执行命令

perl [Makefile.PL](https://makefile.pl/)

make

sudo make install

which alien  //查看是否安装成功

​            3、     安装rpmbuild  ;

错误信息 sh: line 1: rpmbuild: command not found 表明系统中没有安装 rpmbuild 工具。执行下面命令安装

sudo dnf install rpm-build

​            4、     使用 alien 转换 .deb 文件：

sudo alien --to-rpm finalshell_linux_x64.deb

​            5、     安装生成的 .rpm 文件

sudo dnf install finalshell-4.5.12-2.x86_64.rpm

6、手动查找安装位置

sudo find / -name finalshell 2>/dev/null

![img](data:image/png;base64,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)

​            \1.     cd到 /usr/share/finalshell/bin

运行finalshell 文件

 

**OpenEuler系统的实现U盘与Windos同时支持**

u盘类型为 ntfs;

OpenEuler解析 ntfs类型需要 安装 软件ntfs -3g 

sudo yum -y install ntfs-3g