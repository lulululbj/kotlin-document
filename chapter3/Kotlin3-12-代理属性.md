## 代理属性
有一些特定的共同属性，虽然我们可以在每次需要他们的时候去手动实现，但是如果可以只需要为全部属性实现一次，放入一个库中也是极好的。示例如下：

* 延迟属性：第一次访问时才会计算值
* 可观察属性：监听器通知这个属性的变化
* 将属性保存在map，而不是每个单独的域中

为了覆盖这些（以及其他）情况，Kotlin提供了代理属性：
```
class Example {
    var p: String by Delegate()
}
```
语法是这样的：`val/var <property name>: <Type> by <expression>`，`by`后面的`expression`是`delegate`，因为属性对应的`set()`和`get()`将会代理给它的`setValue()`和`getValue(）`方法。属性代理不需要实现任何接口，但是需要提供`getValue()`函数（对于`var`还需要提供`setValue()`），示例：
```
class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }
 
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name} in $thisRef.'")
    }
}
```
当我们从代理了`Delegate`实例的`p`中读取时，`Delegate`的`getValue()`函数将会被调用，所以它的第一个参数是我们从`p`中读取的对象，第二个参数持有`p`本身的描述（可以当做名字）。例如：
```
val e = Example()
println(e.p)
```
将会打印：
```
Example@33a17727, thank you for delegating ‘p’ to me!
```
类似的，我们给`p`赋值，`setValue()`函数会被调用，前两个参数是一样的，第三个代表被赋予的值：
```
e.p = "NEW"
```
将会打印：
```
NEW has been assigned to ‘p’ in Example@33a17727
```

#### 属性代理要求
这里我们总结了一些代理对象的要求：
对于一个只读属性（val），代理需要提供带有下列参数的`getValue()`函数：
* receiver，必须和属性所有者一样或者是它的超类型（对于扩展属性，类型可以扩大）
* metadata ，必须是`KProperty<*>`类型或者它的超类型
这个函数必须返回和属性一样的类型或者它的子类型

对于可变属性（var）,代理需要额外添加`setValue()`函数，并提供如下参数：
* receiver，和`getValue()`一样
* metadata，和`getValue()`一样
* new value，必须和属性的类型一样或者是它的超类型

`getValue` 和/或者 `setValue`作为代理类的成员函数或者扩展函数，当你需要向一个原本不提供这些函数的对象代理属性的时候，后者更加方便，两种函数都需要用`operator`关键字标记

#### 标准代理
Kotlin标准库为一些有用类型的代理提供了工厂方法

###### 延迟
`lazy()`函数接收一个lambda，返回一个可以充当实现延迟属性的代理的`Lazy<T>`的实例：第一次调用`get()`执行传递给`lazy(）`的lambda表达式并且记住结果，后续调用`get()`返回前面记住的结果
```
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}

fun main(args: Array<String>) {
    println(lazyValue)
    println(lazyValue)
}
```
这个例子会打印：
```
computed!
Hello
Hello
```

默认情况下，延迟属性的求值是同步的：值只会在一个线程中计算，所有线程看到的都是同样的值。如果代理的初始化不是同步的，多个线程可以同时执行，把`LazyThreadSafetyMode.PUBLICATION`作为参数传递给`lazy()`函数。如果你确保初始化只会发生在单一线程中，你可以使用`LazyThreadSafetyMode.NONE`模式，它没有任何线程安全的保证和related overhead


###### Observable
`Delegates.observable()`接收两个参数：初始值和修改的执行者。修改者在每次给属性赋值时调用（在赋值已经完成后），它有三个参数：要赋值的属性，旧值和新值：
```
import kotlin.properties.Delegates

class User {
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}

fun main(args: Array<String>) {
    val user = User()
    user.name = "first"
    user.name = "second"
}
```
这个例子会打印：
```
<no name> -> first
first -> second
```
如果你想拦截一次赋值并且“否决”它，使用`vetoable()`代替`observable()`。传递给`vetoable`的处理者在给新属性赋值完成之前调用

#### 在Map中存储属性
一种常见的用法是在`map`中保存属性的值。这个经常发生在应用中，例如解析json或者其他一些动态的事情。这种情况下，我们需要使用map实例本身作为代理属性的代理
```
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
```
在这个例子中，构造器接收一个map：
```
val user = User(mapOf(
    "name" to "John Doe",
    "age"  to 25
))
```
代理属性从map中取值，通过String类型的key——属性的名称
```
println(user.name) // Prints "John Doe"
println(user.age)  // Prints 25
```
如果使用`MutableMap`代替只读map，将对var类型属性也有用
```
class MutableUser(val map: MutableMap<String, Any?>) {
    var name: String by map
    var age: Int     by map
}
```
