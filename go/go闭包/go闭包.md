# go闭包

函数在Golang中是“一等公民”，“一等公民”意味着函数可以作为对象赋值，作为参数传递，作为返回值返回。
Go语言中闭包是引用了自由变量的函数，被引用的自由变量和函数一同存在，即使已经离开了自由变量的环境也不会被释放或者删除，在闭包中可以继续使用这个自由变量，因此，简单的说：

```text
函数 + 引用环境 = 闭包
```

举例：

```go
package main

import "fmt"

func getSequence() func() int {
   i:=0
   return func() int {
      i+=1
     return i  
   }
}

func main(){
   nextNumber := getSequence()  

   fmt.Println(nextNumber())    // 1
   fmt.Println(nextNumber())    // 2
   fmt.Println(nextNumber())    // 3
   
   nextNumber1 := getSequence()
   fmt.Println(nextNumber1())   // 1
   fmt.Println(nextNumber1())   // 2
}
```

和C++中通过lambda语法来手动捕获当前执行上下文中的变量不同，go语言是自动捕获当前函数中的变量。需要注意的是，在Go语言中，函数参数都是以复制的方式(不支持以引用的方式)传递，但是**Go语言闭包函数对外部变量是以引用的方式使用**。

# 参考资料

- [Go语言闭包](http://c.biancheng.net/view/59.html)
- [Go 语言函数闭包](https://www.runoob.com/go/go-function-closures.html)
