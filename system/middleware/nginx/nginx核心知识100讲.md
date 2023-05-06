
## nginx安装
### nginx目录
- nginx配置vim编辑识别
```shell
mkdir ~/.vim
cp -r contrib/vim/* ~/.vim/
```

### 编译安装
- [下载nginx](https://www.nginx.org)
- 安装
```shell
yum install -y gcc gzip zlib zlib-devel pcre pcre-devel openssl openssl-devel
./configure --prefix=/usr/local/nginx --with-http_ssl_module
make
make install
```
- 修改配置文件nginx.conf
```shell
# 如果是root启动，修改如下
user root;
```
- 启动
```shell
/usr/local/sbin/nginx
```

### nginx命令
- 打印版本，编译信息：-v -V
- 测试配置文件是否有语法错误 -t -T
- -s 放信号
  - stop 立刻停止
  - quit 优雅地停止服务
  - reload 重载
  - reopen重新开始记录日志文件
- -c 指定配置文件
- -g 指定配置指令
- -p 指定运行目录
- -h 帮助

## 升级nginx
- 备份sbin/nginx文件
- ./configure和make编译后，objs文件夹下的nginx，复制到sbin/nginx下
- 进行热部署
  - kill -USR2 nginx master进程
  - kill -WINCH 原来那个nginx master进程
