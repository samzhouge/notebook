# yum
## [阿里镜像网站](https://developer.aliyun.com/mirror/)
## [配置阿里yum源](https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.3e221b11cYLMrM)

## yum命令
- 查看命令所在包 yum provides "yum-config-manager"
- 添加 yum 仓库
  - yum-config-manager --add-repo https://openresty.org/package/centos/openresty.repo
- 查看服务状态 systemctl list-unit-files --type=service