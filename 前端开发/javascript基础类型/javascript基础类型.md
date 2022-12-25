- [变量声明](#变量声明)
- [number型](#number型)
- [字符串型](#字符串型)
- [数组](#数组)
- [对象](#对象)
- [参考资料](#参考资料)

# 变量声明

在javascript中变量声明不用指定类型，可以容纳任意类型的值。

```html
    <script>
        var age = 10;
        age = "haha"
    </script>
```

声明未定义的变量是undefined类型。还有特殊的null类型。

```html
    <script>
        var age;
        console.log(age) // undefined
        age = null
        console.log(age) // null
    </script>
```

# number型

javascript不区分浮点型和整型，都是属于同一的数值型，和其他语言一样不能直接比较浮点数是否相等。

```html
    <script>
        console.log(Math.abs(1 / 3 - (1 - 2 / 3)) < 0.000001) // true
    </script>
```

有两个特殊的数值：

- NaN：任何一个数字都不是NaN，包括NaN自身。
- Infinity：无穷大值。

# 字符串型

```html
    <script>
        var name = 'jack'
        console.log(name)
    </script>
```

可以用加号拼接字符串，可以直接加数字。

```html
    <script>
        var name = 'jack'
        var id = 1
        console.log('hello ' + name + id) // hello jack1
    </script>
```

用`length`可以获取字符串的长度。

```html
    <script>
        'use strict';

        var hello = "hello world";
        console.log(hello.length); // 11
    </script>
```

截取子字符串。

```html
    <script>
        'use strict';

        var hello = "hello world";
        console.log(hello.substring(0, 5)); // hello
    </script>
```

# 数组

javascript的数组不用类型一致，非法下表获取的元素为undefined类型。

```html
    <script>
        var arr = [1, 2, "hello", null, 5]
        console.log(arr[0]) // 1
        console.log(arr[5]) // undefined
    </script>
```

可以直接加减数组的length属性来变更数组大小。

```html
    <script>
        'use strict';

        var arr = [1, 2, 3, 4];
        arr.length += 3
        console.log(arr) // [1, 2, 3, 4, empty x 3]
    </script>
```

截取子数组。

```html
    <script>
        'use strict';

        var arr = [1, 2, 3, 4, 5, 6, 7];
        var sub = arr.slice(0, 3)
        sub[0] = 0
        console.log(arr) // [1, 2, 3, 4, 5, 6, 7]
        console.log(sub) // [0, 2, 3]
    </script>
```

# 对象

可以为对象定义成员名字，构成一个复合的对象

```html
    <script>
        var person = {
            name: "hello",
            age: 10
        }
        console.log(person)
    </script>
```

可以动态增删成员。

```html
    <script>
        'use strict';

        var person = {
            name: "hello",
            age: 10
        }

        delete person.age;
        console.log(person); // {name: 'hello'}
        person.age = 10;
        console.log(person); // {name: 'hello', age: 10}
    </script>
```

查询是否包含成员。

```html
    <script>
        'use strict';

        var person = {
            name: "hello",
            age: 10
        }

        console.log('name' in person) // true
        console.log('toString' in person) // true，继承

        console.log(person.hasOwnProperty('toString')) // false
    </script>
```

# 参考资料
