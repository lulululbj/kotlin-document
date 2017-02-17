## 基本语法
#### 定义包名

包名应该在源文件顶部定义

```
package my.demo

import java.util.*

// ...
```

包名不需要和路径匹配，源文件可以存在文件系统的任何位置

See [Packages]()
 
#### 定义方法
两个`Int`参数，返回值是`Int`的方法
```
fun sum(a: Int, b: Int): Int {
    return a + b
}
```

带有一个表达式，并自动推断返回值类型的方法
```
fun sum(a: Int, b: Int) = a + b
```

返回无意义值的方法
```
fun sun(a: Int,b: Int) : Unit {
    print(a+b)
}
```

当返回值是`Unit`时，可省略
```
fun printSum(a: Int, b: Int) {
    print(a + b)
}
```

See [Functions]()
 
####定义局部变量
常量
```
val a: Int = 1
val b = 1   // 自动推断Int类型
val c: Int  // 没有初始值，必须声明类型
c = 1       // 明确赋值
```
变量
```
var x = 5 // 自动推断Int类型
x += 1
```
See [Properties And Fields]()
 
####注释
就像Java和JacaScript，Kotlin也支持行注释和块注释
```
// This is an end-of-line comment

/* This is a block comment
   on multiple lines. */
```
与Java不同的是，Kotlin的块注释可以嵌套

See [Documenting Kotlin Code]() 了解文档注释的语法
 
####使用字符串模板
```
fun main(args: Array<String>) {
    if (args.size == 0) return

    print("First argument: ${args[0]}")
}
```
See [String templates]()
 
####使用条件表达式
```
fun max(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```
使用`if`作为表达式
```
fun max(a: Int,b: Int)=if (a>b) a else b 
```
See [*if*-expressions]()
 
####使用可空变量以及空值检查
当变量可能为空时必须明确标识这个引用是可空的

如果`str`不是数字，则返回空
```
fun parseInt(str: String): Int? {
    // ...
}
```

返回可空值的方法
```
fun main(args: Array<String>) {
    if (args.size < 2) {
        print("Two integers expected")
        return
    }

    val x = parseInt(args[0])
    val y = parseInt(args[1])

    // 直接使用x ，y可能会报错，因为想x,y可能持有空值
    if (x != null && y != null) {
        // x , y经过空值检查后会自动转化为非空值
        print(x * y)
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
 
####使用类型检测和自动类型转换
`is`操作符用来检测某个表达式是否是某个类型的实例。如果一个不可变的局部变量或者属性值经过类型检测为特定类型后，就没有必要再明确转换
```
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // 在这个分支中， obj 自动转换为`String`类型
        return obj.length
    }

    //在类型检查分支外，obj仍然是`Any`类型
    return null
}
```
or

```
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // 在这个分支中， obj 自动转换为`String`类型
    return obj.length
}
```

or even

```
fun getStringLength(obj: Any): Int? {
    // 在`&&`之后的语句中，obj已经自动转化为`String`类型
    if (obj is String && obj.length > 0) {
        return obj.length
    }

    return null
}
```
See [Classes]()
 
####使用for循环
```
fun main(args : Array<String>){
    for(arg in args){
        print(arg)
    }
}
```
or

```
for (i in args.indices) {
    print(args[i])
} 
```
See [for loop]()
 
####使用while循环
```
fun main(args : Array<String>){
    var i=0
    while(i<args.size){ 
       print(args[i++])   
    }
}
```
See [while loop]()
 
####使用when表达式
```
fun cases(obj: Any) {
    when (obj) {
        1          -> print("One")
        "Hello"    -> print("Greeting")
        is Long    -> print("Long")
        !is String -> print("Not a string")
        else       -> print("Unknown")
    }
}
```
See [when expression]()
 
####使用ranges
使用`in`操作符检测某个数字是否在指定范围内
```
if (x in 1..y-1) {
    print("OK")
}
```

判断数字是否在指定范围之外
```
if(x !in array.lastIndex){
   print("Out")
}
```
遍历
```
for(x in 1..5){
  print(x)
}
```
See [Ranges]()
 
####使用集合
迭代一个集合
```
for(name in names){
    println(name)
}
```

使用`in`操作符判断一个对象是否在集合中
```
if (text in names) //将会调用nemes.contains(text)方法
    print("Yes)
```

使用 lambda 表达式来过滤（filter）和变换（map）集合
```
names
        .filter { it.startsWith("A") }
        .sortedBy { it }
        .map { it.toUpperCase() }
        .forEach { print(it) }
```
See [Higher-order functions and Lambdas]()

> 有任何疑问，欢迎加群讨论：261386924
