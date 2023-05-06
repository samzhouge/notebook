
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
yum install -y gzip zlib-devel
./configure --prefix=/usr/local/nginx
make
make install
```
