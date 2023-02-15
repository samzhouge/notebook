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

