# 基本数据类型是可比较的

> Boolean values are comparable. Two boolean values are equal if they are either both true or both false.

布尔值是可比较的。 如果两个布尔值均为true或false则相等。 

> Integer values are comparable and ordered, in the usual way.

整数值是可比较且有序的。

> Floating point values are comparable and ordered, as defined by the IEEE-754 standard.

浮点值是可比较的且有序的，如IEEE-754标准所定义。

> Complex values are comparable. Two complex values u and v are equal if both real(u) == real(v) and imag(u) == imag(v).

复数值是可比较的。 如果`real(u) == real(v) and imag(u)== imag(v)`，则两个复数值u和v相等。

> String values are comparable and ordered, lexically byte-wise.

字符串值是可比较的且按字节顺序排序。

# 指针，通道，接口是可比较的

> Pointer values are comparable. Two pointer values are equal if they point to the same variable or if both have value nil. Pointers to distinct zero-size variables may or may not be equal.

指针值是可比较的。 如果两个指针值指向相同的变量，或者两个指针的值均为nil则它们相等。 指向不同的大小为0的变量的指针可能相等也可能不相等。

> Channel values are comparable. Two channel values are equal if they were created by the same call to make or if both have value nil.

通道值是可比较的。 如果两个通道值是由相同的make调用创建的，或者两个值都为nil则它们是相等的。 

> Interface values are comparable. Two interface values are equal if they have identical dynamic types and equal dynamic values or if both have value nil.

接口值是可比较的。如果两个接口值具有相同的动态类型并且动态值是可比较的并且动态值相等，或者两个接口值都为nil则它们相等。

> Struct values are comparable if all their fields are comparable. Two struct values are equal if their corresponding non-blank fields are equal.

如果结构的所有字段都是可比较的，则它们的值是可比较的。 如果两个结构值对应的非空白字段相等，则它们相等。

> Array values are comparable if values of the array element type are comparable. Two array values are equal if their corresponding elements are equal.

如果数组元素类型的值可比较，则数组值可比较。 如果两个数组的对应元素相等，则它们相等。

# 函数，切片，字典只能和nil比较

> Function values, Slice values and Map values are not comparable, they can only be compared with nil, as a special case.

函数值， Slice值和Map值不可比较，在特殊情况下只能与nil进行比较。 

# 参考资料

- [golang两切片的值比较_比较Go值](https://blog.csdn.net/cuk0051/article/details/108341131)
