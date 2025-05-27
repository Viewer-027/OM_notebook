









docker部署wordpress

```bash
docker pull wordpress

docker run -it --name wordpress -p 9999:80 -v /usr/local/share/wordpress:/var/www/html -d wordpress

--name wordpress => 为容器指定一个名称，此处命名为 wordpress

-p 9999:80 => 将容器内部的端口映射到宿主机的端口，此处容器将80端口映射到宿主机的9999端口

-v /usr/local/share/wordpress:/var/www/html => 使用挂载卷将宿主机的目录挂载到容器内部，此处是将宿主机的 /usr/local/share/wordpress 挂载到容器内部的 /var/www/html 目录。宿主机目录是什么意思呢，就是打开 ftp 你所看到的目录就是宿主机上实际的目录，操作（增删改）宿主机上 /usr/local/share/wordpress 这个目录下的文件，等同于操作容器内部的 /var/www/html 这个目录，可以看成自动同步吧，我是这么理解的

-d => 这个参数代表以 “detached” 模式运行容器，就是在后台运行

-d 后面的wordpress => 就是刚才拉取的 wordpress 镜像名称
```

配置Mysql

```bash
docker run -d --name mysql -v /usr/local/share/mysql/data:/var/lib/mysql -v /usr/local/share/mysql/conf:/etc/mysql/conf.d -v /usr/local/share/mysql/logs:/var/log/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:8

docker exec -it mysql /bin/bash
mysql -u root -p

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;

create database wordpress;

docker inspect <mysql-container-name> | grep IPAddress

```

编辑

```bash
wp-config-sample.php
```

















