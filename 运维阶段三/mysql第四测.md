### zabbix监控mysql1、mysql2、mycat

```bash
vm  clone zabbixserver mysql1  mysql2  mycat

vm setip zabbixserver 192.168.88.5
vm setip mysql1  192.168.88.51
vm setip mysql2  192.168.88.52
vm setip mycat   192.168.88.50

# 安装zabbix
scp /linux-soft/s2/zzg/zabbix_soft/*.rpm 192.168.88.240:/var/ftp/rpms

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

[dbs]
mysql51 ansible_host=192.168.88.51
mysql52 ansible_host=192.168.88.52

[all:vars]
ansible_ssh_user=root
ansible_ssh_pass=a
```

配置yum、安装服务

```bash
vim files/local88.repo

[rpms]
name = rpms
baseurl = ftp://192.168.88.240/rpms
enabled = 1
gpgcheck = 0

# 为各台主机配置yum
[root@pubserver zabbix]# vim 01-upload-repo.yml 
---
- name: config repos.d
  hosts: all
  tasks:
    - name: upload local88
      copy:
        src: files/local88.repo
        dest: /etc/yum.repos.d/
        
        
# 在zabbixserver上安装相应软件包
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
 
# 6. 启动mysqld服务
[root@pubserver zabbix]# vim 03-start-mysqld.yml
---
- name: config mysqld
  hosts: zabbix,dbs
  tasks:
    - name: start mysqld      # 启动mysqld服务
      service:
        name: mysqld
        state: started
        enabled: yes
```



配置主从

```bash
# mysql51
## 改配置文件
vim /etc/my.cnf.d/mysql-server.cnf
[mysqld]
server-id=51
log-bin=mysql51

[mysql]
user = zabbix
password = zabbix
## 
systemctl restart mysqld
mysql -e "create user zabbix@'location' identified by 'zabbix' ;"
mysql -e "grant all on *.* to zabbix@'location' ;"
mysql -e "show master status"

# mysql52
## 改配置文件
vim /etc/my.cnf.d/mysql-server.cnf
[mysqld]
server-id=52

[mysql]
user = zabbix
password = zabbix
##
mysql>change master to
master_host="192.168.88.51",
master_user="zabbix",
master_password="zabbix",
master_log_file="mysql51.000002",
master_log_pos=667;

//启动slave进程
mysql> start slave ; 
//查看状态信息
mysql> show slave status \G  
```



配置自定义监控项

```bash
# 监控mysql51和mysql的主从结构是否正常
vim check_mysql_replication.sh

#!/bin/bash
# 执行MySQL命令获取从库状态
STATUS=$(mysql -e "show slave status \G" | grep -E "Slave_IO_Running:|Slave_SQL_Running:")
# 使用awk提取值并进行比较
IO_RUNNING=$(echo "$STATUS" | grep "Slave_IO_Running:" | awk '{print $2}')
SQL_RUNNING=$(echo "$STATUS" | grep "Slave_SQL_Running:" | awk '{print $2}')

if [ "$IO_RUNNING" == "Yes" ] && [ "$SQL_RUNNING" == "Yes" ]; then
    echo 1 # 正常运行
else
    echo 0 # 出现问题
fi
```

















```sql
select * from students order by age asc;

select user_id,count(order_id) from orders 
group by user_id order by count(order_id) desc;

select name,price from products where price > 100 
order by price asc limit 5;

```





