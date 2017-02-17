## 基本类型
在Kotlin中，万物都是对象，因此我们可以调用任何对象的成员函数和属性。其中一些类型是内置的，因为他们的实现经过优化，在用户看起来就像普通的类。在这节中，将会提到这些类中的大多数：数字，字符，布尔值，数组等等。

 
#### Numbers
Kotlin处理数字的方式和Java近似，但不完全相同。例如，Kotlin不会通过隐式转换来拓宽数字的精度，并且在一些情况下字面值会略有不同
Kotlin提供了如下几种内置类型的数字（和Java近似）：

| Type| Bit width|  
| :-------------: |:-------------:|
| Byte      | 8|
| Short    | 16|
| Int        | 32 |
| Long   | 64 |
| Float   | 32 |
|Double| 64 |

Note:字符在 Kotlin 中不是数值类型

###### 字面值常量
有以下几种字面值常量：
* 十进制：`123`,`Long`型用大写`L`标记，`123L`
* 十六进制：`0x0f`
* 二进制：`0b00001011`
Note: 不支持八进制

Kotlin也支持传统的浮点数表示方法：
* 默认是`Double`类型：`123.5`, `123.5e10`
* `Float`类型要用`f`或`F`标记：`123.5f`

######表示
在Java平台，数值被作为JVM中原是数据类型物理存储，除非我们需要一个可空值的数值引用（如`Int?`）或者泛型相关，或者会将数字装箱
Note： 数字装箱不会保留同一性
```
val a: Int = 10000
print(a === a) // 打印 'true'
val boxedA: Int? = a
val anotherBoxedA: Int? = a
print(boxedA === anotherBoxedA) // !!!打印 'false'!!!  不是同一个对象
```
另一方面，它保持值相等
```
val a: Int = 10000
print(a == a) // Prints 'true'
val boxedA: Int? = a
val anotherBoxedA: Int? = a
print(boxedA == anotherBoxedA) // Prints 'true'
```
 
#### 显示转换
由于不同的表示，小类型并不是大类型的子类型，如果是的话，将会出现下列问题：
```
// 伪代码，实际不能通过编译
val a: Int? = 1 // 一个装箱后的Int (java.lang.Integer)
val b: Long? = a //隐式转变为一个装箱后的Long(java.lang.Long)
print(a == b) // Surprise!打印了false，因为Long的`equals`方法是用来检测另一部分是否也为Long的
```
因此不止同一性，甚至相等性都将丢失，
所以，小类型是不可以隐式转换为大类型的，这就意味着除了显示转换，我们无法将一个`Byte`类型的值赋给一个`Int`类型 的变量
```
val b: Byte = 1 // OK, 字面值是静态检测的
val i: Int = b // ERROR
```
我们可以通过显示转换来拓宽数字精度
```
val i: Int = b.toInt() // OK: explicitly widened
```
每个数字类型都支持如下转换：
* toByte(): Byte
* toShort(): Short
* toInt(): Int
* toLong(): Long
* toFloat(): Float
* toDouble(): Double
* toChar(): Char

隐式转换的缺少一般并不容易被发现，因为可以从上下文推断类型，并且算术运算会做适当的转换，例如：
```
val l = 1L + 3 // Long + Int => Long
```
 
#### 运算
Kotlin支持标准的数字运算，这些运算符被看做合适的类的成员（但是编译器将函数调用优化为相应的指令）。 See [Operator overloading]()
对于位运算，Kotlin中没有特殊的字符来表示，而是提供了以中缀形式命名的函数，如下所示：
```
val x = (1 shl 2) and 0x000FF000
```
下面是全部的位运算操作符（只可用于`Int`和`Long`）：
* shl(bits) – signed shift left (Java's <<)   有符号左移
* shr(bits) – signed shift right (Java's >>) 有符号右移
* ushr(bits) – unsigned shift right (Java's >>>) 无符号右移
* and(bits) – bitwise and 与
* or(bits) – bitwise or      或
* xor(bits) – bitwise xor  异或
* inv() – bitwise inversion  非

#### 字符
字符用`Char`类型表示，不可以直接当做数字使用
```
fun check(c: Char) {
    if (c == 1) { // ERROR: incompatible types
        // ...
    }
}
```
字符的字面值通常是单一的引用，如 `'1'`，特殊字符可以通过反斜杠转义，如下字符支持转义：`\t`, `\b`, `\n`, `\r`, `\'`, `\"`, `\\\\, `\$。编译其他字符，使用Unicode转移语法，`\uFF00`
我们可以把字符显式转换为`Int`
```
fun decimalDigitValue(c: Char): Int {
    if (c !in '0'..'9')
        throw IllegalArgumentException("Out of range")
    return c.toInt() - '0'.toInt() // Explicit conversions to numbers
}
```
和数字一样，当需要一个可空引用时，字符会自动装箱，装箱后同一性不会保留

#### 布尔值
布尔值用`Boolean`类型表示，只有`true`和`false`两个值
当需要一个可空引用时，布尔值会被装箱
布尔值的内置运算有：
* || – lazy disjunction
* && – lazy conjunction
* ! - negation

#### 数组
数组用`Array`类型表示，拥有`set` `get`方法（通过运算重载可以转变为`[]`），`size`属性，还有一些有用的成员函数：
```
class Array<T> private constructor() {
    val size: Int
    fun get(index: Int): T
    fun set(index: Int, value: T): Unit

    fun iterator(): Iterator<T>
    // ...
}
```
我们使用库函数`arrayOf()`来创建数组并传值进去，例如使用`arrayOf(1,2,3)`可以创建数组`[1,2,3]`。库函数`arrayOfNulls()`可以创建一个指定大小的空数组
另一种创建方式是使用工厂函数`Array()`，这个函数需要两个参数，数组大小和一个函数来返回每个元素的值，如下:
```
// Creates an Array<String> with values ["0", "1", "4", "9", "16"]
val asc = Array(5, { i -> (i * i).toString() })
```
如上面说过的，`[]`运算符代表调用了`set` `get`方法
Note: 与Java不同，Kotlin中数组是不可变的，这就意味着我们不能将`Array<String>`转化为`Array<Any>`，以防止可能的运行时错误（但是可以使用`Array<out Any>`） see [Type Projections]()
Kotlin有专门的类来表示原始数据类型的数组，避免过度装箱，`ByteArray`, `ShortArray`, `IntArray`等等。这些类和`Array`类没有继承关系，但是和`Array`类具有同样的方法和属性，另外都具有相应的工厂方法
```
val x: IntArray = intArrayOf(1, 2, 3)
x[0] = x[1] + x[2]
```
 
#### 字符串
字符串由`String`类表示，`String`是不可变的，它的每一个字符元素可以通过下标获取：`s[i]`，字符串可以通过for循环迭代：
```
for (c in str) {
    println(c)
}
```

###### 字符串字面值
Kotlin有两种字符串字面值，转义字符串和原始字符串。转义字符串含有转义字符。原始字符串可以包含换行和任意文本
```
val s = "Hello, world!\n"
```
转义以反斜杠形式，See [Characters]() above for the list of supported escape sequences.

原始字符串由`'''`界定，不包含转义字符，可以包含换行和其他文本
```
val text = """
    for (c in "foo")
        print(c)
"""
```
你可以使用` trimMargin()`方法移除字符开头的空格
```
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```
By default `|` is used as margin prefix, but you can choose another character and pass it as a parameter, like `trimMargin(">")`

###### 字符模板
字符串可以包含模板表达式，即一些求值的代码段并且求值结果会被拼接到字符串中。模板表达式以美元符`$`开头，并且包含一个简单的名字：
```
val i = 10
val s = "i = $i" // evaluates to "i = 10"
```
或者在花括号中包含任意表达式：
```
val s = "abc"
val str = "$s.length is ${s.length}" // evaluates to "abc.length is 3"
```
在原始字符串和转义字符串内部都支持模板表达式。你可以使用以下表达式在原始字符串中代表美元符（转义字符串中不支持）
```
val price = """
${'$'}9.99
"""
```

> 有任何疑问，欢迎加群讨论：261386924
