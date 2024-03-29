# 安装
* 设置网卡名为eth0
  * 开始安装时候，按Tab键，修改网卡显示名eth0，在最下面quiet后面追加
      * net.ifnames=0 biosdevname=0

# 基础配置
## 配置yum源
**直接使用官方源也可以，速度还可以**

[阿里源文档](https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.3e221b111DMGEx)
```
yum install -y wget

mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel-7.repo https://mirrors.aliyun.com/repo/epel-7.repo  # epel源
yum clean all && yum makecache
yum repolist
```

## 安装基础软件
```shell
yum install -y vim tree ntp net-tools bind-utils yum-utils unzip dos2unix lrzsz lsof wget bash-completion
yum install -y gcc pcre-devel
yum install -y htop iftop iotop dstat sysstat nethogs strace psmisc nload perf
yum install -y telnet nmap nc tcpdump traceroute mtr bind-utils
```
注:
* net-tools
  - ifconfig
  - route
  - netstat
* bind-utils
  - dig
* psmisc
  - pstree
* sysstat # sar iostat opstat
  - mpstat
  - pidstat
* nload # 网卡流量
* bash-completion # 命令补全工具
* bind-utils
  - nslookup
  - dig

### yum命令
```
yum provides "*/ifconfig"  # 查询命令所在的包
```

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

## ssh配置
```shell
vim /etc/ssh/sshd_config
UseDNS no
ClientAliveInterval 60

systemctl restart sshd
```

## 配置时间和ntp服务
```
- yum install -y ntp
- 设置时区
  - timedatectl set-timezone Asia/Shanghai
  - 或者 cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
- 手动同步时间 ntpdate ntp3.aliyun.com
- 配置文件 /etc/ntp.conf
- 启动服务
  - systemctl start ntpd
  - systemctl enable ntpd
```

## 修改主机名

## 配置网卡
- 参照 软件包安装 网络

## 需改文件和进程数限制 ulimit
[参考](https://www.jianshu.com/p/2c398f08a0e2)
```shell
cat >> /etc/security/limits.conf << 'EOF'
* soft nproc 655360
* hard nproc 655360
* soft nofile 1000000
* hard file 1000000
* soft core 102400
* hard core 102400
* soft stack unlimited
* hard stack unlimited
EOF

# 注：cat<<'EOF'，以EOF输入字符为标准输入结束

# 并且配置(提供对shell及其启动的进程的可用文件句柄的控制，这是进程级别的)
echo "fs.file-max = 1000000" >> /etc/sysctl.conf
```

## profile配置
profile配置，TMOUT登陆超时退出
/etc/profile.d/文件名.sh
注：TMOUT表示多少秒没活动，断开终端
```shell
cat > /etc/profile.d/me.sh << 'EOF'
TMOUT=3600
readonly TMOUT
export TMOUT
export HISTSIZE=5000
export HISTTIMEFORMAT='%F %T '
umask 027
export PS1='\n\e[1;37m[\e[m\e[1;32m\u\e[m\e[1;33m@\e[m\e[1;36m\h\e[m \e[4m`pwd`\e[m\e[1;37m]\e[m\e[1;36m\e[m\n\$'
alias grep='grep --color=auto'
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
