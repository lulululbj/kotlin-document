## 习惯用法
这里是在Kotlin中的一些习惯用法
#### 创建DTOs (POJOs/POCOs)[](http://kotlinlang.org/docs/reference/idioms.html#creating-dtos-pojospocos)
```
data class Customer(val name:String,val email:String)
 ```
向`Customer`类提供以下功能：
* 对所有属性的getter方法（对于var 提供setter方法）
* equals()
* hashCode()
* toString()
* copy()
* 所有属性的 component1(), component2(), … 等等 (See [Data classes]())
 

#### 方法参数默认值
```
fun foo(a: Int = 0, b: String = "") { ... }
```
 
#### 过滤list
```
val positives = list.filter { x -> x > 0 }
```
even shorter,

```
val positives = list.filter { it > 0 }
```
 
#### 字符串插入
```
println("Name $name")
```
 
#### 类型检查
```
when (x) {
    is Foo -> ...
    is Bar -> ...
    else   -> ...
}
```
 
#### 遍历key-value型集合
```
for ((k, v) in map) {
    println("$k -> $v")
}
```
k,v可以随便命名
 
#### 使用ranges
```
for (i in 1..100) { ... }  // 闭合的，包括100
for (i in 1 until 100) { ... } // 半开放的，不包括100
for (x in 2..10 step 2) { ... }
for (x in 10 downTo 1) { ... }
if (x in 1..10) { ... }
```
 
#### 只读list
```
val list = listOf("a", "b", "c")
```
 
#### 只读map
```
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```
 
#### 访问map
```
println(map["key"])
map["key"] = value
```
 
#### 懒属性
```
val p: String by lazy {
    // compute the string
}
```
 
#### 懒属性
```
val p: String by lazy {
    // compute the string
}
```
 
#### 扩展函数
```
fun String.spaceToCamelCase() { ... }

"Convert this to camelcase".spaceToCamelCase()
```
 
#### 创建单例
```
object Resource{
   val name="Name"
}
```
 
#### if not null 简写
```
val files = File("Test").listFiles()

println(files?.size)
```
 
#### if not null and else 简写
```
val files = File("Test").listFiles()

println(files?.size ?: "empty")
```
 
#### 运行遇到null
```
val data = ...
val email = data["email"] ?: throw IllegalStateException("Email is missing!")
```
 
#### 不为null时执行
```
val data = ...

data?.let {
    ... // not null时执行
}
```
 
#### 返回when表达式
```
fun transform(color: String): Int {
    return when (color) {
        "Red" -> 0
        "Green" -> 1
        "Blue" -> 2
        else -> throw IllegalArgumentException("Invalid color param value")
    }
}
```
 
#### try/catch 表达式
```
fun test() {
    val result = try {
        count()
    } catch (e: ArithmeticException) {
        throw IllegalStateException(e)
    }

    // Working with result
}
```
 
#### if 表达式
```
fun foo(param: Int) {
    val result = if (param == 1) {
        "one"
    } else if (param == 2) {
        "two"
    } else {
        "three"
    }
}
```
 
#### Builder-style usage of methods that return Unit
```
fun arrayOfMinusOnes(size: Int): IntArray {
    return IntArray(size).apply { fill(-1) }
}
```
 
#### 单一表达式函数
```
fun theAnswer() = 42
```
等价于
```
fun theAnswer(): Int {
    return 42
}
```
可以和其他习惯用法有效结合，简化代码，例如when表达式：
```
fun transform(color: String): Int = when (color) {
    "Red" -> 0
    "Green" -> 1
    "Blue" -> 2
    else -> throw IllegalArgumentException("Invalid color param value")
}
```
 
#### 使用with调用一个对象的多个方法
```
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()
with(myTurtle) { //draw a 100 pix square
    penDown()
    for(i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
```
 
#### Java 7's try with resources
```
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```
 
#### 更方便的泛型方法
```
//  public final class Gson {
//     ...
//     public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException {
//     ...

inline fun <reified T: Any> Gson.fromJson(json): T = this.fromJson(json, T::class.java)
```
 
#### 处理一个可空的 Boolean值
```
val b: Boolean? = ...
if (b == true) {
    ...
} else {
    // b为false  或者 null
}
```

> 有任何疑问，欢迎加群讨论：261386924
