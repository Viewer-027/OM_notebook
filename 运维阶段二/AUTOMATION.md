# AUTOMATION

## Ansible简介

IT运维是目前IT服务中的重要组成部分

运维方式：

- 传统运维
  - 手动操作：很多任务需要手动完成，包括系统部署、配置和故障排查等
  - 资源密集型：依赖大量人力投入，容易受到人为因素的影响
  - 反应式：更多地是根据问题出现后再进行处理和修复，缺乏主动性和预防性
  - 时间成本高：由于手动操作和反应式处理，时间成本相对较高，而且容易出现人为失误
- 自动化运维
  - 自动化工具：广泛使用自动化工具来完成系统管理、部署、监控和故障处理等任务
  - 自动化流程：通过编写脚本或使用自动化平台来实现工作流程的自动化，减少人工干预
  - 预防性维护：更注重预防性维护，通过自动化监控和报警系统，能够在问题出现前进行预警和处理
  - 时间成本低：自动化运维能够大大降低时间成本，提高效率，减少人为错误

总的来说，掌握自动化运维有助于提高工作效率，简化运维工作



#### 自动化运维工具

- Puppet：
  - Puppet 是一个基于客户端/服务器模型的自动化配置管理工具
  - 使用Ruby语言开发
  - 基于SSL，远程执行命令能力较弱能
- Chef：
  - Chef 使用 Ruby 语言编写配置文件
  - 能够实现基础设施的自动化部署、配置和管理
- SaltStack：
  - SaltStack 是一个快速、灵活的系统管理和自动化软件
  - 使用 Python 编开发
  - 支持远程执行命令、配置管理等功能
- Ansible
  - 目前应用最为广泛的自动化运维工具
  - 2012年首次发布，2015年被RedHat收购
  - 基于 Python的paramiko 开发，分布式
  - 基于SSH进行远程管理，被控节点无需额外安装任何客户端软件，轻量级
  - 配置语法使用 YMAL 及 Jinja2 模板语言
  - 使用模块管理被控节点，具备更强的远程命令执行操作能力

#### Ansible使用环境

- 控制节点
  - 安装Ansible软件
  - Python环境支持：Python>=2.6
  - 必要的模块：如PyYAML等
- 被控节点
  - 启用SSH服务
  - 允许控制节点登录，通常设置免密登录
  - Python环境支持





## Ansible部署配置

#### 配置系统源 和 自定义Ansible软件YUM源

```bash
## 自定义Ansible软件yum源

# 配置系统YUM源
[root@pubserver ~]# yum -y install vsftpd               #安装vsftpd软件
[root@pubserver ~]# vim /etc/vsftpd/vsftpd.conf         #编辑vsftpd配置启用匿名用户访问
12 anonymous_enable=YES
[root@pubserver ~]# systemctl enable --now vsftpd       #加入开机自启并立即启动
[root@pubserver ~]# ss -antlp | grep 21                 #确认21端口被监听
LISTEN 0      32                 *:21              *:*    users:(("vsftpd",pid=752,fd=3))

[root@pubserver ~]# mkdir /var/ftp/dvd                  #创建ISO文件挂载目录
[root@pubserver ~]# vim /etc/fstab                      #编辑fstab文件实现开机自动挂载
...
/dev/cdrom      /var/ftp/dvd/   iso9660 defaults 0 0
[root@pubserver ~]# mount -a                            #确认fstab配置

[root@pubserver ~]# vim /etc/yum.repos.d/local.repo     #修改local.repo文件使用本机
[local_BaseOS]
name=Rocky Linux $releasever - BaseOS
baseurl="ftp://192.168.88.240/dvd/BaseOS"       #更改
enabled=1
gpgcheck=0                                      #更改

[local_AppStream]
name=Rocky Linux $releasever - AppStream
baseurl="ftp://192.168.88.240/dvd/AppStream"    #更改
enabled=1
gpgcheck=0                                      #更改
[root@pubserver ~]# yum clean all; yum repoinfo         #确认本机网络yum源可用
...
Total packages: 8,265

# 上传Ansible相关软件到pubserver主机
[root@pubserver ~]# mkdir /var/ftp/rpms                 #创建rpm包存放目录

[root@server1 ~]# scp /linux-soft/s2/zzg/ansible_soft/* root@192.168.88.240:/var/ftp/rpms/                      #上传Ansible相关软件

[root@pubserver ~]# yum -y install createrepo_c     #安装createrepo命令包
[root@pubserver ~]# createrepo /var/ftp/rpms/           #创建软件仓库信息
[root@pubserver ~]# ls /var/ftp/rpms/
ansible-6.3.0-1.el8.noarch.rpm  
ansible-core-2.13.3-1.el8.x86_64.rpm  
cowsay-3.04-16.el8.noarch.rpm  
repodata        #确保有该目录

# 配置自定义yum源
[root@pubserver ~]# vim /etc/yum.repos.d/local.repo     #增加自定义yum源配置，在文件最后追加
...
[ansible-rpms]
name=ansible-rpms
baseurl="ftp://192.168.88.240/rpms"
enabled=1
gpgcheck=0
[root@pubserver ~]# yum clean all; yum repoinfo
...
Total packages: 8,268

# 同步repo文件到所有被控节点
[root@pubserver ~]# for i in 192.168.88.1{1..3}
 do
 scp /etc/yum.repos.d/local.repo root@$i:/etc/yum.repos.d/
 done
```

#### 被控节点基础配置

```bash
## 配置hosts文件，确保控制节点通过主机名访问到被控节点
[root@pubserver ~]# vim /etc/hosts                      #编辑hosts文件配置主机名解析
192.168.88.240  pubserver
192.168.88.11   web1
192.168.88.12   web2
192.168.88.13   db1
#测试hosts文件配置
[root@pubserver ~]# for i in pubserver web1 web2 db1    
 do
 ping -c 2 $i
 done
```

- 配置免密登录被控节点

```bash
## 配置控制节点免密登录被控节点
[root@pubserver ~]# ssh-keygen -t rsa -f /root/.ssh/id_rsa -N ''    #非交互生成秘钥对
#发送公钥到被控节点
[root@pubserver ~]# for i in web1 web2 db1              
 do
 ssh-copy-id root@$i
 done
 
#确认免密登录，结果显示web1，web2，db1
[root@pubserver ~]# for i in web1 web2 db1              
 do 
 ssh root@$i "hostname"
 done
```

#### 被控节点Ansible管理环境配置

- 配置目标

  - 使用同一套Ansible软件管理多个环境，如开发环境、测试环境、生产环境

  - 多用户使用同一个控制节点管理不同主机
  - 每个用户有属于自己的配置环境(工作目录)

- Ansible配置文件查找顺序

  - 首先检测ANSIBLE_CONFIG变量定义的配置文件

  ```bash
  ##配置ANSIBLE_CONFIG变量
  #1）
  export ANSIBLE_CONFIG=/root/ansible/ansible.cfg
  
  #2）或者永久设置
  [root@pubserver ansible]# vim ~/.bashrc
  export ANSIBLE_CONFIG=/root/ansible/ansible.cfg
  [root@pubserver ansible]# source ~/.bashrc
  
  #取消变量设置
  unset ANSIBLE_CONFIG
  ```

  - 其次检查当前目录下的./ansible.cfg文件
  - 再次检查当前用户家目录下的~/ansible.cfg文件
  - 最后检查/etc/ansible/ansible.cfg文件

- 控制节点配置

```bash
## 根据上述结果，创建自定义Ansible工作目录
# 查看默认配置文件
[root@pubserver ~]# cat /etc/ansible/ansible.cfg            #查看默认配置，通常不用该文件
[root@pubserver ~]# ansible-config init     #根据提示查看配置条目
# 自定义Ansible工作目录
[root@pubserver ~]# mkdir ansible       #自定义工作目录，名称自定义
[root@pubserver ~]# cd ansible/         #进入工作目录，以后关于ansible操作均在此目录下进行
# 配置Ansible工具
[root@pubserver ansible]# vim ansible.cfg               #编辑配置文件         
[defaults]                  #通用配置
inventory = inventory       #主机清单列表文件
host_key_checking = false   #不检查主机秘钥，=两边有无空格均可

[root@pubserver ansible]# vim inventory     #编辑主机清单列表文件
[webservers]        #定义主机组，名称自定义
web[1:2]            #[1:2]表示从1到2

[dbs]
db1

[cluster:children]  #cluster为组名，:children为固定写法，表示为cluster的子组
webservers
dbs

## 确认配置结果
# 命令操作一定在工作目录下执行
[root@pubserver ansible]# ansible all --list-hosts          #查看所有被控主机列表
  hosts (3):
    web1
    web2
    db1
[root@pubserver ansible]# ansible webservers --list-hosts   #查看webservers组主机列表
  hosts (2):
    web1
    web2
```



### Ansible使用方法

#### 使用方法介绍

- ad-hoc临时命令
  - 在命令行下使用ansible命令调用Ansible模块，实现被控节点远程管理
  - 通常用于查询信息或临时简易操作
- Playbook剧本
  - 把管理任务以特定的格式编辑在文件中，通过ansible-playbook命令远程管理被控节点
  - 通常用于复杂任务设计及远程执行

#### 使用方法样例

- ad-hoc临时命令

```bash
## 语法
# ansible [主机或组列表] -m 模块 -a "参数"
# 常用额外选项：
    # -i：指定主机清单列表文件
    # -k：使用密码登录远程主机，通常用于某个特殊被控节点未做免密登录的场景下
    
## 样例：ping模块用于测试是否可以SSH远程登录被控节点主机
[root@pubserver ansible]# ansible all -m ping
web1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "ping": "pong"
}
...
# 返回结果为绿色的SUCCESS状态信息则为成功
# 返回结果为红色的UNREACHABLE则为失败，需要检测管理节点与被控节点网络统计和是否可以免密登录
```

- Playbook剧本(暂时先了解)

```bash
## 语法
# ansible-playbook 剧本名称
## 样例：ping模块测试
[root@pubserver ansible]# vim ping.yaml
---
- name: test connection
  hosts: all
  tasks:
    - name: test ssh connection
      ping:
[root@pubserver ansible]# ansible-playbook ping.yaml 

PLAY [test connection] *******************************************************************************

TASK [Gathering Facts] *******************************************************************************
ok: [web2]
ok: [web1]
ok: [db1]

TASK [test ssh connection] ***************************************************************************
ok: [web1]
ok: [web2]
ok: [db1]

PLAY RECAP *******************************************************************************************
db1                        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
web1                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
web2                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```



## Ansible常用模块

#### 模块是什么

- Ansible在使用过程中通过模块来完成指定任务
- Ansible模块的本质是一个文件，通常是为实现具体功能的Python脚本
- Ansible已经有很多开发好的模块，可以直接调用，具备开发能力也可以自行开发
- 多数模块都支持使用参数，需要使用的时候指定参数
- 主要学习已有的常用模块的用途及常用参数

#### 模块如何查询

- 一定要掌握ansible-doc命令

```bash
## ansible-doc命令使用方法
# 列出目前全部可用Ansible模块，按空格键向下翻页，按q退出返回命令行
[root@pubserver ansible]# ansible-doc -l

# 统计已有模块数量，WARNING信息直接忽略即可
[root@pubserver ansible]# ansible-doc -l | wc -l
7214

# 查看包含yum的模块
[root@pubserver ansible]# ansible-doc -l | grep yum

# 查看指定模块的帮助文档，按空格键向下翻页，按q退出返回命令行
[root@pubserver ansible]# ansible-doc yum
```

#### Linux系统命令相关模块

- **command模块**
  - Ansible使用的默认模块，用于在被控节点执行Linux命令
  - 不支持bash特性，如管道、重定向

```bash
# 样例
[root@pubserver ansible]# ansible web1 -m command -a "hostname" #获取被控节点主机名
[root@pubserver ansible]# ansible web1 -a "ip a s"              #获取被控节点IP地址信息

[root@pubserver ansible]# ansible web1 -a "ip a s | head -2"        #加入|后报错
web1 | FAILED | rc=255 >>
Error: either "dev" is duplicate, or "head" is a garbage.non-zero return code

[root@pubserver ansible]# ansible web1 -a "ip a s > /opt/ip.txt"    #加入>后报错
web1 | FAILED | rc=255 >>
Error: either "dev" is duplicate, or "/opt/ip.txt" is a garbage.non-zero return code
```

- **shell模块**
  - 用于在被控节点执行Linux命令
  - 支持bash特性

```bash
## 样例

# 获取IP信息前两行
[root@pubserver ansible]# ansible web1 -m shell -a "ip a s | head -2"

# 将IP地址信息重定向保存到指定文件
[root@pubserver ansible]# ansible web1 -m shell -a "ip a s > /opt/ip.txt"

# 确认重定向结果
[root@pubserver ansible]# ansible web1 -m shell -a "cat /opt/ip.txt"

# 可以修改配置文件调整默认模块
[root@pubserver ansible]# vim ansible.cfg 
[defaults]
inventory = inventory
host_key_checking = false
module_name = shell         #加入此行设置默认模块
```

- **script模块**
  - 用于在被控节点执行脚本
  - 不局限于Shell脚本

```bash
# Shell脚本
[root@pubserver ansible]# vim test.sh                               #编写shell脚本，创建张三用户，设置密码
#!/bin/bash
useradd zhangsan
echo "123456" | passwd --stdin zhangsan
[root@pubserver ansible]# ansible webservers -m script -a "test.sh" #执行test.sh脚本
[root@pubserver ansible]# ansible webservers -a "id zhangsan"       #确认执行结果

# Python脚本(可借助ChatGPT帮忙生成)
[root@pubserver ansible]# vim test.py                               #编写Python脚本
#!/usr/bin/env python3
import subprocess

username = "lisi"
password = "123456"
subprocess.run(['sudo', 'useradd', '-m', username])
subprocess.run(['sudo', 'passwd', username], input=password.encode())
print(f"用户 {username} 已创建，并设置密码成功")
[root@pubserver ansible]# ansible webservers -m script -a "test.py" #执行test.py脚本
[root@pubserver ansible]# ansible webservers -a "id lisi"           #确认执行结果
```

#### Linux文件操作相关模块

- **file模块**
  - 用于在被控节点创建文件、目录、链接文件等
  - 还可以修改权限、归属
  - 幂等性 ：执行同样等操作不会有额外的影响

```bash
## 常用参数
    path：指定文件路径
    owner：设置文件所有者
    group：设置文件所属组
    state：状态。touch表示创建文件，directory表示创建目录，link表示创建软链接，absent表示删除
    mode：设置权限
    src：source的简写，源
    dest：destination的简写，目标
## 样例
# 获取file模块帮助信息
[root@pubserver ansible]# ansible-doc file
...

# 使用file模块创建文件、目录、软链接
[root@pubserver ansible]# ansible webservers -m file -a "path=/tmp/file.txt state=touch"    #touch指文件不存在则创建，存在则刷新时间戳
[root@pubserver ansible]# ansible webservers -m file -a "path=/tmp/demo state=directory"
[root@pubserver ansible]# ansible webservers -m file -a "src=/etc/hosts dest=/tmp/hosts.txt state=link"
[root@pubserver ansible]# ansible webservers -a "ls -l /tmp/"

# 使用file模块修改文件权限和归属
[root@pubserver ansible]# ansible webservers -a "ls -l /tmp/file.txt"
[root@pubserver ansible]# ansible webservers -m file -a "path=/tmp/file.txt owner=sshd group=adm mode=0777"
[root@pubserver ansible]# ansible webservers -a "ls -l /tmp/file.txt"

# 使用file模块删除被控节点指定文件、目录、软链接
[root@pubserver ansible]# ansible webservers -m file -a "path=/tmp/file.txt state=absent"
[root@pubserver ansible]# ansible webservers -m file -a "path=/tmp/demo state=absent"
[root@pubserver ansible]# ansible webservers -m file -a "path=/tmp/hosts.txt state=absent"
```

- **copy模块**
  - 用于将控制节点指定文件发送被被控节点
  - 可理解为上传操作

```bash
## 常用参数
    src：源。控制端的文件路径
    dest：目标。被控制端的文件路径
    content：内容。需要写到文件中的内容
    
## 样例
# 获取copy模块帮助信息
[root@pubserver ansible]# ansible-doc copy
...

# 使用copy模块发送控制节点指定文件到被控节点指定目录并重命名(如果dest指定到目录则文件名不变)
[root@pubserver ansible]# ansible webservers -m copy -a "src=test.sh dest=/tmp/adduser.sh"
[root@pubserver ansible]# ansible webservers -a "ls /tmp/adduser.sh"

# 使用copy模块发送指定内容到被控节点并存储在文件中
[root@pubserver ansible]# ansible webservers -m copy -a "content='Hello World' dest=/tmp/mytest.txt"
[root@pubserver ansible]# ansible webservers -a "cat /tmp/mytest.txt"
```

- **fetch模块**
  - 用于将被控节点指定文件发送到控制节点
  - 可理解为下载操作



#### Linux用户管理相关模块

- **user模块**
  - 用于实现Linux用户管理

```bash
## 常用参数
    name：待创建的用户名
    uid：用户ID
    group：设置主组
    groups：设置附加组
    home：设置家目录
    password：设置用户密码
    state：状态。present表示创建，它是默认选项。absent表示删除
    remove：删除家目录、邮箱等。值为yes或true都可以

## 样例
# 使用user模块创建用户
[root@pubserver ansible]# ansible webservers -m user -a "name=tom"
[root@pubserver ansible]# ansible webservers -a "id tom"

# 使用user模块创建用户并设置用户属性，group是属组，groups是附加组
[root@pubserver ansible]# ansible webservers -m user -a "name=jim uid=1010 group=adm groups=daemon,root home=/home/jim"
[root@pubserver ansible]# ansible webservers -a "id jim"

# {{}}是固定格式，表示执行命令
# password_hash()是函数，sha512是加密算法
[root@pubserver ansible]# ansible webservers -m user -a "name=tom password={{'123456'|password_hash('sha512')}}"

[root@pubserver ansible]# ansible webservers -m user -a "name=tom state=absent"
[root@pubserver ansible]# ansible webservers -m user -a "name=jim state=absent remove=true"
```

- **group模块**
  - 用于实现Linux组管理

```bash
## 常用参数
    name：待创建的组名
    gid：组的ID号
    state：present表示创建，它是默认选项。absent表示删除

## 样例
# 使用group模块创建devops组
[root@pubserver ansible]# ansible webservers -m group -a "name=devops"
[root@pubserver ansible]# ansible webservers -a "cat /etc/group | grep devops"

# 使用group模块删除devops组
[root@pubserver ansible]# ansible webservers -m group -a "name=devops state=absent"
[root@pubserver ansible]# ansible webservers -a "cat /etc/group | grep devops"
```





#### Linux软件管理相关模块

- yum_repository模块
  - 用于管理被控节点YUM源配置文件(repo文件)

```bash
## 常用选项
    file：指定repo文件名，如果不写则以name字段为标准
    name：仓库唯一标识符
    description：仓库描述信息
    baseurl：仓库地址
    enabled：是否启用该仓库
    gpgcheck：是否校验秘钥
    state：文件状态

## 样例
# 添加BaseOS源
[root@pubserver ~]# cd /root/ansible/
[root@pubserver ansible]# ansible webservers -m yum_repository -a "file=myrepo name='BaseOS' description='Rocky Linux BaseOS' baseurl='ftp://192.168.88.240/dvd/BaseOS/' gpgcheck=false enabled=true"
[root@pubserver ansible]# ansible webservers -a "cat /etc/yum.repos.d/myrepo.repo"

# 添加AppStream源，添加到同一个文件中，会追加到文件的最后
[root@pubserver ansible]# ansible webservers -m yum_repository -a "file=myrepo name='AppStream' description='Rocky Linux AppStream' baseurl='ftp://192.168.88.240/dvd/AppStream/' gpgcheck=false enabled=true"
[root@pubserver ansible]# ansible webservers -a "cat /etc/yum.repos.d/myrepo.repo"

# 清理指定文件中的源(如果清理指定name的源文件内还有其他源则文件保留，如果无其他源则同时删除文件)
[root@pubserver ansible]# ansible webservers -m yum_repository -a "file=myrepo name=BaseOS state=absent"
[root@pubserver ansible]# ansible webservers -m yum_repository -a "file=myrepo name=AppStream state=absent"
```

#### Linux服务管理相关模块

- service模块
  - 用于管理Linux系统服务，如启动、关闭、重启、是否开机自启动

```bash
## 常用参数
    name：控制的服务名
    state：started表示启动；stopped表示关闭；restarted表示重启
    enabled：yes表示设置开机自启；no表示设置开机不要自启

## 样例
# 使用yum模块安装nginx服务
[root@pubserver ansible]# ansible webservers -m yum -a "name=nginx state=latest"

# 使用service模块管理nginx服务,启动nginx，加入开机自启
[root@pubserver ansible]# ansible webservers -m service -a "name=nginx state=started enabled=true"

# 验证被控节点nginx服务是否启动且设置为开机自启动
[root@pubserver ansible]# ansible webservers -a "systemctl is-active nginx"
[root@pubserver ansible]# ansible webservers -a "systemctl is-enabled nginx"
```

#### Linux磁盘/逻辑卷/文件系统管理相关模块

- parted模块
  - 用于管理被控磁盘分区

```bash
## 常用参数
    device：待分区的设备
    number：分区编号
    state：present表示创建，absent表示删除
    part_start：分区的起始位置，不写表示从开头
    part_end：表示分区的结束位置，不写表示到结尾
    label：表示标签，要使用的磁盘标签类型，分区表

## 样例
# 将web1虚拟机关机，添加两块20G的虚拟磁盘(该步骤自行操作)
[root@pubserver ansible]# ansible web1 -a "lsblk"
web1 | CHANGED | rc=0 >>
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
vda    253:0    0  20G  0 disk 
└─vda1 253:1    0  20G  0 part /
vdb    253:16   0  20G  0 disk 
vdc    253:32   0  20G  0 disk 
# 磁盘分区知识点回顾
    常用的分区表类型有：MBR（主引导记录）、GPT（GUID分区表）
    MBR最多支持4个主分区，或3个主分区加1个扩展分区。最大支持2.2TB左右的硬盘
    GPT最多支持128个主分区。支持大硬盘

# 使用parted模块对/dev/vdb磁盘进行分区操作
[root@pubserver ansible]# ansible web1 -m parted -a "device=/dev/vdb number=1 part_end=5GiB label=gpt state=present"        #不指定part_start则从0%开始
[root@pubserver ansible]# ansible web1 -m parted -a "device=/dev/vdb number=2 part_start=5GiB label=gpt state=present"      #不指定part_end则到100%为止
[root@pubserver ansible]# ansible web1 -a "lsblk"
web1 | CHANGED | rc=0 >>
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
vda    253:0    0  20G  0 disk 
└─vda1 253:1    0  20G  0 part /
vdb    253:16   0  20G  0 disk 
├─vdb1 253:17   0   5G  0 part 
└─vdb2 253:18   0  15G  0 part 
vdc    253:32   0  20G  0 disk 
```

- lvg模块
  - 用于管理卷组，包括创建、删除等

```bash

## 常用参数
    vg：定义卷组名。vg：volume group
    pvs：由哪些物理卷构成。pvs：physical volumes
    pesize：指定物理区块大小，默认4M。pe：physical extent
    state：present创建卷组；absent删除卷组

## 样例
# 使用yum模块安装lvm2相关软件包
[root@pubserver ansible]# ansible web1 -m yum -a "name=lvm2 state=present"

# 使用lvg模块创建myvg卷组，卷组由/dev/vdb1组成
[root@pubserver ansible]# ansible web1 -m lvg -a "vg=myvg pvs=/dev/vdb1"
[root@pubserver ansible]# ansible web1 -a "vgs"
web1 | CHANGED | rc=0 >>
  VG   #PV #LV #SN Attr   VSize  VFree 
  myvg   1   0   0 wz--n- <5.00g <5.00g

# 使用lvg模块扩容myvg卷组，卷组由/dev/vdb1和/dev/vdb2组成(扩容时需要指定所有物理卷)
[root@pubserver ansible]# ansible web1 -m lvg -a "vg=myvg pvs=/dev/vdb1,/dev/vdb2"
[root@pubserver ansible]# ansible web1 -a "vgs"
web1 | CHANGED | rc=0 >>
  VG   #PV #LV #SN Attr   VSize  VFree 
  myvg   2   0   0 wz--n- 19.99g 19.99g
```

- lvol模块
  - 用于管理逻辑卷，包括创建、删除、修改逻辑卷大小

```bash
## 常用参数
    vg：指定在哪个卷组上创建逻辑卷
    lv：创建的逻辑卷名。lv：logical volume
    size：逻辑卷的大小，不写单位，以M为单位
    state：present创建逻辑卷；absent删除逻辑卷
    
## 样例
# 使用lvol模块创建mylv逻辑卷，vg=myvg在哪个卷组创建，lv=mylv指定逻辑卷的名字，size=2G逻辑卷的大小
[root@pubserver ansible]# ansible web1 -m lvol -a "vg=myvg lv=mylv size=2G"
[root@pubserver ansible]# ansible web1 -a "lvs"
web1 | CHANGED | rc=0 >>
  LV   VG   Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  mylv myvg -wi-a----- 2.00g                                                    

# 使用lvol模块扩容mylv逻辑卷，扩容直接更改大小即可，如size=4G
[root@pubserver ansible]# ansible web1 -m lvol -a "vg=myvg lv=mylv size=4G"
[root@pubserver ansible]# ansible web1 -a "lvs"
web1 | CHANGED | rc=0 >>
  LV   VG   Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  mylv myvg -wi-a----- 4.00g
```

- filesystem模块
  - 用于格式化分区，即创建文件系统

```bash
## 常用参数
    fstype：指定文件系统类型
    dev：指定要格式化的设备，可以是分区，可以是逻辑卷
    state：present用于创建文件系统；absent用于擦除文件系统
    force：用于强制操作
    
## 样例
# 使用filesystem模块格式化mylv逻辑卷，文件系统类型为ext4
[root@pubserver ansible]# ansible web1 -m filesystem -a "dev=/dev/myvg/mylv fstype=ext4 state=present"

#查看结果
[root@pubserver ansible]# ansible web1 -a "blkid /dev/myvg/mylv"
web1 | CHANGED | rc=0 >>
/dev/myvg/mylv: UUID="3d2730be-1eaf-4ec1-9340-03f86deba8fc" BLOCK_SIZE="4096" TYPE="ext4"

# 使用filesystem模块强制格式化mylv逻辑卷，文件系统类型调整为xfs
[root@pubserver ansible]# ansible web1 -m filesystem -a "dev=/dev/myvg/mylv fstype=xfs force=yes"

#查看结果
[root@pubserver ansible]# ansible web1 -a "blkid /dev/myvg/mylv"
web1 | CHANGED | rc=0 >>
/dev/myvg/mylv: UUID="8d32db4d-3412-498e-94a9-82df5e0b9e50" BLOCK_SIZE="512" TYPE="xfs"
```

- mount模块
  - 用于挂载指定分区

```bash
## 常用参数
    path：挂载点。如果挂载点不存在，自动创建。
    src：待挂载的设备
    fstype：文件系统类型
    state：mounted表示永久挂载；unmounted表示临时卸载；present表示临时挂载；absent表示永久卸载

## 样例
# 使用mount模块实现mylv逻辑卷永久挂载
[root@pubserver ansible]# ansible web1 -m mount -a "src=/dev/myvg/mylv path=/data fstype=xfs state=mounted"
[root@pubserver ansible]# ansible web1 -a "df -hT | grep data"  #查看结果
web1 | CHANGED | rc=0 >>
/dev/mapper/myvg-mylv xfs       4.0G   61M  4.0G   2% /data
[root@pubserver ansible]# ansible web1 -a "tail -1 /etc/fstab"  #已经写入fstab文件
web1 | CHANGED | rc=0 >>
/dev/myvg/mylv /data xfs defaults 0 0

# 使用mount模块实现mylv逻辑卷永久卸载
[root@pubserver ansible]# ansible web1 -m mount -a "path=/data state=absent"
[root@pubserver ansible]# ansible web1 -a "ls /data"            #挂载点已经直接删除，查看失败
[root@pubserver ansible]# ansible web1 -a "cat /etc/fstab"      #已经清理fstab文件，挂载的mylv已经清理

# 使用lvol模块删除mylv逻辑卷，force=yes：这个参数用于强制执行，如在删除逻辑卷时，即使它处于活动状态也会被强制删除
[root@pubserver ansible]# ansible web1 -m lvol -a "vg=myvg lv=mylv state=absent force=yes"

# 使用lvg模块删除myvg卷组
[root@pubserver ansible]# ansible web1 -m lvg -a "vg=myvg state=absent"
[root@pubserver ansible]# ansible web1 -a "lvs"     #没有内容显示
[root@pubserver ansible]# ansible web1 -a "vgs"     #没有内容显示
```

## Test练习

**ansible管理远程主机有哪些方式？**

答：ad-hoc临时命令、playbook剧本

**ansible ad-hoc语法格式是什么样的？**

答：语法格式为：ansible [options] pattern [-m module_name] [-a args]

pattern指定要执行任务的主机或主机组，module_name指定要使用的模块，args指定模块的参数。

















## Ansible Playbook剧本

#### 使用Playbook剧本的目的

- 将需要经常执行的任务写入一个文件(剧本)，适用于周期性执行的复杂任务
- 剧本中可以包含多个任务并可以重复使用
- Playbook剧本要求按照YAML格式编写

#### YAML是什么

- YAML（YAML Ain't Markup Language）
  - 是一种人类可读性高且面向数据序列化的格式
  - 是一种格式而不是语言
  - 具备易读性、简洁和可扩展性，对人类友好，支持跨语言操作
  - 常用于配置文件、数据传输等场景
- YAML语法格式
  - 文件以 .yaml 或 .yml 扩展名为常见
  - 文件一般以`---`作为第一行，不是必须的，但是常用
  - 键值对使用冒号`:`表示，冒号后面必须有空格
  - 数组使用`-`表示，`-`后面必须有空格
  - 相同的层级必须有相同的缩进。如果缩进不对，则有语法错误。每一级缩进，建议2个空格
  - 全文不能使用tab，必须使用空格
- YAML格式数据样例



#### 配置VIM编辑器

```bash
## 编辑vimrc文件，配置yml编写约束
[root@pubserver ansible]# vim ~/.vimrc
set ai et ts=2
set cursorline
set cursorcolumn
```

#### Playbook基础语法

```bash
## 编写Playbook使用ping模块测试网络连通

[root@pubserver ansible]# vim test_ping.yml
---
- name: test network        #剧本名称(可选项)
  hosts: all                #作用主机(组)
  tasks:                    #任务
    - name: test via ping   #任务1(名称可选，可以写中文)
      ping:                 #使用的模块
      
[root@pubserver ansible]# ansible-playbook --syntax-check test_ping.yml #检查文件语法

playbook: test_ping.yml
[root@pubserver ansible]# ansible-playbook test_ping.yml                #执行剧本
```







### 













