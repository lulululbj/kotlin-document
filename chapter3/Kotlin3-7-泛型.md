## 泛型
和Java一样，Kotlin中的类可以有类型参数：
```
class Box<T>(t: T) {
    var value = t
}
```
通常，创建这种类的实例，我们需要提供类型参数：
```
val box: Box<Int> = Box<Int>(1)
```
但是如果参数类型可以从构造函数参数或者其他方面推断出来，参数类型可以省略：
```
val box = Box(1) // 1 has type Int, so the compiler figures out that we are talking about Box<Int>
```

#### Variance（变化）
Java的类型系统中最微妙的特点之一就是通配符类型（see [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)），但是Kotlin中并没有，取而代之的是，声明位置变量和类型推断
首先，让我们思考一下为什么需要这些难以理解的通配符，`Effective Java`第28项中解释了这个问题：使用界定通配符增加API的灵活性。首先，Java中泛型类型时不可变的，这就意味着`List<String>`不是`List<Object>`的子类型。为什么要这样？如果`List`是可变的，它将不会优于Java中的数组，下面代码编译时将会产生异常：
```
// Java
List<String> strs = new ArrayList<String>();
List<Object> objs = strs; // !!! The cause of the upcoming problem sits here. Java prohibits this!
objs.add(1); // Here we put an Integer into a list of Strings
String s = strs.get(0); // !!! ClassCastException: Cannot cast Integer to String
```
所以，为了运行时安全，这在Java中是禁止的。但是这会产生一些影响，例如：对于`Collection`接口中的`addAll()`方法，这个方法的签名是什么？直观地，我们这样定义：
```
// Java
interface Collection<E> ... {
  void addAll(Collection<E> items);
}
```
不过，我们将不能做以下这些简单的事情（保证安全的情况下）：
```
// Java
void copyAll(Collection<Object> to, Collection<String> from) {
  to.addAll(from); // !!! Would not compile with the naive declaration of addAll:
                   //       Collection<String> is not a subtype of Collection<Object>
}
```
(In Java, we learned this lesson the hard way, see [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html), Item 25: *Prefer lists to arrays*)
这就是为什么`addAll()`的实际签名如下所示：
```
// Java
interface Collection<E> ... {
  void addAll(Collection<? extends E> items);
}
```
这个通配符类型参数 `? extends T` 表明这个方法接受的`T`的子类对象集合，而不是`T`本身。这意味着我们可以安全的读取`T's`（T的子类的实例集合中的元素），但是不可以写，因为我们不知道T的子类到底是什么对象。作为这一限制的补偿，`Collection<String>`是`Collection<? extends Object>`的子类型。很巧妙的，带`extend`限定（上限）的通配符使得类型可变

理解为何这样做的关键在于：如果你只需要从集合中读取元素，使用`String`集合并从中读取`Object`是没问题的。相反的，如果你只向集合中存入元素，使用`Object`集合并存入`String`也是没问题的。在Java中，`List<? super String>`是`List<Object>`的超类型

后者称为抗变性。对于`List<?  super String>`，你只可以调用使用String作为参数的方法（例如，你可以调用`add(String)`或者`set(int,String)`）。如果你调用函数返回`List<T>`中的`T`，你获取到的不是`String`，而是`Object`

`Joshua Bloch` 将那些你只读取的对象称之为生产者，只写入的对象称之为消费者。他建议：“为了最大化的灵活性，在输入参数中使用通配符类型来代表生产者和消费者”。同时，他提出如下术语：
> PECS stands for Producer-Extends, Consumer-Super.

Note:当使用一个生产者对象，`List<? extends Foo>`，你不可以调用`add()`或者`set()`方法，但这并不意味着对象是不可变的，例如，你可以调用`clear()`方法清除集合的所有元素，因为`clear()`根本不需要任何参数。通配符（or other types of variance）唯一保证的是类型安全。不变性是另外一回事

###### 声明位置变量（Declaration-site variance）
假设我们有一个泛型接口`Source<T>`，其中没有任何以`T`为参数的方法，仅有一个返回`T`的方法：
```
// Java
interface Source<T> {
  T nextT();
}
```
这样，在一个`Source<Object>`类型的变量中保存`Source<String>`实例的变量是极为安全的，没有消费者方法可以调用，但是Java并不知道这一点，仍然是禁止的：
```
// Java
void demo(Source<String> strs) {
  Source<Object> objects = strs; // !!! Not allowed in Java
  // ...
}
```
为了修复这个问题，我们需要将对象声明为`Source<? extends Object>`类型，有一点无意义，因为我们依旧可以调用这些变量的相同方法，没有更多的复杂类型添加了值，但是编译器并不知道

在Kotlin中，编译器有办法解释这种事情，称为声明位置变量：我们可以通过注释Source的类型参数`T`来确保它只从`Source<T>`的成员中返回（生产），并且绝不消费。为此，我们提供了`out`修饰符：
```
abstract class Source<out T> {
    abstract fun nextT(): T
}

fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs // This is OK, since T is an out-parameter
    // ...
}
```
基本规则：当类`C`的一个类型参数`T`被声明为`out`，它只可以在`C`的`out position`出现，但是`C<Base>`可以安全的作为`C<Derived>`的超类型

简单的说，`C`对于参数`T`是协变的，或者说`T`是协变类型参数，你可以把`C`当做`T`的生产者，并不是消费者

`out`修饰符被称为`variance annotation`。由于它提供了参数类型声明位置，我们称为`declaration-site variance`。与Java的`use-site variance`相比，在类型使用中的通配符使得类型协变

除`out`之外，Kotlin补充了一个variance 注解：`in`，它使得参数类型时逆变的，只可以被消费，不可以生产。`Comparable`就是逆变类的一个好例子：
```
abstract class Comparable<in T> {
    abstract fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    x.compareTo(1.0) // 1.0 has type Double, which is a subtype of Number
    // Thus, we can assign x to a variable of type Comparable<Double>
    val y: Comparable<Double> = x // OK!
}
```
我们认为对于`in`和`out`的解释是一目了然的（由于他们在C#中已经成功应用很长时间），所以上面的解释不是必须的。你也可以重新深入了解他们：**[The Existential](http://en.wikipedia.org/wiki/Existentialism) Transformation: Consumer in, Producer out!** :-)


#### 类型推断

###### 使用处variance：类型推断
把一个参数类型`T`声明为`out`是非常方便的，并且在使用位置子类也是没问题的。是的，当问题中的类被限制为仅可以返回`T`，但如果不能呢，`Array`是一个好例子：
```
class Array<T>(val size: Int) {
    fun get(index: Int): T { /* ... */ }
    fun set(index: Int, value: T) { /* ... */ }
}
```
该类中的`T`既不可以`co-`也不可以协变，还造成了一定的不变，考虑如下函数：
```
fun copy(from: Array<Any>, to: Array<Any>) {
    assert(from.size == to.size)
    for (i in from.indices)
        to[i] = from[i]
}
```
这个函数的功能是将一个数组的元素复制到另一个数组，使用方法如下：
```
val ints: Array<Int> = arrayOf(1, 2, 3)
val any = Array<Any>(3)
copy(ints, any) // Error: expects (Array<Any>, Array<Any>)
```
这里我们遇到了同样的问题：`Array<T>`是不可变的，因此无论是`Array<Int>`还是`Array<Any>`，没有一个是另一个的子类型。这是为什么？因为`copy`可能带来坏处，因为它可能写入一个String，但是我们实际上传递的是一个Int数组，将会抛出类型转换异常
我们需要确保的是`copy`不会带来坏处，我们希望限制它向`from`中写入，我们可以这样做：
```
fun copy(from: Array<out Any>, to: Array<Any>) {
 // ...
}
```
这就被称作类型推断。`from`并不仅仅是一个数组，是一个restricted (projected)：我们只可以调用那些返回参数类型`T`的方法，既然如此，就它意味着我们只可以调用`get()`。这就是我们use-site variance的方法，和Java的`Array<? extends Object>`一致，但是稍微简便一些

我们也可以用`in`推断一个类型：
```
fun fill(dest: Array<in String>, value: String) {
    // ...
}
```
`Array<in String>`和Java中的`Array<? super String>`一致，你可以给`fill()`函数传递一个`CharSequence`数组或者`Object`数组

###### Star-projections
有时候你对类型参数一无所知，但是仍然想安全的使用它，安全的方法是定义泛型推断，每一个具体的泛型实例化都是这个推断的子类型
Kotlin为此准备了叫做`star-projection`的语法：

* 对于`Foo<out T>`，`T`是带有上限`TUpper`的协变类型参数。`Foo<*>`和`Foo<out TUpper>`是等价的，这就意味着当`T`未知时，你可以安全的从`Foo<*>`中读取`TUpper`的值
* 对于`Foo<in T>`,`T`是逆变类型参数。`Foo<*>`和`Foo<in Nothing>`是等价的，这就意味着当`T`未知时，你不能安全的向`Foo<*>`写入任何东西
* 对于`Foo<T>`，`T`是带有上限`TUpper`的不可变类型参数，`Foo<*>`和`Foo<out TUpper>`在读取值上是等价的，和`Foo<in Nothing>`在写入值方面是等价的

如果一个泛型有几种类型参数，每一种都可以被独立的推断，例如，如果一个类型被声明为`interface Function<in T, out U>`,我们可以推测出下列`star-projections`：
* `Function<*, String>` 意味着`Function<in Nothing, String>`
* `Function<Int, *>` 意味着`Function<Int, out Any?>`  
* `Function<*, *>` 意味着`Function<in Nothing, out Any?>`

`star-projections`和Java中的原始类型和相似，但更加安全

#### 泛型函数
不仅类可以有类型参数，函数也可以。类型参数通常放在函数名的前面
```
fun <T> singletonList(item: T): List<T> {
    // ...
}

fun <T> T.basicToString() : String {  // extension function
    // ...
}
```
调用泛型函数时，在函数名后指定参数类型

#### 泛型约束
最常见的约束类型是上限，和Java中的`extends`关键字一致
```
fun <T : Comparable<T>> sort(list: List<T>) {
    // ...
}
```
冒号后面指定的类型就是上限：只有`Comparable<T>`的子类型可以代替`T`。例如：
```
sort(listOf(1, 2, 3)) // OK. Int is a subtype of Comparable<Int>
sort(listOf(HashMap<Int, String>())) // Error: HashMap<Int, String> is not a subtype of Comparable<HashMap<Int, String>>
```
没有指定 的情况下默认上限是`Any?`。尖括号内只可以指定一个上限，如果同样的类型参数需要不止一个上限，需要使用分割`where`语句：
```
fun <T> cloneWhenGreater(list: List<T>, threshold: T): List<T>
    where T : Comparable,
          T : Cloneable {
  return list.filter { it > threshold }.map { it.clone() }
}
```

> 有任何疑问，欢迎加群讨论：261386924
