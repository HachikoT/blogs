- [channel](#channel)
  - [创建channel](#创建channel)
  - [close channel](#close-channel)
  - [channel复用](#channel复用)

# channel

## 创建channel

可以通过`make`函数来创建channel，channel是引用类型，空值为`nil`。

```go
ch := make(chan int)
```

## close channel

使用`close`函数关闭channel之后，后续的发送调用都会`panic`，后续的接收调用都会收到空值，可以用下面的语法判断channel是否被关闭。

```go
value, ok := <- ch
```

使用`for range`语法可以自动判断channel是否已经关闭

```go
func main() {
	naturals := make(chan int)
	squares := make(chan int)
	// Counter
	go func() {
		for x := 0; x < 100; x++ {
			naturals <- x
		}
		close(naturals)
	}()
	// Squarer
	go func() {
		for x := range naturals {
			squares <- x * x
		}
		close(squares)
	}()
	// Printer (in main goroutine)
	for x := range squares {
		fmt.Println(x)
	}
}
```

当`close`一个已经close的channel，或者`nil` channel时，都会引起`panic`。

## channel复用

go中提供`select`语法，可以对channel进行复用。

```go
func main() {
	ch := make(chan int, 1)
	for {
		select {
		case ch <- 0:
		case ch <- 1:
		}
		i := <-ch
		fmt.Println(i)
	}
}
```

`select`随机选择一个就绪的可读或者可写的channel执行，如果都没有就绪就会陷入等待，上面的代码会随机输出0和1的序列。
