- [for循环](#for循环)
  - [条件判断](#条件判断)
  - [完整的`for`语句](#完整的for语句)
  - [`for range`语法](#for-range语法)
- [参考资料](#参考资料)

# for循环

在go中，将c语言的`for`和`while`语法都吸收了，统一用`for`循环。并且支持更便捷的`for range`语法。

一共有3种`for`语法：

1. 条件判断（替代`while`）：`for [condition] {}`。
2. 完整的`for`语法：`for [initStmt]; [condition]; [postStmt] {}`。
3. `for range`语法：`for [expressionList = | identifierList :=] range expression {}`。

## 条件判断

实现和`while`一样的效果，只判断条件。

```go
for n != 0 {
    ...
}
```

条件可以省略，变为死循环。

```go
for {
    ...
}
```

## 完整的`for`语句

也是常用的形式。

```go
for i := 0; i < 10; i++ {
    ...
}
```

初始语句，判断条件和后面的迭代步骤都可以选择省略。

```go
for ; i < 10; i++ {
    ...
}
```

## `for range`语法

`for range`语法是按照key，value的形式来获取集合的元素的，支持字符串，数组，切片，`map`和`channel`。

- 对于字符串，数组，切片。返回的是key是下标，value是元素值。
- 对于`map`。返回的就是`map`里对应的k，v值。
- 对于`channel`只返回一个值，即通道内的数据，直到通道被关闭。

下面是使用示例。

```go
for k, v := range []int{1, 2, 3} {
    ...
}

for k := range []int{1, 2, 3} {
    ...
}

for range []int{1, 2, 3} {
    ...
}
```

# 参考资料

- [golang for语句完全指南](https://blog.csdn.net/jfkidear/article/details/89813758)
