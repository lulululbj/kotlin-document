## 函数

#### 函数声明

在Kotlin中使用`fun`关键字声明方法
```
fun double(x: Int): Int {
}
```

#### 函数用法
使用传统方法调用函数：
```
val result = double(2)
```
使用`.`调用成员函数
```
Sample().foo() // create instance of class Sample and calls foo
```
###### 中缀符号
在下列情况中，函数也可以使用中缀符号调用：

* 成员函数或者扩展函数
* 只有一个参数
* 用`infix`关键字标记

```
// Define extension to Int
infix fun Int.shl(x: Int): Int {
...
}

// call extension function using infix notation

1 shl 2

// is the same as

1.shl(2)
```

###### 参数
函数参数使用Pascal符号定义，即`name:type`，参数之间使用逗号分隔，每个参数必须显示指定类型
```
fun powerOf(number: Int, exponent: Int) {
...
}
```

###### 默认参数
函数参数可以有默认值，在对应参数被省略时使用。和其他语言相比，减少了重载
```
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size()) {
...
}
```
默认值在类型后使用`=`定义

重写的方法和基本方法使用同样的默认参数值。当使用默认参数值重写一个方法的时候，默认参数值必须在签名中省略：
```
open class A {
    open fun foo(i: Int = 10) { ... }
}

class B : A() {
    override fun foo(i: Int) { ... }  // no default value allowed
}
```

###### 命名参数
函数参数可以再函数被调用时命名。当函数有大量参数或者默认参数的时候会很方便

看下面这个函数：
```
fun reformat(str: String,
             normalizeCase: Boolean = true,
             upperCaseFirstLetter: Boolean = true,
             divideByCamelHumps: Boolean = false,
             wordSeparator: Char = ' ') {
...
}
```
我们可以使用默认参数调用它：
```
reformat(str)
```
但是，当我们使用非默认参数调用时，代码是这样的：
```
reformat(str,
    normalizeCase = true,
    upperCaseFirstLetter = true,
    divideByCamelHumps = false,
    wordSeparator = '_'
)
```
如果不需要所以参数的话
```
reformat(str, wordSeparator = '_')
```
当调用Java函数的时候不可以使用命名参数的语法，因为Java的字节码不能保护函数参数的名字

###### 返回`Unit`的函数
如果一个函数不返回任何有意义的值，它的返回类型就是`Unit`。`Unit`类型仅有一个值`Unit`，这个值不需要显示返回
```
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello ${name}")
    else
        println("Hi there!")
    // `return Unit` or `return` is optional
}
```
`Unit`返回类型的声明也是可选的，上面代码和如下等价：
```
fun printHello(name: String?) {
    ...
}
```

###### 单一表达式函数
当一个函数返回一个单一的表达式，花括号可以省略，函数体直接跟在`=`后面
```
fun double(x: Int): Int = x * 2
```
如果编译器可以自动推断，显示声明返回类型也是可选的
```
fun double(x: Int) = x * 2
```
###### 显示返回类型
带有程序块体的函数必须显式指定返回类型，除非它本身就是用来返回`Unit`的，这种情况下可以省略。Kotlin不会推断带有程序块体的函数的返回类型，因为这种函数在函数体中可能有复杂的控制流程，并且返回类型可能对读者来说是不明显的（有时甚至对于编译器也是这样）

###### 可变参数
函数的参数（通常是最后一个）可以用`vararg `修饰符标记:
```
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts is an Array
        result.add(t)
    return result
}
```
允许给函数传入一个可变参数：
```
val list = asList(1, 2, 3)
```
在函数中，一个`T`类型的`vararg`参数被看成`T`的一个集合，即上面例子中的`ts`变量是`Array<out T>`类型的

只有一个参数可以被`vararg`标记。如果`vararg`参数不是最后一个的话，后面的参数的值可以使用命名参数语法传入，如果参数是函数类型，就需要在括号外传入lambda表达式

当我们调用一个`vararg`函数，可以一个接一个地传入参数，例如`asList(1, 2, 3)`。或者我们已经有了一个数组，想把它的内容传给函数，可以使用`spread`操作符（数组加前缀`*`）：
```
val a = arrayOf(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```

#### 函数作用域
在Kotlin中函数可以声明在文件的顶部，这就意味着你不需要像Java，C#，Scala那样创建一个类来持有函数。除此之外，Kotlin函数还可以被声明为局部的，成员函数和扩展函数

###### 局部函数
Kotlin支持局部函数，例如函数中嵌套函数
```
fun dfs(graph: Graph) {
    fun dfs(current: Vertex, visited: Set<Vertex>) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v, visited)
    }

    dfs(graph.vertices[0], HashSet())
}
```
局部函数可以访问外部函数的局部变量（即闭包），下面的`visited`就是一个局部变量：
```
fun dfs(graph: Graph) {
    val visited = HashSet<Vertex>()
    fun dfs(current: Vertex) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v)
    }

    dfs(graph.vertices[0])
}
```

###### 成员函数
成员函数是定义在类中或对象中的
```
class Sample() {
    fun foo() { print("Foo") }
}
```
成员函数直接用`.`调用
```
Sample().foo() // creates instance of class Sample and calls foo
```
For more information on classes and overriding members see [Classes](http://kotlinlang.org/docs/reference/classes.html) and [Inheritance](http://kotlinlang.org/docs/reference/classes.html#inheritance)

#### 泛型函数
泛型函数具有泛型参数，泛型参数在函数名前以尖括号指定
```
fun <T> singletonList(item: T): List<T> {
    // ...
}
```
For more information on generic functions see [Generics](http://kotlinlang.org/docs/reference/generics.html)

#### 内联函数
Inline functions are explained [here](http://kotlinlang.org/docs/reference/inline-functions.html)

#### 扩展函数
Extension functions are explained in [their own section](http://kotlinlang.org/docs/reference/extensions.html)

#### 高阶函数和Lambda表达式
Higher-Order functions and Lambdas are explained in [their own section](http://kotlinlang.org/docs/reference/lambdas.html)

#### 尾部递归函数
Kotlin支持函数式编程的一种形式，尾部递归。这允许一些本来使用循环的算法使用递归函数来代替，从而降低了堆栈溢出的风险。当一个函数被`tailrec`修饰符标记并且满足编译器优化递归的需要，用迅速高效的循环代替
```
tailrec fun findFixPoint(x: Double = 1.0): Double
        = if (x == Math.cos(x)) x else findFixPoint(Math.cos(x))
```
这段代码是用来计算余弦定点的，是一个数学常量。它简单的从1.0开始重复调用Math.cos，直到结果不再变化，结果是0.7390851332151607。和下面的传统形式是等价的：
```
private fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = Math.cos(x)
        if (x == y) return y
        x = y
    }
}
```
为了使`tailrec`符合条件，函数必须在最后一个操作中调用自己，当调用递归之后还有任何代码的时候你就不能使用尾部递归。你也不可以在`try/catch/finally`块中使用。目前，尾部递归只支持JVM后端
