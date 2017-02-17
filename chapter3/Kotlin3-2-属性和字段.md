## 属性和字段

#### 属性声明
Kotlin中的类可以有属性，可以声明为`val`或者`var`
```
public class Address { 
    public var name: String = ...
    public var street: String = ...
    public var city: String = ...
    public var state: String? = ...
    public var zip: String = ...
}
```
就像Java中的字段一样，我们可以简单的通过名称来使用属性：
```
fun copyAddress(address: Address): Address {
    val result = Address() // there's no 'new' keyword in Kotlin
    result.name = address.name // accessors are called
    result.street = address.street
    // ...
    return result
}
```

#### Getters and Setters
声明一个属性的完整语法：
```
var <propertyName>: <PropertyType> [= <property_initializer>]
    [<getter>]
    [<setter>]
```
`initializer` `getter` `setter`都是可选的，如果属性类型可以从初始化代码或者重写的基类成员推断出来，那么也是可以省略的。例如：
```
var allByDefault: Int? // error: explicit initializer required, default getter and setter implied
var initialized = 1 // has type Int, default getter and setter
```
只读属性的声明和可变属性有两点不同：以`val`开头而不是`var`，不允许有`setter`
```
val simple: Int? // has type Int, default getter, must be initialized in constructor
val inferredType = 1 // has type Int and a default getter
```
我们可以在属性声明内部定义自定义访问器，就像普通函数一样
这是一个自定义`getter`的示例：
```
val isEmpty: Boolean
    get() = this.size == 0
```
自定义`setter`:
```
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // parses the string and assigns values to other properties
    }
```
约定俗成，`setter`参数的名字是`value`，但是你也可以选择使用你喜欢的名字
如果你想改变访问器的可见性或者想注释它，但不改变它的默认实现，你可以定义一个不带函数体的访问器：
```
var setterVisibility: String = "abc"
    private set // the setter is private and has the default implementation

var setterWithAnnotation: Any? = null
    @Inject set // annotate the setter with Inject
```

###### 备用字段
在Kotlin中类不可以有字段。但是，有时使用自定义访问器时，备用字段是必须的。出于这些目的，Kotlin提供了可以通过`field`标识符使用的备用字符：
```
var counter = 0 // the initializer value is written directly to the backing field
    set(value) {
        if (value >= 0) field = value
    }
```
`field`标识符只能用在属性访问器内
如果一个属性使用了至少一个访问器的默认实现，或者它的自定义访问器通过`field`标识符引用它，就会生成备份字段。
例如，下面情况就没有备份字段：
```
val isEmpty: Boolean
    get() = this.size == 0
```

###### 备用属性
如果你需要做一些事情并不适合“隐式备用字段”，你总是可以使用备用属性：
```
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap() // Type parameters are inferred
        }
        return _table ?: throw AssertionError("Set to null by another thread")
    }
```
综合来说，在这些方面和Java是相似的，In all respects, this is just the same as in Java since access to private properties with default getters and setters is optimized so that no function call overhead is introduced

#### 编译时常量
编译时就知道值的属性可以使用`const`修饰符标记为编译时常量，这种属性需要完全满足下列要求：
* Top-level or member of an object    对象的上层或成员？ 
* 使用`String`或者基本类型初始化
* 没有自定义`getter`

这种属性也可以在注解中使用：
```
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { ... }
```

#### 延迟初始化属性
通常，被声明为可以有非空类型的属性必须在构造函数中初始化，但是这样经常会造成不方便。例如，属性可以通过依赖注入来初始化，或者通过单元测试的构建方法。这种情况下，你不能在构造函数中提供一个非空的初始化语句，但是你仍然希望在类体中引用属性的时候避免空值检测。
为了处理这种情况，你可以使用`lateinit`修饰符来标记属性：
```
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()  // dereference directly
    }
}
```
这个修饰符只可以用在声明在类体中（不是在主构造函数中）的`var`类型的属性，而且这个属性不可以有自定义的`setter`和`getter`。这个属性的类型必须是非空的，不可以是基本类型
在一个`lateinit`属性初始化之前使用会产生一个特殊的异常

#### 重写属性
See [Overriding Properties](http://kotlinlang.org/docs/reference/classes.html#overriding-properties)

#### 代理属性
The most common kind of properties simply reads from (and maybe writes to) a backing field. On the other hand, with custom getters and setters one can implement any behaviour of a property. Somewhere in between, there are certain common patterns of how a property may work. A few examples: lazy values, reading from a map by a given key, accessing a database, notifying listener on access, etc.
Such common behaviours can be implemented as libraries using [*delegated properties*](http://kotlinlang.org/docs/reference/delegated-properties.html).

> 有任何疑问，欢迎加群讨论：261386924
