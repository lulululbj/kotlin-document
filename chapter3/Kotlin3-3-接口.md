## 接口
Kotlin中的接口和Java8非常相似，他们都可以包含抽象方法的声明，也可以有方法的实现。使得接口不同于抽象类的地方是接口不可以保存状态。接口可以有属性，但是必须是抽象的，或者提供访问器实现
接口使用`interface`关键字定义
```
interface MyInterface {
    fun bar()
    fun foo() {
      // 可选的 body
    }
}
```

#### 实现接口
一个类或者对象可以实现一个或多个接口
```
class Child : MyInterface {
    override fun bar() {
        // body
    }
}
```

#### 接口中的属性
你可以在接口中声明属性。声明在接口中的属性要么是抽象的，要么提供了访问器实现，不可以拥有备用字段，因此访问器不可以访问他们
```
interface MyInterface {
    val prop: Int // abstract

    val propertyWithImplementation: String
        get() = "foo"

    fun foo() {
        print(prop)
    }
}

class Child : MyInterface {
    override val prop: Int = 29
}
```

#### 解决重写冲突
当我们在父类中声明了许多类型时，就可能出现继承了同一个方法的多个实现，例如：
```
interface A {
    fun foo() { print("A") }
    fun bar()
}

interface B {
    fun foo() { print("B") }
    fun bar() { print("bar") }
}

class C : A {
    override fun bar() { print("bar") }
}

class D : A, B {
    override fun foo() {
        super<A>.foo()
        super<B>.foo()
    }
}
```
接口`A`和`B`都声明了函数`foo()`和`bar()`，他们都实现了`foo()`，只有`B`实现了`bar()`，`bar()`在`A()`中没有标记为`abstract`，因为在接口中，函数没有函数体的话，这是默认的。现在，我们由`A`派生出`C`,显然需要重写`bar()`，并提供一个实现。当我们由`A`和`B`派生出`D`时，无需重写`bar()`，因为我们已经继承了它的唯一实现。但是我们继承了`foo()`的两个实现，所以编译器不知道选择哪一个，我们就必须重写`foo()`并显示告诉编译器我们自己的实现

> 有任何疑问，欢迎加群讨论：261386924
