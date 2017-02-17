## 数据类
我们经常创建一些类仅仅用来保存数据，这些类的标准功能是机械的获取数据。在Kotlin中称为数据类，并用`data`标记：
```
data class User(val name: String, val age: Int)
```
编译器自动为声明在主构造函数中的所有属性衍生下列成员：
* `equals()/hashCode()`对
* `toString()`，形式如`"User(name=John, age=42)"`
* `componentN()`函数，根据属性声明的顺序
* `copy()`函数

如果这些函数中的任意一个在类体中显式声明了或者从基类中继承了，编译器就不会再自动生成
为了保证自动生成代码的稳定性和行为有意义，数据类需要完全满足下列要求：
* 主构造函数需要至少一个参数
* 主构造函数的所有参数必须以`val`或者`var`标记
* 数据类不可以是`abstract` `open` `sealed` `inner`的
* 数据类不可以扩展其他类（可以实现接口）

> 在JVM中，如果生成的类需要一个无参构造函数，那么所有属性都必须指定默认值（see [Constructors](http://kotlinlang.org/docs/reference/classes.html#constructors)）
`data class User(val name: String = "", val age: Int = 0)`

#### 复制
我们常常需要在复制一个对象的时候仅仅改变它的一些属性，而保持其他属性不变，这就是`copy()`函数的用处。对于`User`类，它的实现如下所示：
```
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)    
```
我们可以这样使用：
```
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

#### 数据类和多重声明
数据类的组成函数可以在多重声明中使用：
```
val jane = User("Jane", 35) 
val (name, age) = jane
println("$name, $age years of age") // prints "Jane, 35 years of age"
```

#### 标准数据类
标准库提供了`Pair`和`Triple`，大多数情况下，命名为数据类是更好的设计选择，因为它所带来的有意义的名称和属性使代码更具可读性

> 有任何疑问，欢迎加群讨论：261386924
