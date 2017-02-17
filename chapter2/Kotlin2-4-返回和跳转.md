## 返回和跳转
Kotlin有三种结构跳转操作符：
* `return` 默认从最近的函数或者匿名函数返回
* `break` 结束当前循环
* `continue` 继续下一次循环

#### `Break`和`Continue`标签
在Kotlin中任何表达式都可以用·label·来标记。`label`的形式通常是标识符后面加符号`@`，例如`abc@` ,`fooBar@`都是合法的标签，我们只需要在表达式前面加上`label`就可以标记它：
```
loop@ for (i in 1..100) {
    // ...
}
```

我们可以使用`label`来限定`break`和`continue`
```
loop@ for (i in 1..100) {
    for (j in 1..100) {
        if (...) break@loop
    }
}
```
`label`限定的`break`跳转到该`label`标记的循环后面执行，`continue`跳转到下次循环

#### 在`Label`处返回
字面函数，局部函数，对象表达式，在Kotlin中函数是可以嵌套的，特定的`return`可以跳出外层函数。最重要的用法就是从lambda表达式中返回。回顾我们之前是如何使用的：
```
fun foo() {
    ints.forEach {
        if (it == 0) return
        print(it)
    }
}
```
这里的`return`直接从最近的闭合函数`foo`返回（Note：这种非局部的返回只支持传给内联函数的lambda表达式），如果我们需要从lambda返回，需要标计它并且限定`return`
```
fun foo() {
    ints.forEach lit@ {
        if (it == 0) return@lit
        print(it)
    }
}
```
现在这个`return`就从lambda表达式返回。通常使用隐式标签更加方便，比如使用和传入lambda的函数相同名字的标签：
```
fun foo() {
    ints.forEach {
        if (it == 0) return@forEach
        print(it)
    }
}
```
或者，我们可以使用匿名函数代替lambda表达式。匿名函数中的`return`将会跳出匿名函数本身
```
fun foo() {
    ints.forEach(fun(value: Int) {
        if (value == 0) return
        print(value)
    })
}
```
当返回一个值时，编译器会优先选择限定的`return`
```
return@a 1
```
means "return 1 at label @a" and not "return a labeled expression (@a 1)".
意味着 在标签`@a`处返回1 而不是 返回一个标签表达式`@a 1`

> 有任何疑问，欢迎加群讨论：261386924
