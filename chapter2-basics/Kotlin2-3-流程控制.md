## 流程控制

#### `if`表达式
在Kotlin中，`if`是一个表达式，它返回一个值，因此没有三目运算符（`condition ? then : else`），通常`if`就可以胜任这个角色
```
// 通常用法
var max = a 
if (a < b) max = b

// With else 
var max: Int
if (a > b) {
    max = a
} else {
    max = b
}
 
// 作为表达式
val max = if (a > b) a else b
```

`if`的分之可以是代码块，代码块的最后一个表达式作为返回值
```
val max = if (a > b) {
    print("Choose a")
    a
} else {
    print("Choose b")
    b
}
```
如果你把`if`作为一个表达式而不是statement（返回值或者赋值给变量），就必须有`else`分支


#### `When`表达式
`when`代替了类C语言的`switch`操作符，最简单的形式如下：
```
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> { // Note the block
        print("x is neither 1 nor 2")
    }
}
```
`when`会依次匹配分支，直到有合适的分支可以匹配。
`when`可以作为表达式，也可以作为语句。如果被当做表达式，符合条件的分支的值就是表达式的值。如果被当做语句，个别分支的值会被忽略（就像`if`，每一个分支可以是一个代码块，代码块的最后一个表达式是这个分支的值）

如何没有任何符合条件的分支，就会匹配`else`分支。如果`when`被用作表达式，`else`是必须有的，除非编译器认为所有可能出现的情况都在分支中

如果一些情况可以以相同方式处理，可以将分支条件用逗号`,`合并：
```
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```

我们可以使用任意表达式（并不仅仅是常量）作为分支条件：
```
when (x) {
    parseInt(s) -> print("s encodes x")
    else -> print("s does not encode x")
}
```

我们也可以检测一个值存在或者不存在一个范围或集合中：
```
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
```

另一种用法是检测某个值是否是某类型。由于类型智能转换，你不需要额外的检测就可以访问类型的方法和属性
```
val hasPrefix = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}
```

`when`可以 代替`if-else-if`，如果不提供参数，分支的条件就是简单的布尔值表达式，条件为true时就执行这个分支
```
when {
    x.isOdd() -> print("x is odd")
    x.isEven() -> print("x is even")
    else -> print("x is funny")
}
```

#### for循环
`for`可以对任何提供迭代器的对象进行迭代，语法如下：
```
for (item in collection) print(item)
```
循环体可以是代码块：
```
for (item: Int in ints) {
    // ...
}
```

正如前面所提到的，`for`可以对任何提供迭代器的对象进行迭代，迭代器满足下面几个条件：
* 有一个成员或者扩展函数`iterator()`,返回类型
* 有一个成员或者扩展函数`next()`
* 有一个成员或者扩展函数`hasNext()`,返回布尔值
上面三个方法都需要标记为`opeator`

数组的循环是基于下标的循环，并不会创建迭代器对象
如果你想遍历一个数组或者集合，如下：
```
for (i in array.indices) {
    print(array[i])
}
```
这样遍历一个范围是优化的实现方式，不会创造额外的对象

你也可以使用库函数`withIndex `
```
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```

#### `while`循环
`while`和`do...while`照常工作：
```
while (x > 0) {
    x--
}

do {
    val y = retrieveData()
} while (y != null) // y is visible here!
```

#### 循环中的`Break`和`Continue`
Kotlin支持传统的`break`和`continue`

> 有任何疑问，欢迎加群讨论：261386924
