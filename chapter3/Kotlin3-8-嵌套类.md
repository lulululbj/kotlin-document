## 嵌套类
类中可以嵌套其他类
```
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```

#### 内部类
被`inner`标记的类可以访问外部类的成员。内部类持有一个外部类对象的引用：
```
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```
See [Qualified *this* expressions](http://kotlinlang.org/docs/reference/this-expressions.html) to learn about disambiguation of *this* in inner classes

#### 匿名内部类
匿名内部类实例在使用对象表达式的时候创建：
```
window.addMouseListener(object: MouseAdapter() {
  override fun mouseClicked(e: MouseEvent) {
    // ...
  }
                                                      
  override fun mouseEntered(e: MouseEvent) {
    // ...
  }
})
```
如果这个对象是一个功能性Java接口（带有抽象方法的Java接口）的实例，你可以使用以接口类型作为前缀的Lambda表达式来创建
```
val listener = ActionListener { println("clicked") }
```
