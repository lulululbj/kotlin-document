## 枚举类
枚举类的最基本用法是类型安全的枚举实现
```
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```
每一个枚举常量都是一个对象，枚举常量之间以逗号分隔

#### 初始化
因为每一个枚举都是枚举类的实例，所以他们可以被初始化：
```
enum class Color(val rgb: Int) {
        RED(0xFF0000),
        GREEN(0x00FF00),
        BLUE(0x0000FF)
}
```

#### 匿名类
枚举实例也可以声明为他们自己的匿名类
```
enum class ProtocolState {
    WAITING {
        override fun signal() = TALKING
    },

    TALKING {
        override fun signal() = WAITING
    };

    abstract fun signal(): ProtocolState
}
```
枚举常量对应的方法和重写基本方法一样。如果枚举类定义了一些成员，你必须用分号`;`把枚举常量和成员分隔开，就像Java中那样

#### 使用枚举常量
和Java中一样，Kotlin中的枚举类有合成方法可以列出定义的枚举常量，并通过名字获取。这些方法的签名如下（假设枚举类的名字是`EnumClass`）：
```
EnumClass.valueOf(value: String): EnumClass
EnumClass.values(): Array<EnumClass>
```
如果指定的名称没有匹配到类中定义的任何一个枚举常量，`valueOf()`方法将会抛出一个·IllegalArgumentException·

每个枚举常量在枚举类声明的时候都有属性来获取它的名称和位置
```
val name: String
val ordinal: Int
```
枚举常量也实现了`Comparable`接口，以他们在枚举类中定义的顺序自然排列
