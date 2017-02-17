## 类和继承

#### 类
在Kotlin中，用`class`关键字定义类：
```
class Invoice {
}
```
类声明包括类名，类头（指定参数类型，主构造函数），类体（由花括号包围）。类头和类体是可选的，如果没有类体，花括号可以省略
```
class Empty
```

###### 构造函数
Kotlin中一个类可以有一个主构造函数和一个或多个次构造函数。主构造函数是类头的一部分，跟在类名后面（参数类型是可选的）
```
class Person constructor(firstName: String) {
}
```
如果主构造函数没有注解或者可见性修饰符，可以省略`constructor`关键字
```
class Person(firstName: String) {
}
```
主构造函数中不能包含任何代码，初始化代码可以放置在`init`关键字作为前缀的初始化模块中
```
class Customer(name: String) {
    init {
        logger.info("Customer initialized with value ${name}")
    }
}
```
主构造函数的参数可以在初始化模块中使用，也可以在声明在类体中的属性初始化时使用
```
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```
事实上，Kotlin有更简洁的语法来声明属性并初始化
```
class Person(val firstName: String, val lastName: String, var age: Int) {
    // ...
}
```
和普通属性一样，在主构造函数中声明的属性可以是`var`也可以是`val`
如果构造函数有注解或者可见性修饰符，`constructor`关键字是必需的，并且修饰符在它之前
```
class Customer public @Inject constructor(name: String) { ... }
```
For more details, see [Visibility Modifiers](http://kotlinlang.org/docs/reference/visibility-modifiers.html#constructors)


###### 次构造函数
类也可以声明次构造函数，以`constructor`为前缀：
```
class Person {
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```
如果类有一个主构造函数，那么每一个子构造函数都需要委托主构造函数，直接委托或者通过另一个子构造函数间接委托。在同一个类中委托其他构造函数使用`this`关键字
```
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```
如果一个非抽象类没有声明任何构造函数，系统默认会赋予一个无参主构造函数，并且是`public`的。如果你不希望有一个公共的构造函数，你可以声明一个非public的空的主构造函数：
```
class DontCreateMe private constructor () {
}
```

> Note: 在JVM中，如果主构造函数的所有参数都有默认值，编译器将会生成一个额外的无参构造函数，这个构造函数直接使用默认值。通过无参构造创建类的实例变得更加简单
```class Customer(val customerName: String = "")```


###### 创建类的实例
如果构造器是一个普通的函数，我们直接调用构造函数来创建类的实例
```
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

Note： Kotlin中没有`new`关键字
Creating instances of nested, inner and anonymous inner classes is described in [Nested classes](http://kotlinlang.org/docs/reference/nested-classes.html).

###### 类成员
类包含：
* 构造函数和初始化模块
* 函数
* 属性
* 嵌套/内部类
* 对象声明

## 继承
在Kotlin中，所有类都有一个共同的超类`Any`，对于没有声明超类类型的类，这就是超类
```
class Example //隐式继承 Any
```
`Any`不是`java.lang.Object`，事实上，除了`equals()` `hashCode()` `toString()`之外没有其他成员。Please consult the [Java interoperability](http://kotlinlang.org/docs/reference/java-interop.html#object-methods) section for more details.
我们可以在类头中冒号的后面放置类型，来显示声明超类类型：
```
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

如果类有一个主构造函数，其基类可以并且必须在这初始化，使用主构造函数的参数
如果类没有主构造函数，那么没个子构造函数必须使用`super`关键字初始化基类，或者通过其他构造函数代理，这样不同的子构造函数可以调用基类不同的构造函数：
```
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```
类中的`open`注解和Java中的`final`是相反的：它允许类可以被继承。默认情况下，Kotlin中所有类都是`final`的，which corresponds to [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 17: *Design and document for inheritance or else prohibit it*.

###### 重写方法
正如我们前面说过的，我们坚持让Kotlin中的事情是明确的。不同于Java，Kotlin需要显示注解可重写的成员（我们称之为`open`）
```
open class Base {
    open fun v() {}
    fun nv() {}
}
class Derived() : Base() {
    override fun v() {}
}
```
对于`Derived.v()`来说`override`是必须的，如果没有，编译器会发牢骚的。如果一个方法没有`open`注解，像`Base.nv()`，在子类中定义同名的这个方法是不合法的，无论是否有`override`。在一个`final`类（没有`open`注解）中，`open`类型的成员是禁止的

被`override`修饰的成员是`open`的，它可能在子类中被重写。如果不想被重写，使用`final`：
```
open class AnotherDerived() : Base() {
    final override fun v() {}
}
```

###### 重写属性
重写属性和重写方法类似。超类中定义的属性在衍生类中重写时必须以`override`开头，它必须类型兼容。每一个声明的属性都可以被重写，通过初始化程序或者`getter`方法
```
open class Foo {
    open val x: Int get { ... }
}

class Bar1 : Foo() {
    override val x: Int = ...
}
```
你可以用一个`var`属性重写一个`val`属性，但是反过来不行。因为`val`属性本身定义了`getter`方法，重写为`var`属性会在衍生类中额外声明一个`setter`方法

你可以在主构造函数中使用`override`关键字作为属性声明的一部分
```
interface Foo {
    val count: Int
}

class Bar1(override val count: Int) : Foo

class Bar2 : Foo {
    override var count: Int = 0
}
```

###### 重写规则
在Kotlin中，实现继承遵循以下规则：如果一个类实现了它的不同直接超类中的相同成员，它必须重写这个成员并提供自己的实现（也许使用继承的一个）。为了表示继承实现的哪个超类型，我们使用`super`关键字和尖括号来限制超类型名，如`super<Base>`：
```
open class A {
    open fun f() { print("A") }
    fun a() { print("a") }
}

interface B {
    fun f() { print("B") } // interface members are 'open' by default
    fun b() { print("b") }
}

class C() : A(), B {
    // The compiler requires f() to be overridden:
    override fun f() {
        super<A>.f() // call to A.f()
        super<B>.f() // call to B.f()
    }
}
```
同时继承`A`和`B`是可以的，对于`a()`和`b()`也是没问题的，因为`C`继承了每个方法的单一实现。但是对于`f()`，有两个实现，所以我们必须在`C`中重写`f()`提供自己的实现来消除歧义

#### 抽象类
一个类和他的成员可能被声明为`abstract`。一个抽象成员在它的类中没有实现。我们不需要给抽象类和抽象函数`open`注解，因为这是默认的
我们可以用一个抽象成员重写一个非抽象`open`成员
```
open class Base {
    open fun f() {}
}

abstract class Derived : Base() {
    override abstract fun f()
}
```

#### 伴生对象
在Kotlin中，不同于Java，C#，类没有静态方法，通常建议使用包级别的函数代替
如果你需要一个函数无需类实例但是可以调用类的内部（比如工厂方法），你可以把它作为该类对象声明的一部分
更特别的情况，如果你在类中声明了伴生对象，你可以像Java/C#调用静态方法一样调用类中成员，仅使用类名作为限定符

#### 密封类
密封类用来表示受限的类结构，值可以是某个限定集合中的一种类型，而不能有其他类型。在某种意义上，是枚举类的扩展；枚举类型的值集合也是受限的，但是每个枚举常量只有一个实例，但是密封类可以包含不同状态的多个实例
我们在类名前使用`sealed`修饰符来声明密封类。密封类可以有子类，但是必须嵌套在密封类声明内部
```
sealed class Expr {
    class Const(val number: Double) : Expr()
    class Sum(val e1: Expr, val e2: Expr) : Expr()
    object NotANumber : Expr()
}
```
密封类子类的扩展可以声明在任何地方，不需要在密封函数声明内部
使用密封类的最大的好处是当我们在`when`表达式中使用时，可以保证覆盖所有情况，而不需要添加`else`语句
```
fun eval(expr: Expr): Double = when(expr) {
    is Expr.Const -> expr.number
    is Expr.Sum -> eval(expr.e1) + eval(expr.e2)
    Expr.NotANumber -> Double.NaN
    // the `else` clause is not required because we've covered all the cases
}
```

> 有任何疑问，欢迎加群讨论：261386924
