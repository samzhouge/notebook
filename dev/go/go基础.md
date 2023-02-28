# go基础

## 安装
### GOROOT和GOPATH配置
[教程](https://www.jianshu.com/p/4e699ff478a5)
假设安装路径为：D:/software/go 代码路径：D:/data
GOROOT是安装路径
GOPATH是源码和第三包安装位置
代码放在D:/data/src下
go get xxx 下载的包在D:/data/pkg下，可执行文件在D:/data/bin下

环境变量设置
GOROOT=D:/software/go
GOPATH=D:/data
两个的bin都加入PATH中，为了让第三方安装的可执行文件可以运行
PATH=%GOROOT%/bin;%GOPATH%/bin

### mac设置go环境变量
GOROOT=/usr/local/go
GOPATH=/Users/samzhou/Desktop/studygo/go

go env -w GOPROXY=https://goproxy.io,direct
go env -w GO111MODULE=on



## Go系列教程
[Go系列教程](https://studygolang.com/subject/2)

## 数组和切片
### 切片
* 切片类型的零值为nil
```
var names []string //zero value of a slice is nil
if names == nil {
    ...
}
```
* 合并切片
```
a := []string{"a1", "a2"}
b := []string{"b1", "b2"}
c := append(a, b...)
```
* 切片传递给函数时，即使它通过值传递，指针变量也将引用相同的底层数据
```
func subtactOne(numbers []int) {
    for i := range numbers {
        numbers[i] -= 2
    }
}
```

## 函数
### 函数语法
```
func functionname(parametername type) returntype {  
    // 函数体（具体实现的功能）
}
```
* 函数中的参数列表和返回值并非是必须的，所以下面这个函数的声明也是有效的

### 函数示例
```
func calculateBill(price int, no int) int {  
    var totalPrice = price * no // 商品总价 = 商品单价 * 数量
    return totalPrice // 返回总价
}
```

## type
* 定义别名
返回int
```
func main() {
    type MyInt = int
    var x MyInt = 1
    fmt.Printf("%T", x)
}
```
返回MyInt
```
func main() {
    type MyInt int
    var x MyInt = 1
    fmt.Printf("%T", x)
}
```

## 接口interface
### 说明
* struct 实现了接口定义的方法，这个struct就是多态
* struc是具象的，interface是抽象的
* interface不能实例化
* struct完成组合接口 1.接口支持组合 -- 继承 2.结构体组合实现了所有的接口方法也可以
* go语言本身也推荐鸭子🦆类型
* 空接口interface{}
* 接口的结尾一般以er结尾


### error是interface
```
func main() {
    // var err error = errors.New("错误")
    s := "文件不存在"
    var err error = fmt.Errorf("错误:%s", s)
    fmt.Println(err)
}
```

### 断言，类型推断
简单例子
```
if v, ok := x.(int); ok {
    fmt.Printf("%d(整数)", v)
}
```
使用switch来判断
```
func print(x interface{}){
    switch v := x.(type){
    case string:
        fmt.Printf("%s(字符串)\n", v)
    case int:
        fmt.Printf("%d(数字)\n", v)
    }
}

func main() {
    i := 10
    print(i)
    s := "bobby"
    print(s)
}
```

## package包
### 包说明
* import "A/b"  import后面的是包路径，A这个路径是根据go.mod中module的定义
* b目录下文件c.go中内容如下，使用方法b2.Add()
```go
package b2
func Add(x, y int) int {
    return x + y
}
```
* 同一文件夹下的go文件，开头定义的package要一样

### import方法
* import "fmt"   # 使用方法fmt.Println()
* import . "fmt"  # 导入fmt下所有的内容，使用时候不接fmt，比如Println()
* import fmt_alias "fmt"  # 别名，使用方法fmt_alias.Println()
* import _ "fmt"  # 匿名引用，定义了不使用




