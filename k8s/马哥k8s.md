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
192.168.196.11 master
192.168.196.12 node01
192.168.196.13 node02
````
/etc/hosts  # 修改如下
192.168.196.11 master
192.168.196.12 node01
192.168.196.13 node02

/etc/resolv.conf  # 修改如下
search magedu.com
````

## 2.2 安装yum软件
* docker
* kubeadmin
* kubelet
* kubectl  # 客户端程序，node可以不安装
### 2.3 配置yum源和安装
#### 2.3.1 阿里yum源配置
* [yum配置docker](https://developer.aliyun.com/mirror/docker-ce?spm=a2c6h.13651102.0.0.3e221b11c9NoqD)  
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

* [yum配置k8s](https://developer.aliyun.com/mirror/kubernetes?spm=a2c6h.13651102.0.0.3e221b11c9NoqD)

```
两个gpgcheck改成0

cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# 安装软件
yum install kubelet kubeadm kubectl -y

# 开机启动kubelet，不需要启动  
systemctl enable kubelet
```

* 配置docker
  * 配置国内镜像
    ```
    vim /etc/docker/daemon.json
      {
      "exec-opts": ["native.cgroupdriver=systemd"],
      "registry-mirrors": [
          "http://hub-mirror.c.163.com",
          "https://docker.mirrors.ustc.edu.cn"
      ]
    }


    systemctl daemon-reload  # 重新加载配置
    systemctl restart docker
    ```

## 2.4 kubeadm安装master
* kubeadm init初始化master  
[参考](https://k8s.easydoc.net/docs/dRiQjyTY/28366845/6GiNOzyZ/nd7yOvdY)
  ```shell
  # 查看kueadmin默认配置，imageRepository是镜像拉取地址
  kubeadm config print init-defaults

  # 查看需要的镜像
  kubeadm config images list 

  最后需要使用普通用户做一些配置，本次使用root
  kubeadm init --image-repository=registry.aliyuncs.com/google_containers

  mkdir -p $HOME/.kube
  cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  ```
* node加入集群命令，先记录下，方便以后ch
  ```shell
    kubeadm join 192.168.196.11:6443 --token joy19f.v0rzd9zdsu501d96 \
	--discovery-token-ca-cert-hash sha256:6c9e4bf9478021db569ccc6a71011d0fba337ab440d88041042e51f0468752bc
  ```
* 查看状态
  ```shell
  kubectl get cs/componentstatus  # 查看组件状态
  kubectl get nodes  # 获取节点信息
  kubectl get ns  # 查看名称空间
  ```
## 2.5 安装flannel网络
  ```shell
  进入flannel github地址
  https://github.com/flannel-io/flannel

  kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml

  kubectl get pos -n kube-system  -o wide  # 看到kube-flannel-ds... 表示安装好了
  kubectl get nodes  # 此时node状态为Ready
  ```
## kubeadm添加node
  ```
  kubeadm join 192.168.196.11:6443 --token joy19f.v0rzd9zdsu501d96 \
    --discovery-token-ca-cert-hash sha256:6c9e4bf9478021db569ccc6a71011d0fba337ab440d88041042e51f0468752bc
  ```

