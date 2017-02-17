## 可见性修饰符
类，对象，接口，构造器，函数，属性以及他们的`setter`都可以有可见性修饰符（`getter`总是和属性的可见性一样）。在Kotlin中，有4种可见性修饰符：`private` `protected` `internal` `public`，没有显示指定修饰符时默认是`public`
下面将解释不同声明类型的作用域

#### 包
函数，属性和类，对象，接口都可以声明在顶层，直接在包内：
```
// file name: example.kt
package foo

fun baz() {}
class Bar {}
```

* 如果你没有指定任何可见性修饰符，默认使用`public`，这意味着你的声明随处可见
* 如果你把声明标记为`private`，仅在包含声明的文件内可见
* 如果标记为`internal`，在同一个moudle内随处可见
* `protected`不适用于顶层声明

举例：
```
// file name: example.kt
package foo

private fun foo() {} // visible inside example.kt

public var bar: Int = 5 // property is visible everywhere
    private set         // setter is visible only in example.kt
    
internal val baz = 6    // visible inside the same module
```

#### 类和接口
对于声明在类中的成员：
* `private`意味着仅在类中可见（包括类中所有成员）
* `protected`和`private`一样，并且对所有子类可见
* `internal`意味着在这个`module`中任意可以看到声明的这个类的客户端都可以看到它的`internal`成员
* `public`意味着任何可以看到声明的这个类的客户端都可以看到它的`public`成员

Java开发者注意：在Kotlin中外部类不可以访问内部类的`private`成员

如果你重写了一个`protected`成员并且没有显示指定它的可见性，那么重写的成员也是`protected`的
举例：
```
open class Outer {
    private val a = 1
    protected open val b = 2
    internal val c = 3
    val d = 4  // public by default
    
    protected class Nested {
        public val e: Int = 5
    }
}

class Subclass : Outer() {
    // a is not visible
    // b, c and d are visible
    // Nested and e are visible

    override val b = 5   // 'b' is protected
}

class Unrelated(o: Outer) {
    // o.a, o.b are not visible
    // o.c and o.d are visible (same module)
    // Outer.Nested is not visible, and Nested::e is not visible either 
}
```

###### 构造函数
通过如下语法来指定一个类的主构造函数的可见性（首先你必须显示添加`constructor`关键字）：
```
class C private constructor(a: Int) { ... }
```
这里的构造函数是`private`的，默认情况下所有的构造函数都是`public`的。实际上只要类是可见的，他们就是随处可见的，但是一个`internal`类的构造函数只是同一个module中可见

###### 局部声明
局部变量，函数和类不可以有可见性修饰符

#### Modules
`internal`可见性修饰符意味着成员在同一个`module`中是可见的，更具体的说，`module`是一系列Kotlin文件编译在一起：
* 一个`Intellij IDEA`的`module`
* 一个`Maven`或者`Gradle`的工程
* `Ant`任务编译的一系列文件

> 有任何疑问，欢迎加群讨论：261386924
