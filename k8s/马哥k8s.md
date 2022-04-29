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
## 2.2 安装yum软件
* docker
* kubeadmin
* kubelet
* kubectl  # 客户端程序，node可以不安装
### 2.3 配置yum源和安装
#### 2.3.1 阿里yum源配置
* [yum配置docker](https://developer.aliyun.com/mirror/docker-ce?spm=a2c6h.13651102.0.0.3e221b11c9NoqD)
* [yum配置k8s](https://developer.aliyun.com/mirror/kubernetes?spm=a2c6h.13651102.0.0.3e221b11c9NoqD)
* 安装软件: `yum install docker-ce,kubelet,kubeadmin,kubectl`
* 配置docker
  * 配置国内镜像
    ```
    # vim /usr/lib/systemd/system/docker.service
    在其中添加变量
    Environment=“HTTPS_PROXY=http://www.ik8s.io:10080”
    Environment=“NO_PROXY=127.0.0.0/8,172.0.0.0/16”
    ```
  * 启动
    * systemctl start docker
    * systemctl enable docker
* 开机启动kubelet，不需要启动  
  `systemctl enable kubelet`
## 2.4 kubeadm安装master
* kubeadm init初始化master
  ```shell
  最后需要使用普通用户做一些配置，本次使用root

  
  ```
* node加入集群
  ```shell

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

kubectl get pos -n kube-system  -o wide  # 看到kube-flannel-ds... 表示安装好了
kubectl get nodes  # 此时node状态为Ready
```
## kubeadm添加node
```

```