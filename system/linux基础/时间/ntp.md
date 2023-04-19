## 同步时间
- yum install ntp
- 设置时区
  - timedatectl set-timezone Asia/Shanghai
  - 或者 cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
- 手动同步时间 ntpdate ntp3.aliyun.com
- 配置文件 /etc/ntp.conf
- 启动服务
  - systemctl start ntpd
  - systemctl enable ntpd


- [参考](https://zhuanlan.zhihu.com/p/156757418)
