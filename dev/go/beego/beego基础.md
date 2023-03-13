
## [beego教程](https://git-books.github.io/books/beego/)

## 安装bee工具
```
# go 1.16 以前的版本
go get -u github.com/beego/bee/v2

# go 1.16及以后的版本
go install github.com/beego/bee/v2@latest
```

### bee环境变量
设置环境变量$GOPATH/bin加入PATH

## new新建项目
该命令必须在 $GOPATH/src 下执行
bee new myproject

## api命令
bee api apiproject
