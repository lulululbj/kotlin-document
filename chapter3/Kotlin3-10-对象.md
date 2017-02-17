## 对象表达式和声明
有时候我们需要创建一个对类作了轻微修改的对象，但是并不显示声明一个新的子类。Java中通过匿名内部类处理这种情况，Kotlin通过对象表达式和对象声明稍微扩展了这个概念

#### 对象表达式
为了创建一个继承了一些类型的匿名类的对象，如下：
```
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
```
如果父类型有构造函数，必须传递给他合适的构造器参数。冒号后如果有多个父类型，需要用逗号分隔：
```
open class A(x: Int) {
    public open val y: Int = x
}

interface B {...}

val ab: A = object : A(1), B {
    override val y = 15
}
```
如果我们仅仅需要一个没有父类型的对象，可以这样写：
```
val adHoc = object {
    var x: Int = 0
    var y: Int = 0
}
print(adHoc.x + adHoc.y)
```
和Java的匿名内部类一样，对象表达式中的代码可以访问闭合范围内的变量，（不同于Java，只可以访问`final`变量）
```
fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
    // ...
}
```

#### 对象声明
[单例模式](http://en.wikipedia.org/wiki/Singleton_pattern)是一种非常有用的设计模式，Kotlin使得声明单例很简单：
```
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ...
    }

    val allDataProviders: Collection<DataProvider>
        get() = // ...
}
```
这就被称为对象声明。在`object`关键字之后总是有一个名字，就像一个变量声明，对象声明不是一个表达式并且不能用于赋值
我们直接使用它的名称来引用对象：
```
DataProviderManager.registerDataProvider(...)
```
这些对象可以有超类型：
```
object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
}
```
Note：对象声明不可以是局部的（比如直接嵌套在函数内），但是可以嵌套在其他对象声明或者非内部类中

###### 伴生对象
在类中的对象声明可以用`companion `关键字标记：
```
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```
伴生对象的成员可以直接用类名作为修饰符来调用：
```
val instance = MyClass.create()
```
伴生对象的名字可以省略，这种情况下需要使用`Companion `：
```
class MyClass {
    companion object {
    }
}

val x = MyClass.Companion
```
注意，虽然伴生对象的成员在其他语言看来就像静态成员，但是在运行时他们仍然是真实对象的成员实例并且可以（举例来说）实现接口：
```
interface Factory<T> {
    fun create(): T
}


class MyClass {
    companion object : Factory<MyClass> {
        override fun create(): MyClass = MyClass()
    }
}
```
但是，在JVM上，如果你使用`@JvmStatic`注解，你可以让伴生对象的成员生成实际的静态方法和域，See the [Java interoperability](http://kotlinlang.org/docs/reference/java-to-kotlin-interop.html#static-fields) section for more details

###### 对象表达式和对象声明的语义区别
对象表达式和对象声明有一个重要的语义区别：
* 对象表达式在使用时是立即执行（并初始化）
* 对象声明是延迟初始化的，在第一次被引用的时候才初始化
* 伴生对象当相对应的类被加载时初始化，对应Java静态初始化器的语义
