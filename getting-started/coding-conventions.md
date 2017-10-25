## 编码规范

本页包含了 Kotlin 语言当前代码风格。

### 命名风格

如有不确定，默认使用 Java 的编码风格，例如 ：

* 使用驼峰命名法（命名避免使用下划线）
* 类型名称首字母大写
* 方法和属性名首字母小写
* 缩进使用4个空格
* 公共函数需要写文档，以便在Kotlin Doc中查看

 
### 冒号

类型和父类型之间的冒号前需要有空格，实例和类型之间的冒号前不需要空格 ：  

```
interface Foo<out T : Any> : Bar {
    fun foo(a: Int): T
}
```
 
### Lambdas

在 lambda 表达式中，花括号附近需要使用空格，分离函数体中参数和表达式的箭头两侧需要空格，尽可能把表达式写在圆括号外。

```
list.filter { it > 10 }.map { element -> element * 2 }
}
```

在简短非嵌套的 lambda 表达式中，通常建议不要显示声明参数，而使用 `it` 代替。反之，在嵌套的Lambdas表达式中，总是应该显式声明属性。
 
#### Unit
如果一个函数返回`Unit`，应该被省略
```
fun foo() { // ": Unit" is omitted here

}
```
 
#### Functions vs Properties
In some cases functions with no arguments might be interchangeable with read-only properties. Although the semantics are similar, there are some stylistic conventions on when to prefer one to another.

Prefer a property over a function when the underlying algorithm:
* does not throw
* has a O(1) complexity
* is cheap to calculate (or caсhed on the first run)
* returns the same result over invocations

> 有任何疑问，欢迎加群讨论：261386924
