## 惯用语法

Kotlin 中的一些常用惯用语法。

### 创建数据传输对象 (POJOs/POCOs)

```
data class Customer(val name:String,val email:String)
```

`Customer` 类具有以下函数 ：

* 对所有属性的 getters（对于变量提供 setters 方法）
* `equals()`
* `hashCode()`
* `toString()`
* `copy()`
* `component1()` , `component2()` , … ,  for all properties (See [Data classes]())
 

### 函数参数默认值

```
fun foo(a: Int = 0, b: String = "") { ... }
```
 
### 过滤 list

```
val positives = list.filter { x -> x > 0 }
```
even shorter ：

```
val positives = list.filter { it > 0 }
```
 
### 字符串插入

```
println("Name $name")
```
 
### 类型检查

```
when (x) {
    is Foo -> ...
    is Bar -> ...
    else   -> ...
}
```
 
### 遍历 map/键值对集合

```
for ((k, v) in map) {
    println("$k -> $v")
}
```
`k` , `v` 可以任意命名
 
### 使用 ranges

```
for (i in 1..100) { ... }  // 闭合 range: 包括100
for (i in 1 until 100) { ... } // 半开 range: 不包括100
for (x in 2..10 step 2) { ... }
for (x in 10 downTo 1) { ... }
if (x in 1..10) { ... }
```
 
### 只读 list

```
val list = listOf("a", "b", "c")
```
 
### 只读 map

```
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```
 
### 访问 map

```
println(map["key"])
map["key"] = value
```
 
### 懒属性

```
val p: String by lazy {
    // compute the string
}
```
 
### 扩展函数

```
fun String.spaceToCamelCase() { ... }

"Convert this to camelcase".spaceToCamelCase()
```
 
### 创建单例

```
object Resource{
   val name="Name"
}
```
 
### if not null 简写

```
val files = File("Test").listFiles()

println(files?.size)
```
 
### if not null and else 简写

```
val files = File("Test").listFiles()

println(files?.size ?: "empty")
```
 
### 为 null 时执行

```
val data = ...
val email = data["email"] ?: throw IllegalStateException("Email is missing!")
```
 
### 不为null时执行

```
val data = ...

data?.let {
    ... // not null 时执行
}
```

### 不为 null 时返回非 null map

```
val value = ...
val mapped = value?.let { transformValue(it) } ?: defaultValueIfValueIsNull
```
 
### 返回 when 表达式
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
 
### try/catch 表达式

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
 
### if 表达式

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
 
### Builder-style usage of methods that return Unit

```
fun arrayOfMinusOnes(size: Int): IntArray {
    return IntArray(size).apply { fill(-1) }
}
```
 
### 单一表达式函数

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
 
### 使用 with 调用一个对象的多个方法

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
 
### Java 7's try with resources

```
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```
 
### 需要泛型类型信息的泛型函数的简便形式

```
//  public final class Gson {
//     ...
//     public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException {
//     ...

inline fun <reified T: Any> Gson.fromJson(json): T = this.fromJson(json, T::class.java)
```
 
### 使用可空 Boolean 值

```
val b: Boolean? = ...
if (b == true) {
    ...
} else {
    // b为false  或者 null
}
```

> update at 2017/10/25  
> 有任何疑问，欢迎加群讨论：261386924
