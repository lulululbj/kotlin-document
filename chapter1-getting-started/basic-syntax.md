## 基本语法

### 包

包名在源文件顶部定义 ：

```
package my.demo

import java.util.*

// ...
```

包名不需要和目录相匹配，源文件可以存在文件系统的任何位置。

See [Packages]()
 
### 函数

两个 `Int` 参数，返回值是 `Int` 的函数 ：

```
fun sum(a: Int, b: Int): Int {
    return a + b
}
```

表达式作为函数体，自动推断返回值类型的函数 ：

```
fun sum(a: Int, b: Int) = a + b
```

返回无意义值的函数 ：

```
fun printSum(a: Int, b: Int): Unit {
    println("sum of $a and $b is ${a + b}")
}
```

返回类型是 `Unit` 时，可省略 ：

```
fun printSum(a: Int, b: Int) {
    println("sum of $a and $b is ${a + b}")
}
```

See [Functions]()
 
### 局部变量

常量( `assign-once` `read-only` ) ：

```
val a: Int = 1
val b = 1   // 自动推断 Int 类型
val c: Int  // 没有提供初始值，必须声明类型
c = 1       // 延迟赋值
```

变量 ：

```
var x = 5 // 自动推断 Int 类型
x += 1
```

See [Properties And Fields]()
 
### 注释

和 Java , JacaScript 一样，Kotlin 也支持行注释和块注释 ：

```
// This is an end-of-line comment

/* This is a block comment
   on multiple lines. */
```

与 Java 不同的是，Kotlin 的块注释可以嵌套。

See [Documenting Kotlin Code]() 了解文档注释语法
 
### 使用字符串模板

```
var a = 1
// simple name in template:
val s1 = "a is $a"

a = 2
// arbitrary expression in template:
val s2 = "${s1.replace("is", "was")}, but now is $a"
```
See [String templates]()
 
### 使用条件表达式

```
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```

使用 `if` 作为表达式 ：

```
fun maxOf(a: Int, b: Int) = if (a > b) a else b
```

See [*if*-expressions]()
 
### 使用可空变量以及空值检查

当引用可能为空时必须显式标识这个引用是可空的。

如果 `str` 不是数字，则返回空 ：

```
fun parseInt(str: String): Int? {
    // ...
}
```

返回可空值的函数 ：

```
fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)

    // 直接使用 `x * y` 可能会报错，因为它们可能持有空值
    if (x != null && y != null) {
        // x , y 经过空值检查后会自动转化为非空值
        println(x * y)
    }
    else {
        println("either '$arg1' or '$arg2' is not a number")
    }    
}
```
或者
```
  // ...
    if (x == null) {
        print("Wrong number format in '${args[0]}'")
        return
    }
    if (y == null) {
        print("Wrong number format in '${args[1]}'")
        return
    }

    // x , y经过空值检查后会自动转化为非空值
    print(x * y)
```

See [Null-safety]()
 
### 使用类型检测和自动类型转换

`is` 操作符用来检测某个表达式是否是某个类型的实例。如果一个不可变的局部变量或者属性值经过类型检测为特定类型后，就没有必要再显示转换 ：

```
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // obj 自动转换为 `String` 类型
        return obj.length
    }

    //在类型检查分支外，obj仍然是 `Any` 类型
    return null
}
```
or

```
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // obj 自动转换为 `String` 类型
    return obj.length
}
```

or even

```
fun getStringLength(obj: Any): Int? {
    // 在`&&`之后的语句中，obj已经自动转化为 `String` 类型
    if (obj is String && obj.length > 0) {
        return obj.length
    }

    return null
}
```
See [Classes]() and [Type casts]()
 
### 使用for循环

```
val items = listOf("apple", "banana", "kiwi")
for (item in items) {
    println(item)
}
```
or

```
val items = listOf("apple", "banana", "kiwi")
for (index in items.indices) {
    println("item at $index is ${items[index]}")
}
```
See [for loop]()
 
### 使用while循环

```
val items = listOf("apple", "banana", "kiwi")
var index = 0
while (index < items.size) {
    println("item at $index is ${items[index]}")
    index++
}
```
See [while loop]()
 
### 使用when表达式

```
fun describe(obj: Any): String =
when (obj) {
    1          -> "One"
    "Hello"    -> "Greeting"
    is Long    -> "Long"
    !is String -> "Not a string"
    else       -> "Unknown"
}
```
See [when expression]()
 
### 使用 ranges

使用 `in` 操作符检测某个数字是否在指定范围内 ：

```
val x = 10
val y = 9
if (x in 1..y+1) {
    println("fits in range")
}
```

判断数字是否在指定范围之外 ：

```
val list = listOf("a", "b", "c")

if (-1 !in 0..list.lastIndex) {
    println("-1 is out of range")
}
if (list.size !in list.indices) {
    println("list size is out of valid list indices range too")
}
```

遍历 ：

```
for(x in 1..5){
  print(x)
}
```

over a progression ：

```
for (x in 1..10 step 2) {
    print(x)
}
for (x in 9 downTo 0 step 3) {
    print(x)
}
```
See [Ranges]()
 
### 使用集合

迭代一个集合 ：

```
for(name in names){
    println(name)
}
```

使用 `in` 操作符判断一个对象是否在集合中 ：

```
when {
    "orange" in items -> println("juicy")
    "apple" in items -> println("apple is fine too")
}
```

使用 lambda 表达式来过滤（filter）和变换（map）集合 ：
```
fruits
.filter { it.startsWith("a") }
.sortedBy { it }
.map { it.toUpperCase() }
.forEach { println(it) }
```
See [Higher-order functions and Lambdas]()

### 创建类和实例 ：  

```
val rectangle = Rectangle(5.0, 2.0) //no 'new' keyword required
val triangle = Triangle(3.0, 4.0, 5.0)
```

See [classes]() and [objects and instances]()



> update at 2017/10/25  
> 有任何疑问，欢迎加群讨论：261386924
