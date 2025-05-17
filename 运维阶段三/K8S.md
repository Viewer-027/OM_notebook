# kubernetes

## 集群图例

![image-20250510162801012](images/image-20250510162801012.png)

## kubernetes 安装

### 主机清单

| 主机名    | IP地址         | 最低配置    |
| --------- | -------------- | ----------- |
| harbor    | 192.168.88.240 | 2CPU,4G内存 |
| master    | 192.168.88.50  | 2CPU,4G内存 |
| node-0001 | 192.168.88.51  | 2CPU,2G内存 |
| node-0002 | 192.168.88.52  | 2CPU,2G内存 |
| node-0003 | 192.168.88.53  | 2CPU,2G内存 |









# k8s集群管理

| **子命令**    | **说明**                           |
| ------------- | ---------------------------------- |
| help          | 用于查看命令及子命令的帮助信息     |
| cluster-info  | 显示集群的相关配置信息             |
| api-resources | 查看当前服务器上所有的资源对象     |
| api-versions  | 查看当前服务器上所有资源对象的版本 |
| config        | 管理当前节点上的认证信息           |

```bash
# 查看帮助命令信息
[root@master ~]# kubectl help version

# 查看集群状态信息
[root@master ~]# kubectl cluster-info 

# 查看资源对象类型
[root@master ~]# kubectl api-resources 

# 查看资源对象版本
[root@master ~]# kubectl api-versions 

# 查看当前认证使用的用户及证书
[root@master ~]# kubectl config get-contexts 

# 使用 view 查看详细配置
[root@master ~]# kubectl config view 


```



## 集群管理授权

```bash
[root@harbor ~]# dnf makecache
[root@harbor ~]# dnf install -y kubectl
[root@harbor ~]# mkdir -p $HOME/.kube
[root@harbor ~]# rsync -av master:/etc/kubernetes/admin.conf $HOME/.kube/config
[root@harbor ~]# chown $(id -u):$(id -g) $HOME/.kube/config
[root@harbor ~]# kubectl get nodes
NAME        STATUS   ROLES           AGE   VERSION
master      Ready    control-plane   24h   v1.29.2
node-0001   Ready    <none>          22h   v1.29.2
node-0002   Ready    <none>          22h   v1.29.2
node-0003   Ready    <none>          22h   v1.29.2



```

## Pod 管理

```bash

```





