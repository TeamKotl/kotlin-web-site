---
type: doc
layout: reference
category: "Syntax"
title: "Generics"
---

# Generics

java에서 처럼,  Kotlin의 클래스에는 type파라메터를 가질 수 있습니다.

```kotlin
class Box<T>(t: T) {
    var value = t
}
```

일반적으로, 이러한 클래스의 인스턴스를 만들기 위해서는 type 인수를 제공해야 합니다.

```kotlin
val box: Box<Int> = Box<Int>(1)
```

그러나 매개변수가 추론될 수 있다면, 예를들어 생성자의 인수 또는 다른 방법으로  type 인수를 생략할 수 있습니다.

```kotlin
val box = Box(1) // 1 Int type 이므로, 컴파일러는 우리가 Box<Int>에 대해 말하고 있다고 이해합니다.
```

## 변화 Variance

Java타입의 시스템에서 가장 하기 까다로운 것중 하나는 wildcard type입니다. ( [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html) 문서를 참고하세요).    그렇지만  Kotlin에는 아무것도 없습니다. 대신 `declaration-site variance` 그리고 `type projections(타입 추론)` 두가지 다른것이 있습니다. 

먼저, 자바가 왜 그와 같은 이해하기 어려운 와일드 카드를 필요로하는지 생각해 봅시다. 이 문제는  [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html)에서 설명합니다. ( Item 28 : 바운드 와일드 카드를 사용하여 API 유연성 향상 )

우선, Java의 generic type은 불변, 변하지 않습니다. 즉, List <String>는 List <Object>의 subtype이 아닙니다.

왜 그럴까요? List가 불변 적이 지 않으면 다음 코드가 컴파일되어 런타임에 예외가 발생 하므로  Java의 배열보다 좋을 수 없습니다.

``` java
// Java
List<String> strs = new ArrayList<String>();
List<Object> objs = strs; // !!!다가올 문제의 원인은 여기에 있습니다. java는 이를 금지합니다.
objs.add(1); // 여기에 Integer를 문자열 목록에 넣습니다.
String s = strs.get(0); // !!! ClassCastException: integer를 string으로 캐스팅할 수 없습니다.
```
그래서,  Java는 런타임 안전을 보장하기 위해 이러한 일을 금지합니다. 하지만 이는 몇가지 함축적인 의미를 가지고 있습니다. 예를들어,  `Collection` 인터페이스의  `addAll()` 메서드를 생각해보죠.  이 메서드의 signature는 무엇일까요? 직관적으로 저희는 다음과 같이 표현하였습니다.

``` java
// Java
interface Collection<E> ... {
  void addAll(Collection<E> items);
}
```

그렇지만, 우리는 그렇게 다음과 같은 간단한 일을 할 수 없을 것입니다. (이것은 아주 안전합니다.):

``` java
// Java
void copyAll(Collection<Object> to, Collection<String> from) {
  to.addAll(from); // !!!addAll의 순수 선언으로 컴파일 되지 않습니다.
                   // Collection<String>은  Collection<Object>의 subType이 아닙니다.
}
```

(Java에서는 이러한 교훈을 어려운 방식으로 배웠습니다. [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html) 에있는 ` Item 25`: *Prefer lists to arrays* 를 참고하세요. ) 


이것이 실제 `addAll()`의 실제 signature와 다음과 같은 이유입니다.

``` java
// Java
interface Collection<E> ... {
  void addAll(Collection<? extends E> items);
}
```

**wildcard type 의 인수** `? extends E` 는 이메소드 자체가 `E` 자체는 아니고 `E`의 subtype의 오브젝트 컬렉션을 받아들이는 것을 나타냅니다. 그것은 여러분이 항목의 `E`를 안전하게  **read**할 수 있다는 것을 의미합니다. (이 컬렉션의 요소는 `E`의 서브클래스의 인스턴스입니다.) , 하지만 알수 없는 `E`의 subtype을 준수하는 object가 무엇인지 알 수 없기때문에 항목에 **쓸 수 없습니다.** 
이 제한에 대한 대가로 우리는 원하는 행동을 취합니다. `Collection<String>` 은 `Collection<? extends Object>`의  subtype입니다.

"clever words"에서 **extends**\-bound (**upper** bound)를 가진 와일드카드는 type을 `함께 변하도록 합니다.`

이 트릭이 왜 작동 하는지 이해 할 수 있게 하는 열쇠는 아주 간단합니다. : 만약 컬렉션의 아이템들만 **가져올 수 있다면,** `문자열 (String)` 컬렉션을 사용고 `Object`를 읽는것이 더 좋습니다. 반대로 , 컬렉션에 아이템들을 넣을 수 있다면, `Object` 컬렉션을 가져오고 `String`을 넣어도 됩니다. : java에서는`List<Object>` 의  **supertype** 인  `List<? super String>` 를 가지고 있습니다.

후자를 **contravariance** 라고 부르며, `String`을 인수로 가지는 메서드만  `List<? super String>`  (예 : `add(String)` 또는 `set(int, Stirng`) )을 호출할 수 있지만 , `List<T>`에서   `T` 를  반환하는 항목을 호출하면 `String`이 아니라 `Object`를 얻을 수 있습니다.

Joshua Bloch calls those objects you only **read** from **Producers**, and those you only **write** to **Consumers**. He recommends: "*For maximum flexibility, use wildcard types on input parameters that represent producers or consumers*", and proposes the following mnemonic:

*PECS stands for Producer-Extends, Consumer-Super.*

*NOTE*: if you use a producer-object, say, `List<? extends Foo>`, you are not allowed to call `add()` or `set()` on this object, but this does not mean 
that this object is **immutable**: for example, nothing prevents you from calling `clear()` to remove all items from the list, since `clear()` 
does not take any parameters at all. The only thing guaranteed by wildcards (or other types of variance) is **type safety**. Immutability is a completely different story.

### Declaration-site variance

Suppose we have a generic interface `Source<T>` that does not have any methods that take `T` as a parameter, only methods that return `T`:

``` java
// Java
interface Source<T> {
  T nextT();
}
```

Then, it would be perfectly safe to store a reference to an instance of `Source<String>` in a variable of type `Source<Object>` -- there are no consumer-methods to call. But Java does not know this, and still prohibits it:

``` java
// Java
void demo(Source<String> strs) {
  Source<Object> objects = strs; // !!! Not allowed in Java
  // ...
}
```

To fix this, we have to declare objects of type `Source<? extends Object>`, which is sort of meaningless, because we can call all the same methods on such a variable as before, so there's no value added by the more complex type. But the compiler does not know that.

In Kotlin, there is a way to explain this sort of thing to the compiler. This is called **declaration-site variance**: we can annotate the **type parameter** `T` of Source to make sure that it is only **returned** (produced) from members of `Source<T>`, and never consumed. 
To do this we provide the **out** modifier:

``` kotlin
abstract class Source<out T> {
    abstract fun nextT(): T
}

fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs // This is OK, since T is an out-parameter
    // ...
}
```

The general rule is: when a type parameter `T` of a class `C` is declared **out**, it may occur only in **out**\-position in the members of `C`, but in return `C<Base>` can safely be a supertype 
of `C<Derived>`.

In "clever words" they say that the class `C` is **covariant** in the parameter `T`, or that `T` is a **covariant** type parameter. 
You can think of `C` as being a **producer** of `T`'s, and NOT a **consumer** of `T`'s.

The **out** modifier is called a **variance annotation**, and  since it is provided at the type parameter declaration site, we talk about **declaration-site variance**. 
This is in contrast with Java's **use-site variance** where wildcards in the type usages make the types covariant.

In addition to **out**, Kotlin provides a complementary variance annotation: **in**. It makes a type parameter **contravariant**: it can only be consumed and never 
produced. A good example of a contravariant class is `Comparable`:

``` kotlin
abstract class Comparable<in T> {
    abstract fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    x.compareTo(1.0) // 1.0 has type Double, which is a subtype of Number
    // Thus, we can assign x to a variable of type Comparable<Double>
    val y: Comparable<Double> = x // OK!
}
```

We believe that the words **in** and **out** are self-explaining (as they were successfully used in C# for quite some time already), 
thus the mnemonic mentioned above is not really needed, and one can rephrase it for a higher purpose:

**[The Existential](http://en.wikipedia.org/wiki/Existentialism) Transformation: Consumer in, Producer out\!** :-)

## Type projections

### Use-site variance: Type projections

It is very convenient to declare a type parameter T as *out* and avoid trouble with subtyping on the use site, but some classes **can't** actually be restricted to only return `T`'s! 
A good example of this is Array:

``` kotlin
class Array<T>(val size: Int) {
    fun get(index: Int): T { /* ... */ }
    fun set(index: Int, value: T) { /* ... */ }
}
```

This class cannot be either co\- or contravariant in `T`. And this imposes certain inflexibilities. Consider the following function:

``` kotlin
fun copy(from: Array<Any>, to: Array<Any>) {
    assert(from.size == to.size)
    for (i in from.indices)
        to[i] = from[i]
}
```

This function is supposed to copy items from one array to another. Let's try to apply it in practice:

``` kotlin
val ints: Array<Int> = arrayOf(1, 2, 3)
val any = Array<Any>(3)
copy(ints, any) // Error: expects (Array<Any>, Array<Any>)
```

Here we run into the same familiar problem: `Array<T>` is **invariant** in `T`, thus neither of `Array<Int>` and `Array<Any>` 
is a subtype of the other. Why? Again, because copy **might** be doing bad things, i.e. it might attempt to **write**, say, a String to `from`,
and if we actually passed an array of `Int` there, a `ClassCastException` would have been thrown sometime later.

Then, the only thing we want to ensure is that `copy()` does not do any bad things. We want to prohibit it from **writing** to `from`, and we can:

``` kotlin
fun copy(from: Array<out Any>, to: Array<Any>) {
 // ...
}
```

What has happened here is called **type projection**: we said that `from` is not simply an array, but a restricted (**projected**) one: we can only call those methods that return the type parameter 
`T`, in this case it means that we can only call `get()`. This is our approach to **use-site variance**, and corresponds to Java's `Array<? extends Object>`, 
but in a slightly simpler way.

You can project a type with **in** as well:

``` kotlin
fun fill(dest: Array<in String>, value: String) {
    // ...
}
```

`Array<in String>` corresponds to Java's `Array<? super String>`, i.e. you can pass an array of `CharSequence` or an array of `Object` to the `fill()` function.

### Star-projections

Sometimes you want to say that you know nothing about the type argument, but still want to use it in a safe way.
The safe way here is to define such a projection of the generic type, that every concrete instantiation of that generic type would be a subtype of that projection.

Kotlin provides so called **star-projection** syntax for this:

- For `Foo<out T>`, where `T` is a covariant type parameter with the upper bound `TUpper`, `Foo<*>` is equivalent to `Foo<out TUpper>`. It means that when the `T` is unknown you can safely *read* values of `TUpper` from `Foo<*>`.
- For `Foo<in T>`, where `T` is a contravariant type parameter, `Foo<*>` is equivalent to `Foo<in Nothing>`. It means there is nothing you can *write* to `Foo<*>` in a safe way when `T` is unknown.
- For `Foo<T>`, where `T` is an invariant type parameter with the upper bound `TUpper`, `Foo<*>` is equivalent to `Foo<out TUpper>` for reading values and to `Foo<in Nothing>` for writing values.

If a generic type has several type parameters each of them can be projected independently.
For example, if the type is declared as `interface Function<in T, out U>` we can imagine the following star-projections:

- `Function<*, String>` means `Function<in Nothing, String>`;
- `Function<Int, *>` means `Function<Int, out Any?>`;
- `Function<*, *>` means `Function<in Nothing, out Any?>`.

*Note*: star-projections are very much like Java's raw types, but safe.

## Generic functions

Not only classes can have type parameters. Functions can, too. Type parameters are placed before the name of the function:

``` kotlin
fun <T> singletonList(item: T): List<T> {
    // ...
}

fun <T> T.basicToString() : String {  // extension function
    // ...
}
```

To call a generic function, specify the type arguments at the call site **after** the name of the function:

``` kotlin
val l = singletonList<Int>(1)
```

## Generic constraints

The set of all possible types that can be substituted for a given type parameter may be restricted by **generic constraints**.

### Upper bounds

The most common type of constraint is an **upper bound** that corresponds to Java's *extends* keyword:

``` kotlin
fun <T : Comparable<T>> sort(list: List<T>) {
    // ...
}
```

The type specified after a colon is the **upper bound**: only a subtype of `Comparable<T>` may be substituted for `T`. For example

``` kotlin
sort(listOf(1, 2, 3)) // OK. Int is a subtype of Comparable<Int>
sort(listOf(HashMap<Int, String>())) // Error: HashMap<Int, String> is not a subtype of Comparable<HashMap<Int, String>>
```

The default upper bound (if none specified) is `Any?`. Only one upper bound can be specified inside the angle brackets.
If the same type parameter needs more than one upper bound, we need a separate **where**\-clause:

``` kotlin
fun <T> cloneWhenGreater(list: List<T>, threshold: T): List<T>
    where T : Comparable,
          T : Cloneable {
  return list.filter { it > threshold }.map { it.clone() }
}
```
