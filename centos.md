

## 1.用yum安装wget下载工具 

```
yum install wget -y
```

用wget修改yum源和epel源 

```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo 
```

```
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo 
```

生成缓存并更新 

```
yum makecache 
```

```
yum update  -y 
```



## 2.额外安装一些常用的软件包

```
yum install tree nmap dos2unix lrzsz nc lsof wget tcpdump htop iftop iotop sysstat nethogs -y 
```

| 软件包名 | 用途                                  |
| -------- | ------------------------------------- |
| tree     | tree以树形结构显示文件和目录          |
| nmap     | nmap扫描端口的工具                    |
| dos2unix | 转换脚本格式的工具                    |
| lrzsz    | 上传(rz)和下载(sz)文件工具            |
| nc       | 文件传输、端口检查工具                |
| lsof     | 反查端口进程，以及服务开发文件工具    |
| wget     | 下载软件包工具                        |
| tcpdump  | 抓包、监听等重要排错工具              |
| htop     | 系统进程相关信息查看工具              |
| iftop    | 查看主机网卡带宽工具                  |
| iotop    | 监视磁盘I/O使用状况                   |
| sysstat  | 含有sar，iostat等重要系统性能查看工具 |
| nethogs  | 显示进程的网络流量                    |

## 3.CentOs7 要安装的企业运维常用基础工具包 

```
yum install psmisc net-tools bash-completion vim-enhanced -y 
```



| 软件包名        | 用途                                    |
| --------------- | --------------------------------------- |
| psmisc          | 含有killall、pstree等命令               |
| net-tools       | 含有netstat、ifconfig、route、arp等命令 |
| bash-completion | tab补全功能工具包                       |
| vim-enhanced    | vim编辑器工具包                         |

如果在系统安装过程中时落下某些需要的软件包组，可以执行以下命令来安装： 

查看当前组包 

```
yum groups mark convert 
```

```
yum grouplist 
```

如发现系统安装过程中落下某些组包，可用下面方法安装 

```
yum groupinstall "组包名" -y 
```

## 4.docker安装

查看安装的相关软件

```
yum list installed kube*
```

卸载

```
yum remove  xxx
```

安装所需的软件包

```
yum install -y yum-utils  device-mapper-persistent-data lvm2 
```

选择国内镜像

```
yum-config-manager   --add-repo      http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 
```

查看资源版本

```
yum list docker-ce --showduplicates | sort -r 
```

安装特定版本

```
yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

```
systemctl start docker
systemctl enable docker
```

## 5.docker使用命令

查看docker相关命令  

```
 docker
```

查看正在运行中容器  docker ps  

启动容器 docker run -it  <image> /bin/bash

后台运行容器 docker run -itd --name <image> /bin/bash

退出终端 exit

查看所有容器 docker ps -a

启动一个停止的容器 docker start  <containerId>

重启一个容器  docker restart <containerId>

删除容器 docker rm -f <containerId>

进入一个正在运行中的容器 docker exec -it <containerId> /bin/bash 

拉取镜像  docker pull  <image>

查找镜像 docker search <image>

删除镜像 docker rmi <image>

更新镜像 docker commit <containerId> <image>:version

宿主主机拷贝文件到docker  docker cp /wxg/nginx.conf  <containerId>：/etc/nginx/nginx.conf

docker中拷贝文件到宿主机  docker cp <containerId>：/etc/nginx/nginx.conf /wxg/nginx.conf

docker安装包更新使用阿里源 sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list 

添加域名解析：

echo "nameserver 8.8.8.8" >> /etc/resolv.conf 

echo "nameserver 8.8.4.4" >> /etc/resolv.conf 

docker 删除所有容器：

```
docker stop $(docker ps -q) & docker rm $(docker ps -aq) 
```

docker 删除所有镜像：

```
docker rmi `docker images -q` 
```



## 6.k8s安装

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

```
yum -y install kubelet-XX kubeadm-xx kubectl-xx  kubernetes-cni-xx
```

```
cat <<EOF> /etc/cni/net.d/10-flannel.conf
{
"name": "cbr0",
"type": "flannel",
"delegate": {
"isDefaultGateway": true
}
}
EOF
```

```
mkdir /usr/share/oci-umount/oci-umount.d -p 
```



```
cat <<EOF> /run/flannel/subnet.env
FLANNEL_NETWORK=10.244.0.0/16
FLANNEL_SUBNET=10.244.1.0/24
FLANNEL_MTU=1450
FLANNEL_IPMASQ=true
EOF
```

```
kubeadm config images pull


images=(kube-apiserver:v1.19.4 kube-controller-manager:v1.19.4 kube-scheduler:v1.19.4 kube-proxy:v1.19.4 pause:3.2 etcd:3.4.13-0 coredns:1.7.0)
for imageName in ${images[@]} ; do
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName k8s.gcr.io/$imageName
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName
done
```

```
kubeadm init --kubernetes-version=v1.19.4 --pod-network-cidr=10.244.0.0/16 
```



```
kubectl get pod
kubectl taint nodes --all node-role.kubernetes.io/master- 
kubectl get pod --namespace=kube-system
```

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml 
```

```
kubectl get pods --all-namespaces      //查看kube-flannel 状态    running 为安装正常
kubectl get pods --all-namespaces -o wide
kubectl get nodes
kubectl get cs
kubectl get csr
kubectl apply -f  xxxx.yaml
kubectl create -f xxxx.yaml
kubectl delete -f xxxxx.yaml
```

```
启动报错查询： journalctl -xefu kubelet 
```

修改端口范围：

```
vi /etc/kubernetes/manifests/kube-apiserver.yaml
添加到如下位置就行了
\- command:
   - kube-apiserver
   - --service-node-port-range=1-65535 
```

swapoff -a 



coredns异常：

```
kubeadm reset 
systemctl stop kubelet 
systemctl stop docker
rm -rf /var/lib/cni/
rm -rf /var/lib/kubelet/*
rm -rf /etc/cni/ 
ip link set cni0 down;brctl delbr cni0
ip link set flannel.1 down
ip link set docker0 down 
ip link delete cni0 
ip link delete flannel.1 
systemctl restart network 
systemctl start docker 
systemctl start kubelet 
kubectl delete -f kube-flannel.yml
kubectl apply -f kube-flannel.yml 
```



# mysql安装

搭建nfs存储

```
yum -y install nfs-utils 
systemctl enable rpcbind
mkdir -p /nfs/data
vim /etc/exports
/nfs/data *(rw,sync,no_root_squash)
systemctl start nfs-server
systemctl enable nfs-server
showmount -e
```

  

mysql -uroot -p{your password} 

//使用mysql数据库 mysql>use mysql 

//修改数据库 mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456'; 

//重新修改密码后可连接成功 mysql> alter  user 'root'@'%' identified by '123456'; 

//刷新数据库 mysql> flush privileges; 



# apollo安装

```
helm uninstall -n your-namespace apollo-service-dev
helm install apollo-service-dev -f values.yaml -n your-namespace apollo/apollo-service 
```



