## 包
一个源文件以包的声明开始
```
package foo.bar

fun baz() {}

class Goo {}

// ...
```
源文件中的所有内容（函数和类）都包含在包声明中。所以，在上面的示例代码中，函数`baz()`的全名是`foo.bar.baz`，类`Goo`的全名是`foo.bar.Goo`
如果没有指定包，文件中的内容属于一个没有名字的默认包

#### 导入
除了默认的导入外，每个文件可能会有自己的导入指令。Syntax for imports is described in the [grammar]().

我们可以导入一个单独的名字：
```
import foo.Bar // 现在Bar可以不用限定符访问
```
或者一定范围内的所有可访问内容（package, class, object 等等）
```
import foo.* // everything in 'foo' becomes accessible
```
如果存在命名冲突，通过`as`关键字本地重命名冲突项来解决
```
import foo.Bar // Bar is accessible
import bar.Bar as bBar // bBar stands for 'bar.Bar'
```
`import`关键字不仅限于导入类，还可以导入其他声明
*  顶层函数和属性
* functions and properties declared in [object declarations]()
* 枚举常量

与Java不同，Kotlin中没有`import static`的用法，所有声明都使用`import`导入

#### 顶层声明可见性
If a top-level declaration is marked *private*, it is private to the file it's declared in (see [Visibility Modifiers]())

> 有任何疑问，欢迎加群讨论：261386924
