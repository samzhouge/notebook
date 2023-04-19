# 安装
* 设置网卡名为eth0
  * 开始安装时候，按Tab键，修改网卡显示名eth0，在最下面quiet后面追加
      * net.ifnames=0 biosdevname=0

# 基础配置
## 配置yum源
**直接使用官方源也可以，速度还可以**

[阿里源文档](https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.3e221b111DMGEx)
```
yum install wget

mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel-7.repo https://mirrors.aliyun.com/repo/epel-7.repo  # epel源
yum clean all && yum makecache
yum repolist
```

### yum命令
```
yum provides "*/ifconfig"  # 查询命令所在的包
```

## 安装基础软件
```shell
yum install vim tree telnet bind-utils yum-utils nmap dos2unix lrzsz nc lsof wget tcpdump htop iftop iotop dstat sysstat nethogs strace psmisc nload perf bash-completion -y
```
注:
* bind-utils  # dig
* psmisc # pstree
* sysstat # sar iostat opstat
  * mpstat
  * pidstat
* nload # 网卡流量
* bash-completion # 命令补全工具

## 关闭NetworkManager
```shell
systemctl disable NetworkManager
systemctl stop NetworkManager
```

## 关闭防火墙和SELinux
```shell
systemctl stop firewalld
systemctl disable firewalld

setenforce 0
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
```

## 安装ifconfig
```shell
yum install network-tools
systemctl start network && systemctl enable network
```

## ssh配置
```shell
vim /etc/ssh/sshd_config
UseDNS no
ClientAliveInterval 60

systemctl restart sshd
```

## profile配置
profile配置，TMOUT登陆超时退出
/etc/profile.d/文件名.sh
注：TMOUT表示多少秒没活动，断开终端
```shell
cat > /etc/profile.d/me.sh <<EOF
TMOUT=3600
readonly TMOUT
export TMOUT
export HISTSIZE=5000
export HISTTIMEFORMAT='%F %T '
umask 027
EOF
```

## vmware新建一个虚拟机
设置mac地址，网卡适配器-网卡高级选项（在最下面，展开），生成MAC。不配置会每次也会自动生成。

秘钥文件
```
# 生成秘钥文件
ssh-keygen

加入本电脑的key到授权文件
ssh-copy-id root@模板虚拟机ip
```
.bash_profile配置，添加如下文件
```
alias chnic="vim /etc/sysconfig/network-scripts/ifcfg-ens160"
alias chhostname="hostnamectl set-hostname "

alias dil="docker image ls"
alias dcl="docker container ls"
alias dcla="docker container ls -a"
alias dcr="docker container run --rm -it"
alias dcpsa="docker ps -a"
alias dcst="docker stop "
alias dsl="docker service ls"
alias dsps="docker service ps "
alias dnl="docker network ls"
alias dce="docker container exec -it "
alias dni="docker network inspect "
```

# docker服务器环境配置
关闭
```shell
swapoff -a
yes | cp /etc/fstab /etc/fstab_bak
cat /etc/fstab_bak |grep -v swap > /etc/fstab
```

## 修改时区

## 修改时间

## 修改主机名