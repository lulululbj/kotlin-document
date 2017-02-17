## 扩展
和`C#` `Gosu`类似，Kotlin也提供了为类扩展新功能的能力，不需要继承父类也不需要使用类似装饰模式的设计模式。这是通过一种叫`extensions`的特殊声明来实现的。Kotlin支持扩展函数和属性

#### 扩展函数
声明一个扩展函数，我们需要一个接收者类型（就是要扩展的类型）的名称作为前缀。下面是给`MutableList<Int>`添加一个`swap`函数：
```
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
```
扩展函数中的`this`关键字对应接收者对象（就是`.`之前的对象）。现在我们可以对任意`MutableList<Int>`对象调用`swap`方法：
```
val l = mutableListOf(1, 2, 3)
l.swap(0, 2) // 'this' inside 'swap()' will hold the value of 'l'
```
当然，这个方法对任意的`MutableList<T>`都是适用的，我们可以使它变得通用：
```
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
```
我们在函数名称之前声明了通用的参数类型，使得任何类型的参数表达式都是可用的
 See [Generic functions](http://kotlinlang.org/docs/reference/generics.html)

#### 扩展静态解析
扩展实际上没有修改他所扩展的类。定义一个扩展，你并没有在类中插入新的成员，仅仅是让类可以通过`.`调用新的函数
我们想强调扩展函数是静态分发的，they are not virtual by receiver type，这就意味着扩展函数的调用是由参与的函数的表达式类型决定的，而不是由编译时表达式的执行结果的类型决定的。例如：
```
open class C

class D: C()

fun C.foo() = "c"

fun D.foo() = "d"

fun printFoo(c: C) {
    println(c.foo())
}

printFoo(D())
```
结果会打印`c`。因为扩展函数的调用是由声明的参数类型`c`决定的
如果一个类有同样名称，同样接收类型的成员函数和扩展函数，在调用时，总是成员函数优先。例如：
```
class C {
    fun foo() { println("member") }
}

fun C.foo() { println("extension") }
```
对`C`的任意实例`c`调用`c.foo()`，将打印`member`而不是`extension`
但是对于相同名称，不同签名的情况，扩展函数会重写成员函数：
```
class C {
    fun foo() { println("member") }
}

fun C.foo(i: Int) { println("extension") }
```
调用`C().foo()`将会打印`extension`

#### 可空接收者
扩展可以使用可空接收者类型定义，这样的扩展，即使值为空的对象变量也可以调用它，并在扩展体内检查`this==null`。这就是为什么在Kotlin中无需非空检查就可以调用`toString()`，这个检查是发生在扩展函数内部的
```
fun Any?.toString(): String {
    if (this == null) return "null"
    // after the null check, 'this' is autocast to a non-null type, so the toString() below
    // resolves to the member function of the Any class
    return toString()
}
```

#### 扩展属性
和函数类似，Kotlin支持扩展属性：
```
val <T> List<T>.lastIndex: Int
    get() = size - 1
```
由于扩展实际上并没有向类中插入成员，所以扩展属性不能有备用字段。这就是为什么扩展属性不能初始化，扩展属性只能通过显式提供的getter和setter来定义，例如：
```
val Foo.bar = 1 // error: initializers are not allowed for extension properties
```

#### 扩展伴生对象
如果一个类定义了伴生对象，你就可以为这个伴生对象定义扩展函数或者属性
```
class MyClass {
    companion object { }  // will be called "Companion"
}

fun MyClass.Companion.foo() {
    // ...
}
```
和伴随成员的普通对象一样，可以直接用类名调用：
```
MyClass.foo()
```

#### 扩展的作用域
大多数情况我们在顶层定义扩展，直接在包内：
```
package foo.bar
 
fun Baz.goo() { ... } 
```
在声明的包外使用这个扩展，我们需要在调用的地方导入它：
```
package com.example.usage

import foo.bar.goo // importing all extensions by name "goo"
                   // or
import foo.bar.*   // importing everything from "foo.bar"

fun usage(baz: Baz) {
    baz.goo()
)
```

#### 将扩展作为成员声明
在一个类中，你可以为另一个类声明扩展。在这种扩展中，有多样的隐式接收者——不需要限定符就可以访问的对象成员。声明扩展的类实例成为分发接收者，扩展方法的接收类型实例称为扩展接收者
```
class D {
    fun bar() { ... }
}

class C {
    fun baz() { ... }

    fun D.foo() {
        bar()   // calls D.bar
        baz()   // calls C.baz
    }

    fun caller(d: D) {
        d.foo()   // call the extension function
    }
}
```
如果分发接收者和扩展接收者的成员存在命名冲突，扩展接收者优先。如果指的是分发接收者的成员，你可以使用这样的限定符语法：
```
class C {
    fun D.foo() {
        toString()         // calls D.toString()
        this@C.toString()  // calls C.toString()
    }
```

被声明为成员的扩展，可以用`open`标记，也可以被子类重写。这就意味着分发这样的函数对于分发接收者类型是虚拟的，但是对于扩展接收者类型时静态的
```
open class D {
}

class D1 : D() {
}

open class C {
    open fun D.foo() {
        println("D.foo in C")
    }

    open fun D1.foo() {
        println("D1.foo in C")
    }

    fun caller(d: D) {
        d.foo()   // call the extension function
    }
}

class C1 : C() {
    override fun D.foo() {
        println("D.foo in C1")
    }

    override fun D1.foo() {
        println("D1.foo in C1")
    }
}

C().caller(D())   // prints "D.foo in C"
C1().caller(D())  // prints "D.foo in C1" - dispatch receiver is resolved virtually
C().caller(D1())  // prints "D.foo in C" - extension receiver is resolved statically
```

#### 动机
在Java中，我们习惯于以`*Utils`命名的类：`StringUtils` `FileUtils`等等。著名的`java.util.Collections`也是一样。这些Utils类不友好的地方在于我们需要这样使用它们：
```
// Java
Collections.swap(list, Collections.binarySearch(list, Collections.max(otherList)), 
Collections.max(list))
```
Those class names are always getting in the way.我们通过静态导入来使用它们：
```
// Java
swap(list, binarySearch(list, max(otherList)), max(list))
```
这样稍微好一点，但是我们从IDE的自动完成代码获取了很少甚至没有帮助。如果可以这样使用会好很多：
```
// Java
list.swap(list.binarySearch(otherList.max()), list.max())
```
但是我们不想实现`List`中的所有可能的方法，这就是扩展帮助到我们的地方

> 有任何疑问，欢迎加群讨论：261386924
