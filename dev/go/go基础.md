# go基础

## Go系列教程
[Go系列教程](https://studygolang.com/subject/2)

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

