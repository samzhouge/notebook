# **马哥k8s**

# 1 概要
## 1.1 组件
![](../k8s/imgs/k8s组件.png)
### 1.2 网络
![](imgs/k8s网络.png)

# 2 kubeadmin安装k8s
## 2.1 环境
* 系统：centos7
* 关闭防火墙
* 关闭swap
### 2.1.1 服务器信息
````
vim /etc/hosts  # 修改如下
192.168.196.11 master
192.168.196.12 node01
192.168.196.13 node02

vim /etc/resolv.conf  # 修改如下
search magedu.com
````

## 2.2 安装yum软件
* docker-ce
* kubeadm
* kubelet
* kubectl  # 客户端程序(node可以不安装)

### 2.2.1 docker yum配置和安装 
#### [阿里文档](https://developer.aliyun.com/mirror/docker-ce?spm=a2c6h.13651102.0.0.3e221b11c9NoqD)  
```
# step 1: 安装必要的一些系统工具
yum install -y yum-utils device-mapper-persistent-data lvm2
# Step 2: 添加软件源信息
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# Step 3
sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
# Step 4: 更新并安装Docker-CE
yum makecache fast
yum -y install docker-ce
# Step 4: 开启Docker服务，并设置开机启动
service docker start
systemctl enable docker
```
#### 配置国内镜像
```
vim /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "registry-mirrors": [
      "http://hub-mirror.c.163.com",
      "https://docker.mirrors.ustc.edu.cn"
  ]
}

重新加载配置
systemctl daemon-reload
systemctl restart docker
```

### 2.3.2 k8s yum配置和安装
[阿里源文档](https://developer.aliyun.com/mirror/kubernetes?spm=a2c6h.13651102.0.0.3e221b11c9NoqD)
```
新增yum配置(两个gpgcheck改成0)
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

安装软件
yum install -y kubelet kubeadm kubectl

开机启动kubelet，不需要启动  
systemctl enable kubelet
```
## 2.4 安装master
### 2.4.1 kubeadm init初始化master  
[kubeadm init参考](https://k8s.easydoc.net/docs/dRiQjyTY/28366845/6GiNOzyZ/nd7yOvdY)

安装前查看
```
查看kueadm默认配置，imageRepository是镜像拉取地址
kubeadm config print init-defaults

查看需要的镜像
kubeadm config images list 
```

开始安装
```
kubeadm init --pod-network-cidr=10.244.0.0/16 --image-repository=registry.aliyuncs.com/google_containers
```
注：如果不指定pod-network-cidr，安装flannel网络后，会有问题

最后需要使用普通用户做一些配置，本次使用root
```
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```

记录node加入集群命令，给node加入集群使用
```
kubeadm join 192.168.196.11:6443 --token wukhpw.nhdvbxumw9j5l54z --discovery-token-ca-cert-hash sha256:7b359813c95747b3bf82b819d196b133bf55c8e183aadb749d6fd95f3c837ceb
```

查看状态
```
kubectl get cs  # 查看组件状态
kubectl get nodes  # 获取节点信息
kubectl get ns  # 查看名称空间
```
## 2.5 安装flannel网络
不安装的话nodes状态为NoReady

[flannel文档](https://github.com/flannel-io/flannel)

运行命令，进行安装
```
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

查看命令
```
看到kube-flannel-ds... 表示安装好了
kubectl get pods -n kube-system  -o wide

过一会，node状态为Ready
kubectl get nodes
```
![](imgs/k8s-get-nodes.png)


## 2.6 添加node到集群
```
kubeadm join 192.168.196.11:6443 --token wukhpw.nhdvbxumw9j5l54z --discovery-token-ca-cert-hash sha256:7b359813c95747b3bf82b819d196b133bf55c8e183aadb749d6fd95f3c837ceb
```

