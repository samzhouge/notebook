# ccmouse慕课网视频（Google资深工程师深度讲解Go语言）
[课程地址](https://coding.imooc.com/class/chapter/180.html#Anchor)
## 环境
* Go1.13
## 1-3 国内镜像配置
* go env -w GOPROXY=https://goproxy.io,direct
* go env -w GO111MODULE=on
## 1-4 IntelliJ Idea 的安装和配置
### Idea 编辑器
* 安装插件go
* 安装goimports
  * go get ...
* 安装插件file watcher
  * 添加 goimports
* 隐藏参数提示（对java有用，go不太有用）
  * parameter hint
    * show parameter name hints 取消勾选

### Goland 编辑器
* editor/code style/go/sorting type/选择 goimports(默认是gofmt)
* 隐藏参数
  * show parameter hints/去掉 GO
## 2-3 常量与枚举
* consts 数值可以作为各种类型使用
  * consts filename = "abc.txt"  // 不定义类型
  * 枚举 iota
* rune 字符串类型 int32别名
### 2-4 条件语句



## 17-3 Docker的安装和使用

### 安装elasticsearch
* [安装文档](https://www.elastic.co/guide/en/elasticsearch/reference/7.17/docker.html)
```
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.17.9
docker run -p 127.0.0.1:9200:9200 -p 127.0.0.1:9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.17.9
或者
docker pull elasticsearch:7.17.9
docker run -p 127.0.0.1:9200:9200 -p 127.0.0.1:9300:9300 -e "discovery.type=single-node" elasticsearch:7.17.9
```
## 17-4 ElasticSearch入门
* <server>:9200/index/type/id
  * index -> database
  * type -> table
* 全文搜索
  * <server>:9200/<index>/<type>/_search?q=全文搜索

