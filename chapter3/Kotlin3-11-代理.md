## 代理

#### 类代理
代理模式已经被证明是实现继承的一个好选择，Kotlin无需引用任何代码，原生支持代理模式。如下，`Derived`类继承`Base`接口并且向一个特定的类代理了它的所有公共方法：
```
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main(args: Array<String>) {
    val b = BaseImpl(10)
    Derived(b).print() // prints 10
}
```
父类型`Derived`中的`by`语句表示`b`将会存储在`Derived`对象内部，编译器将会为`b`自动生成`Base`的所有方法
