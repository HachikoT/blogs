- [context](#context)
  - [emptyCtx](#emptyctx)
  - [valueCtx](#valuectx)
  - [cancelCtx](#cancelctx)
  - [timerCtx](#timerctx)
- [参考资料](#参考资料)

# context

`context`是go1.7之后引入的包，主要用于在多层级的子协程中控制流程和传递信息。
`context`的接口如下。

```go
type Context interface {
    // 返回绑定当前context的任务被取消的截止时间；如果没有设定期限，将返回ok == false。
    Deadline() (deadline time.Time, ok bool)

    // 当绑定当前context的任务被取消时，将返回一个关闭的channel；如果当前context不会被取消，将返回nil。
    Done() <-chan struct{}

    // 如果Done返回的channel没有关闭，将返回nil;如果Done返回的channel已经关闭，将返回非空的值表示任务结束的原因。
    Err() error

    // 返回context存储的键值对中当前key对应的值，如果没有对应的key,则返回nil。
    Value(key interface{}) interface{}
}

```

## emptyCtx

`emptyCtx`是一个`int`类型的变量，但实现了`context`的接口。`emptyCtx`没有超时时间，不能取消，也不能存储任何额外信息，所以`emptyCtx`用来作为`context`树的根节点。

```go
type emptyCtx int

func (*emptyCtx) Deadline() (deadline time.Time, ok bool) {
    return
}

func (*emptyCtx) Done() <-chan struct{} {
    return nil
}

func (*emptyCtx) Err() error {
    return nil
}

func (*emptyCtx) Value(key interface{}) interface{} {
    return nil
}

var (
    background = new(emptyCtx)
    todo       = new(emptyCtx)
)

func Background() Context {
    return background
}

func TODO() Context {
    return todo
}
```

`Background`通常被用于主函数、初始化以及测试中，作为一个顶层的`context`，也就是说一般我们创建的`context`都是基于`Background`；而`TODO`是在不确定使用什么`context`的时候才会使用。

## valueCtx

```go
type valueCtx struct {
    Context
    key, val interface{}
}

// 一层一层地往上找到key对应的value
func (c *valueCtx) Value(key interface{}) interface{} {
    if c.key == key {
        return c.val
    }
    return c.Context.Value(key)
}

```

`valueCtx`可以用来传递数据，源码的注释建议`key`的类型最好是自定义类型，不要用内建类型。避免别的包传过来的`key`和自己要设置的`key`冲突。

```go
package main

import (
	"context"
	"fmt"
	"time"
)

type KeyString string

func main() {
	valueCtx := context.WithValue(context.Background(), KeyString("name"), "rc")
	go func() {
		fmt.Println(valueCtx.Value(KeyString("name")))
	}()
	time.Sleep(1 * time.Second)
	fmt.Println("exit")
}
```

## cancelCtx

`cancelCtx`可以用来取消某一`context`树结点往下所有`cancelCtx`类型的结点。

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc) {
	if parent == nil {
		panic("cannot create context from nil parent")
	}
	c := newCancelCtx(parent)
	propagateCancel(parent, &c)
	return &c, func() { c.cancel(true, Canceled) }
}

func propagateCancel(parent Context, child canceler) {
	done := parent.Done()
	if done == nil {
		return // parent is never canceled
	}

	select {
	case <-done:
		// parent is already canceled
		child.cancel(false, parent.Err())
		return
	default:
	}

    // 一直往树根找第一个可以cancel的祖先结点，将自己添加到这个祖先结点的孩子当中，日后取消的时候可以通知到自己
    // 没找到返回false
	if p, ok := parentCancelCtx(parent); ok {
		p.mu.Lock()
		if p.err != nil {
			// parent has already been canceled
			child.cancel(false, p.err)
		} else {
			if p.children == nil {
				p.children = make(map[canceler]struct{})
			}
			p.children[child] = struct{}{}
		}
		p.mu.Unlock()
	} else {
		atomic.AddInt32(&goroutines, +1)
		go func() {
			select {
			case <-parent.Done():
				child.cancel(false, parent.Err())
			case <-child.Done():
			}
		}()
	}
}

func (c *cancelCtx) cancel(removeFromParent bool, err error) {
	if err == nil {
		panic("context: internal error: missing cancel error")
	}
	c.mu.Lock()
	if c.err != nil {
		c.mu.Unlock()
		return // already canceled
	}
	c.err = err
	if c.done == nil {
		c.done = closedchan
	} else {
		close(c.done)
	}
    // 遍历取消所有的后代结点
	for child := range c.children {
		// NOTE: acquiring the child's lock while holding parent's lock.
		child.cancel(false, err)
	}
	c.children = nil
	c.mu.Unlock()

	if removeFromParent {
		removeChild(c.Context, c)
	}
}
```

据个栗子。

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	cancelCtx1, cancelFunc1 := context.WithCancel(context.Background())
	go func() {
		cancelCtx2, _ := context.WithCancel(cancelCtx1)
		go func() {
			<-cancelCtx2.Done()
			fmt.Println("go 2 done")
		}()
        <-cancelCtx1.Done()
		fmt.Println("go 1 done")
	}()
	cancelFunc1()
	time.Sleep(1 * time.Second)
	fmt.Println("main exit")
}

```

## timerCtx

`timerCtx`支持到时间自动`cancel`。

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	cancelCtx1, _ := context.WithTimeout(context.Background(), 1*time.Second)
	go func() {
		cancelCtx2, _ := context.WithCancel(cancelCtx1)
		go func() {
			<-cancelCtx2.Done()
			fmt.Println("go 2 done")
		}()
		<-cancelCtx1.Done()
		fmt.Println("go 1 done")
	}()
	time.Sleep(2 * time.Second)
	fmt.Println("main exit")
}

```

# 参考资料

- [深入理解Golang之context](https://zhuanlan.zhihu.com/p/110085652)
