---
type: doc
layout: reference
title: "What's New in Kotlin 1.1"
---

# Kotlin 1.1의 새로운 기능

## 목차

* [Coroutines](#coroutines-experimental)
* [Other language features](#other-language-features)
* [Standard library](#standard-library)
* [JVM backend](#jvm-backend)
* [JavaScript backend](#javascript-backend)

## JavaScript

Kotlin 1.1부터는,  JavaScript의  target이 더이상 실험적이라고 생각하지 않으셔도 됩니다. 모든 언어의 기능이 지원되며느 프론트엔드의 환경의 통합을 위한 새로운 tool들이 많아졌습니다! 

아래부터[ [below](#javascript-backend) ]  변경사항에 대한 자세한 부분을 알 수 있습니다.

## 코루틴 (실험적 적용사항) - Coroutines (experimental)

kotlin 1.1의 핵심 신기능인 *coroutines* 은, `async`/`await`, `yield`  및 유사한 프로그래밍 패턴을 지원합니다.  kotlin의 디자인 핵심기능은 *coroutines* 실행의 구현이 라이브러리의 일부이며, 언어가 아니기 때문에 어떤  특정 프로그래밍의  패러다임이나 동시성 라이브러리에 국한되지 않습니다.

*coroutine* 은 일시중단 되고 다시 시작할 수 있는 경량 스레드 입니다. *coroutine* 은  일시중단 기능을 통해 지원됩니다. 

[*suspending functions*](coroutines.html#suspending-functions): 이러한 함수를 호출하면 *coroutine*을 잠시 중단할 수 있고, 새로운 *coroutine*을 시작하기 위해 일반적으로 익명의 일시중지 기능을 사용합니다. (즉 , 람다의 일시정지 )

외부 라이브러리 [kotlinx.coroutines](https://github.com/kotlin/kotlinx.coroutines): 로 구현된  `async`/`await`  를 살펴보실 수  있습니다.

``` kotlin
// 백그라운드 스레드풀에서 코드를 실행합니다.
fun asyncOverlay() = async(CommonPool) {
    // 두개의 async를 운영합니다.
    val original = asyncLoadImage("original")
    val overlay = asyncLoadImage("overlay")
    // 두 결과에 overlay를 적용합니다.
    applyOverlay(original.await(), overlay.await())
}

// UI context에서 새로운 coroutine을 실행합니다.
launch(UI) {
    // 비동기 async가 완료될때까지 기다립니다.
    val image = asyncOverlay().await()
    // UI를 보여줍니다.
    showImage(image)
}
```

여기, `async { ... }` 은 coroutine 을 실행하고, 여러분이 `await()` 를 사용할때, 기다리고있는 작업이 실행되는 동안 coroutine의 실행이 일시 중단되고, 작업이 대기 될 때 (아마도 다른 스레드에서) 다시 시작되어 완료됩니다.

표준 라이브러리는 coroutine을 사용하여  `yield` 및 `yieldAll` 함수로 지연 생성 시퀀스(lazily generated sequences)를 제공합니다.

이러한 시퀀스에서, 시퀀스의 요소를 반환하는 코드블럭은 각 요소가 재시작된 이후에 일시 중단 되며, 다음 요소가 요청될 때 재개됩니다. 아래에서 예시를 보실 수 있습니다.

<div class="sample" markdown="1" data-min-compiler-version="1.1"> 

``` kotlin
import kotlin.coroutines.experimental.*

fun main(args: Array<String>) {
//예제 시작 
  val seq = buildSequence {
      for (i in 1..5) {
          // yield a square of i
          yield(i * i)
      }
      // yield a range //?
      yieldAll(26..28)
  }
  
  // print the sequence
  println(seq.toList())
//sampleEnd
}
```

</div>

위의 예제를 실행하여 결과를 확인해보세요. 언제든지 편집하고 다시 실행해볼 수 있습니다!

더 많은 정보를 보기위해서는,  [coroutine documentation](/docs/reference/coroutines.html) 과 [tutorial](/docs/tutorials/coroutines-basic-jvm.html) 을 확인해 보시면 됩니다.

coroutine은 현제 **실험적 기능**으로 간주됩니다. 이것은, Kotlin 팀이 최종 1.1 릴리스 이후에 이 기능의 이전 버전과의 호환성을 지원하지 않는다는 것을 의미합니다.


## 기타 언어 기능 - Other Language Features

### Type aliases

Type alias 를 사용하면 기존 type 에 대한 대체 이름을 정의할 수 있습니다. 이건 collection과 같은 generic type이나 function type (함수타입)에 가장 유용합니다.

아래에서 예를 보실 수 있습니다.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
//samplestart
typealias OscarWinners = map<Stirng, string>

fun countLaLaLand(oscarWinners: OscarWinners) =
oscarWinner.count {it.value.contains("La La Land")}

// Note that the type name (initial and the type alias) are interchangeable
fun checkLaLaLandIsTheBestMove(oscarwinners: Map<String, String>) =
oscarWinners["Best picture"] == "La La Land"

fun oscarWinners(); OscarWinners {
  return mapOf(
  			"Best Song" to "City of Stars (La La Land)",
    		"Best actress" to "Emma Stone (La La Land)",
            "Best picture" to "Moonlight" /* ... */)
  )
}

fum main(args: Array<String>) {
  	val oscarWinners = oscarWinners()
  
  	val laLaLandAwards = countLaLALAnd(oscarWinners)
  	println("LaLaLandAwards = $laLaLandAwards (in our small example), but actually it's 6.")
})
    val laLaLandIsTheBestMovie = checkLaLaLandIsTheBestMovie(oscarWinners)
    println("LaLaLandIsTheBestMovie = $laLaLandIsTheBestMovie")
}
```
</div>

자세한 설명은 [documentation](type-aliases.html) 와 [KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/type-aliases.md) 을 참고해주세요.



### 레퍼런스 호출 - Bound callable references 

이제 `::` 연산자를 사용하여 특정 객체 인스턴스의 메서드나 속성을 가리키는 [member reference](reflection.html#function-references) 를 가져올 수 있습니다. 예전에는 lamda표현식을 이용해서만 사용할 수 있었습니다.

아래 예제를 보실 수 있습니다.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
//sampleStart
val numberRegex = "\\d+".toRegex()
val numbers = listOf("abc", "123", "456").filter(numberRegex::matches)
//sampleEnd

fun main(args: Array<String>) {
    println("Result is $numbers")
}
```
</div>


자세한 사항은 [documentation](reflection.html#bound-function-and-property-references-since-11) 와 [KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/bound-callable-references.md) 를 참조해 주세요!



### Sealed and data classes  

kotlin 1.0에서는 `sealed`  가 붙어진 봉인class 모든 서브class가 모두 봉인class의 안에 위치해야 했습니다.

하지만 Kotlin 1.1에서는 kotlin 1.0에 있는 data class 및 class에 대한 일부 제한을 제거했습니다. 이제 최상위 레벨인 **봉인class**의 하위 class를  **봉인class**의 중첩된 class가아닌 같은 파일의 최상위 레벨에 정의할 수 있습니다. 

Data class는 이제 다른 클래스를 확장 할 수 있습니다. 이는 표현 class의 계층구조를 훌륭하고 명확하게 정의하는데 사용할 수있습니다.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
//sampleStart
sealed class Expr

data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()

fun eval(expr: Expr): Double = when (expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
}
val e = eval(Sum(Const(1.0), Const(2.0)))
//sampleEnd

fun main(args: Array<String>) {
    println("e is $e") // 3.0
}
```
```kotlin
// 예제 아래 링크되어있는 sealed class의 설명에서 볼 수 있는 보다 쉬운 예제입니다.
// FILE: 1.kt
sealed class A {
  class B : A() { // B is declared inside A -- ok
    class C: A() // C is declared inside A -- ok
  }
}

class D : A() { // D and A are declared in same file -- ok
  
  class E : A() // E is declared outside A -- error 
}

// FILE: 2.kt
class F: A() // F and A are declared in different files -- error
```

</div>

더 궁금하시 사항은  [documentation](sealed-classes.html) 이나
[sealed class](https://github.com/Kotlin/KEEP/blob/master/proposals/sealed-class-inheritance.md) ,
[data class](https://github.com/Kotlin/KEEP/blob/master/proposals/data-class-inheritance.md) 에서 확인하실 수 있습니다.



### 람다의 Destructuring - Destructuring in lambdas

이제  [destructuring declaration](multi-declarations.html) 선언 구문을 사용하여 람다에 압축되어 전달 된 인수를 해제하여 볼 수 있습니다.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
fun main(args: Array<String>) {
//sampleStart
    val map = mapOf(1 to "one", 2 to "two")
    // before
    println(map.mapValues { entry ->
        val (key, value) = entry
        "$key -> $value!"
    })
    // now
    println(map.mapValues { (key, value) -> "$key -> $value!" })
//sampleEnd    
}
```
</div>

자세한 내용은 [documentation](multi-declarations.html#destructuring-in-lambdas-since-11) 와 [KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/destructuring-in-parameters.md)에서 확인 하실 수 있습니다.



### 사용하지 않는 파리메터(인수) 를 위한 언더스코어 

### - Underscores for unused parameters

여러개의 파라메터가 존재하는 람다의 경우, `_` 문자를 사용하여 여러분이 사용하지않는 파라메터의 이름이 있는곳에 바꿔 사용할 수 있습니다.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
fun main(args: Array<String>) {
    val map = mapOf(1 to "one", 2 to "two")

//sampleStart
    map.forEach { _, value -> println("$value!") }
//sampleEnd    
}
```
</div>

이것은 [destructuring declarations](multi-declarations.html) 에도 효과적입니다:

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
data class Result(val value: Any, val status: String)

fun getResult() = Result(42, "ok").also { println("getResult() returns $it") }

fun main(args: Array<String>) {
//sampleStart
    val (_, status) = getResult()
//sampleEnd
    println("status is '$status'")
}
```
</div>

 [KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/underscore-for-unused-parameters.md) 에서 자세한 내용을 확인하실 수 있습니다.

### numeric(숫자) literals의 언더스코어 

### - Underscores in numeric literals

Java8에서 처럼 , kotlin에서도 numeric literals에 언더스코어를 사용하여 숫자 그룹을 구분할 수 있습니다.:

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
//sampleStart
val oneMillion = 1_000_000
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
//sampleEnd

fun main(args: Array<String>) {
    println(oneMillion)
    println(hexBytes.toString(16))
    println(bytes.toString(2))
}
```
</div>

자세한 사항은 [KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/underscores-in-numeric-literals.md) 에서 확인해주세요.

### properties 의 짧은 구문 

### - Shorter syntax for properties

표현식의 바디에 정의되어 있는 getter메서드에 있는 속성의 경우에는, 속성 type을 생략가능합니다.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
//sampleStart
data class Person(val name: String, val age: Int) {
    val isAdult get() = age >= 20 // Property type inferred to be 'Boolean'
}
//sampleEnd

fun main(args: Array<String>) {
    val akari = Person("Akari", 26)
    println("$akari.isAdult = ${akari.isAdult}")
}
```
</div>

### 인라인의 속성 접근자 

### - Inline property accessors

이제 속성에 backing field가 없다면 `inline`으로 속성접근자를 표시하시면 됩니다.
이러한 접근자는  [inline functions](inline-functions.html) (인라인 함수) 와 같은 방식으로 컴파일 됩니다..

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
//sampleStart
public val <T> List<T>.lastIndex: Int
    inline get() = this.size - 1
//sampleEnd

fun main(args: Array<String>) {
    val list = listOf('a', 'b')
    // the getter will be inlined
    println("Last index of $list is ${list.lastIndex}")
}
```
</div>

전체 속성을 `inline` 으로 표시할 수도 있습니다. 그럼 modifier(수정자)가 두 접근자에 모두에 적용됩니다.

자세한 내용은 [documentation](inline-functions.html#inline-properties-since-11) 와 [KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/inline-properties.md) 에서 확인하실 수 있습니다.

### 로컬 delegated 속성 

### -  Local delegated properties

로컬 변수와 함께  [delegated property](delegated-properties.html) 의 구문을 사용할 수 있습니다.
한 가지 사용가능한 방법은 필요할 때 호출되서 사용할 수 있는 `lazily evaluated` 로컬 변수를 정의할 수 있습니다. 

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
import java.util.Random

fun needAnswer() = Random().nextBoolean()

fun main(args: Array<String>) {
//sampleStart
    val answer by lazy {
        println("Calculating the answer...")
        42
    }
    if (needAnswer()) {                     // returns the random value
        println("The answer is $answer.")   // answer is calculated at this point
    }
    else {
        println("Sometimes no answer is the answer...")
    }
//sampleEnd
}
```
</div>

자세한 사항은 [KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/local-delegated-properties.md) 에서 확인하세요.

### delegated property의 바인딩 차단 

### - Interception of delegated property binding

[delegated properties](delegated-properties.html)의 경우, 이제`provideDelegate` 연산자를 사용하여 속성 바인딩에 대한 delegate(위임)를 가로챌 수 있습니다.

예를들어, 바인딩하기전에 속성의 이름을 체크하려면 아래의 예제와 같이 작성할 수 있습니다.

``` kotlin
class ResourceLoader<T>(id: ResourceID<T>) {
    operator fun provideDelegate(thisRef: MyUI, prop: KProperty<*>): ReadOnlyProperty<MyUI, T> {
        checkProperty(thisRef, prop.name)
        ... // property creation
    }

    private fun checkProperty(thisRef: MyUI, name: String) { ... }
}

fun <T> bindResource(id: ResourceID<T>): ResourceLoader<T> { ... }

class MyUI {
    val image by bindResource(ResourceID.image_id)
    val text by bindResource(ResourceID.text_id)
}
```

 `provideDelegate` 메서드는 `MyUI` 인스턴스를 생성하는 동안 각 속성에 대해 호출되며 필요한 검증을 바로 수행 할 수 있습니다.

자세한 정보는 [documentation](delegated-properties.html#providing-a-delegate-since-11) 에서 확인하세요.


### Generic enum value access

이제 enum class의 값을 일반적인 방법으로 열거할 수 있습니다.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
//sampleStart
enum class RGB { RED, GREEN, BLUE }

inline fun <reified T : Enum<T>> printAllValues() {
    print(enumValues<T>().joinToString { it.name })
}
//sampleEnd

fun main(args: Array<String>) {
    printAllValues<RGB>() // prints RED, GREEN, BLUE
}
```
</div>

### DSL에서 암시적 리시버의 범위제어

### - Scope control for implicit receivers in DSLs

[`@DslMarker`](/api/latest/jvm/stdlib/kotlin/-dsl-marker/index.html) annotation(어노테이션)은 DSL 컨텍스트에서 외부 스코프의 리시버의 사용을 제한할 수 있습니다.

정식 [HTML builder example](type-safe-builders.html) 예제를 확인해보세요.:

``` kotlin
table {
    tr {
        td { +"Text" }
    }
}
```

kotlin 1.0에서는, `td` 에 전달된 lamda코드는 세 개의 암묵적인 리시버에 대한 접근(액세스)권한을 가집니다. : (`tr` 및 `td` , `table`로 전달된 리시버) . 이렇게하면 컨텍스트에서 의미가 없는 메서드를 호출할 수 있습니다. 예를 들어` td`에서` tr`을 호출하여 <tr> 태그를  <td>에 넣을 수 있습니다.

Kotlin 1.1 에서는 이를 제한 할 수 있으므로 `td`의 암시적인 리시버에 정의 된 메서드만  `td`에 전달 된 lamda 내부에서 사용할 수 있습니다. @DslMarker 메타 주석으로 표시된 주석을 정의하고이를 태그 클래스의 기본 클래스에 적용하면됩니다.

자세한 내용은 [documentation](type-safe-builders.html#scope-control-dslmarker-since-11) 와 [KEEP](https://github.com/Kotlin/KEEP/blob/master/proposals/scope-control-for-implicit-receivers.md) 에서 보실 수 있습니다.

### `rem` 연산자 

### - `rem` operator

The `mod` operator is now deprecated, and `rem` is used instead. See [this issue](https://youtrack.jetbrains.com/issue/KT-14650) for motivation.

## Standard library

### String to number conversions

There is a bunch of new extensions on the String class to convert it to a number without throwing an exception on invalid number:
`String.toIntOrNull(): Int?`, `String.toDoubleOrNull(): Double?` etc.

```
val port = System.getenv("PORT")?.toIntOrNull() ?: 80
```

Also integer conversion functions, like `Int.toString()`, `String.toInt()`, `String.toIntOrNull()`,
each got an overload with `radix` parameter, which allows to specify the base of conversion (2 to 36).

### onEach()

`onEach` is a small, but useful extension function for collections and sequences, which allows to perform some action,
possibly with side-effects, on each element of the collection/sequence in a chain of operations.
On iterables it behaves like `forEach` but also returns the iterable instance further. And on sequences it returns a
wrapping sequence, which applies the given action lazily as the elements are being iterated.

``` kotlin
inputDir.walk()
        .filter { it.isFile && it.name.endsWith(".txt") }
        .onEach { println("Moving $it to $outputDir") }
        .forEach { moveFile(it, File(outputDir, it.toRelativeString(inputDir))) }
```

### also(), takeIf() and takeUnless()

These are three general-purpose extension functions applicable to any receiver.

`also` is like `apply`: it takes the receiver, does some action on it, and returns that receiver. 
The difference is that in the block inside `apply` the receiver is available as `this`, 
while in the block inside `also` it's available as `it` (and you can give it another name if you want).
This comes handy when you do not want to shadow `this` from the outer scope:

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
class Block {
    lateinit var content: String
}

//sampleStart
fun Block.copy() = Block().also {
    it.content = this.content
}
//sampleEnd

// using 'apply' instead
fun Block.copy1() = Block().apply {
    this.content = this@copy1.content
}

fun main(args: Array<String>) {
    val block = Block().apply { content = "content" }
    val copy = block.copy()
    println("Testing the content was copied:")
    println(block.content == copy.content)
}
```
</div>

`takeIf` is like `filter` for a single value. It checks whether the receiver meets the predicate, and
returns the receiver, if it does or `null` if it doesn't. 
Combined with an elvis-operator and early returns it allows to write constructs like:

``` kotlin
val outDirFile = File(outputDir.path).takeIf { it.exists() } ?: return false
// do something with existing outDirFile
```

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
fun main(args: Array<String>) {
    val input = "Kotlin"
    val keyword = "in"

//sampleStart
    val index = input.indexOf(keyword).takeIf { it >= 0 } ?: error("keyword not found")
    // do something with index of keyword in input string, given that it's found
//sampleEnd
    
    println("'$keyword' was found in '$input'")
    println(input)
    println(" ".repeat(index) + "^")
}
```
</div>

`takeUnless` is the same as `takeIf`, but it takes the inverted predicate. It returns the receiver when it _doesn't_ meet the predicate and `null` otherwise. So one of the examples above could be rewritten with `takeUnless` as following:

``` kotlin
val index = input.indexOf(keyword).takeUnless { it < 0 } ?: error("keyword not found")
```

It is also convenient to use when you have a callable reference instead of the lambda:

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
private fun testTakeUnless(string: String) {
//sampleStart
    val result = string.takeUnless(String::isEmpty)
//sampleEnd

    println("string = \"$string\"; result = \"$result\"")
}

fun main(args: Array<String>) {
    testTakeUnless("")
    testTakeUnless("abc")
}
```
</div>

### groupingBy()

This API can be used to group a collection by key and fold each group simultaneously. For example, it can be used
to count the number of words starting with each letter:

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
fun main(args: Array<String>) {
    val words = "one two three four five six seven eight nine ten".split(' ')
//sampleStart
    val frequencies = words.groupingBy { it.first() }.eachCount()
//sampleEnd
    println("Counting first letters: $frequencies.")

    // The alternative way that uses 'groupBy' and 'mapValues' creates an intermediate map, 
    // while 'groupingBy' way counts on the fly.
    val groupBy = words.groupBy { it.first() }.mapValues { (_, list) -> list.size }
    println("Comparing the result with using 'groupBy': ${groupBy == frequencies}.")
}
```
</div>

### Map.toMap() and Map.toMutableMap()

These functions can be used for easy copying of maps:

``` kotlin
class ImmutablePropertyBag(map: Map<String, Any>) {
    private val mapCopy = map.toMap()
}
```

### Map.minus(key)

The operator `plus` provides a way to add key-value pair(s) to a read-only map producing a new map, however there was not a simple way to do the opposite: to remove a key from the map you have to resort to less straightforward ways to like `Map.filter()` or `Map.filterKeys()``.
Now the operator `minus` fills this gap. There are 4 overloads available: for removing a single key, a collection of keys, a sequence of keys and an array of keys.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
fun main(args: Array<String>) {
//sampleStart
    val map = mapOf("key" to 42)
    val emptyMap = map - "key"
//sampleEnd
    
    println("map: $map")
    println("emptyMap: $emptyMap")
}
```
</div>

### minOf() and maxOf()

These functions can be used to find the lowest and greatest of two or three given values, where values are primitive numbers or `Comparable` objects. There is also an overload of each function that take an additional `Comparator` instance, if you want to compare objects that are not comparable themselves.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
fun main(args: Array<String>) {
//sampleStart
    val list1 = listOf("a", "b")
    val list2 = listOf("x", "y", "z")
    val minSize = minOf(list1.size, list2.size)
    val longestList = maxOf(list1, list2, compareBy { it.size })
//sampleEnd
    
    println("minSize = $minSize")
    println("longestList = $longestList")
}
```
</div>

### Array-like List instantiation functions

Similar to the `Array` constructor, there are now functions that create `List` and `MutableList` instances and initialize
each element by calling a lambda:

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
fun main(args: Array<String>) {
//sampleStart
    val squares = List(10) { index -> index * index }
    val mutable = MutableList(10) { 0 }
//sampleEnd

    println("squares: $squares")
    println("mutable: $mutable")
}
```
</div>

### Map.getValue()

This extension on `Map` returns an existing value corresponding to the given key or throws an exception, mentioning which key was not found.
If the map was produced with `withDefault`, this function will return the default value instead of throwing an exception.

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
fun main(args: Array<String>) {

//sampleStart    
    val map = mapOf("key" to 42)
    // returns non-nullable Int value 42
    val value: Int = map.getValue("key")

    val mapWithDefault = map.withDefault { k -> k.length }
    // returns 4
    val value2 = mapWithDefault.getValue("key2")

    // map.getValue("anotherKey") // <- this will throw NoSuchElementException
//sampleEnd
    
    println("value is $value")
    println("value2 is $value2")
}
```
</div>

### Abstract collections

These abstract classes can be used as base classes when implementing Kotlin collection classes.
For implementing read-only collections there are `AbstractCollection`, `AbstractList`, `AbstractSet` and `AbstractMap`, 
and for mutable collections there are `AbstractMutableCollection`, `AbstractMutableList`, `AbstractMutableSet` and `AbstractMutableMap`.
On JVM these abstract mutable collections inherit most of their functionality from JDK's abstract collections.

### Array manipulation functions

The standard library now provides a set of functions for element-by-element operations on arrays: comparison
(`contentEquals` and `contentDeepEquals`), hash code calculation (`contentHashCode` and `contentDeepHashCode`),
and conversion to a string (`contentToString` and `contentDeepToString`). They're supported both for the JVM
(where they act as aliases for the corresponding functions in `java.util.Arrays`) and for JS (where the implementation
is provided in the Kotlin standard library).

<div class="sample" markdown="1" data-min-compiler-version="1.1">

``` kotlin
fun main(args: Array<String>) {
//sampleStart
    val array = arrayOf("a", "b", "c")
    println(array.toString())  // JVM implementation: type-and-hash gibberish
    println(array.contentToString())  // nicely formatted as list
//sampleEnd
}
```
</div>

## JVM Backend

### Java 8 bytecode support

Kotlin has now the option of generating Java 8 bytecode (`-jvm-target 1.8` command line option or the corresponding options
in Ant/Maven/Gradle). For now this doesn't change the semantics of the bytecode (in particular, default methods in interfaces
and lambdas are generated exactly as in Kotlin 1.0), but we plan to make further use of this later.


### Java 8 standard library support

There are now separate versions of the standard library supporting the new JDK APIs added in Java 7 and 8.
If you need access to the new APIs, use `kotlin-stdlib-jre7` and `kotlin-stdlib-jre8` maven artifacts instead of the standard `kotlin-stdlib`.
These artifacts are tiny extensions on top of `kotlin-stdlib` and they bring it to your project as a transitive dependency.


### Parameter names in the bytecode

Kotlin now supports storing parameter names in the bytecode. This can be enabled using the `-java-parameters` command line option.


### Constant inlining

The compiler now inlines values of `const val` properties into the locations where they are used.


### Mutable closure variables

The box classes used for capturing mutable closure variables in lambdas no longer have volatile fields. This change improves
performance, but can lead to new race conditions in some rare usage scenarios. If you're affected by this, you need to provide
your own synchronization for accessing the variables.


### javax.script support

Kotlin now integrates with the [javax.script API](https://docs.oracle.com/javase/8/docs/api/javax/script/package-summary.html) (JSR-223).
The API allows to evaluate snippets of code at runtime:

``` kotlin
val engine = ScriptEngineManager().getEngineByExtension("kts")!!
engine.eval("val x = 3")
println(engine.eval("x + 2"))  // Prints out 5
```

See [here](https://github.com/JetBrains/kotlin/tree/master/libraries/examples/kotlin-jsr223-local-example)
for a larger example project using the API.


### kotlin.reflect.full

To [prepare for Java 9 support](https://blog.jetbrains.com/kotlin/2017/01/kotlin-1-1-whats-coming-in-the-standard-library/), the extension functions and properties in the `kotlin-reflect.jar` library have been moved
to the package `kotlin.reflect.full`. The names in the old package (`kotlin.reflect`) are deprecated and will be removed in
Kotlin 1.2. Note that the core reflection interfaces (such as `KClass`) are part of the Kotlin standard library,
not `kotlin-reflect`, and are not affected by the move.


## JavaScript Backend

### Unified standard library

A much larger part of the Kotlin standard library can now be used from code compiled to JavaScript.
In particular, key classes such as collections (`ArrayList`, `HashMap` etc.), exceptions (`IllegalArgumentException` etc.) and a few
others (`StringBuilder`, `Comparator`) are now defined under the `kotlin` package. On the JVM, the names are type
aliases for the corresponding JDK classes, and on the JS, the classes are implemented in the Kotlin standard library.

### Better code generation

JavaScript backend now generates more statically checkable code, which is friendlier to JS code processing tools,
like minifiers, optimisers, linters, etc.

### The `external` modifier

If you need to access a class implemented in JavaScript from Kotlin in a typesafe way, you can write a Kotlin
declaration using the `external` modifier. (In Kotlin 1.0, the `@native` annotation was used instead.)
Unlike the JVM target, the JS one permits to use external modifier with classes and properties.
For example, here's how you can declare the DOM `Node` class:

``` kotlin
external class Node {
    val firstChild: Node

    fun appendChild(child: Node): Node

    fun removeChild(child: Node): Node

    // etc
}
```

### Improved import handling

You can now describe declarations which should be imported from JavaScript modules more precisely.
If you add the `@JsModule("<module-name>")` annotation on an external declaration it will be properly imported
to a module system (either CommonJS or AMD) during the compilation. For example, with CommonJS the declaration will be
imported via `require(...)` function.
Additionally, if you want to import a declaration either as a module or as a global JavaScript object,
you can use the `@JsNonModule` annotation.

For example, here's how you can import JQuery into a Kotlin module:

``` kotlin
external interface JQuery {
    fun toggle(duration: Int = definedExternally): JQuery
    fun click(handler: (Event) -> Unit): JQuery
}

@JsModule("jquery")
@JsNonModule
@JsName("$")
external fun jquery(selector: String): JQuery
```

In this case, JQuery will be imported as a module named `jquery`. Alternatively, it can be used as a $-object,
depending on what module system Kotlin compiler is configured to use.

You can use these declarations in your application like this:

``` kotlin
fun main(args: Array<String>) {
    jquery(".toggle-button").click {
        jquery(".toggle-panel").toggle(300)
    }
}
```
